# Las fotos viven en R2, sin tope de retención, a 2576 px

Una foto se redimensiona **en el navegador** a 2576 px de lado largo antes de salir del móvil, se guarda en **R2** como archivo único —sin conservar el original de la cámara—, y **no se borra nunca**. En la base de datos vive un `PhotoRef` con la clave de R2, jamás el binario.

## El tope de retención sería teatro

El ticket preguntaba cuántas fotos se conservan por planta y durante cuánto tiempo. La cuenta contesta sola: **4 plantas** ([#2](https://github.com/Joosle/Plantas/issues/2)), una foto mensual por planta, ~800 KB por archivo → **~38 MB al año**. El free tier de R2 son **10 GB**, sin coste de salida.

Son más de doscientos años. Cualquier límite por planta que se fije hoy no protege de nada: sólo borra el único registro visual del jardín. Si algún día deja de ser cierto será porque el inventario creció un orden de magnitud, y esa es una decisión que se toma entonces con el dato delante, no ahora a ciegas.

R2 es además el único almacén de blobs de Cloudflare con free tier propio, y Cloudflare de extremo a extremo ya estaba fijado por [#10](https://github.com/Joosle/Plantas/issues/10). Meter 800 KB de JPEG en D1 sería usar SQLite de saco.

## 2576 px no es un número redondo, es el techo del modelo

Verificado contra la documentación de visión de la API: Opus 5 está en el tramo de **alta resolución**, con **lado largo máximo 2576 px** y tope de **4784 tokens visuales**; el coste en tokens es `⌈ancho/28⌉ × ⌈alto/28⌉`. Todo lo que se suba por encima de 2576 px lo reescala el servidor antes de mirarlo.

O sea que subir el original de 4 MB del móvil **gasta los bytes dos veces**: se pagan en tiempo de subida y se tiran en el servidor. Redimensionar en el cliente a 2576 px deja el archivo en ~800 KB, que es **5× menos tiempo de subida** con la cobertura que hay en el jardín — la preocupación que el ticket planteaba explícitamente. El redimensionado va en el canvas del navegador y no cuesta nada al Worker.

**No se conserva el original.** El modelo lo reescalaría igualmente y el ojo en una pantalla de móvil no distingue más; quintuplicar volumen y tiempo de subida por un recorte hipotético no compensa.

### Lo que esto cuesta

~0,024 $ por imagen analizada. Con dos imágenes por análisis (la nueva y la de referencia estacional) y ~4 análisis al mes, son **~2,3 $/año**. Suma a la tabla de #10 sin romper el «céntimos al mes, cero suscripciones»: sigue sin haber suscripción y sigue sin costar nada el camino de alertas.

## La foto nunca bloquea la checklist

Una entrada de diario son ~200 bytes; una foto, 800 KB. Con la misma política en la misma cola, un fallo de subida de foto atasca entradas de texto que ya estaban listas.

La foto va por tanto en la **misma cola de IndexedDB pero como ítem propio**: respondes al ítem de la checklist, la entrada de diario se escribe local y al instante y sube en cuanto haya red; la foto se encola aparte y sube cuando puede, sin que nada la espere. Nunca hay una pantalla de «subiendo…» en el jardín. Si la subida de la foto falla, la entrada ya está guardada y la foto reintenta.

## El backup se lleva las fotos, en su propio repo

[#10](https://github.com/Joosle/Plantas/issues/10) volcó la base a un repo privado de Git, «kilobytes por commit», clonable y legible por cualquier SQLite. Desde el [ADR 0015](0015-la-foto-se-analiza-al-subirla-y-el-diario-es-la-junta.md) la foto es la **fuente** de la que sale media evidencia del sistema, así que un backup que guarda las conclusiones y tira los datos que las produjeron no deja auditar nada.

Las fotos van a un **segundo repo privado**, empujadas en lote por el cron del lunes, para no estropear la propiedad que hace bueno el backup de #10. Git con binarios es feo en general, pero aquí no duele: los archivos **nunca se modifican**, así que no se rebotan versiones — es un almacén tonto con hospedaje gratis. GitHub recomienda por debajo de 1 GB y avisa a los 5 GB: más de veinte años a este ritmo, y 0,00 €.

## Considered Options

- **Tope de N fotos por planta, rotando las viejas.** La respuesta refleja a «lo único que puede crecer sin techo». Rechazada por la aritmética: no hay techo que alcanzar en este siglo, y el borrado sería pérdida pura.
- **Guardar el original y servir una versión reducida.** Máxima opcionalidad futura. Rechazada: 5× el volumen y 5× el tiempo de subida por un uso que hoy no existe.
- **Cloudflare Images** en vez de R2. Redimensionado y variantes gestionados. Rechazada: no tiene free tier, y romper el «cero suscripciones» de #10 por un `canvas.toBlob()` de diez líneas es mal cambio.
- **La foto sube antes de dar el ítem por respondido.** Coherencia fuerte entre lo que ves y lo que hay. Rechazada: convierte un toque en una espera con mala cobertura, justo en el sitio donde la app tiene que ser rápida.
- **Sin backup de fotos**, sólo R2. La durabilidad de R2 sobra y el riesgo real es que Cloudflare cierre. Rechazada por el ADR 0015: sin las fotos, el `.sql` ya no es «tu diario entero».
- **Fotos en el mismo repo que el `.sql`.** Una pieza menos. Rechazada: mete binarios en el artefacto cuya virtud es clonarse en segundos.

## Consequences

- El `PhotoRef` necesita, además de la clave de R2, una marca de **analizada / pendiente** que el cron del lunes pueda barrer, y una de **referencia estacional**.
- Aparece un **segundo repo privado** y con él un segundo destino para el PAT de backup de #10.
- El coste del sistema deja de ser sólo el LLM del lunes: ahora hay gasto **proporcional al número de fotos**. Sigue siendo despreciable con 4 plantas, pero ya no es plano respecto al inventario, que es la primera magnitud del sistema que escala con él.
