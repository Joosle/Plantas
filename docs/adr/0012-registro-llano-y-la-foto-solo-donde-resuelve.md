# La interfaz habla en llano, y la foto sólo se pide donde una foto resuelve la duda

Todo lo que el usuario lee va en **idioma corriente**: sin coeficientes, sin siglas, sin códigos de zona. `KL Rosa · 0,10–0,60 (género)` se enseña como «No sé cuánta agua pide un rosal»; `AEMET · zona 681903 Alcarria de Guadalajara` como «Lo avisa AEMET para Guadalajara». Los `citedFacts` de [#8](https://github.com/Joosle/Plantas/issues/8) conservan su id y su entrada de catálogo: lo que cambia es que **cada hecho lleva su redacción llana al lado de la técnica**. La trazabilidad no se pierde — deja de ser lo que se enseña por defecto.

Y cuando el sistema tiene una duda que no puede cerrar solo, puede pedir **una foto en vez de un argumento** — pero sólo donde una foto resuelva la duda la mayoría de las veces. El criterio es que la duda sea sobre algo **visible en la superficie de la planta**.

Ese criterio no abre un eje nuevo: coincide con el cubo **fenológico 🌱** que [#17](https://github.com/Joosle/Plantas/issues/17) ya había señalado como el único sitio donde se concentra lo accionable — escapo floral por encima de las hojas, hijuelos de 20 cm, raíces asomando por el drenaje, madera sin acículas — más la identificación, que es una foto desde [#5](https://github.com/Joosle/Plantas/issues/5). Es la misma clase de pregunta: algo que se ve.

Por el mismo criterio quedan fuera:

- **El estado hídrico.** El `reading: seco | húmedo` de [#9](https://github.com/Joosle/Plantas/issues/9) es una lectura **de tacto**; una foto de la tierra bajo un alero dice poco.
- **Y, incómodamente, el caso que originó la regla.** La supresión del riego del rosal pregunta si le hace falta agua —una cantidad oculta— y la foto sólo la contesta por un rodeo: si tiene mala cara. Ese rodeo es **asimétrico**: una planta que se ve mal está mal, pero una que se ve bien puede estar a dos días de venirse abajo. La foto podría por tanto **vetar** una supresión y nunca confirmarla, y una decisión que sólo puede caer de un lado no es una decisión. El rosal conserva sus dos botones, dichos en llano.

## Considered Options

- **Mantener el registro técnico con un botón de «explícamelo sencillo».** Dos registros que escribir y mantener, y el técnico gana por defecto justo en el camino que importa. Un botón así es además una confesión de que el defecto está mal elegido.
- **La foto como salida general para cualquier duda.** Traslada el coste al usuario —levantarse e ir hasta la planta— en un sistema donde `docs/adr/0006` da por hecho que la mitad de las semanas no habrá nada que hacer. Si cada duda cuesta un paseo, el domingo deja de ser barato.
- **Ninguna foto.** Renuncia a la evidencia más barata y más fuerte disponible precisamente para las tareas que [#17](https://github.com/Joosle/Plantas/issues/17) identificó como las accionables.

## Consequences

- **Cada hecho de la tabla de [#18](https://github.com/Joosle/Plantas/issues/18) necesita su redacción llana como campo**, escrita una vez al crear el hecho. No es una traducción en tiempo de ejecución: el LLM no debe parafrasear un número que no calculó.
- **Lo llano no es lo seguro.** «Su coeficiente está sin determinar entre 0,10 y 0,60» pasó a «hay rosales que beben bastante y otros que casi nada»: lo que se dice es la **anchura** del intervalo de [#18](https://github.com/Joosle/Plantas/issues/18), que es donde esa decisión puso el mensaje. Traducir no puede convertir un rango en una afirmación.
- **Abre una dependencia que la arquitectura no tiene**: contestar una tarea fenológica con una foto exige un modelo de visión que **diagnostique estado**, y [#5](https://github.com/Joosle/Plantas/issues/5) dejó fijado que Pl@ntNet identifica especie, no condición. Roza la niebla de plagas y enfermedades del mapa, que ya había localizado dos candidatos (`/diseases` de Pl@ntNet, `crop.health` de Kindwise). No abre ticket: cae sobre dos que ya existen — [#26](https://github.com/Joosle/Plantas/issues/26), que decide la forma de la pregunta fenológica, y [#28](https://github.com/Joosle/Plantas/issues/28), que hasta ahora sólo contemplaba la foto **periódica** del ADR 0007 y ahora tiene que contemplar también la foto **a demanda** que cierra una pregunta concreta.
- Mientras ese modelo no exista, las tareas fenológicas se contestan como decidió [#17](https://github.com/Joosle/Plantas/issues/17) —el usuario responde sí o no a algo que ve— y la foto es sólo un adjunto para el diario. La regla no bloquea nada: describe dónde tiene sentido gastar el paseo cuando se pueda.
