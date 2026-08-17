# Proteger es una temporada, y el diario apaga la alerta

Al resolver [#19](https://github.com/Joosle/Plantas/issues/19) la pregunta obvia era con cuánta antelación avisar de una helada. La pregunta que la desactiva es otra: **¿por qué habría que decidir cada noche algo que se decide una vez por invierno?**

## El aviso noche a noche no era la forma

De las cuatro plantas de la casa sólo el aloe se puede mover — las otras tres están arraigadas en el suelo. Y [#17](https://github.com/Joosle/Plantas/issues/17) encontró que protegerlo en invierno no es un seguro contra la noche mala: da **+37 % de hoja**. O sea que la respuesta racional a un invierno de El Casar no es sacarlo y meterlo cuarenta veces, es **meterlo y dejarlo dentro**.

Con eso, un aviso por cada noche que cruce el umbral produciría decenas de interrupciones al invierno para mover la misma maceta de un lado a otro — el modo de fallo que el propio ticket señaló: el ruido gasta la credibilidad del canal justo antes de la noche en que hace falta.

Así que la protección es una **tarea de temporada** —un `PlanItem` estacional de #17— y la alerta se gana el sitio sólo en los bordes, que son además las que de verdad matan: la helada **temprana** de octubre, antes de meterlo, y la **tardía** de abril, después de sacarlo. Las que pillan a la planta fuera.

## La ventana la abre el pronóstico, no el calendario

Las demás tareas estacionales de #17 llevan fechas. Ésta no, y por un motivo que se paga solo: **si la ventana la abre el primer pronóstico de helada del otoño, «helada temprana» e «inicio de temporada» dejan de ser dos eventos y pasan a ser uno.** El borde que preocupaba se cierra por delante sin mecanismo adicional, y no hay dos fechas inventadas que mantener.

El cierre usa el mismo principio para no inventar tampoco su número: **el «sácala» nace cuando el horizonte completo de pronóstico —los 10 días que ya se descargan— no contiene ningún cruce.** Si después llega una tardía, la alerta dispara y la ventana **se vuelve a abrir**: el mecanismo se cura solo por los dos lados.

## El diario apaga la alerta

Ésta es la parte que llega más lejos, y no era del ticket.

Una alerta que pide meter la maceta **no tiene sentido si la maceta ya está dentro**. Y quién sabe si está dentro no es el pronóstico ni la configuración: es el **diario**, que [#7](https://github.com/Joosle/Plantas/issues/7) ya había declarado el estado del sistema. La regla general que sale de aquí:

> **La alerta se suprime cuando la acción que pediría ya está hecha.**

No es un caso especial del aloe. Es la misma relación que el balance hídrico tiene con `water`, aplicada a una señal perecedera en vez de a una acumulativa.

Su consecuencia inmediata es que **`shelter` (proteger) pasa a ser el tercer verbo que el motor lee**, junto a `water` y `mulch`. Y cumple la regla de admisión del ADR `0003` (rama de [#9](https://github.com/Joosle/Plantas/issues/9)) por **las dos mitades a la vez** —el plan puede mandarlo y el motor lo lee—, que es lo que ninguno de los otros siete verbos hace hoy. Esto **enmienda a [#9](https://github.com/Joosle/Plantas/issues/9)**, que lo tenía como verbo mudo.

## Vigilancia el lunes, confirmación la víspera

La antelación no necesita un estado nuevo de `Alert`: necesita repartirse entre los dos crons que [#10](https://github.com/Joosle/Plantas/issues/10) ya separó.

- El **plan del lunes** —que por diseño **no interrumpe**— lleva una línea de **vigilancia**: «el jueves puede helar sobre el aloe; te lo confirmo la víspera». Es previsión, no tarea.
- El **cron diario** de la víspera emite la `Alert`, que **sí** interrumpe, con la ventana ya caducada de origen.

Que el aviso lejano sea una línea del plan y no una alerta temprana es lo que evita duplicar el vocabulario de estados. A siete días una mínima falla varios grados; a la víspera, casi nada. Cada cron avisa con la certeza que tiene.

**Y el desdecirse se resuelve sin tocar nada.** El `WeeklyPlan` es inmutable (ADR [0002](./0002-plan-inmutable-y-diario-derivado-de-la-checklist.md)), así que si el miércoles el pronóstico se cae, la línea del lunes no se puede reescribir — pero tampoco hace falta: **su estado se deriva**, igual que el de cualquier `PlanItem`, de si la `Alert` llegó a nacer. Al pasar la víspera queda confirmada con enlace a la alerta, o descartada. El plan escrito no se toca y el silencio deja de ser ambiguo, porque la propia línea cambia de cara.

Una `Alert` por evento, con su ventana. Si el frío se alarga, **la misma alerta se extiende** — no nace otra — y sólo se vuelve a interrumpir si **escala**: cruza un umbral peor del que ya avisó. Es #7 tal cual: caduca sola, nunca se arrastra.

## Considered Options

- **Todo por alerta, noche a noche.** Máxima precisión teórica y ninguna regla de calendario que mantener. Rechazada por el ruido: decenas de avisos por invierno para mover la misma maceta.
- **Todo por temporada, sin alerta de helada de planta.** Lo más simple con diferencia. Rechazada porque deja descubiertos justo la temprana y la tardía, que son las que matan.
- **Ventana de fechas fijas** (1-nov a 31-mar), como las demás tareas de #17. Predecible y sin lógica. Rechazada: dos fechas inventadas, y el año que hiele el 12 de octubre pilla la maceta fuera.
- **Abre el pronóstico, cierra el calendario.** Asimétrico a propósito: entrar es urgente, salir no, y una fecha evita sacarla en el falso final de invierno de febrero. Rechazada por tener dos mecanismos para una sola ventana cuando el horizonte de pronóstico ya da un cierre sin inventar nada — y porque la tardía posterior reabre la ventana igual.
- **Avisar en cuanto la helada asome en el pronóstico** (hasta 10 días), con la incertidumbre declarada. Más margen para actuar. Rechazada como **alerta**; se adopta como **línea del plan**, que es lo mismo sin interrumpir.
- **Dos alertas, una temprana que no interrumpe y otra la víspera que sí.** Cubre planificación y ejecución. Rechazada porque duplica los estados de `Alert` para conseguir lo que el `WeeklyPlan` ya da gratis.
- **Retirada explícita cuando el pronóstico se cae** («la helada del jueves ya no se espera»). Coherente con que nada desaparezca en silencio. Rechazada porque interrumpe por una no-noticia, que es exactamente lo que #10 decidió no hacer.
- **No hacer nada si no llega la alerta.** Cero mecanismo. Rechazada por el fallo que #10 legisló dos veces: «no pasó nada» y «el motor está muerto» se ven igual — y aquí encima con una promesa explícita del lunes sin contestar.

## Consequences

La supresión por diario crea una dependencia nueva: el cron diario tiene que leer el estado de `shelter` de cada planta antes de emitir. Es la primera vez que una alerta depende del diario y no sólo del clima.

Si el usuario mete la maceta pero **no lo registra**, seguirá recibiendo alertas. Es coherente con que el diario sea el estado del sistema, pero conviene que la alerta ofrezca registrar la acción de un toque desde el propio aviso — el mismo gesto de una pasada que fijó [#11](https://github.com/Joosle/Plantas/issues/11).

La ventana abierta por pronóstico significa que **el primer aviso del otoño siempre interrumpe**, aunque sea una helada suave: es el que abre la temporada. No es un fallo de calibración.

Con las cuatro plantas de hoy, todo este mecanismo tiene **un solo sujeto**: el aloe. Igual que el motor de riego. Es el patrón que [#2](https://github.com/Joosle/Plantas/issues/2) encontró y que sigue mandando en el proyecto.
