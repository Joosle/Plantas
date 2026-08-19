# La pregunta es la tarea: fenología, ventanas y el silencio no-hídrico

En lo no-hídrico **el diario es el sensor**, no el clima. Una tarea fenológica no se
emite como orden sino como **pregunta sobre algo que el usuario ve**, y la respuesta es a
la vez el disparo de la tarea y la entrada de diario. No hace falta entidad nueva: el
mismo `PlanItem`, con **dos mitades congeladas juntas** —la pregunta y la acción que se
activa si la respuesta es afirmativa— y un vocabulario de respuesta distinto.

Es la inversión exacta del riego. Allí el clima es el sensor y el diario la corrección;
aquí no hay más sensor que el diario. Lo estableció
[Calendario de tareas no-hídricas](https://github.com/Joosle/Plantas/issues/17) al
clasificar por granularidad todo lo afirmable con cita: el cubo que concentra lo
accionable es el **fenológico**, no el climático.

## Un solo `PlanItem`, dos vocabularios de respuesta

Un ítem de riego se responde con una `CareAction`. Una pregunta fenológica se responde con
una `Observation`. Eso es toda la diferencia: **no hay dos clases de `PlanItem`, hay dos
clases de respuesta**, y el camino de «una `Observation` engendra una tarea» ya estaba
abierto en el [ADR 0003](0003-regla-de-admision-del-vocabulario-del-diario.md) para `treat`
(tras `pest` o `disease`) y `harvest` (tras `fruiting`). Lo fenológico usa el mecanismo que
hay; no inventa ninguno.

En la pantalla que ganó
[Prototipo: la pantalla del plan semanal](https://github.com/Joosle/Plantas/issues/11)
—agrupada por verbo— las preguntas forman el grupo **«Mira · N», que encabeza la lista**.
Son las tareas más baratas del plan y las que alimentan todo lo demás.

## Las dos mitades nacen congeladas

El [ADR 0002](0002-plan-inmutable-y-diario-derivado-de-la-checklist.md) congela el
`WeeklyPlan`. Pero esperar al domingo siguiente para decir «trasplanta» es absurdo, y en el
escapo floral del aloe —que hay que despuntar **antes de que abra**— es fatal. La salida no
es mutar el plan ni añadir una tercera entidad junto a `PlanItem` y `Alert`: el ítem se
genera ya con sus dos mitades.

> **Mira el aloe** — ¿asoman raíces por los agujeros de la maceta?
> → si **sí**, toca **trasplantar**.
> *(Región de Murcia, 2018 — frase literal)*

Contestar `presente` no **añade** nada al plan: **activa** una mitad que ya estaba escrita.
El acta sigue siendo exacta. El ítem vive en «Mira» mientras es pregunta y pasa al grupo de
su verbo («Trasplanta · 1») en cuanto se activa la segunda mitad; el agrupado por verbo
funciona sin excepción.

Que la consecuencia sea visible antes de contestar es deliberado. Sesga algo la respuesta
—«si digo que sí, me cae trabajo»—, pero preguntar sin decir para qué es opaco, y este
módulo entero se construye sobre la auditabilidad: ver por qué te mandan a mirar es lo que
hace que mirar valga la pena.

## El «no» es dato, y cabe en una sola `Reading`

`phenology`, escala **`presente | ausente`**, anclada al **hecho citable** que hizo la
pregunta. Una `Reading` genérica y no una etiqueta por pregunta, porque
[La tabla de cuidados curada](https://github.com/Joosle/Plantas/issues/18) fijó que la
unidad de cuidado es el hecho citable: con etiquetas por pregunta
(`roots_showing`, `flower_stalk_up`, `pups_20cm`…) el vocabulario cerrado crecería **una
entrada por hecho**, que es justo lo que el ADR 0003 evitó al preferir una regla a una
lista. Así **el vocabulario no crece nunca: crecen los hechos**.

Pasa la regla de admisión por las dos puntas: el motor **lee** `presente` para activar la
mitad de acción y **lee** `ausente` para reprogramar la pregunta.

Las respuestas son **dos, no tres**. En fenología «no» *es* «todavía no» —las raíces
acabarán asomando— y no hay diferencia observable entre ambas. La tercera respuesta
aparente no es una respuesta: es **no haberla mirado**, y eso ya tiene mecanismo, el
arrastre del ADR 0002 con la antigüedad visible. Se preserva así la distinción que el
[ADR 0004](0004-el-silencio-del-diario-es-evidencia-con-caducidad.md) defiende: *no había
nada* (`ausente`, con fecha) es información; *no lo he mirado* no lo es.

Nota de solape: la etiqueta `flowering` del ADR 0003 activa la cadencia de `deadhead`, y el
escapo del aloe se despunta **aguas arriba** de la floración. No colisionan porque son
cosas distintas —un hecho citable y una etiqueta—, lo que es un argumento más para la
`Reading` genérica.

## Cadencia: ventana + frecuencia del hecho, sin backoff

La cadencia es **propiedad del hecho citable**, no de la planta ni de las respuestas del
usuario. Un `ausente` **reinicia el reloj pero no lo alarga**.

**La ventana mata la fatiga, no el backoff.** Preguntar «¿hay hijuelos?» cada semana
durante dos años no se arregla espaciando la pregunta: se arregla **no haciéndola fuera de
temporada**. Preguntar por el escapo floral en noviembre no es molesto, es *incorrecto*.

Y el backoff exponencial —la solución obvia— **falla justo en el caso más valioso**: tras
tres `ausente` seguidos se saltaría la semana en que el escapo aparece. Optimiza la
comodidad a costa de la única tarea con caducidad real.

| Pregunta | Ventana | Dentro | Fuera |
|---|---|---|---|
| Escapo floral por encima de las hojas (aloe) | floración | **semanal** | no se pregunta |
| Hijuelos ≥ 20 cm (aloe) | todo el año, revisión bienal | ~6 meses | — |
| Raíces asomando por los agujeros (aloe) | todo el año | ~6 meses | — |
| Crecimiento pobre / acículas descoloridas (conífera) | fin de invierno | una vez | no se pregunta |

Sobre el inventario real eso es «Mira · 1» la mayoría de los domingos. Y al ser la cadencia
un campo del hecho, **es auditable como todo lo demás**: «¿por qué me preguntas esto cada
semana?» tiene respuesta con cita. Si la fuente no dice cada cuánto mirar, la cadencia es
una **derivada** del proyecto, y el #17 obliga a marcarla como tal y a no darle confianza
`alta`.

## El silencio no-hídrico no lleva mecanismo

**Ninguna reconciliación fuera del riego.** El modo de fallo es otro y no duele: el ADR 0004
reconcilia porque el silencio hace **derivar un modelo cuantitativo** y produce consejo
seguro de sí mismo y falso («URGENTE, 12 días sin agua» cuando regaste el martes). Si no
contestas «¿asoman raíces?», la app simplemente **no dispara** el trasplante: se pierde una
oportunidad, no se emite una falsedad. Con las ventanas anuales y holgadas que encontró el
#17 —y con IFAPA descartando explícitamente la penalización por podar el olivo fuera de
plazo— ese coste es de semanas.

Hay además un argumento casi lógico: el ADR 0004 se autoimpuso ser **la única interrupción
que este diseño se permite**. Un ítem fenológico sin responder **ya es una pregunta**;
reconciliarlo sería preguntar por qué no contestaste a una pregunta.

**Lo que sí necesita regla: la caducidad de la ventana.** El ADR 0002 dio dos
comportamientos —el `PlanItem` se arrastra indefinidamente, la `Alert` caduca sola— y una
tarea estacional no es ninguno. Un «poda el rosal» sin responder no puede arrastrarse hasta
agosto acumulando antigüedad como un reproche: **caduca al cerrarse la ventana y lo dice
sin dramatismo** («la ventana de poda del rosal se cerró en marzo; no pasa nada, vuelve en
enero»). El olivo es la excepción que autoriza su propia fuente: allí la ventana no cierra
con penalización, así que el ítem sigue ofreciéndose sin alarma.

No se registra nada al caducar. Si podaste, hay `CareAction(prune)`; si no, no lo hay, y el
motor dice «última poda: hace dos años» sin que ningún número derive.

## 📅 es el último recurso

**Si existe un observable citable, la tarea es 🌱 y no 📅.** El calendario es el último
recurso, no la forma por defecto — la conífera lo ilustra sola: su abonado parecía
estacional y su único disparador respaldado es un síntoma que mirar.

Las 📅 que sobreviven **sí se emiten**, con tres condiciones:

1. **Una vez al abrirse la ventana**, no cada semana. «Abona el aloe» repetido de abril a
   septiembre son 26 copias de la misma frase, y es lo que hace que se deje de leer la
   lista. Para un hecho 📅, la cadencia del hecho vale «una vez por ventana».
2. **Declaradas como calendario en el propio texto**: «abril–septiembre es la época de
   abonado del aloe (CARM 2018) — la app no puede saber si le hace falta». El #17 fue
   tajante en que la app no debe fingir inteligencia aquí.
3. **Filtradas por la regla dura del #17** antes de emitirse: si la tarea presupone riego o
   fertirrigación y el ejemplar no lo recibe, se suprime y se explica.

| Tarea 📅 candidata | Resultado |
|---|---|
| Abonado del olivo | **suprimida por falta de respaldo** (ver abajo) |
| Abonado del aloe (abril–sep) | se emite — en maceta, y sí se riega |
| Poda del rosal (enero) | se emite |
| Abonado del rosal | **compuesta**: «abona **y riega a fondo**» o no se emite — está bajo alero |
| Abonado de la conífera | **no es 📅**: disparador fenológico; si crece bien, *no la abones* |

## Dos filtros distintos, y no hay que confundirlos

Es el error que estuvo a punto de colarse en este ticket, y conviene dejarlo escrito.

- **«La tarea presupone agua y el ejemplar no la recibe.»** El agua no tiene que venir de la
  manguera: puede venir del cielo. Al **olivo no le aplica** — el #17 sacó de ahí su mejor
  conversión climática: MAPA («para aprovechar todas las lluvias primaverales») y BOJA 2026
  («cerca de las raíces **cuando se prevén lluvias**») convierten el *cuándo* del abonado en
  `precipitation_sum` del pronóstico. Si el olivo se abonara, sería tarea **🌡, no 📅**.
  Donde este filtro sí muerde es en el **rosal bajo alero**, y por eso su tarea sale
  compuesta.
- **«No consta que lo necesite.»** Éste es el que afecta al olivo. Todo el método español
  calcula la dosis desde la cosecha esperada (15–20 kg N por 1.000 kg de aceituna): sin
  cosecha la base se anula, y no existe publicación española sobre nutrición del olivo
  *ornamental*. El #17 lo listó entre lo no respaldado.

**Donde no consta que haga falta, la app lo dice una vez, como consejo negativo declarado**
—«no consta que un olivo ornamental sin cosecha necesite abono; todo el método español
calcula la dosis desde la aceituna recolectada»— y no cada semana. Callarse dejaría un
hueco raro (la planta más grande de la casa, y el plan nunca menciona abonarla); emitirla
igual con confianza baja sería la falsa precisión que el #17 prohíbe, y encima con la dosis
inventada. El #17 ya estableció que **los consejos negativos son de primera clase** y hay
que diseñarlos como éxito, no como hueco.

## Considered Options

- **Un tipo propio, `PhenologyCheck`, junto a `PlanItem` y `Alert`.** Nombra bien la
  diferencia, pero duplica arrastre, derivación de estado y renderizado para representar
  algo que sólo se distingue en el vocabulario de la respuesta.
- **Una etiqueta de `Observation` por pregunta.** Más explícito y más fácil de consultar,
  pero el vocabulario cerrado pasaría a crecer al ritmo de la tabla de cuidados, que es
  exactamente lo que el ADR 0003 quiso evitar.
- **Backoff exponencial tras cada `ausente`.** La respuesta natural a la fatiga, y falla en
  el único caso con caducidad estricta: se salta la semana del escapo. La fatiga la resuelve
  la ventana, no el espaciado.
- **Reconciliación no-hídrica simétrica a la del riego.** Coherente de forma, pero
  interrumpe para arreglar un fallo que aquí no existe: sin modelo cuantitativo detrás, el
  silencio no produce consejo falso.
- **La mitad de acción nace en el `WeeklyPlan` siguiente.** Lo más respetuoso con la
  inmutabilidad y lo peor para el usuario: una semana de retraso pierde el escapo floral,
  que es la mejor tarea del inventario.
- **Callarse donde no hay respaldo.** Nunca miente, pero deja al olivo sin explicación y
  parece un olvido de la app en vez de una decisión.

## Consequences

- **Enmienda a [#18](https://github.com/Joosle/Plantas/issues/18)**: el hecho citable
  necesita tres campos que hoy no tiene — **`ventana`**, **`cadencia`** y la **mitad de
  acción** que se activa con `presente`. Es aplicar la decisión del #18 (la unidad es el
  hecho citable), no revisarla.
- **`Reading` deja de ser un caso único.** Tenía un solo miembro —humedad del sustrato,
  `seco | húmedo`— y ahora tiene dos. El mecanismo del ADR 0003 queda confirmado como
  general.
- **Las tareas fenológicas producen la primera señal medible de lo no-hídrico**: la app
  predice una ventana y el usuario contesta `presente` en una fecha, así que se puede juzgar
  si la ventana acertó. Eso afila la niebla que quedaba sobre cómo evaluar el consejo
  no-hídrico — ticket propio.
- **La caducidad de ventana es un tercer comportamiento** junto al arrastre del `PlanItem` y
  la expiración de la `Alert`. Hay que implementarla explícitamente.
- Sobre el inventario de hoy, **casi toda la carga fenológica es del aloe**. La conífera
  aporta una pregunta al año, y el olivo y el rosal ninguna: su parte es 🌡 y 📅.
