# Plantas

Seguimiento y cuidado de las plantas de una vivienda, combinando hechos de cuidado por especie con el clima real del sitio para producir un plan semanal y alertas puntuales.

Los identificadores del código van en **inglés**; la interfaz se traduce al **español**. Cada término lleva su etiqueta española entre paréntesis: es la que ve el usuario, no un sinónimo alternativo en el código.

## Language

### El ejemplar y lo que se sabe de él

**Plant** (planta):
Un ejemplar concreto que vive en la casa. Dos aloes son dos Plant.
_Avoid_: Specimen, individual

**Taxon** (taxón):
Un nombre científico en GBIF, a cualquier rango — especie o género —, con enlace a su padre. Es identidad, no conocimiento de cuidado.
_Avoid_: Species, taxonomy

**Unidentified** (sin identificar):
Estado de una Plant que aún no tiene Taxon. Es un estado legítimo y reintentable, nunca un requisito de alta.
_Avoid_: Unknown, pending identification

**CareProfile** (perfil de cuidado):
Los hechos citables de cuidado de un Taxon. Cuelga del rango donde la fuente los tenga, que no siempre es el rango al que se identificó la planta.
_Avoid_: Care sheet, species data, care requirements

**Site** (sitio):
El lugar donde vive una Plant, con lo que es propio del lugar: interior o exterior, cuánta lluvia le llega y su microclima. La exposición solar no es del Site sino de la Plant, porque varía dentro de un mismo cuarto.
_Avoid_: Location, place, spot, room

**Archived** (archivada):
Plant que ya no está al cuidado del usuario, con el motivo por el que se fue. Sale de planes y alertas; conserva su diario íntegro.
_Avoid_: Deleted, inactive, removed

### El diario

**JournalEntry** (entrada de diario):
Lo que se sabe de una Plant a lo largo del tiempo. Es CareAction, Observation o LifecycleEvent. El diario es el estado del sistema: si no hay entradas, el motor está ciego.
_Avoid_: Log entry, record, event

**occurredAt / recordedAt** (cuándo pasó / cuándo se anotó):
Las dos fechas que lleva toda JournalEntry. El motor calcula con `occurredAt`, así que anotar tarde no falsea el estado; `recordedAt` es lo que permite saber cuánto se anota al día.
_Avoid_: date, timestamp, createdAt a secas

**CareAction** (acción):
Algo que el usuario hizo a una Plant, de un vocabulario cerrado de Verb, en una fecha. Responde a "¿cuándo se hizo esto por última vez?". Lleva siempre una nota libre opcional.
_Avoid_: Task, activity, care event

**Verb** (verbo):
El vocabulario cerrado de las CareAction. Un verbo existe sólo si alguna regla del motor lo lee o el plan puede llegar a mandarlo — ver ADR 0003. Lo que no pasa esa regla no es un verbo: es una Observation.
_Avoid_: action type, care type, task type

**Observation** (observación):
Algo que el usuario vio en una Plant. Texto libre, con etiquetas cerradas, opcionalmente una lectura estructurada y opcionalmente fotos.
_Avoid_: Note, comment, remark

**Reading** (lectura):
La parte estructurada y legible por el motor de una Observation — un juicio sobre un estado, no una prosa sobre él. Hoy sólo existe la humedad del sustrato (seco / húmedo).
_Avoid_: Measurement, sensor value

**LifecycleEvent** (hito):
Entrada de diario que **nadie escribe a mano**: la genera el sistema cuando cambia la ficha de una Plant — alta, identificación, cambio de Site, archivado. No es CareAction (no se le hizo nada a la planta) ni Observation (no se vio nada); es la ficha dejando rastro con fecha.
_Avoid_: Audit log, history, change event

**Batch** (gesto en lote):
Varias CareAction escritas de una vez con un mismo `batchId` porque nacieron de un solo gesto — acolchar un Site entero. Es agrupación de interfaz: cada entrada sigue siendo de una Plant concreta, y por eso sigue siendo cierta cuando el Site cambia de inquilinos.
_Avoid_: bulk entry, group action

**Silencio con caducidad** (stale journal):
No anotar nada de una Plant significa no haber hecho nada — hasta que el silencio supera con holgura la cadencia que el motor esperaba de *esa* planta. Pasado ese punto el silencio deja de ser evidencia, el consejo degrada de cuantitativo a cualitativo y se pide una reconciliación de un toque. Ver ADR 0004.
_Avoid_: inactivity, abandono

### El consejo

**WeeklyPlan** (plan semanal):
El acta inmutable del consejo dado en una semana, para todas las Plant, con los hechos que lo justificaron. No se regenera: se conserva para poder auditarlo y compararlo con el de la semana anterior.
_Avoid_: Schedule, weekly tasks, care plan

**PlanItem** (tarea del plan):
Una tarea sobre una Plant dentro de un WeeklyPlan. Su estado no se guarda: se deriva de las JournalEntry que lo responden.
_Avoid_: Todo, task, checklist item

**Carried over** (arrastrada):
Un PlanItem que nace en un WeeklyPlan porque su equivalente de la semana anterior quedó sin responder, apuntando al viejo para que se vea cuánto lleva pendiente.
_Avoid_: Overdue, rolled over, pending

**Alert** (alerta):
Un aviso disparado por el pronóstico, con ventana de validez, sobre una Plant o sobre un Site entero. Caduca sola y nunca se arrastra — eso la separa del PlanItem.
_Avoid_: Warning, notification, urgent task

**WeatherSnapshot** (instantánea de clima):
El estado del tiempo — observado y pronosticado — tal como se descargó en una fecha. Se conserva porque el consejo que lo citó debe poder auditarse después.
_Avoid_: Forecast, weather data
