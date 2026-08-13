# Lo que se mide no se ajusta, y el detector no autoriza la corrección

Sólo dos números del modelo hídrico son ajustables: `KL`, cuánta agua gasta un ejemplar, y `u`, cuánto aguanta antes de sufrir. Cada uno se mueve únicamente con el tipo de prueba que le corresponde. Todo lo demás que #6 propuso como ajustable —el área de dosel, el volumen de sustrato, la profundidad radicular— **no se ajusta porque se mide**: si la evidencia apunta ahí, el sistema no toca ningún número, te pide el metro.

Y por encima del ajuste hay una separación que ordena el resto: **la señal que detecta el error no es la que autoriza corregirlo.**

## Ninguna planta es no-regable por decreto

Esto es lo primero, porque invalida dos cosas escritas.

`docs/research/modelo-agronomico-riego.md` §6.10 manda **desactivar el balance para crasas y CAM** y darles sólo calendario estacional. Y [#2](https://github.com/Joosle/Plantas/issues/2) observó que hoy no se riega nada y 3 de las 4 plantas son ejemplares arraigados. Puestas juntas, las dos afirmaciones dejaban al motor hídrico sin un solo ejemplar sobre el que emitir un consejo falsable: tres plantas que nunca disparan y una cuarta excluida por regla.

**Se anula la regla.** El balance corre sobre las cuatro. Si el olivo necesita agua un agosto por temperatura y sitio, el motor tiene que decirlo, y una regla que se lo impide por categoría botánica es un fallo, no una precaución. El «hoy no se riega nada» de #2 vuelve a ser lo que era —una observación de la práctica actual— y no configuración.

Lo que hace que un aloe casi nunca dispare y un olivo dispare en un agosto duro no es un interruptor: **son los parámetros de este ADR**. Con los valores por defecto de v1 (`ks` = 0,3 de WUCOLS, `kd` = `kmc` = 0,75, `Zr` = 1 m, franco 150 mm/m), el olivo sale con `KL` ≈ 0,17, `ETc` ≈ 1 mm/día en verano y `RAW` ≈ 99 mm: **~99 días de sequía efectiva hasta pedir agua**, que es exactamente el filo de un verano peninsular. Un factor 2 en `KL` —la resolución real de los bins de WUCOLS— decide entre «cada agosto» y «nunca». El parámetro no afina el consejo: lo crea o lo suprime.

## Dos mandos, y cada uno con su prueba

| Parámetro | Qué gobierna | Única evidencia que lo mueve |
| --- | --- | --- |
| `KL` | cuánta agua gasta | `reading` de humedad, intervalos de riego |
| `u` | cuánto aguanta antes de sufrir | síntomas: comentarios de texto libre y fotos |

Colapsarlos en un único factor por planta —la fase v3 de #6— era la opción defendible mientras la única evidencia fuera «cada cuánto riegas»: en modo maceta el intervalo depende sólo del cociente `W_max·u / (KL·A_dosel)`, así que los factores son indistinguibles entre sí y tres mandos que mueven lo mismo invitan a atribuir al azar.

Lo que rompe el empate es que **la evidencia no es de un solo tipo**. El cajón de comentarios está disponible para toda planta, se le pida acción o no —el ADR 0006 les da sitio en la línea por planta del plan vacío, que es la pantalla de la mitad de las semanas—, y habrá además un registro de fotos periódicas. Un comentario y una foto no hablan de consumo: hablan de **cómo está la planta**. Dos canales distintos identifican dos parámetros distintos aun con muy pocos datos, que es justo lo que un cociente único no puede hacer.

Esto tiene además un efecto de segundo orden que conviene declarar: es lo que da a las tres plantas que nunca se riegan una forma de contradecir al modelo. Sin comentarios ni fotos, el olivo es mudo por construcción.

**El dosel, la maceta y `Zr` quedan fuera.** No por rigidez, sino porque un ajuste sobre una magnitud física es el sistema arreglándose la contabilidad inventando un dosel que nadie ha medido — y con ~20 riegos al año no habría con qué desmentirlo. Cuando la evidencia apunte ahí, lo que se emite es una petición de medida.

## Los límites son físicos y por paso

Dos vallas, ninguna arbitraria:

- **Dominio del parámetro**: `KL` ∈ [0,05 , 1,2] (el `clamp` que #6 ya tiene) y `u` ∈ [0,2 , 0,85]. Cualquier valor de dentro es físicamente interpretable.
- **Tope por paso**: un ajuste no mueve más de ×÷2 en `KL` ni ±0,15 en `u`.

El `[0.5, 2.0]` de #6 protegía un ajuste **automático**; con ratificación humana el tope cambia de función. Ya no protege del algoritmo: **separa dos operaciones distintas**. Un ajuste dentro del paso es calibrar. Querer más que eso no es calibrar — es afirmar que WUCOLS clasificó mal a esta planta, y eso es discutir un **hecho citado con procedencia**, no un parámetro local. Sale por otro camino y se ve como lo que es.

Esa distinción es la que hace que el sistema aprenda algo sobre el mundo y no sólo sobre sí mismo: «WUCOLS falla en España para el olivo» es información; `KL × 3` no lo es.

## Tres hechos concordantes, y al menos uno tiene que contradecir

Para que exista una propuesta hacen falta **3 hechos fechados concordantes**, sin contradicción sin explicar. No es un umbral estadístico y no pretende serlo: con ~20 riegos al año sobre una planta, cualquier `n ≥ 5, p < 0,05` sería decorado. Lo que se exige es concordancia, no significación.

**Al menos uno de los tres tiene que ser una señal negativa** — un `reading` de humedad de la reconciliación del [ADR 0004](0004-el-silencio-del-diario-es-evidencia-con-caducidad.md), o una supresión aceptada del [ADR 0006](0006-el-llm-supervisa-y-el-codigo-propone.md). Ésta es la regla que compra la protección contra la inercia, y es el punto más delicado del diseño.

El diario registra cuándo **regaste**, nunca cuándo hacía falta. Si riegas el aloe los domingos por costumbre, tres riegos concordantes no son evidencia: son tu calendario. Un modelo calibrado con eso aprende tu hábito y luego te lo devuelve como consejo, con toda la apariencia de haber aprendido algo. Un riego sólo puede confirmar; las dos señales negativas son las únicas que pueden **contradecir**.

**El desajuste es visible desde el primer hecho**, en la línea por planta del plan: *«dije 20 % y anotaste húmedo»*. Así la propuesta nunca es la primera noticia — llevas semanas viendo el caso montarse, que es la diferencia entre ratificar y firmar. Y un síntoma que contradice al modelo se señala **el mismo día**, como aviso; no espera a tres, pero tampoco ajusta nada por sí solo.

## La propuesta se juzga por el efecto y se audita por la evidencia

`KL × 1,6` no significa nada para nadie. Una propuesta enseña, en este orden:

1. **El contrafactual, en lenguaje de jardín**: *«el aloe pasaría a pedir agua cada 12 días en vez de cada 19; sobre el año pasado habría acertado 4 de 5 veces en vez de 1»*. Reproyectar el histórico con el parámetro nuevo es aritmética que ya está escrita — la misma recursión de §6.6 sobre datos que ya están en la base.
2. **Debajo, desplegables, los 3 hechos fechados** con enlace a su entrada de diario y marcado cuál es la señal negativa.

Sólo el contrafactual sería un número optimizado contra los mismos datos que lo generaron: siempre parecería una mejora, y no dejaría ver que se apoya en un solo agosto raro. Sólo la evidencia te haría ratificar a ciegas sobre la consecuencia, que es lo único que de verdad importa.

## Revocar es libre; ajustar no

Un ajuste se **revoca** cuando quieras, sin evidencia y sin pasar por el LLM: retirar tu propia ratificación no necesita prueba. Restaura el **valor exacto anterior**, así que no pelea con el tope por paso ni se pasa de largo.

La asimetría es deliberada. Si deshacer exigiera lo mismo que hacer, el sistema sería fácil de estropear y difícil de arreglar, y un ajuste aceptado con prisa un domingo se quedaría meses esperando tres hechos que lo desmintieran.

Y la revocación **queda como hecho fechado**. El rastro *«propuesto en septiembre, revocado en marzo»* es evidencia contra volver a proponer lo mismo: sin él, el LLM re-propondría cada temporada la corrección que ya rechazaste, y el coste de decir que no cada año es exactamente el entrenamiento para aceptar sin mirar.

## El detector: error de predicción con signo

La calibración es el «qué se hace cuando falla». Faltaba el detector — y sin él el modo de fallo más probable es el **silencioso**: un modelo mal parametrizado no se equivoca a gritos, simplemente no dice nada nunca, y el silencio se parece mucho a acertar.

Cada planta lleva un «próximo riego previsto» (§6.6). Al registrar un riego, el sistema anota la diferencia en días **con signo**. No pide ninguna interacción nueva, no cuesta nada y produce una serie que se lee de un vistazo: *«llevo 5 predicciones largándome de media 9 días»*.

**El detector no autoriza.** Es la separación que sostiene todo lo anterior: la fecha real de riego está contaminada por el hábito, así que sirve para saber que algo va mal —que es una pregunta sobre el modelo— pero no para decidir hacia dónde moverlo —que es una pregunta sobre la planta, y ésa la contestan las señales negativas. Confundirlas es cerrar el lazo sobre uno mismo.

Para las tres plantas que no se riegan, la predicción es «ningún riego previsto en el horizonte» y el falsador no es una fecha: es la foto y el comentario.

## El ajuste cuelga del ejemplar

Un `ParameterAdjustment` pertenece a una `Plant`, no a un `CareProfile`. Sale de la propia definición: `KL = ks · kd · kmc`, y `kd` (densidad) y `kmc` (microclima) son de **ese** ejemplar en **ese** sitio. El ajuste absorbe la realidad del rincón donde vive la planta, no una verdad sobre la especie — y si lo que está mal es la especie, ya tiene su camino, que es impugnar el hecho citado.

Hoy da igual: 4 plantas, 4 taxones distintos, ninguna repetida ([#2](https://github.com/Joosle/Plantas/issues/2)). Importa el día que aparezca un segundo aloe, para que lo aprendido en una maceta al suroeste no contamine a un ejemplar a la sombra.

## Considered Options

- **Un único factor opaco `c_planta`** (fase v3 de #6). Imposible de mal atribuir porque no hay nada que atribuir, y era la respuesta correcta mientras la única evidencia fueran los intervalos. Rechazada al aparecer comentarios y fotos: un número que en 1,6 no sabe decir si el aloe bebe más o si la maceta es menor de lo declarado no se puede explicar ni auditar, y desperdicia un canal de evidencia que sí discrimina.
- **Los tres parámetros de #6 por separado.** Máxima explicabilidad, pero `W_max`, `KL` y `A_dosel` sólo entran en el intervalo por su cociente: son indistinguibles con los datos que habrá, y dos ajustes podrían cancelarse sin que nadie lo viera.
- **Ajustar también las magnitudes físicas.** Más potente y más rápido de cerrar. Rechazada: permite al sistema tapar un desajuste inventando geometría, y es indesmentible con esta cadencia de datos.
- **Sin tope, la ratificación es el tope.** Coherente con «nada se aplica solo», pero la ratificación de un toque en el móvil es barata y en tres años no se recuerda por qué se aceptó. Además pierde la señal de «esto no es calibrar, esto es que la fuente se equivocó», que es información sobre WUCOLS en España.
- **Esperar una estación completa antes de proponer nada.** Encaja con el cierre de temporada anual del ADR 0006. Rechazada porque el primer año el modelo no se corregiría nunca, aunque fallara de forma obvia desde junio.
- **Admitir riegos solos si el intervalo es irregular** (comprobando que no son múltiplos de 7 días). Elegante y quirúrgica contra la inercia, pero detectar periodicidad con ~20 observaciones al año no se sostiene.
- **Ajustes que caducan solos cada temporada.** Se autolimpian sin intervención, a cambio de perder el conocimiento bueno por omisión y de obligar a re-ratificar lo mismo cada año — el camino más corto a ratificar sin mirar.
- **El detector es el cierre de temporada anual, o eres tú por el cajón de comentarios.** Ambos son señales reales y siguen ahí, pero ninguno se dispara cuando el motor falla **callando**, que es su modo de fallo dominante con tres plantas que nunca piden nada.

## Consequences

- **Enmienda a `docs/research/modelo-agronomico-riego.md`**: cae la regla de §6.10 que desactivaba el balance para crasas y CAM, y la fase v3 se sustituye por este contrato. La advertencia fisiológica sigue siendo cierta —un aloe no se describe bien con ET0— pero se expresa en los parámetros (`ks` muy bajo, `u` alto) y no en un interruptor.
- **El `reading` binario `seco | húmedo` del [ADR 0003](0003-regla-de-admision-del-vocabulario-del-diario.md) queda cargado de peso**: es la señal negativa que el aloe produce con más frecuencia, y por tanto la que más gobierna la corrección de `KL`. Si algún día hiciera falta más resolución, es el primer sitio donde mirar.
- **El «próximo riego previsto» pasa de adorno del plan a dato persistido**: sin guardarlo no hay error con signo que medir. Refuerza la elección del [ADR 0002](0002-plan-inmutable-y-diario-derivado-de-la-checklist.md) de persistir el `WeeklyPlan`.
- **`ParameterAdjustment` y su revocación son hechos fechados de la `Plant`**, con evidencia citada por identificador y validada por el código, igual que los `citedFacts` del ADR 0006.
- **Aparece una petición de medida** como cosa que el sistema puede pedirte («mide el dosel del aloe») y que no es ni ajuste ni tarea de cuidado. Habrá que ver dónde vive dentro del vocabulario del ADR 0003.
- **Las fotos periódicas dejan de ser una idea suelta y pasan a ser la vía de evidencia de `u`**, y la única señal de las plantas que no se riegan. Queda como pregunta abierta del mapa: cadencia, dónde se guardan —el [ADR 0005](0005-stack-cloudflare-y-pwa-local-first.md) eligió D1, que no almacena binarios— y si el LLM las mira.
