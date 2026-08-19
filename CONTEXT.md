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
Lo que se sabe de una Plant a lo largo del tiempo. Es CareAction u Observation. El diario es el estado del sistema: si no hay entradas, el motor está ciego.
_Avoid_: Log entry, record, event

**CareAction** (acción):
Algo que el usuario hizo a una Plant, de un vocabulario cerrado, en una fecha. Responde a "¿cuándo se hizo esto por última vez?".
_Avoid_: Task, activity, care event

**Observation** (observación):
Algo que el usuario vio en una Plant. Texto libre con etiquetas, y opcionalmente una lectura estructurada.
_Avoid_: Note, comment, remark

**Reading** (lectura):
La parte estructurada y legible por el motor de una Observation — un juicio sobre un estado, no una prosa sobre él. Hoy hay dos: humedad del sustrato (`seco | húmedo`) y fenología (`presente | ausente`, anclada al hecho citable que hizo la pregunta).
_Avoid_: Measurement, sensor value

### El consejo

**WeeklyPlan** (plan semanal):
El acta inmutable del consejo dado en una semana, para todas las Plant, con los hechos que lo justificaron. No se regenera: se conserva para poder auditarlo y compararlo con el de la semana anterior.
_Avoid_: Schedule, weekly tasks, care plan

**PlanItem** (tarea del plan):
Una tarea sobre una Plant dentro de un WeeklyPlan. Su estado no se guarda: se deriva de las JournalEntry que lo responden.
_Avoid_: Todo, task, checklist item

**Phenological item** (ítem fenológico):
Un PlanItem cuya primera mitad es una pregunta sobre algo que el usuario ve, y cuya segunda mitad —la acción— nace congelada con ella y se activa si la respuesta es `presente`. No es un tipo aparte: es un PlanItem con otro vocabulario de respuesta.
_Avoid_: Check, inspection task, observation task

**Window** (ventana):
El tramo del año en que una tarea tiene sentido, declarado por el hecho citable que la funda. Fuera de la ventana la tarea no se emite; al cerrarse, el ítem sin responder caduca en vez de arrastrarse.
_Avoid_: Season, date range, deadline

**Carried over** (arrastrada):
Un PlanItem que nace en un WeeklyPlan porque su equivalente de la semana anterior quedó sin responder, apuntando al viejo para que se vea cuánto lleva pendiente.
_Avoid_: Overdue, rolled over, pending

**Alert** (alerta):
Un aviso disparado por el pronóstico, con ventana de validez, sobre una Plant o sobre un Site entero. Caduca sola y nunca se arrastra — eso la separa del PlanItem.
_Avoid_: Warning, notification, urgent task

**WeatherSnapshot** (instantánea de clima):
El estado del tiempo — observado y pronosticado — tal como se descargó en una fecha. Se conserva porque el consejo que lo citó debe poder auditarse después.
_Avoid_: Forecast, weather data
