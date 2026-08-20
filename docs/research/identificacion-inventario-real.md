# Probar el camino de identificación contra las plantas reales de casa

**Fecha:** 2026-08-20
**Ticket:** [#15](https://github.com/Joosle/Plantas/issues/15)
**Contexto:** resolución de [#5](https://github.com/Joosle/Plantas/issues/5) (Pl@ntNet como vía
principal) y [#14](https://github.com/Joosle/Plantas/issues/14) (key viva, `useful` = 5.549 especies).

> Todo lo de aquí está medido contra las APIs reales el 2026-08-20, no citado de documentación.
> Lo que **no** se ha podido medir está marcado como tal y explicado en la última sección.

---

## Resumen

El ticket traía tres preguntas y un riesgo. Tres de las cuatro se han podido responder **sin una
sola foto**, y dos de ellas salen al revés de como el ticket las planteaba:

| | pregunta | veredicto |
| --- | --- | --- |
| 1 | ¿Acierta Pl@ntNet con estas plantas? | **Sin medir** — hace falta foto. Es lo único que queda. |
| 2 | ¿Cuánto trabajo es la tabla de alias españoles? | **Casi ninguno, pero no desde GBIF.** Pl@ntNet la regala: 95 % frente al 47 % de GBIF. |
| 3 | ¿Hace falta el rescate de Plant.id? | **No se puede saber, y no por las fotos**: el inventario no tiene ni una planta de interior. |
| — | ¿Son estables las claves de COL XR? | **Sí, y además da igual**: el proyecto no guarda claves de COL. |

---

## 2. La tabla de alias españoles: el trabajo no es el que se pensaba

El ticket asumía que había que tirar de `vernacularNames` de GBIF filtrando `language=='spa'`,
contar los huecos, y escribir a mano el resto. La medición dice que **esa fuente no sirve**, y que
hay otra que sí.

### GBIF: el problema no es la cobertura, son las etiquetas

Sobre los tres taxones del inventario:

| taxon | clave | nombres totales | `spa` |
| --- | --- | --- | --- |
| *Olea europaea* | 5415040 | 58 | **0** |
| *Rosa* | 8395064 | 144 | **0** |
| *Aloe vera* | 2777724 | 98 | 18 |

El olivo —posiblemente la planta más española que existe— tiene 58 nombres comunes en GBIF, en diez
idiomas incluido el **suajili** y el noruego en sus dos normas escritas, y **ninguno en español**.
`Olivo` sí está en la lista: etiquetado como **italiano**. *Rosa* tiene 144 nombres en unas 78
lenguas —vasco, ojibwa, tongano, quechua, guaraní— y cero en español; `Rosa` aparece, también como
italiano.

Y no se arregla rescatando los mal etiquetados: `olivo` es *también* la palabra italiana. La
ambigüedad es real, no un error de tecleo.

### Lo que hay bajo `spa` tampoco es el español de aquí

Ampliando a 19 taxones (inventario + los cuatro de interior que nombra el ticket + jardín y huerto
corrientes en España), y preguntando no «¿hay algún nombre `spa`?» sino **«¿está el nombre que una
persona en España escribiría?»**:

| taxon | GBIF `spa` (clave aceptada) | Pl@ntNet `lang=es` |
| --- | --- | --- |
| *Olea europaea* | — falta — | olivo |
| *Rosa* | — falta — | rosal |
| *Aloe vera* | aloe | aloe |
| *Monstera deliciosa* | costilla de adán | costilla de adán |
| *Epipremnum aureum* | poto (en «poto asiático») | poto |
| *Ficus lyrata* | árbol lira | ficus lira |
| *Ficus benjamina* | — falta — | ficus |
| *Sansevieria trifasciata* | — falta — | lengua de tigre |
| *Nerium oleander* | adelfa | adelfa |
| *Lavandula angustifolia* | — falta — | lavanda |
| *Citrus limon* | limonero | limonero |
| *Rosmarinus officinalis* | — falta — | **— falta —** |
| *Solanum lycopersicum* | — falta — | tomate |
| *Cupressus sempervirens* | ciprés | ciprés |
| *Picea abies* | — falta — | abeto (en «abeto falso») |
| *Abies alba* | abeto (en «abeto blanco») | abeto |
| *Thuja occidentalis* | tuya (en «tuya occidental») | tuya |
| *Pelargonium peltatum* | — falta — | geranio |
| *Bougainvillea glabra* | — falta — | buganvilla |

**GBIF 9/19 (47 %). Pl@ntNet 18/19 (95 %).**

Lo que hay bajo `spa` en GBIF, cuando lo hay, es mayoritariamente uso **americano**: `sábila`,
`zabila`, `acíbar` para el aloe; `Bugambilia`, `Santa Rita`, `Curazao` para la buganvilla —
peninsular `buganvilla` no está—; `lengua de suegra` para la sansevieria. Y hay entradas
directamente **equivocadas**: el único nombre `spa` de *Solanum lycopersicum* es **`frutilla`**, que
en América es la fresa. Un alias erróneo es peor que un hueco: el hueco se ve, el error no.

### La respuesta: la tabla no se escribe, se descarga

`GET /v2/projects/useful/species?lang=es` devuelve, para cada una de las 5.549 especies, un campo
`commonNames` **ya en español**. 3.639 de las 5.549 (66 %) traen al menos uno; entre las que
importan aquí, prácticamente todas.

Es la fuente que el proyecto **ya usa y para la que ya tiene key**, no consume cuota de
identificación, y se descarga entera en una llamada paginada. La tabla de alias deja de ser trabajo
manual y pasa a ser una carga inicial más un puñado de correcciones.

**Número que pedía el ticket: de 19 taxones, 1 a mano** (el romero, y por otra razón — ver abajo).
No los 12 que salían con GBIF.

### El único hueco real: no hay romero

*Rosmarinus officinalis* **no está en `useful`**, ni bajo ese nombre ni bajo el aceptado
*Salvia rosmarinus*. Un catálogo de 5.549 «plantas cultivadas y ornamentales» sin romero es un
agujero de cobertura de verdad, no un problema de nombres. No afecta al inventario de hoy, pero
marca que `useful` **no** es exhaustivo ni siquiera en lo obvio mediterráneo, y que hace falta una
red de seguridad (`k-world-flora`, 84.571 especies) cuando `useful` no devuelva nada.

---

## El sinónimo: la trampa de integración que nadie había mirado

Al cruzar las dos listas aparece un modo de fallo silencioso.

**Pl@ntNet va por detrás de GBIF en nomenclatura.** La sansevieria está en `useful` como
`Sansevieria trifasciata`; el nombre aceptado hoy es `Dracaena trifasciata`. Si la app guarda la
clave aceptada y luego compara **cadenas** con lo que devuelve Pl@ntNet, no casan y la planta se
pierde sin error visible.

Las dos APIs sí resuelven el sinónimo, y hay que dejarles hacerlo:

| consulta | v1 backbone | COL XR (v2 + `checklistKey`) |
| --- | --- | --- |
| `Sansevieria trifasciata` | 2770610 SYNONYM → **11041822** | 6XJ74 SYNONYM → **8WTWT** *Dracaena trifasciata* |
| `Rosmarinus officinalis` | 2926634 SYNONYM → **10902460** | 4TFBR SYNONYM → **DNX5M** *Salvia rosmarinus* subsp. *rosmarinus* |

**Regla:** nunca comparar nombres científicos como texto. Pasar siempre lo que devuelva Pl@ntNet por
`species/match` y quedarse con `acceptedUsageKey` cuando venga.

Dos avisos que salen de la misma tabla:

- **Las dos claves no coinciden en rango.** Para el romero, el backbone acepta la *especie*
  (*Salvia rosmarinus*) y COL XR acepta la *subespecie*. Mezclar claves de los dos espacios da
  taxones de rango distinto para la misma planta.
- **Seguir la clave aceptada puede costar nombres comunes.** `Sansevieria trifasciata` (sinónimo)
  tiene dos nombres `spa` en GBIF; `Dracaena trifasciata` (aceptado) tiene **cero**. Por eso la
  cifra de GBIF *empeora* de 58 % a 47 % al hacer lo correcto. La identidad va por la clave
  aceptada; los alias hay que buscarlos **también** en los sinónimos.

---

## El riesgo de las claves de COL XR: falsa alarma, por partida doble

El ticket preguntaba si guardar `accepted_key_col` es frágil porque COL XR se republica cada mes.

**Primero: el proyecto no guarda claves de COL XR.** El inventario anotó `5415040`, `8395064`,
`2777724`. Esas son claves del **GBIF Backbone Taxonomy** (`taxonID: gbif:5415040`, dataset
`d7dddbf4…`, publicado en **2023-08-28**), no de COL. La confusión viene de que
`api.gbif.org/v2/species/match` **sin** `checklistKey` devuelve claves del backbone, exactamente las
mismas que v1. Sólo pasándole `checklistKey=7ddf754f-…` devuelve la clave nativa de COL, que es
alfanumérica:

| | clave |
| --- | --- |
| Backbone GBIF (lo que hay guardado) | `5415040` |
| COL XR (`checklistKey` explícito) | `493JT` |

(Confirmado contra la API propia de COL, ChecklistBank: `/dataset/3LR/nameusage/search` devuelve
`id: 493JT` para *Olea europaea*.)

**Segundo: aunque se guardaran, aguantan.** Mismo taxón contra seis XR mensuales consecutivas:

| taxon | 26.7 XR | 26.6 XR | 26.5 XR | 26.4 XR | 26.3 XR | 26.2 XR |
| --- | --- | --- | --- | --- | --- | --- |
| *Olea europaea* | 493JT | 493JT | 493JT | 493JT | 493JT | 493JT |
| *Rosa* | 63S6D | 63S6D | 63S6D | 63S6D | 63S6D | 63S6D |
| *Aloe vera* | C46V | C46V | C46V | C46V | C46V | C46V |
| *Picea abies* | 4HPZF | 4HPZF | 4HPZF | 4HPZF | 4HPZF | 4HPZF |
| *Monstera deliciosa* | 73PS8 | 73PS8 | 73PS8 | 73PS8 | 73PS8 | 73PS8 |
| *Nerium oleander* | 4768L | 4768L | 4768L | 4768L | 4768L | 4768L |

**6/6 idénticas de febrero a julio de 2026.** El riesgo queda cerrado.

Lo que sí hay que corregir es la **prosa del inventario**, que dice «consultado contra
`v2/species/match` (COL XR)»: la consulta fue correcta y las claves son correctas, pero son del
backbone, y la columna de la tabla ya las llama «GBIF key», que es lo acertado.

---

## Cobertura de `useful`: sí cubre lo de esta casa, y cubre el abeto sea el que sea

El ticket preguntaba si `useful` cubre de verdad monstera, potos, ficus y sansevieria. Descargado el
catálogo entero (5.549 especies, confirma la cifra de #14):

| grupo | taxon | ¿en `useful`? | nombres es |
| --- | --- | --- | --- |
| inventario | *Olea europaea* | sí | Olivo, Olivera, Acebuche |
| inventario | *Aloe vera* | sí | Aloe, Sávila, Acíbar |
| inventario | *Rosa* | **como género no; 25 especies del género sí** | — |
| interior | *Monstera deliciosa* | sí | Costilla de Adán, Monstera |
| interior | *Epipremnum aureum* | sí | Potus, Poto, Teléfono |
| interior | *Ficus benjamina* | sí | Ficus, Laurel de la India |
| interior | *Ficus lyrata* | sí | Árbol lira, Lirata |
| interior | *Sansevieria trifasciata* | sí (nombre antiguo) | Lengua de suegra, Sansevieria |

Los cuatro de interior están. El género tiene fondo además del ejemplar concreto: *Ficus* 26
especies, *Dracaena* 8, *Sansevieria* 6, *Epipremnum* 3, *Monstera* 2.

**El rosal.** `useful` no lleva el género pelado, lleva 25 especies de *Rosa*. Como el rango terminal
correcto del rosal de la fachada **es** el género (cultivar híbrido, según #2), Pl@ntNet devolverá
alguna especie concreta —*Rosa chinensis*, *Rosa canina*…— que hay que **colapsar a género** al
guardarla. No es un fallo de identificación: es la regla de join que necesita el rosal.

**El abeto.** Es el caso de prueba número uno y sigue sin foto, pero se puede acotar el riesgo por
adelantado: de los 16 taxones que en España se llaman «abeto» o se confunden con él,
**`useful` cubre los 16**, todos con nombre español correcto.

| | |
| --- | --- |
| *Picea abies* | Abeto rojo, Árbol de Navidad, Pícea |
| *Picea pungens* / *glauca* | Abeto de Colorado / Abeto blanco |
| *Abies alba* / *nordmanniana* / *pinsapo* | Abeto blanco / Abeto de Nordmann / Pinsapo |
| *Cupressus sempervirens* / *arizonica* / *macrocarpa* | Ciprés común / de Arizona / de Monterrey |
| *Thuja occidentalis* / *plicata* | Tuya de Canadá / Tuya gigante |
| *Chamaecyparis lawsoniana* | Ciprés de Lawson |
| *Cedrus atlantica* / *deodara* | Cedro del Atlas / del Himalaya |
| *Araucaria heterophylla*, *Pinus pinea* | Araucaria, Pino piñonero |

Sea cual sea, el camino lo cubre. Lo que falta por saber no es si Pl@ntNet **conoce** la planta,
sino si la **acierta** desde una foto — y eso sigue necesitando la foto.

---

## 3. Plant.id: la pregunta no está bloqueada por las fotos, sino por el inventario

El ticket condicionaba el rescate de Plant.id a que «el grupo de interior falle de forma
sistemática». **El inventario no tiene ni una planta de interior**: las cuatro son de exterior
(olivo, abeto, rosal, aloe de terraza).

Así que la condición de disparo no es que no se haya medido: es que **no se puede medir**, ni con
fotos ni sin ellas, mientras el inventario sea el que es. Gastar los 100 créditos de prueba ahora
sería gastarlos sobre el grupo equivocado.

Esto no pertenece a este ticket. Pertenece a la niebla que el mapa ya tiene escrita —«El interior no
tiene banco de pruebas»— y ahí se queda. **Veredicto: Plant.id no entra ni se queda fuera; la
pregunta se aplaza a que aparezca una planta de interior.** Los 100 créditos siguen sin gastar, que
es exactamente donde deben estar.

---

## 1. Lo que no se ha podido medir, y por qué

**La tasa de acierto de Pl@ntNet sobre estas plantas sigue sin medir.** Es la única pregunta del
ticket que exige una foto de cada planta de esta casa, y el canal por el que se trabaja este mapa no
admite fotos — es la niebla que el mapa ya había anotado a propósito de #25.

No se ha sustituido por un proxy. Se podría haber tirado de fotos públicas de los mismos taxones,
pero eso mide si Pl@ntNet reconoce *la especie*, no si acierta con *esta* planta desde el móvil, con
su luz, su encuadre y su estado — que es literalmente lo que el ticket pide. Y en el caso decisivo,
el abeto, el proxy es imposible por definición: no se sabe qué especie es, que es el problema.

**Lo que hace falta para desbloquearlo:** cuatro fotos en el repositorio, en `fotos/`, una por
planta —`olivo.jpg`, `abeto.jpg`, `rosal.jpg`, `aloe.jpg`—, de la hoja o del porte según el
`organs` que se vaya a declarar. Con eso, la medición es una tarde: cuota de 500/día, cuatro plantas,
y sitio de sobra para probar `useful` y `k-world-flora` en la misma sesión.

**Del abeto conviene más de una:** porte entero, detalle de las acículas y, si tiene, un cono. Es la
diferencia entre *Picea*, *Abies* y *Cupressus*, y es justo lo que el ticket llama el caso de prueba
número uno.

---

## Reproducir

Todo lo de arriba sale de llamadas a APIs públicas (GBIF y ChecklistBank, sin key) y a Pl@ntNet (con
la key de #14, que vive fuera del repositorio). Los catálogos descargados —`useful` con sus 5.549
especies y `monver` con 231— no se commitean: se regeneran con
`GET /v2/projects/{proyecto}/species?lang=es&page=N&pageSize=500`.

Comprobado de paso, y sigue vigente lo de #14: `GET /v2/_status` → `{"status":"ok"}` y
`GET /v2/quota` → `{"quota":{"identify":500}}`, ninguno de los dos consume identificaciones.
El host correcto es **`my-api.plantnet.org`**, no `my.plantnet.org` — este último devuelve 404 a
todo y es un error fácil de cometer leyendo la resolución de #14, que cita las rutas sin el host.
