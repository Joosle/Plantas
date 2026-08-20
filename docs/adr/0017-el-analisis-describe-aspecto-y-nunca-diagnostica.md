# El análisis de foto describe aspecto, nunca diagnostica, y sus errores se desmienten con otra entrada

La `Observation` de autor `system` que produce el análisis de una foto ([ADR 0015](0015-la-foto-se-analiza-al-subirla-y-el-diario-es-la-junta.md)) escribe en el **mismo vocabulario cerrado** del [ADR 0003](0003-regla-de-admision-del-vocabulario-del-diario.md), pero sólo en su mitad de **aspecto**. Le quedan vetadas `pest` y `disease`.

| El análisis puede escribir | Le queda vetado |
| --- | --- |
| `yellowing`, `wilting` | `pest` |
| `flowering`, `fruiting` | `disease` |
| `frost_damage`, `sun_damage` | |

La línea **no** es «lo que no acciona»: `flowering` activa la cadencia de `deadhead` y `fruiting` activa `harvest`, igual de mandonas que `pest`. La línea es la que el [ADR 0012](0012-registro-llano-y-la-foto-solo-donde-resuelve.md) ya había trazado para decidir cuándo pedir una foto: **el aspecto se ve, el diagnóstico se deduce**. Unas hojas amarillas son superficie de la planta; una plaga es atribuir una causa a lo que se ve.

Ese veto no es simetría estética con [#5](https://github.com/Joosle/Plantas/issues/5), sino el mismo criterio del ADR 0015 aplicado de nuevo: el LLM mira las fotos porque «esta hoja está parda» no tiene fuente citable mejor que él, mientras que identificar la especie sí la tiene. Diagnosticar una fitopatología **también** tiene fuentes mejores que un modelo generalista, y además el dueño del jardín no puede desmentir un falso positivo. `treat` es echar producto sobre una planta sana.

## La precisión vive en la escala, no en la prosa

Cada etiqueta de aspecto lleva una **extensión ordinal cerrada** de tres o cuatro pasos — `puntual | parte | mayoría` — que es un `Reading` más, la figura que `CONTEXT.md` define como «un juicio sobre un estado, no una prosa sobre él». Hoy sólo existía uno, la humedad del sustrato.

La extensión pasa la regla de admisión de [#9](https://github.com/Joosle/Plantas/issues/9) porque **el falsador de `u` la lee**: el ADR 0015 montó todo el mecanismo sobre detectar *cambio* frente a las descripciones anteriores, y sin nada comparable entre semanas no hay cambio que detectar, sólo dos prosas distintas. Para el olivo, que nunca se riega, es el único falsador que existe.

Se rechaza el porcentaje. «Puntas pardas en un 30 % del follaje» es más comparable, pero el número lo inventa el modelo, nadie puede desmentirlo, y un 10 % → 15 % que en realidad es ruido de encuadre se lee como deterioro. Un salto de paso ordinal es señal; dentro del mismo paso, silencio. Se paga que un deterioro lento no se ve hasta que cruza el umbral.

Con la precisión alojada en el `Reading`, la **redacción llana** del ADR 0012 aplica aquí sin excepción. Parecía haber tensión —el texto tiene dos lectores, el usuario y el análisis de la semana siguiente— y no la hay: lo que el segundo necesita es la escala, no la prosa. Un registro, no dos.

## Corregirla es escribir, no editar

Un error del análisis no se queda quieto: el ADR 0015 realimenta cada análisis con **sus propias descripciones anteriores**, así que una línea base falsa se hereda semana tras semana hasta convertirse en el pasado oficial de esa planta.

La corrección es por tanto **una entrada más**: una `Observation` tuya que apunta a la del sistema y la marca refutada. Nada se borra ni se edita — el [ADR 0002](0002-plan-inmutable-y-diario-derivado-de-la-checklist.md) compró la auditabilidad al congelar el plan, y editar el diario en el sitio la devuelve. Lo que cambia no es el histórico sino **lo que se le pasa al análisis siguiente**: la vista resuelta, con la descripción refutada sustituida por la tuya. Auditoría intacta, línea base saneada.

Es el mismo patrón que el ADR 0002 ya usa para el estado de un `PlanItem`: se deriva de las entradas, no se muta.

## Se reconoce leyendo, no sólo mirando

La entrada del sistema vive en la **misma línea de tiempo** que las tuyas, ordenada por `occurredAt`, con la autoría marcada. Y además el análisis escribe con **voz propia**: en tercera persona y **citando siempre la foto fechada de la que sale** — «en la foto del 12 de mayo se ven puntas pardas en parte del follaje» —, frente a tu primera persona en pasado.

El marcador de autoría solo no basta porque hojeando se pierde, y estas entradas son **opinadas**: si una se te queda en la cabeza como si la hubieras escrito tú, no la desmientes nunca y la corrección de la sección anterior se queda sin usar. Citar la foto pone además el falsador a un toque: si dice «mayoría del follaje» y la abres y no lo está, desmentirla es inmediato.

## Considered Options

- **Sólo prosa llana, sin ninguna etiqueta, con `inspect` como única salida legible.** Máxima coherencia con «el LLM describe, no acciona». Rechazada: deja al motor sin nada estructurado que leer, así que la comparación entre semanas vive entera dentro del modelo — exactamente el fallo silencioso que el ADR 0003 rechazó.
- **Las ocho etiquetas, igual que las tuyas.** Una foto podría disparar `treat` sola, que es lo más útil para quien no sabe de plagas. Rechazada: el productor de la evidencia mandando la acción cierra el lazo que el ADR 0015 dejó abierto a propósito, y sobre un falso positivo de `disease` no hay quien arbitre.
- **Etiquetas propias del analizador** (moteado, defoliación, clorosis internervial). Un analizador ve cosas que un humano no nombra igual. Rechazada: dos vocabularios que mantener y una regla de admisión que re-litigar, a cambio de un detalle que el motor no lee.
- **Puede sugerir `pest`/`disease` marcadas sin confirmar, y tú las ratificas.** Nada se pierde y nada acciona solo. Rechazada por ahora: añade un estado nuevo a `Observation` y una bandeja de pendientes que, sin atender, llena el diario de propuestas muertas. Vuelve si el ticket de plagas encuentra fuente citable.
- **Marcarla errónea sin más**, en vez de desmentirla con una entrada. Más barato. Rechazada: dice que estaba mal pero no qué era lo correcto, así que el análisis pierde tres semanas de referencia en vez de recuperarlas.
- **Carril aparte para lo automático.** Separación imposible de confundir. Rechazada: rompe la cronología única del ADR 0015 y obliga a cruzar dos columnas a mano para leer la historia de una planta.

## Consequences

- **El motor se queda sin ningún canal para plagas y enfermedades.** El veto es deliberado, pero deja un agujero real: ninguna de las cuatro plantas del inventario tiene forma de registrar una plaga que el motor lea. Ticket propio — la pregunta es si un clasificador *especializado* con fuente citable puede escribir lo que el generalista no.
- **`Reading` deja de ser un caso único.** Pasa de un valor suelto (humedad `seco | húmedo`) a un mecanismo con al menos dos instancias, una de ellas ordinal y **acoplada a una etiqueta**. La spec tiene que darle forma de mecanismo, no de campo.
- **Aparece una «vista resuelta» del diario**, distinta del diario crudo: la que se le pasa al análisis, con las descripciones refutadas sustituidas. Es la segunda vista derivada del proyecto, después de la resolución de rango del [ADR 0009](0009-la-unidad-de-cuidado-es-el-hecho-citable.md).
- **La prosa del análisis es contrato, no adorno.** La cita de la foto y la tercera persona son lo que hace reconocible al autor en una línea de tiempo compartida, así que son instrucciones del prompt con un porqué, no estilo.
- **`flowering` y `fruiting` escritas por una máquina crean tarea.** Es aceptado: una flor y una aceituna se ven sin ambigüedad, y despuntar de más no daña nada. Pero es la primera vez que el LLM mete un `PlanItem` en el plan sin pasar por ti.
