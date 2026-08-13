# El LLM supervisa el plan, el código lo propone, y borrar es una pregunta

El motor semanal tiene tres capas y una regla que las ordena. El **código propone**: genera el borrador de `PlanItem` desde el balance hídrico y el calendario. El **LLM supervisa**: añade ítems que el código no vio, ordena, prioriza y escribe el `reason` de todos. Y **borrar no es una acción del LLM, es una pregunta** — un ítem que cree que sobra sale en la checklist marcado como cuestionado, con su motivo y dos respuestas de un toque.

El mapa fijó el híbrido «hechos citables + redacción por LLM». Al concretarlo se vio que ese reparto no se sostenía: **lo que se consume es la checklist, no la prosa**, y un LLM que sólo redacta un párrafo que nadie lee añade coste, latencia y una forma de romperse sin aportar nada. El LLM se gana el sitio decidiendo qué hay que hacer, o no tiene sitio.

Lo que **no** cambia: el balance hídrico lo calcula el código. No por desconfianza, sino porque es una recursión día a día sobre el pronóstico ([#6](https://github.com/Joosle/Plantas/issues/6)) que corre en el cron diario sin LLM y alimenta las alertas ([ADR 0005](0005-stack-cloudflare-y-pwa-local-first.md)). El LLM no rehace aritmética: la recibe como hecho.

## Nada desaparece en silencio

Es la propiedad que ordena el resto del diseño, y la única objeción que sobrevivió al debate. Un ítem que el LLM omite no se ve —la checklist parece normal, sólo más corta—, y sería **el único fallo del sistema que no se declara**: el clima rancio se declara ([#4](https://github.com/Joosle/Plantas/issues/4)), el silencio del diario se declara ([ADR 0004](0004-el-silencio-del-diario-es-evidencia-con-caducidad.md)), el plan viejo se declara ([ADR 0005](0005-stack-cloudflare-y-pwa-local-first.md)).

De ahí sale la **supresión ratificada**: el ítem se queda, marcado, con la propuesta del LLM visible y dos botones — *«tienes razón, no toca»* / *«lo hago igual»* — más un **«aceptar cambios»** que despacha varias de golpe con el comentario delante. Aceptar en bloque registra **una entrada de diario por supresión**, no una: [#13](https://github.com/Joosle/Plantas/issues/13) necesita saber de qué planta no hacía falta, no que «hubo cambios». Y el comentario se congela dentro del `WeeklyPlan`, porque releer marzo en agosto tiene que enseñar por qué aceptaste.

Eso convierte además el ítem cuestionado en la **señal negativa** que faltaba: el diario registra cuándo *regaste*, nunca cuándo *no hacía falta*.

## Cuatro cosas se proponen, ninguna se aplica sola

El mismo mecanismo aparece cuatro veces, y conviene verlo como uno solo:

| El LLM propone | Tú ratificas | Al aceptarse |
| --- | --- | --- |
| Suprimir un `PlanItem` | en la checklist, un toque | `Observation` (con `reading` si el motivo era la humedad) |
| Un **verbo nuevo** para el vocabulario | `VocabularyProposal` en la app | entra en el vocabulario del [ADR 0003](0003-regla-de-admision-del-vocabulario-del-diario.md) |
| Un **ajuste de parámetro** (`W_max`, `KL`, umbral) | propuesta en la app | hecho fechado, con motivo y autor |
| Una **conclusión de temporada** (una vez al año) | propuesta en enero | hecho citable por `citedFacts` |

Ninguna interrumpe: el [ADR 0004](0004-el-silencio-del-diario-es-evidencia-con-caducidad.md) dejó la reconciliación de humedad como la **única** interrupción que este diseño se permite, y ninguna de estas cuatro supera ese listón. Hasta que ratifiques, lo que el LLM quería hacer se degrada a lo que ya podía: decirlo en el `reason` de un ítem existente, o nada.

## El vocabulario cerrado es lo que hace viable «el LLM decide»

El LLM no devuelve prosa: devuelve `{plant, verb, reason, citedFacts}`, con `verb` acotado por `enum` a los nueve del [ADR 0003](0003-regla-de-admision-del-vocabulario-del-diario.md) y garantizado por el esquema de la API, no por el prompt.

La razón es mecánica, no estética. El [ADR 0002](0002-plan-inmutable-y-diario-derivado-de-la-checklist.md) dice que un `PlanItem` **no guarda estado**: se marca cuando aparece la `JournalEntry` que lo responde, emparejada por `verb` + `plant`. Un ítem en prosa —*«deberías revisar el rosal»*— no se empareja con nada: **nace imposible de tachar**. Y un verbo inventado no tiene `CareAction` que lo cierre.

El razonamiento no se pierde: viaja en el `reason` de cada ítem, que es la única prosa que de verdad se lee.

## Citar es elegir de un catálogo, no escribir

Cada `PlanItem` lleva `citedFacts` con **identificadores** de hechos que el código le sirvió: el `CareProfile` con su procedencia, el `WeatherSnapshot` concreto, las `JournalEntry` concretas, el valor del balance de ese día. El código **valida que cada referencia existe** antes de persistir el plan.

Eso cambia la naturaleza del error posible, y es la defensa principal contra el generador de texto plausible. *«El aloe necesita riego quincenal en invierno»* suena impecable venga de donde venga, incluido de ningún sitio. Colgando de un identificador, la alucinación deja de ser indistinguible del buen consejo.

Es también lo que hace rentable el trabajo ya hecho: [#5](https://github.com/Joosle/Plantas/issues/5) hizo que el join a los cuidados fuera **por clave y no por cadena**, [#18](https://github.com/Joosle/Plantas/issues/18) va a curar la tabla **con procedencia**, y el [ADR 0002](0002-plan-inmutable-y-diario-derivado-de-la-checklist.md) congela el plan **con los hechos que citó**.

**Un ítem cuya cita no valida no se borra**: sale marcado *«sin respaldo»*. Borrarlo sería supresión silenciosa por la puerta de atrás — y además, un ítem sin respaldo visible informa: dice que el motor se está inventando cosas.

## El contexto: diario íntegro, derivados calculados, y una escalera para el año 5

El LLM recibe el **diario entero sin tocar** más lo que el código ya sabe destilar: la serie del balance día a día, días desde cada verbo por planta, los `CareProfile` con sus citas, y **el plan de la semana pasada con qué se respondió y qué no** — que es lo que da memoria a la llamada semanal.

El miedo al coste no aplica: la parte fija son ~7.000 tokens y el diario crece unos 5.000 al año con cuatro plantas. El presupuesto de **25.000 tokens** —tres veces y media toda la documentación de este repo— no se toca hasta el **año 4 o 5**.

Un **resumen que sustituya al diario** queda descartado: destruye exactamente lo que se le pide al LLM. «Aprender de qué pasó cuando regué menos» es un patrón que vive en la relación entre entradas y fechas concretas; un resumen que dice «riego irregular en verano» ha borrado el dato. Y comprime **en silencio**, que es la misma objeción que el [ADR 0003](0003-regla-de-admision-del-vocabulario-del-diario.md) le puso a que el LLM extrajera etiquetas.

Pero un **cierre de temporada anual** que se *suma* al diario es otra cosa, y es lo que resuelve el año 5. Una llamada al año destila conclusiones —*«el aloe aguantó 24 días en agosto sin señal de estrés»*— que tú ratificas. Con ellas existe la escalera:

| | qué entra en el contexto |
| --- | --- |
| Años 1–4 | diario íntegro **+** conclusiones anuales (redundante, y cabe) |
| Año 5+ | se sueltan los años crudos más viejos; **sus conclusiones se quedan** |

Se pierde el detalle, no el aprendizaje, y se pierde **declaradamente**. Es la misma escalera de [#4](https://github.com/Joosle/Plantas/issues/4) y del [ADR 0004](0004-el-silencio-del-diario-es-evidencia-con-caducidad.md), aplicada ahora a la memoria del motor.

**El riesgo de esto es la superstición con trazabilidad.** *«Este verano más fruto regando menos»*, con un año y una planta, no es evidencia: es una coincidencia con buena prosa. Si entra como ley, el motor riega menos citándola y ya no habrá con qué desmentirla cuando los años crudos se suelten. Por eso una conclusión anual se registra **con su evidencia y su tamaño de muestra**, y por eso es la que menos puede entrar sola: es la decisión más duradera que el sistema toma — no caduca ni se corrige a sí misma.

## La consistencia se compra declarando, no congelando

Primero, un hecho que encoge el problema: **el riego no bandea**. La existencia del ítem de regar la decide el balance, que es aritmética determinista. La superficie de variación real son los ítems que el LLM añade y los `reason`.

Ahí la regla es el **anclaje declarado**: el plan anterior ya está en el contexto, y el LLM **debe justificar en el `reason` cualquier cambio de criterio** respecto a la semana pasada. El bandazo que se temía —*«riega abundantemente»* → *«cuidado con el exceso de agua»*— deja de ser invisible: o lo justifica con un hecho, o se ve a simple vista que ha cambiado de opinión sin motivo.

La reproducibilidad estricta («mismos hechos, mismo plan») se descarta por imposible antes que por cara: **los hechos nunca son los mismos dos semanas seguidas** — el diario creció, el balance corrió siete días, el pronóstico es otro. Lo que sí se rescata es barato y concreto: el `WeeklyPlan` guarda **modelo y versión de prompt**, para poder distinguir *«cambió el jardín»* de *«cambié yo el prompt hace tres semanas»*. Sin eso, esa pregunta no tiene respuesta.

## El plan vacío es la pantalla principal

Con 3 de 4 plantas arraigadas ([#2](https://github.com/Joosle/Plantas/issues/2)) y el aloe pidiendo agua cada 2–3 semanas, **cerca de la mitad de las semanas del año el plan sale sin una sola tarea**. No es un borde: es lo que más se va a ver.

Sale como **vacío razonado**: «nada que hacer», y debajo **una línea por planta** diciendo por qué — *«olivo: arraigado, 62 mm de lluvia en 30 días, sin acción»*, *«aloe: depósito al 61 %, cruza umbral hacia el 24»*. Es la prueba de vida que el [ADR 0005](0005-stack-cloudflare-y-pwa-local-first.md) sólo cubría a medias con la edad visible del plan, y es donde se caza al motor equivocándose: si dice 61 % y el sustrato está hecho polvo, has descubierto que el modelo deriva.

El relleno queda excluido **por construcción, no por criterio**: no existe un verbo `inspect` porque ninguna regla lo lee y ningún plan lo manda, así que «añade una tarea de observación» hoy es imposible sin pasar por la ratificación de vocabulario. Y está bien que no quepa: una checklist que **siempre** tiene ítems deja de significar nada, y el día de la helada estarías entrenado para ignorarla.

## Modelo y coste

**Claude Opus 5** (`claude-opus-5`), salida estructurada por esquema JSON, pensamiento adaptativo (activo por defecto en este modelo; hay que dar margen a `max_tokens`, que cubre razonamiento y respuesta juntos).

Con 52 llamadas al año, ~25.000 tokens de entrada y ~2.000 de salida: **≈ 9 $/año**. Sonnet 5 saldría por ~5 $ y Haiku 4.5 por ~1,80 $. **La diferencia entre el más caro y el más barato son 7 dólares al año**, y el trabajo que se le pide —supervisar el plan, detectar patrones en el diario, proponer correcciones del modelo hídrico— es la parte de razonamiento más exigente del sistema. Recortar ahí sería una economía absurda.

Sin caché de prompt: entre lunes pasa una semana y cualquier caché ha caducado. Coherente con el [ADR 0005](0005-stack-cloudflare-y-pwa-local-first.md) («no hay ninguna caché en el sistema»).

## Considered Options

- **El LLM sólo redacta; el código decide todo.** La opción más segura y la que menos superficie de error tiene. Rechazada porque el producto es la checklist: un párrafo bien escrito que nadie lee no justifica ni el coste ni la fragilidad. Si el LLM no decide, sobra.
- **El LLM es autor único; el código sólo le sirve hechos.** Máxima capacidad de razonamiento, sin ninguna rigidez. Rechazada por el fallo silencioso: un ítem que falta no se ve.
- **Suelo de código no suprimible.** Los ítems mecánicos están sí o sí y el LLM no puede quitarlos. Rechazada por dos motivos: se apoyaba de más en «¿y si el LLM cae un lunes?» —regar el martes en vez del lunes no tiene consecuencias, y el coste de reejecutar es mínimo—, y además impide que el LLM diga *«esta semana no, mira que anotaste sustrato húmedo el sábado»*, que es justo el criterio que se le quiere pedir.
- **Suprimir con justificación registrada, sin preguntar.** Rechazada porque en la práctica es «autor único» con un trámite: siempre habrá un motivo plausible, y el fallo invisible vuelve con mejor prosa.
- **Memoria comprimida mantenida por el LLM.** Descrita arriba: destruye el patrón que se quiere aprender y comprime en silencio.
- **Reproducibilidad estricta.** Suena a rigor y no protege de nada real, porque el caso «mismos hechos» no ocurre nunca en producción.

## Consequences

- **Enmienda a [#7](https://github.com/Joosle/Plantas/issues/7)**: el `PlanItem` gana `reason`, `citedFacts` y el estado *cuestionado*; aparecen `VocabularyProposal` y el ajuste de parámetros como hecho fechado.
- **Reencuadre de [#13](https://github.com/Joosle/Plantas/issues/13)**: con ~20 riegos al año sobre una sola planta, no hay muestra para una calibración estadística — ni este año ni dentro de tres. Ese es justamente el régimen donde el razonamiento cualitativo sobre el diario gana al ajuste numérico. #13 pasa de «ajusta los parámetros» a «define qué parámetros son ajustables, con qué límites y cómo se registra el ajuste».
- **Un fallo del LLM un lunes no es una semana perdida**: se reejecuta, o se riega el martes.
- **El presupuesto de contexto es un guardarraíl declarado**, no un límite duro: si se supera antes de lo previsto, la respuesta es reabrir esta decisión con datos reales, nunca resumir en silencio.
- El vocabulario cerrado por `enum` de la API significa que **un verbo nuevo requiere despliegue**, no sólo ratificación. Con la cadencia esperada (uno cada muchos meses) es aceptable.
