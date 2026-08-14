# Los cuidados se leen como intervalo, nunca como punto

Al concretar la tabla curada de [#3](https://github.com/Joosle/Plantas/issues/3) apareció que **no hay ningún hecho puntual en esta fuente**. El olivo sale `LO` en WUCOLS, y `LO` es `0,10–0,30`: un factor 3 de ancho en un valor que parecía limpio. Los bins de WUCOLS son `VL <0.10`, `LO 0.10–0.30`, `M 0.40–0.60`, `H 0.70–0.90`, corrección que #3 ya aplicó al modelo agronómico de [#6](https://github.com/Joosle/Plantas/issues/6).

Por tanto la escala nativa se **guarda** tal cual y se traduce **al leer**, a un intervalo `[min, max]`. El valor puntual no existe en la tabla de cuidados: nace más abajo, cuando [#13](https://github.com/Joosle/Plantas/issues/13) lo siembra como parámetro ajustable, y **la anchura del intervalo es el margen de ese ajuste**.

Lo que decide esta forma es el rosal. WUCOLS no tiene entrada genérica para `Rosa`, sólo 74 cultivares, y su reparto en la región 2 es:

```
M 37  ·  LO 31  ·  U 4  ·  VL 1  ·  NA 1
```

#3 lo resumió como «68 de 74 concordantes, moda `M`» y propuso tomar `ks = Moderate`. **Se rechaza.** 37 contra 31 es 54/46 —una moneda al aire— y `M` (0,40–0,60) frente a `LO` (0,10–0,30) es justo el factor 2 del que #13 dijo que «no afina el consejo, lo crea o lo suprime»: con los defaults de v1 decide entre «cada agosto» y «nunca». Colapsar esa distribución en su moda fabrica precisión falsa **exactamente en el punto donde la precisión decide la salida**. El rosal resuelve a `[0.10, 0.60]`, y su anchura declara sola que ahí no se sabe.

Eso hace que un hecho **derivado** —calculado sobre las filas de la fuente, no dicho por nadie— no necesite ser un tipo aparte: es una fuente más, al fondo de la lista de precedencia (ADR [0009](./0009-la-unidad-de-cuidado-es-el-hecho-citable.md)), y en lugar de `quote` lleva la receta reproducible y el conteo. Derivar sólo **ensancha**.

**La dispersión entre regiones no ensancha.** Las seis regiones de WUCOLS son seis climas de California, y ninguno es El Casar; la variación entre R1 y R6 no es incertidumbre sobre la planta sino sobre dónde está plantada. Mezclarlas sería el error que #3 prohibió —«nunca mezclar escalas»— disfrazado de prudencia. Se lee la región 2 y sólo la región 2.

**El ascenso de rango para en el género.** Si no hay hecho para la especie se sube a `parentKey` una vez, marcado como dato del género; si tampoco lo hay, el campo queda **ausente** y el motor se apaga para él. La familia nunca: *Oleaceae* contiene el olivo y el jazmín, *Rosaceae* el rosal y el manzano, y heredar `ks` de ahí sería decir que riegan igual. Por encima del género no continúa el árbol taxonómico — entra el grupo funcional de [#17](https://github.com/Joosle/Plantas/issues/17), que es otro mecanismo con su propia regla de resolución.

**En la interfaz sólo se marca la procedencia que invita a actuar**: dato del género, dato derivado, dato ausente. El resto —fuente, URL, frase literal, intervalo, snapshot— está siempre a un toque, para todos los ítems sin excepción. Nada se esconde, que es lo que exige [#8](https://github.com/Joosle/Plantas/issues/8), y nada estorba la checklist, que es lo que de verdad se consume. El disparador es estructural y no un umbral que haya que calibrar, aplicando a la interfaz la regla de admisión de [#9](https://github.com/Joosle/Plantas/issues/9): un marcador está en la cara si cambia lo que harías.

## Considered Options

- **Traducir a un punto, el centro del bin.** El motor consume números y no aritmética de intervalos. Rechazada porque `0,20` para el olivo y `0,50` para el rosal se ven igual de firmes en pantalla, y uno de los dos es un 37–31.
- **Normalizar a una escala interna en el import.** Una sola representación en toda la base. Rechazada porque retraducir al entrar otra fuente exigiría reimportarlo todo, y porque pierde el bin, que es donde vive la anchura.
- **Subir el ascenso hasta donde haya dato (familia, orden).** Maximiza cobertura: casi ninguna planta se queda sin valor. Rechazada porque el valor vendría de un pariente que riega al revés, y un número inventado es peor que una ausencia declarada.
- **El techo del ascenso depende del campo** (`ks` para en género, sustrato puede subir a familia). Más fiel a la biología. Rechazada por ahora: exige justificar el techo campo a campo con cita, y ningún campo de los que hoy se usan lo pide.
- **Enseñar la cita siempre, debajo de cada ítem.** Máxima transparencia. Rechazada porque la mitad de las semanas el plan está vacío y aun así llevaría cuatro líneas de bibliografía.
- **Enseñar la cita bajo umbral de confianza.** Reutiliza un mecanismo ya decidido. Rechazada por el abeto: WUCOLS es firme sobre *Picea* y sobre *Cupressus* —confianza alta— y aun así es el caso que más urge que el usuario actúe, porque el `ks` se mueve un factor 2,5 según cuál sea.

## Consequences

El resolutor devuelve intervalos, así que todo consumidor de `CareProfile` tiene que decidir explícitamente qué hace con la anchura. Para `KL` ese consumidor ya existe y ya está decidido: es la siembra del parámetro ajustable de #13.

Un intervalo suficientemente ancho es indistinguible de una ausencia a efectos de consejo. No se fija aquí el umbral en que deja de merecer la pena emitir la recomendación; queda para cuando haya un caso que lo pida — hoy el único intervalo ancho es el del rosal, que está arraigado y no se riega.
