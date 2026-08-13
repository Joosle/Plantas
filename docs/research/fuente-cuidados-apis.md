# Fuente de hechos de cuidado por especie: APIs y bases de datos consultables

> Investigación para el issue [#3](https://github.com/Joosle/Plantas/issues/3), hijo del mapa [#1](https://github.com/Joosle/Plantas/issues/1).
> Fecha: 2026-08-13. Todas las llamadas de este documento se hicieron en vivo ese día.
> Alcance: **APIs y bases de datos consultables programáticamente** de cuidados de plantas.
> Fuera de alcance: WUCOLS y datasets agronómicos de riego — los cubre
> [Modelo agronómico](modelo-agronomico-riego.md) y una investigación paralela.
>
> Banco de pruebas obligatorio: las cuatro plantas de [docs/inventario-plantas.md](../inventario-plantas.md).
> **No se acepta ningún recuento total como prueba de cobertura.**

---

## Resumen de lo que dicen las fuentes

| Pregunta | Respuesta corta | Fuente que la posee |
|---|---|---|
| ¿Existe una API viva, gratuita y con licencia sana de **cuidados**? | **No.** Ninguna cumple las tres a la vez | esta investigación, §1–§9 |
| ¿Sigue vivo Trefle? | El servidor responde y el repo recibe *commits*, pero **sólo de dependabot**; sin trabajo de datos desde 2020 | [trefle.io](https://trefle.io/) · [treflehq/trefle-api](https://github.com/treflehq/trefle-api/commits/master) |
| ¿Y OpenFarm? | **Muerto.** El dominio entero redirige 301 a GitHub y el repo está **archivado** | `curl openfarm.cc` → 301 · [openfarmcc/OpenFarm](https://github.com/openfarmcc/OpenFarm) |
| ¿Trefle sirve las 4 plantas de casa? | **No.** Su volcado oficial no tiene *Aloe vera*, ni *Abies alba*, ni rango género | [treflehq/dump](https://github.com/treflehq/dump/releases) (verificado, §1.3) |
| ¿Qué % del volcado de Trefle tiene datos de luz? | **0,9 %** (3.550 de 416.473 filas) | volcado oficial, recuento propio §1.3 |
| ¿Perenual da ventanas de poda? | **Sí**, `pruning_month` y prosa de poda… pero es **texto generado**, no citable | [docs Perenual](https://perenual.com/docs/api) · §2 |
| ¿Licencia de Perenual? | Prohíbe *"data mining"* y *"derivative uses"*; **la copia local es dudosa** | [ToS Perenual](https://perenual.com/terms-of-service) |
| ¿Plant.id / Kindwise resuelve cuidados? | **No.** Su propia tabla de cobertura: *Watering* **9,50 %** | [kindwise.com/plant-id](https://www.kindwise.com/plant-id) |
| ¿Pl@ntNet aporta cuidados? | **No.** Sólo taxonomía + id GBIF/POWO | [doc Pl@ntNet](https://my.plantnet.org/doc/api/identify) |
| ¿GBIF tiene rasgos de cuidado? | **No.** `speciesProfiles` da `lifeForm`/`habitat`; `descriptions` es texto libre multilingüe | llamadas en vivo §5 |
| ¿Wikidata? | **No tiene cuidados**, pero es el **mejor puente de identificadores** que existe | consulta SPARQL en vivo §6 |
| ¿La única fuente que cubre las 4 plantas con hechos citables? | **PFAF**, y **no tiene API** | [pfaf.org](https://pfaf.org/) · §3 |
| ¿La única con rangos numéricos duros y dominio público? | **USDA PLANTS**, pero vacía para 4 de las 5 pruebas | API en vivo §4 |
| ¿Alguna fuente sirve algo a rango **género** (*Rosa*)? | **Ninguna.** Y PFAF además devuelve **una especie equivocada sin avisar** | §3.3 |

**Conclusión de altura**: no existe en 2026 una API de cuidados que sea simultáneamente (a) viva,
(b) con licencia que permita copia local, (c) con rangos numéricos accionables y (d) con cobertura
de las plantas de esta casa. Las que están vivas o no tienen datos o no tienen licencia; la que tiene
datos buenos (PFAF) es una web, no una API. **La respuesta correcta no es elegir una API: es construir
una tabla local curada de ~10 taxones, sembrada de PFAF + USDA PLANTS y unida por clave GBIF vía
Wikidata.** El argumento completo está en la [recomendación](#recomendación).

---

## 0. Cómo se ha juzgado cada candidata

Seis preguntas por fuente, en este orden, porque cada una puede matar a la siguiente:

1. **Vitalidad 2026** — ¿responde hoy? ¿último *commit*/anuncio?
2. **Licencia** — ¿permite uso personal **y copia local**?
3. **Coste y cuotas** — ¿gratis? ¿límite diario? ¿tarjeta?
4. **Granularidad** — ¿rangos numéricos o prosa tipo "riego moderado"?
5. **Cobertura de hechos** — temperatura mínima / rusticidad, exposición, agua base y estacionalidad,
   sustrato y drenaje, ventana de abonado, poda y trasplante.
6. **Join** — ¿por nombre científico canónico o clave GBIF, o sólo por id interno?

Y sobre todo, la **prueba de las cuatro plantas**: *Olea europaea* (GBIF 5415040), *Rosa* a **género**
(GBIF 8395064), *Aloe vera* (GBIF 2777724) y el abeto sin identificar, comprobado como *Abies alba*
(GBIF 2685484) y *Picea abies* (GBIF 5284884).

---

## 1. Trefle

### 1.1 Vitalidad

Ambigua, y hay que desmontarla con cuidado porque a primera vista parece viva.

**A favor**: el endpoint responde. Verificado en vivo el 2026-08-13:

```
GET https://trefle.io/api/v1/plants?token=x
→ HTTP 401  {"error":true,"messages":"Invalid access token"}
```

Un 401 bien formado significa que la aplicación Rails está levantada y valida tokens. La portada
sigue anunciando *"Explore 1 million indexed plants! including 500 thousand with detailed data"*
y *"Trefle is an Open Source API and the code & platform are provided totally free of charge"*
([trefle.io](https://trefle.io/)).

**En contra**, y es decisivo. Los *commits* de [treflehq/trefle-api](https://github.com/treflehq/trefle-api/commits/master)
de los últimos doce meses son **exclusivamente de dependabot** salvo uno:

| Fecha | Autor | Mensaje |
|---|---|---|
| 2026-07-21 | André Aubin | Merge pull request #169 …dependabot/brace-expansion |
| 2026-04-17 | André Aubin | `fix: mailer` |
| 2026-04-14 | André Aubin | Merge …dependabot/axios-1.15.0 |

Es decir: **parcheo de seguridad automático y un arreglo del envío de correo**. Cero trabajo sobre datos.
El repositorio de volcados [treflehq/dump](https://github.com/treflehq/dump) está **archivado** y su
último *release* es de **2020-10-15**. La entrada más reciente del blog oficial es la **1.6.0, de julio de 2020**
([docs.trefle.io/blog](https://docs.trefle.io/blog)).

Y la pregunta directa de la comunidad sigue sin respuesta del mantenedor:
[issue #119, *"Is this project still maintained for the forseeable future?"*](https://github.com/treflehq/trefle-api/issues/119),
abierta desde 2024-04-25, cuyo único contenido es *"According to their twitter, the project has been abandoned."*
Le acompaña la [#120 *"Confirmation E-Mail not sent"*](https://github.com/treflehq/trefle-api/issues/120),
abierta desde 2024 — lo que explica el `fix: mailer` de 2026 y advierte de que **darse de alta para
obtener un token es un punto de fricción real y documentado**.

**Veredicto de vitalidad**: zombi. Servidor encendido, datos congelados en 2020, mantenedor ausente.

### 1.2 Licencia, coste y granularidad

Código y volcado bajo **AGPL-3.0** ([treflehq/dump](https://github.com/treflehq/dump), campo `license`
vía API de GitHub). El volcado se distribuye como *release* público, así que **la copia local está
explícitamente contemplada** — es el único de todos los candidatos que regala su base de datos entera.
Gratis, sin tarjeta.

El esquema es, sobre el papel, **exactamente lo que este proyecto pide**
([docs.trefle.io/docs/advanced/plants-fields](https://docs.trefle.io/docs/advanced/plants-fields)):

- `light` — *"Required amount of light, on a scale from 0 (no light, <= 10 lux) to 10 (very intensive insolation, >= 100 000 lux)"*
- `atmospheric_humidity` — escala 0–10
- `minimum_temperature` / `maximum_temperature` — **en grados Celsius o Fahrenheit**
- `minimum_precipitation` / `maximum_precipitation` — **mm/año**
- `ph_minimum` / `ph_maximum`
- `soil_texture`, `soil_humidity`, `soil_nutriments`, `soil_salinity` — escalas 0–10
- `growth_months`, `bloom_months`, `fruit_months` — arrays de meses
- `growth_habit`, `average_height_cm`, `minimum_root_depth_cm`

Rangos numéricos accionables, meses de crecimiento y floración, temperatura mínima. En el papel es
la mejor ficha de todas las candidatas.

### 1.3 La prueba de cobertura: el volcado oficial, medido

Se descargó el *release* oficial más reciente (178 MB, 416.473 filas):

```
curl -L https://github.com/treflehq/dump/releases/download/1.0.0-alpha%2B20201015/species.csv
```

**Resultado sobre las cuatro plantas de casa**:

| Consulta | ¿Está en el volcado? | Datos de cuidado |
|---|---|---|
| *Olea europaea* | **Sí**, id 160060, `url_gbif = gbif.org/species/5415040` ✅ | `light=8`, `soil_nutriments=6`, `ph 6.5–7.0`, `growth_habit=Tree, Shrub` |
| *Rosa* a género | **No.** El volcado **no contiene ni una fila de rango género** | — |
| *Aloe vera* | **No.** Sólo aparece como *sinónimo* de *Aloe succotrina* (taxonomía obsoleta) | — |
| *Abies alba* | **No.** Hay *Abies nordmanniana*, *Abies pinsapo* y el híbrido *Abies alba × concolor*, pero **no la especie europea** | — |
| *Picea abies* | **Sí**, id 166753 | `light=7`, `soil_nutriments=5`, `ph 5.0–7.0`, `growth_rate=Slow` |
| *Cupressus sempervirens* | Sí, id 125403 | `light=8`, `ph 7.0–7.5` |
| *Thuja occidentalis* | Sí, id 187860 | `ph 5.2–7.0`, sin `light` |

Recuento de rangos en el volcado: `species` 369.778 · `ssp` 20.575 · `var` 18.657 · `hybrid` 7.125 ·
`form` 332. **Cero géneros.** El rosal de la fachada es inconsultable por construcción.

Y el dato que cierra la discusión — porcentaje de filas con cada campo relleno, contado sobre las
416.473 filas del volcado:

| Campo | Filas con valor | % |
|---|---|---|
| `light` | 3.550 | **0,9 %** |
| `atmospheric_humidity` | 3.504 | 0,8 % |
| `soil_nutriments` | 3.520 | 0,8 % |
| `ph_minimum` | 6.436 | 1,5 % |
| `bloom_months` | 2.133 | 0,5 % |
| **`growth_months`** | **0** | **0,0 %** |
| `growth_habit` | 28.060 | 6,7 % |
| `url_gbif` | 387.769 | **93,1 %** |

El millón de plantas que anuncia la portada es un **catálogo taxonómico**, no una base de cuidados.
Los cuidados existen para unas 3.500 especies —el mismo orden de magnitud que el catálogo de
características de USDA PLANTS, de donde proceden: cada fila con datos trae su `url_usda` (§4)—.
Y `growth_months`, que sería el campo con más valor para el calendario de tareas de este proyecto,
**está vacío en el 100 % de las filas**.

Nota metodológica honesta: el volcado es de 2020 y la API viva podría tener más. No se ha podido
verificar sin token, y obtenerlo pasa por un alta por correo con un fallo abierto desde 2024
(§1.1). Pero el volcado **es el artefacto oficial del propio proyecto**, y con el repo archivado
y sin trabajo de datos desde 2020 la hipótesis de que la API viva contenga mucho más no tiene apoyo.

Además, el volcado **carece por completo de las columnas `minimum_temperature`, `maximum_temperature`
y `precipitation`** que la documentación de la API sí promete — exactamente los campos que este
proyecto más necesitaría (protección invernal). Confirmado leyendo la cabecera del CSV.

### 1.4 Join

**Excelente, y es lo mejor que tiene**: la columna `url_gbif` está rellena en el 93,1 % de las filas
y contiene la URL canónica. Para el olivo devuelve `https://www.gbif.org/species/5415040`, que es
**exactamente la clave del inventario**. También trae `url_powo`, `url_plantnet`, `url_usda`,
`url_wikipedia_en` y `scientific_name` canónico. Si Trefle tuviera datos, el join sería trivial.

### 1.5 ¿Y forks o *mirrors*?

La organización [treflehq](https://github.com/orgs/treflehq/repos) sólo tiene tres repos: `trefle-api`
(vivo a base de dependabot), `documentation` (último *push* 2023-04-18) y `dump` (archivado).
No hay *fork* oficial ni sucesor anunciado. El volcado AGPL de 2020 **es** el *mirror*: cualquiera
puede rehospedarlo, y con 0,9 % de relleno eso no arregla nada.

**Veredicto Trefle**: esquema perfecto, join perfecto, licencia perfecta, **datos inexistentes**.
No sirve. Descartada.

---

## 2. Perenual

### 2.1 Vitalidad

**Viva y comercialmente activa.** La portada de la documentación declara *"API Status - Online"* y
*"Over 10,000+ Species of Plants Available"*; la respuesta de ejemplo del listado devuelve
`"total": 10104` ([perenual.com/docs/api](https://perenual.com/docs/api)). Las páginas públicas de
especie sirven contenido actual con pie *"© 2026 Copyright Perenual"*. Verificado en vivo:

```
GET https://perenual.com/api/species-list?key=demo&q=olive
→ HTTP 404  {"message":"Missing/Issue with API Key. Contact info@perenual.com"}
```

Es la única candidata **claramente en explotación** en 2026.

### 2.2 Coste y cuotas

De su [tarifa oficial](https://perenual.com/subscription-api-pricing):

| Plan | Precio | Cuota | Alcance de datos |
|---|---|---|---|
| **Personal (gratis)** | 0 € | **100 peticiones/día** | *"Access To Species Data(1-3000) API"*, mapa de rusticidad y guías de cuidado sólo de las especies **1–3000** |
| Premium | 59,99 $/mes | 10.000/día | especies 1–10000+, uso comercial |
| Supreme | 139,99 $/mes | 100.000/día | + `xData` (duración de sol en horas, pH, volumen de riego) |
| Enterprise | a medida | a medida | nodo dedicado |

La cuota gratuita de **100 peticiones/día es holgadísima** para una casa con cuatro plantas: bastarían
cuatro llamadas una sola vez. Requiere alta con clave, sin tarjeta en el plan personal.

**Dato favorable, y hay que reconocerlo**: los cuatro taxones de prueba caen **dentro** del tramo
gratuito 1–3000 (véase §2.5). Perenual es la única fuente cuyo tramo gratis cubre todo el banco de pruebas.

### 2.3 Licencia — el problema serio

Sus [Términos de servicio](https://perenual.com/terms-of-service) conceden únicamente el derecho a
*"access and use our Services and Perenual Content for personal purposes"*, y prohíben expresamente:

- *"sell, resell, or use our Services or Perenual Content commercially"*
- usar *"data mining, robots, or similar data collection or extraction tools"*
- *"distribute, publicly showcase, or publicly display Perenual Content, unless we or our licensors explicitly allow it"*
- *"alter Perenual Content, delete any proprietary notices, or make derivative uses"*

El uso personal está permitido, que es este caso. Pero **almacenar una copia local** de las fichas
—que es lo que el proyecto necesita para funcionar sin red y para no gastar cuota— cae en la zona
gris entre "caché" (no regulada explícitamente) y "data extraction" (prohibida). Y *"make derivative uses"*
prohibido choca de frente con el diseño del proyecto, donde un LLM **reescribe** esos hechos
para redactar el consejo semanal. Los ToS no mencionan atribución ni caché en ningún punto.

**Esto no es un detalle jurídico teórico**: el proyecto ya decidió que los hechos duros vivan en una
tabla local citable. Perenual es la fuente que peor encaja con esa decisión.

### 2.4 Granularidad — mixta, y el fondo es prosa generada

El [ejemplo oficial de `species/details`](https://perenual.com/docs/api) es, casualmente, *Abies alba* —
uno de los candidatos del abeto de casa:

```json
{
  "id": 1,
  "common_name": "European Silver Fir",
  "scientific_name": ["Abies alba"],
  "type": "tree",
  "dimensions": { "min_value": 1, "max_value": 1.5, "unit": "feet" },
  "cycle": "Perennial",
  "watering": "Frequent",
  "watering_general_benchmark": { "value": 5-7, "unit": "days" },
  "sunlight": ["full sun", "part shade"],
  "pruning_month": ["March", "April"],
  "pruning_count": { "amount": 1, "interval": "yearly" },
  "hardiness": { "min": "7", "max": "7" },
  "soil": ["Rocky", "Dry", "Well-drained"],
  "drought_tolerant": false,
  "growth_rate": "High",
  "maintenance": "Low",
  "care_level": "Medium"
}
```

Lo bueno: `pruning_month` como array de meses, `pruning_count` con intervalo, `hardiness` como zona
USDA, `watering_general_benchmark` en días, `soil` como lista. **Es la única API viva que emite
ventanas de poda en forma estructurada.** El plan Supreme añade `xSunlightDuration {min:6, max:12, unit:"hours"}`
y `xWateringPhLevel {min:5.5, max:6.5}`, es decir rangos numéricos de verdad — a 139,99 $/mes.

Lo malo, en su propio ejemplo: un *Abies alba* de **"1 a 1,5 pies"** de alto (es un árbol de 40 m),
`hardiness` degenerado a *"min 7, max 7"*, y `watering: "Frequent"` para un abeto. No es un rango,
es un punto; y no es la altura de un abeto, es la de una plántula.

### 2.5 La prueba de cobertura, contra las páginas públicas

Las fichas de Perenual son consultables **sin clave** por web. Verificado en vivo:

| Planta | Página pública | Lo que sirve |
|---|---|---|
| *Olea europaea* | [species/1851](https://perenual.com/plant-database-search-guide/species/1851/guide) — **cultivar 'Arbequina'**, no la especie | Riego "Average", **Hardiness Zone: 8**, Sol "full sun", Suelo "Well-drained", Drought Tolerant "Yes" |
| *Rosa* (género) | **32 fichas distintas**, todas cultivares; **ninguna a rango género** | — |
| *Aloe vera* | [species/614](https://perenual.com/plant-database-search-guide/species/614/guide) | Riego "Minimum", **Hardiness Zone: 10–12**, "full sun", "Well-drained", Indoors "Yes" |
| *Abies alba* | [species/34](https://perenual.com/plant-database-search-guide/species/34/guide) | Riego "Frequent", **Hardiness Zone: 7** (valor único), "full sun", Growth Rate "High" |
| *Picea abies* | [species/342, 343, 388, 694, 1855, 93…](https://perenual.com/plant-database-search-guide?search=Picea+abies) (24 fichas) | — |

**Cobertura: 3 de 4** (falla sólo el rosal, por ser género). Es el mejor resultado de todas las
candidatas vivas. Y el aloe con `Hardiness Zone: 10–12` es información **accionable de verdad**
para una terraza peninsular: dice que hay que protegerlo en invierno.

### 2.6 El problema de fondo: la prosa es generada

Este es el hallazgo que decide el caso Perenual. El texto de poda del olivo, literal de su web pública:

> *"Arbequina European olive trees should be pruned late winter through early spring, while the tree
> is still dormant. Pruning should be done when the tree is 6-7 years old and should continue every
> 2-4 years… Prune the tree so that a pair of strong branches extend downward and outward from each
> main branch, permitting the branches to form an open 'V' shape."*
> — [perenual.com/…/species/1851/guide](https://perenual.com/plant-database-search-guide/species/1851/guide)

Y el de riego, en la misma página:

> *"should be watered well every 7-10 days during warmer months and every 10-14 days during cooler
> months. The soil should be kept consistently moist (not soggy) throughout the year."*

La ventana de poda es correcta. El consejo de riego es **agronómicamente falso** para el caso de esta
casa: un olivo arraigado en suelo en España peninsular no se riega, y "mantener el suelo
consistentemente húmedo todo el año" es la instrucción opuesta a lo que necesita. Es un consejo escrito
para un olivo en maceta en un clima que no es este.

Tres marcas de origen del texto: no cita **ninguna** fuente; la ficha del aloe declara
*"Fruits In Autumn Ready In Summer"* (contradicción interna); y el pie de página avisa de que
*"Perenual and its members cannot be held liable… The information provided is intended to be used
as general guidelines only"*
([ibid.](https://perenual.com/plant-database-search-guide/species/614/guide)).

**Consecuencia de diseño, y es la trampa central de todo este proyecto**: la arquitectura decidida es
*hechos duros citables* → *LLM redacta*. Si los hechos duros salen de prosa generada sin fuente,
el sistema es **un LLM citando a otro LLM**, con la apariencia de rigor de una API REST y sin nada
detrás. Eso es peor que no tener fuente, porque el usuario no puede ver la diferencia.

### 2.7 Join

**Malo.** Se consulta por `id` interno de Perenual o por búsqueda de texto `q=`. No hay clave GBIF,
ni POWO, ni ningún identificador externo en la respuesta. El `scientific_name` es un array de cadenas
**con cultivar incluido** (`"Olea europaea 'Arbequina'"`), lo que impide un join limpio por nombre
canónico: hay 32 fichas para *Rosa* y 24 para *Picea abies*, y elegir entre ellas requiere criterio
humano. Habría que mantener a mano un mapa `GBIF key → Perenual id` por cada planta de casa.

**Veredicto Perenual**: la única API viva que cubre 3 de 4 y da ventanas de poda estructuradas.
Pero licencia hostil a la copia local, sin join taxonómico, y el contenido es prosa generada sin fuente.
**No puede ser la fuente de hechos citables.** Podría ser, como mucho, una fuente de *sugerencias*
claramente etiquetada como no verificada — y ese papel ya lo cubre el propio LLM del proyecto.

---

## 3. PFAF — Plants For A Future

### 3.1 Vitalidad

Viva. El sitio responde y muestra actividad editorial reciente: un banner pide donaciones por
*"our latest book Food Forest Plants for Mediterranean Conditions and our new Native Plants Search
facility"* ([pfaf.org](https://pfaf.org/)) — nótese que el libro nuevo es **específicamente de
condiciones mediterráneas**, que es el clima de esta casa. Organización benéfica registrada en
Inglaterra y Gales (Charity No. 1057719).

### 3.2 Licencia — la mejor de todo el estudio, con una contradicción

La [página de copyright](https://pfaf.org/user/cmspage.aspx?pageid=136) dice:

> *"This work by Plants For A Future is licensed under a Creative Commons Attribution 4.0 License."*

con las imágenes bajo BY-NC-ND y el código fuera de la licencia. **CC-BY 4.0 permite copia local,
modificación y redistribución con atribución** — exactamente lo que el proyecto necesita.

Pero la [página del CD-ROM](https://pfaf.org/user/cmspage.aspx?pageid=71) declara una licencia distinta
para la misma base de datos:

> *"This database and supporting literature is Copyright (c) 1992-2010 Plants For A Future and Ken Fern.
> The database is covered by a Creative Commons licence: (attribution required, non-commercial, share-alike)"*
> → `http://creativecommons.org/licenses/by-nc-sa/1.0`

y lo glosa como *"You are allowed to use it at home or work however you wish"*.

**Contradicción declarada**: CC-BY 4.0 en una página, CC BY-NC-SA 1.0 en otra. Para este proyecto
—personal, no comercial, en local— **ambas lo permiten sin ambigüedad**, y la glosa de PFAF
("en casa como quieras") lo confirma. Si algún día el proyecto se publicase, habría que preguntarles.
Se registra aquí como riesgo conocido, no como bloqueo.

Descarga de la base completa: CD-ROM 68 $ (28 $ tarifa reducida) o descarga digital, en versiones
Access y **texto ASCII con scripts de importación a MySQL**
([ibid.](https://pfaf.org/user/cmspage.aspx?pageid=71)). Coste **cero** si se consulta la web.

### 3.3 La prueba de cobertura — y una trampa importante

Verificado en vivo contra `pfaf.org/user/Plant.aspx?LatinName=…`:

**Olea europaea** — [ficha](https://pfaf.org/user/Plant.aspx?LatinName=Olea+europaea):

```
USDA hardiness: 8-10
Physical Characteristics: evergreen Tree growing to 10 m (32ft) by 8 m (26ft) at a slow rate.
  It is hardy to UK zone 8. In flower from August to September.
  Suitable for: light (sandy), medium (loamy) and heavy (clay) soils, prefers well-drained soil
  and can grow in nutritionally poor soil. Suitable pH: mildly acid, neutral and basic.
  It cannot grow in the shade. It prefers dry or moist soil.
Cultivation details: "…Requires a sunny position[3]. Tolerates salty air[59].
  Generally, older trees are hardy to about -10°c[3, 200]. …
  The plants fruit best on wood that is one year old so any pruning should take this into account[238].
  Pruning can encourage non-fruiting water-shoots[200]."
```

Esto es **cualitativamente distinto** de todo lo anterior: `-10 °C` es un número duro, y los corchetes
`[3, 200, 238]` son **referencias a una bibliografía**. El hecho de poda —"fructifica mejor en madera
de un año, tenlo en cuenta al podar"— tiene fundamento y procedencia. Es citable en el sentido que
este proyecto exige.

**Aloe vera** — [ficha](https://pfaf.org/user/Plant.aspx?LatinName=Aloe+vera):

```
USDA hardiness: 9-11
Cultivation details: "Requires well-drained soil and a very sunny position[1].
  Plants are tolerant of poor soils[200]. If trying to grow this plant outdoors, it will need the
  sunniest and warmest area in the garden and some protection from winter cold (perhaps a glass frame)[K].
  …best grown in a pot placed outdoors in the summer and put in a greenhouse for the winter[1]."
Landscape Uses: Container, Ground cover, Rock garden.
```

Directamente aplicable al aloe de la terraza: maceta fuera en verano, protección en invierno.

**Abies alba** — [ficha](https://pfaf.org/user/Plant.aspx?LatinName=Abies+alba):

```
USDA hardiness: 5-8
Habitats: Forests in mountains, 1000 - 1600 metres.
Cultivation details: "Prefers a good moist but not water-logged soil[1]… Prefers a slightly acid soil,
  with a pH down to about 5, and a north-facing slope[200]. Plants are very shade tolerant…
  Requires a generous rainfall and a sheltered position[11, 81]. Intolerant of windy sites[81].
  The silver fir is a very hardy plant when dormant but it comes into growth in April and is then
  susceptible to damage by late frosts and aphis[185, 238]."
```

Aquí PFAF hace algo que **ninguna otra fuente hizo**: da el contexto ecológico (bosque de montaña,
1000–1600 m, lluvia generosa) que permite concluir que *Abies alba* es una hipótesis **poco probable**
para un jardín de vivienda en España peninsular, y que el abeto de casa es más probablemente
*Picea abies*, *Cupressus* o *Thuja*. Es información que orienta la identificación, no sólo el cuidado.

**Rosa a género — la trampa.** `Plant.aspx?LatinName=Rosa` devuelve **HTTP 200 con una ficha
aparentemente válida**:

```
Rosa - Lindl.
Common Name: Prickly Rose
USDA hardiness: Coming soon
Physical Characteristics: Rosa is a deciduous Shrub growing to 2.5 m (8ft2in)… hardy to UK zone 2.
Range: Northern N. America to N. Europe and Asia.
```

**Esto no es el género *Rosa*.** "Rosa - Lindl." con nombre común "Prickly Rose" y zona UK 2 es
*Rosa acicularis* Lindl., la rosa ártica de Norteamérica y Siberia. PFAF ha resuelto una consulta de
género devolviendo **una especie equivocada, sin ningún aviso, con HTTP 200**. Si el proyecto
consultara PFAF por nombre y aceptara la respuesta, le diría al usuario que su rosal de fachada
mediterránea es rústico hasta zona 2 y aguanta el Ártico.

**Regla de diseño que se deriva de esto**: cualquier consulta a una fuente de cuidados debe verificar
que el **nombre devuelto coincide exactamente** con el nombre consultado. Un HTTP 200 no es una
confirmación de identidad. Y el rango género debe tratarse como **caso explícito**, nunca como
"consulta el nombre a ver qué sale".

**Cobertura PFAF: 3 de 4** (falla el rosal a género, y falla **peligrosamente**, en silencio).

### 3.4 Granularidad y hechos cubiertos

| Hecho | ¿Lo da PFAF? |
|---|---|
| Temperatura mínima / rusticidad | **Sí, doble**: zona USDA (`8-10`) y a menudo °C en prosa (`-10°c`) |
| Exposición | **Sí**, categórico y explícito: *"cannot grow in the shade"* / *"very shade tolerant"* |
| Agua base | **Sí**, cualitativo: *"prefers dry or moist soil and can tolerate drought"* |
| Variación estacional del agua | **Parcial**, en prosa (*"generous rainfall"*, *"moist in summer"*) |
| Sustrato y drenaje | **Sí**, estructurado: textura ligera/media/pesada, pH ácido/neutro/básico, *"prefers well-drained"* |
| Ventana de abonado | **No.** PFAF no cubre fertilización |
| Poda | **Parcial**, en prosa y **con referencia bibliográfica** — sin mes |
| Trasplante | **Parcial**, en la sección de propagación |

### 3.5 Join

**Sin API**, y este es su defecto capital. Se consulta por URL `?LatinName=Olea+europaea`, es decir
por **nombre científico canónico**, que es justo el eje que el proyecto eligió. Pero la respuesta es
HTML que hay que raspar, y no hay endpoint JSON documentado ni clave GBIF en la página.

**El puente existe, y es Wikidata** (§6): la propiedad `P4301` "PfaF ID" enlaza taxón ↔ ficha PFAF.
Verificado en vivo, la cadena `clave GBIF → Wikidata P846 → P4301 → URL de PFAF` funciona para
4 de los 5 taxones probados.

**Veredicto PFAF**: **el mejor contenido de todo el estudio** — numérico donde importa, con
bibliografía, con licencia que permite copia local, con las cuatro plantas cubiertas salvo el género.
Y **no es una API**. Es una web que hay que raspar o una base que hay que comprar.

---

## 4. USDA PLANTS Database

### 4.1 Vitalidad y licencia

**Viva y con API JSON pública sin clave.** Verificado en vivo el 2026-08-13:

```
GET https://plantsservices.sc.egov.usda.gov/api/PlantProfile?symbol=PIAB
→ {"Id":15369,"Symbol":"PIAB","ScientificName":"<i>Picea abies</i> (L.) Karst.",
   "CommonName":"Norway spruce","Rank":"Species","GrowthHabits":["Tree"], …}
```

Obra del gobierno federal de EE. UU. → **dominio público**. Sin clave, sin cuota publicada, sin tarjeta.
Copia local irrestricta. Es, con diferencia, **la licencia más limpia de todo el estudio**.

### 4.2 Granularidad — la mejor de todas, y con margen

```
GET https://plantsservices.sc.egov.usda.gov/api/PlantCharacteristics/15369     [Picea abies]
```

Devuelve 80 características. Extracto de las accionables:

```
Temperature, Minimum (°F)        = -38          →  −38.9 °C
Precipitation, Minimum           = 24           →  610 mm/año
Precipitation, Maximum           = 60           →  1524 mm/año
Frost Free Days, Minimum         = 100
Shade Tolerance                  = Medium
Drought Tolerance                = Medium
Moisture Use                     = Medium
pH, Minimum / Maximum            = 5.0 / 7.0
Root Depth, Minimum (inches)     = 28           →  71 cm
Fertility Requirement            = Medium
Bloom Period                     = Mid Spring
Active Growth Period             = Spring and Summer
Adapted to Coarse/Medium/Fine Textured Soils = No / Yes / Yes
Anaerobic Tolerance              = None
Salinity Tolerance               = Low
Fire Tolerance                   = Low
Growth Rate                      = Slow
Height, Mature (feet)            = 130.0
```

**Esto sí son rangos numéricos accionables**: temperatura mínima en grados, precipitación anual en
un intervalo, profundidad radicular en unidades de longitud, pH acotado, y `Root Depth, Minimum` que
alimenta directamente el `Zr` del modelo hídrico ([modelo-agronomico-riego.md §6.7](modelo-agronomico-riego.md)).
`Shade Tolerance` y `Moisture Use` son categóricos pero de escala definida.

Confirmado también para *Thuja occidentalis* (`Id=15299`, 81 características:
`pH 5.2–7.0`, `Precipitation 35–55`, `Shade Tolerance = Medium`, `Drought Tolerance = Low`,
`Root Depth Min = 30 in`, `Bloom Period = Mid Spring`).

Con una advertencia de calidad: la ficha de *Picea abies* declara `Shape and Orientation = Climbing`.
Una picea no es trepadora. Hay errores en el dataset, y hay que asumirlos.

### 4.3 La prueba de cobertura — donde se rompe

| Consulta | Símbolo | ¿Perfil? | ¿Características? |
|---|---|---|---|
| *Olea europaea* | `OLEU` (Id 49854) | **Sí** | **`[]` — vacío** |
| *Rosa* (género) | `ROSA5` (Id 90742, `"Rank":"Genus"`) | **Sí, y el rango género existe** | **`[]` — vacío** |
| *Aloe vera* | `ALVE2` (Id 27276) | **Sí** | **`[]` — vacío** |
| *Abies alba* | — | **No está** (`ABAL` es *Abronia alpina*) | — |
| *Picea abies* | `PIAB` (Id 15369) | Sí | **Sí, 80 características** |
| *Thuja occidentalis* | `THOC2` (Id 15299) | Sí | **Sí, 81 características** |

**Cobertura de hechos: 1 de 4** (sólo el abeto, y sólo si resulta ser *Picea abies* o *Thuja*).
Y aun así hay un detalle valioso: **USDA sí tiene registros de rango género** (`ROSA5`, `"Rank":"Genus"`),
lo que la convierte en la **única fuente del estudio que admite estructuralmente una consulta a género**.
Sirve el nodo taxonómico; lo que no tiene es sus características.

El patrón es evidente y esperable: la base de USDA está construida para la conservación y restauración
de vegetación **de Estados Unidos**. El olivo y el aloe están catalogados como introducidos y nadie
les ha rellenado la ficha agronómica. Un abeto europeo de bosque de montaña ni siquiera figura.

### 4.4 Hechos cubiertos y join

| Hecho | ¿Lo da USDA? |
|---|---|
| Temperatura mínima | **Sí, numérica** (`Temperature, Minimum (°F)`) — pero nula en muchas fichas (p. ej. *Thuja*) |
| Rusticidad (zona) | No como zona; sí `Frost Free Days, Minimum` |
| Exposición | **Sí**, `Shade Tolerance` (None/Low/Medium/High/Tolerant) |
| Agua base | **Sí**, `Moisture Use` + `Drought Tolerance` + `Precipitation` min/máx en mm/año |
| Variación estacional | **Parcial**, `Active Growth Period` |
| Sustrato y drenaje | **Sí**, adaptación a textura gruesa/media/fina, `Anaerobic Tolerance`, pH |
| **Ventana de abonado** | **Sólo `Fertility Requirement`** (Low/Medium/High). **No hay meses.** |
| **Poda** | **No.** Existe `Coppice Potential` y `Hedge Tolerance`, que no son ventanas de poda |
| Trasplante | **No**, sólo métodos de propagación |

**Join**: por `symbol` USDA (`OLEU`, `PIAB`) o por texto. No devuelve clave GBIF. Pero **Wikidata tiene
la propiedad `P1772` "USDA PLANTS ID"** (verificado: para *Olea europaea* devuelve `OLEU`), así que la
cadena `GBIF → Wikidata → símbolo USDA` es construible igual que la de PFAF.

**Veredicto USDA PLANTS**: dominio público, API abierta sin clave, **los rangos numéricos más duros
de todo el estudio**, rango género soportado — y **vacía para 3 de las 4 plantas de esta casa**.
Es la mejor fuente para el abeto una vez identificado, e inútil para el resto.

---

## 5. GBIF — ¿tiene rasgos?

**No.** Comprobado en vivo, y conviene dejarlo cerrado porque es la fuente que el proyecto ya usa
para taxonomía y sería tentador estirarla.

```
GET https://api.gbif.org/v1/species/5415040
→ {"key":5415040,"canonicalName":"Olea europaea","rank":"SPECIES","taxonomicStatus":"ACCEPTED",
   "family":"Oleaceae","genusKey":3172244, …}
```

Taxonomía impecable, cero cuidados. El endpoint que más se acerca es `speciesProfiles`:

```
GET https://api.gbif.org/v1/species/5415040/speciesProfiles
→ [{"lifeForm":"tree","source":"Global Invasive Species Database"},
   {"habitat":"terrestrial","source":"Global Register of Introduced and Invasive Species - Ireland"},
   {"habitat":"Terrestrial","source":"…Switzerland"}, …]
```

`lifeForm` y `habitat` con vocabulario inconsistente (`"terrestrial"` vs `"Terrestrial"`), procedentes
casi todos de registros de especies invasoras. Nada utilizable.

Y `descriptions` es texto libre heterogéneo y multilingüe:

```
GET https://api.gbif.org/v1/species/5415040/descriptions?limit=5
→ type "materials_examined" | source: "Resolución de 1 de abril de 2022, de la Dirección General de
     Calidad y Evaluación Ambiental… Planta Solar Fotovoltaica Campos 115…"
  type "biology_ecology"    | source: "Info Flora Schweiz - Oleaceae"
     "Oekologische Zeigerwerte (nach Landolt & al. 2010)… Feuchtezahl F maessig trocken,
      Lichtzahl L hell, Reaktionszahl R schwach sauer bis neutral"
```

Curiosidad relevante: el segundo registro **sí es un dato de cuidados** —los índices ecológicos de
Landolt: humedad "moderadamente seco", luz "claro", reacción "ligeramente ácido a neutro"— pero llega
como prosa alemana dentro de un campo de texto, mezclada con la declaración de impacto ambiental de
una planta fotovoltaica de Murcia. No es explotable programáticamente.

**Veredicto GBIF**: es la **columna vertebral de identidad** del proyecto y debe seguir siéndolo.
No es, ni pretende ser, una fuente de cuidados.

---

## 6. Wikidata / Wikispecies

### 6.1 No tiene cuidados

Consultado en vivo el ítem del olivo (`Q37083`) por SPARQL, devuelve decenas de propiedades y
**ninguna de cuidado**: identificadores externos (`P1772` USDA, `P3031` EPPO, `P3151` iNaturalist,
`P4753` Ecocrop, `P1070` Kew), nombres comunes en 30 lenguas, `P4000` "has fruit type" = *drupe*.
No hay temperatura mínima, ni exposición, ni riego, ni poda. Wikispecies es aún más estrecho:
sólo nomenclatura y sinonimia.

### 6.2 Pero es el puente que el proyecto necesita

Esta consulta, ejecutada en vivo contra `query.wikidata.org/sparql`, es el hallazgo operativo más
útil de todo el documento:

```sparql
SELECT ?taxon ?name ?gbif ?pfaf ?powo WHERE {
  VALUES ?gbif { "5415040" "2777724" "8395064" "2685484" "5284884" }
  ?taxon wdt:P846 ?gbif .
  OPTIONAL { ?taxon wdt:P225  ?name }
  OPTIONAL { ?taxon wdt:P4301 ?pfaf }
  OPTIONAL { ?taxon wdt:P5037 ?powo }
}
```

Resultado real:

```
taxon      name          gbif      pfaf          powo
Q34687     Rosa          8395064   (vacío)       …ipni.org:names:30002432-2
Q37083     Olea europaea 5415040   Olea europaea …610675-1
Q80079     Aloe vera     2777724   Aloe vera     …530017-1
Q145992    Picea abies   5284884   Picea abies   …262609-1
Q146992    Abies alba    2685484   Abies alba    …60468511-2
```

**Cinco de cinco resueltos desde clave GBIF**, y **cuatro de cinco con identificador PFAF directo**.
Sólo falla *Rosa*, y falla por lo mismo que en todas partes: es un género y PFAF no tiene ficha de género.

Esto convierte el problema de "join" en un problema resuelto: `GBIF key → wdt:P846 → wdt:P4301` da la
URL de PFAF, y `wdt:P1772` da el símbolo USDA. Gratis, sin clave, licencia **CC0** (Wikidata),
y **sin depender de que ninguna API de cuidados exista**.

**Veredicto Wikidata**: no es fuente de cuidados y **sí** es la capa de identidad que hace viable
todo lo demás. Adóptese como puente.

---

## 7. Plant.id / Kindwise

### 7.1 Vitalidad, coste

Viva y comercial. Precio por petición, de su
[tarifa oficial](https://www.kindwise.com/pricing): Tier A (1.000+ créditos) **0,05 €/petición**,
bajando hasta 0,01 € en Tier F. **100 créditos gratis al registrarse, sin tarjeta**; para comprar más
hace falta facturación.

### 7.2 Granularidad y cobertura — su propia tabla la descarta

Kindwise publica su **tabla de cobertura de contenido** en [kindwise.com/plant-id](https://www.kindwise.com/plant-id).
Cifras suyas, no mías:

| Detalle | Cobertura declarada |
|---|---|
| Taxonomía (reino→género) | 100 % |
| URL | 99,98 % |
| Imagen de Wikipedia | 99,12 % |
| **GBIF ID** | **97,77 %** |
| iNaturalist ID | 95,83 % |
| **Watering** | **9,50 %** |
| Propagation methods | 9,16 % |
| Edible parts | 8,80 % |

**El propio proveedor declara que sólo el 9,50 % de sus taxones tienen dato de riego.** Y `watering`
en Plant.id es un objeto `{min, max}` sobre una escala 1–3 (seco/medio/húmedo): tres niveles, no un
número de días ni de mm. No hay temperatura mínima, ni ventana de poda, ni abonado, ni trasplante
en ningún nivel de servicio.

No se ha hecho llamada real: **requiere clave** y los 100 créditos gratuitos se gastarían en
identificación, no en cuidados. Evaluado por documentación, como se declaró.

### 7.3 Join

**Lo mejor de esta candidata**: GBIF ID al 97,77 %. Si tuviera datos de cuidados, encajaría de
inmediato con la clave del inventario.

**Veredicto Kindwise**: excelente para **identificación** —que es el otro problema del proyecto, el del
abeto—, irrelevante para cuidados. Su valor aquí es negativo pero informativo: una empresa que vive
de esto declara 9,5 % de cobertura de riego, lo que confirma que **el problema no es que yo no haya
encontrado la API buena; es que el dato estructurado de cuidados no existe a escala**.

---

## 8. Pl@ntNet

**No aporta nada de cuidados.** Su
[documentación de `identify`](https://my.plantnet.org/doc/api/identify) enumera la respuesta completa:
`query`, `predictedOrgans`, `bestMatch`, `results[]` (con `score`, `species.scientificNameWithoutAuthor`,
`genus`, `family`, `commonNames`), `otherResults`, `version`, `remainingIdentificationRequests`.
Cada resultado incluye **`gbif.id` y `powo.id`**.

Cero campos de cultivo, riego, exposición, poda o rusticidad. Es un clasificador de imágenes que
devuelve nombres y claves GBIF.

**Veredicto**: relevante para el issue de identificación (y muy bien alineado con la decisión de
usar clave GBIF), **nulo** para cuidados.

---

## 9. Las demás candidatas, en corto

### 9.1 OpenFarm — muerta, sin ambigüedad

```
curl -I https://openfarm.cc/api/v1/crops?filter=olive
→ 301 → https://github.com/openfarmcc/OpenFarm
curl -I https://openfarm.cc/
→ 301 → https://github.com/openfarmcc/OpenFarm
```

**El dominio entero, API incluida, redirige al repositorio de GitHub.** Y ese repositorio está
`"archived": true`, último *push* 2025-04-22, licencia MIT
([openfarmcc/OpenFarm](https://github.com/openfarmcc/OpenFarm) vía API de GitHub).

No hay API. No hay datos servidos. Lo único recuperable sería el código MIT y lo que quede en el repo.
Además su modelo era de "guías de cultivo" aportadas por usuarios para **hortícolas**, no para árboles
ornamentales arraigados. **Descartada por muerta y por alcance.**

### 9.2 APIs de RapidAPI ("House Plants", "Houseplants", plants10…)

Existen varias ([colección de RapidAPI](https://rapidapi.com/collection/plant-api)), todas de autor
individual y todas con la misma forma: un dataset de **plantas de interior** (~300–1.000 fichas)
envuelto en REST, con endpoints tipo `/api/v1/houseplant/{name}`, `/api/v1/families`, `/api/v1/lifeforms`
([Houseplants API, TekBunny](https://rapidapi.com/TekBunny/api/houseplants1)).

Fallan por alcance antes que por nada más: **3 de las 4 plantas de esta casa son leñosas de exterior
arraigadas en suelo** (olivo, rosal, abeto). Un catálogo de *Monstera* y *Zamioculcas* no las contiene.
Sin licencia clara, sin identificadores taxonómicos, sin garantía de continuidad, y con clave de
RapidAPI de por medio. **Descartadas sin llamada real.**

### 9.3 TRY Plant Trait Database

[try-db.org](https://www.try-db.org/TryWeb/Home.php) ofrece *"free and open access to plant trait data"*
bajo CC-BY, con acceso por **solicitud** (no API). El problema es de naturaleza del dato, y lo dice
la propia fuente: su cobertura es casi completa para *"plant growth form"* y para el resto reconoce
*"a humbling lack of completeness and representativeness"*, con ejemplos que son
*"specific leaf area, leaf dry matter content, leaf nitrogen and phosphorus content per area"*.

Son **rasgos funcionales para ecología**, no cuidados de jardín. Área foliar específica no dice
cuándo podar un olivo. **Descartada por naturaleza del dato**, no por vitalidad.

### 9.4 BIEN

Misma familia que TRY: red de datos de biodiversidad de las Américas, con acceso principal vía
paquete de R (`BIEN`), orientada a ocurrencias, rangos y rasgos funcionales. Sesgo geográfico
americano y el mismo problema de naturaleza del dato. **Descartada.**

### 9.5 EOL / TraitBank

[eol.org](https://eol.org/) tiene API pública viva —verificado:
`GET https://eol.org/api/pages/1.0/579181.json` devuelve la página de *Olea europaea* con su
jerarquía taxonómica de 20+ proveedores—. Pero la vista de rasgos (`/pages/579181/data`) está
**tras Cloudflare** (devuelve *"Just a moment…"* a `curl`), y TraitBank agrega rasgos ecológicos del
mismo tipo que TRY. **No verificable en vivo y previsiblemente del tipo equivocado.** No recomendada.

### 9.6 FAO Ecocrop

Interesante en teoría: Ecocrop tabula rangos **absolutos y óptimos** de temperatura y precipitación
por cultivo, y Wikidata ya enlaza a él (`P4753`, olivo = `1553`). Pero el portal histórico está
**tras autenticación**:

```
curl -I "https://ecocrop.review.fao.org/ecocrop/srv/en/cropView?id=1553"
→ 302 → accounts.google.com/o/oauth2/… (Google IAP)
```

Y su alcance es de **cultivos**: tiene olivo, no tiene rosal de jardín ni aloe ornamental ni abeto.
**Descartada por acceso y por alcance**, aunque merece una nota: si algún día hiciera falta un
rango térmico duro para el olivo, ahí está.

---

## 10. La pregunta que más importa: las tareas no-riego

El [inventario](../inventario-plantas.md) es explícito: *"Hoy no se riega nada"*, y 3 de 4 plantas
están arraigadas en suelo. El valor del proyecto está desplazado hacia **poda, abonado, protección
invernal y trasplante**. Esta sección responde a eso, y la respuesta es dura.

| Fuente | Poda | Abonado | Trasplante | Protección invernal |
|---|---|---|---|---|
| **Trefle** | No | No | No | `minimum_temperature` en el esquema, **ausente del volcado** |
| **Perenual** | **`pruning_month: ["March","April"]` + `pruning_count`** + prosa | No | No | `hardiness {min,max}` como zona USDA |
| **PFAF** | Prosa **con referencia bibliográfica**, sin mes | **No** | Prosa (propagación) | **Sí**: zona USDA + °C en prosa + consejo explícito |
| **USDA PLANTS** | No (`Coppice Potential`, `Hedge Tolerance` no lo son) | Sólo `Fertility Requirement` Low/Med/High | No | `Temperature, Minimum (°F)`, `Frost Free Days` |
| **Kindwise** | No | No | No | No |
| **GBIF / Wikidata / Pl@ntNet** | No | No | No | No |

**Ninguna fuente estructurada da una ventana de abonado.** Cero de nueve candidatas. El campo
simplemente no existe en ningún esquema examinado.

**Sólo una fuente da meses de poda en forma estructurada** —Perenual, `pruning_month`— y es
precisamente la que genera su contenido sin citar fuente (§2.6). El resultado es la peor combinación
posible: **el único dato estructurado que existe para el eje de mayor valor del proyecto es también
el menos fiable.**

**Sólo una fuente da poda con fundamento** —PFAF, con corchetes de bibliografía— y lo da en prosa
sin mes, y sin API.

Y hay una razón de fondo, que merece declararse porque explica por qué no aparecerá una API mejor:
**la ventana de poda no es una propiedad de la especie, es una propiedad de la especie × el clima
× el objetivo**. El olivo se poda en invierno tardío en Jaén y en primavera en Galicia; el rosal se
poda distinto si quieres flor o porte. Una API global con un campo `pruning_month` está prometiendo
una precisión que el dominio no tiene. Perenual la promete igualmente.

**Consecuencia**: para el calendario de tareas no-hídricas, la fuente correcta **no es una API global
de especies**. Es literatura agronómica local —extensión agraria española, servicios de sanidad
vegetal autonómicos, IVIA, fichas de cultivo del MAPA— aplicada a **diez taxones**, no a un millón.
Esa investigación no cabe en este documento y debería ser un ticket propio.

---

## 11. Tabla comparativa final

| Fuente | Viva 2026 | Licencia / copia local | Coste | Granularidad | Cobertura 4 plantas | Join |
|---|---|---|---|---|---|---|
| **Trefle** | Zombi (sólo dependabot) | AGPL-3.0, **volcado público** ✅ | Gratis | Esquema numérico excelente | **2/4** y **0,9 %** de relleno | **GBIF 93 %** ✅ |
| **Perenual** | **Sí, comercial** ✅ | ToS prohíbe *data mining* y *derivative uses* ❌ | 100 req/día gratis, tramo 1–3000 | Meses de poda ✅, resto prosa **generada** ❌ | **3/4** ✅ | Sólo id interno ❌ |
| **PFAF** | Sí ✅ | **CC-BY 4.0** (o BY-NC-SA 1.0, contradictorio) ✅ | Gratis web / 68 $ base | **°C, zona USDA, pH, textura + bibliografía** ✅ | **3/4**, y *Rosa* falla **en silencio** ⚠️ | Sin API; **P4301 en Wikidata** ✅ |
| **USDA PLANTS** | Sí, API sin clave ✅ | **Dominio público** ✅ | Gratis, sin cuota | **La más numérica** ✅ | **1/4** ❌ | Símbolo USDA; **P1772** ✅ |
| **GBIF** | Sí ✅ | Abierta ✅ | Gratis | Sin cuidados ❌ | — | **Es la clave** ✅ |
| **Wikidata** | Sí ✅ | **CC0** ✅ | Gratis | Sin cuidados ❌ | 5/5 identificadores ✅ | **El puente** ✅ |
| **Kindwise** | Sí ✅ | Comercial, de pago | 0,01–0,05 €/req | `watering` escala 1–3 | **9,5 % declarado** ❌ | **GBIF 97,8 %** ✅ |
| **Pl@ntNet** | Sí ✅ | Cuota educativa | Gratis con límite | Sin cuidados ❌ | — | GBIF + POWO ✅ |
| **OpenFarm** | **Muerta** ❌ | MIT | — | — | — | — |
| TRY / BIEN / EOL | Sí | CC-BY / varias | Gratis con solicitud | Rasgos **ecológicos**, no cuidados ❌ | — | — |
| FAO Ecocrop | Tras auth ❌ | FAO | — | Rangos T y P ✅ | Sólo cultivos | Wikidata P4753 |

---

## Recomendación

**Ninguna API estructurada de cuidados sirve para este proyecto.** Es la conclusión, y es un hallazgo,
no un fracaso de búsqueda. Conviene enunciarla con precisión porque cada candidata falla por un motivo
distinto y es fácil creer que la siguiente lo arreglará:

- **Trefle** tiene el esquema correcto, la licencia correcta y el join correcto — y **0,9 % de datos**.
  El único campo que importaría para el calendario, `growth_months`, está vacío en las 416.473 filas.
- **Perenual** está viva, cubre 3 de 4 y es la única que emite meses de poda — y su contenido es
  **prosa generada sin fuente**, con un consejo de riego para el olivo que es falso en este clima,
  bajo unos ToS que prohíben el uso derivado que este proyecto haría.
- **USDA PLANTS** es dominio público, sin clave y con los números más duros — y está **vacía para
  3 de las 4 plantas**.
- **PFAF** tiene el mejor contenido, con bibliografía y licencia abierta — y **no es una API**, y
  responde a la consulta de género con **una especie equivocada y HTTP 200**.
- **Kindwise**, que es quien vive de esto, **declara 9,50 % de cobertura de riego**. Ese número es la
  prueba de que el problema es del dominio, no de la búsqueda.

Y sobre el eje de mayor valor del proyecto: **ninguna de las nueve fuentes da una ventana de abonado**,
y la única que da meses de poda estructurados es la que menos fiable es.

### Lo que hay que hacer en su lugar

**Una tabla local curada de taxones, no un cliente de API.** El inventario tiene **4 plantas**.
Aunque crezca a 20, sigue siendo un problema de decenas de filas, no de un millón. Toda la ingeniería
de integración con una API de cuidados existe para resolver un problema de escala que esta casa no tiene.

Forma concreta:

1. **La identidad sigue siendo la clave GBIF**, tal como ya está decidido. No se toca.
2. **Wikidata como puente**, resuelto y verificado (§6.2): `GBIF key → wdt:P846 → wdt:P4301` (PFAF)
   y `→ wdt:P1772` (símbolo USDA). CC0, sin clave, y funcionó para 5 de 5 taxones probados.
3. **Sembrar la tabla con las dos fuentes citables**, en este orden de prioridad:
   - **PFAF** para rusticidad (zona USDA y °C), exposición, tolerancia a sequía, textura y pH del
     sustrato, y las notas de poda con su referencia bibliográfica. Licencia que permite la copia.
   - **USDA PLANTS** para los rangos numéricos duros cuando existan (`Temperature, Minimum`,
     `Precipitation` min/máx, `Root Depth, Minimum` — que alimenta el `Zr` del modelo hídrico).
     Dominio público, sin restricción alguna.
   - **WUCOLS** para `ks`, como ya fijó el [modelo agronómico](modelo-agronomico-riego.md).
4. **Cada campo lleva su fuente y su URL**, en la propia fila. Es lo que exige el diseño
   ("hechos duros de fuente estructurada y citable") y es la razón de que Perenual quede fuera:
   la celda "podar en marzo" tiene que poder responder *"¿quién lo dice?"* con algo que no sea
   otro modelo de lenguaje.
5. **Un campo `confianza` explícito** por fila: `verificado` (fuente primaria citada) /
   `plausible` (fuente secundaria) / `desconocido`. La UI debe distinguirlos, y el LLM debe recibir
   la marca para poder decir "no lo sé" en vez de rellenar el hueco.

### Lo que esto reencuadra del proyecto

**El rango género no es un caso raro: es el caso que rompe todas las fuentes.** *Rosa* falló en
**todas y cada una** de las candidatas — Trefle no tiene ni una fila de género, PFAF devuelve
*Rosa acicularis* disfrazada, Perenual ofrece 32 cultivares y USDA sirve el nodo pero vacío. El
inventario ya había dictaminado que el rosal a género *"no es un fallo de identificación"* sino el
rango terminal correcto. Confirmado: es el rango correcto **y no hay fuente que lo sirva**. El rosal
necesita una ficha escrita a mano. No es deuda técnica; es la única salida.

**La regla de verificación de nombre es obligatoria, no defensiva.** El caso PFAF/*Rosa* —HTTP 200,
ficha con aspecto válido, especie ártica equivocada— demuestra que una consulta por nombre a
cualquiera de estas fuentes puede devolver silenciosamente la planta de otro. Toda respuesta debe
verificar que el **nombre canónico devuelto coincide con el consultado**, y descartarla si no.

**Identificar el abeto cambia lo que se puede saber de él, drásticamente.** Si es *Picea abies* o
*Thuja occidentalis*, USDA PLANTS entrega 80 características numéricas gratis y en dominio público.
Si es *Abies alba*, USDA no lo tiene y sólo queda PFAF —que a cambio aporta el dato más útil de todos:
es un árbol de bosque de montaña de 1000–1600 m que necesita *"generous rainfall"* y es intolerante
al viento, lo que lo hace **poco probable** en un jardín de vivienda peninsular. El ticket de
identificación no es sólo cosmético: **es la puerta de entrada a la única fuente numérica gratuita
del proyecto**.

**El calendario de tareas no-hídricas necesita su propia investigación, local.** Es la mayor parte
del valor —3 de 4 plantas no se riegan— y ninguna API global lo cubre, por una razón estructural:
las ventanas de poda y abonado dependen del clima local y del objetivo, no sólo de la especie.
La fuente correcta es literatura agronómica española (extensión agraria, IVIA, fichas del MAPA)
aplicada a diez taxones. **Debería abrirse como ticket propio**, y probablemente es el ticket de
mayor retorno que queda en el mapa.

---

## Fuentes

**Primarias — llamadas en vivo el 2026-08-13**
- GBIF API — [`/v1/species/5415040`](https://api.gbif.org/v1/species/5415040) · [`/speciesProfiles`](https://api.gbif.org/v1/species/5415040/speciesProfiles) · [`/descriptions`](https://api.gbif.org/v1/species/5415040/descriptions)
- USDA PLANTS API — [`/api/PlantProfile?symbol=PIAB`](https://plantsservices.sc.egov.usda.gov/api/PlantProfile?symbol=PIAB) · `/api/PlantCharacteristics/{Id}` (15369 Picea abies, 15299 Thuja occidentalis, 49854 Olea europaea, 27276 Aloe vera, 90742 Rosa)
- Wikidata SPARQL — [query.wikidata.org/sparql](https://query.wikidata.org/sparql) (P846 GBIF, P4301 PfaF, P1772 USDA PLANTS, P5037 POWO)
- Trefle API — [`/api/v1/plants`](https://trefle.io/api/v1/plants) (401, servidor vivo)
- Trefle, volcado oficial — [species.csv, release 1.0.0-alpha+20201015](https://github.com/treflehq/dump/releases) (178 MB, 416.473 filas; recuentos de relleno propios)
- OpenFarm — `https://openfarm.cc/` → 301 a GitHub
- Perenual — [`/api/species-list`](https://perenual.com/api/species-list) (404 sin clave) y fichas públicas [1851 olivo](https://perenual.com/plant-database-search-guide/species/1851/guide), [614 aloe](https://perenual.com/plant-database-search-guide/species/614/guide), [34 Abies alba](https://perenual.com/plant-database-search-guide/species/34/guide)
- PFAF — fichas [Olea europaea](https://pfaf.org/user/Plant.aspx?LatinName=Olea+europaea), [Aloe vera](https://pfaf.org/user/Plant.aspx?LatinName=Aloe+vera), [Abies alba](https://pfaf.org/user/Plant.aspx?LatinName=Abies+alba), [Rosa](https://pfaf.org/user/Plant.aspx?LatinName=Rosa)
- EOL API — [`/api/pages/1.0/579181.json`](https://eol.org/api/pages/1.0/579181.json) (vivo); `/pages/579181/data` bloqueado por Cloudflare
- FAO Ecocrop — `ecocrop.review.fao.org` → 302 a Google IAP

**Primarias — documentación y términos**
- [Trefle — campos de planta y especie](https://docs.trefle.io/docs/advanced/plants-fields) · [blog, última entrada 2020-07-15](https://docs.trefle.io/blog) · [repo trefle-api](https://github.com/treflehq/trefle-api) · [issue #119 "Is this project still maintained"](https://github.com/treflehq/trefle-api/issues/119) · [issue #120 "Confirmation E-Mail not sent"](https://github.com/treflehq/trefle-api/issues/120)
- [Perenual — documentación de la API](https://perenual.com/docs/api) · [tarifas](https://perenual.com/subscription-api-pricing) · [términos de servicio](https://perenual.com/terms-of-service)
- [PFAF — información de copyright (CC-BY 4.0)](https://pfaf.org/user/cmspage.aspx?pageid=136) · [CD-ROM de la base de datos (CC BY-NC-SA 1.0, 68 $)](https://pfaf.org/user/cmspage.aspx?pageid=71) · [descarga digital](https://pfaf.org/User/cmspage.aspx?pageid=126)
- [Kindwise — plant.id, tabla de cobertura de contenido](https://www.kindwise.com/plant-id) · [tarifas](https://www.kindwise.com/pricing)
- [Pl@ntNet — documentación del endpoint identify](https://my.plantnet.org/doc/api/identify)
- [TRY Plant Trait Database](https://www.try-db.org/TryWeb/Home.php)
- [OpenFarm — repositorio archivado, MIT](https://github.com/openfarmcc/OpenFarm)
- [RapidAPI — colección de APIs de plantas](https://rapidapi.com/collection/plant-api) · [Houseplants API (TekBunny)](https://rapidapi.com/TekBunny/api/houseplants1)

**Del propio repositorio**
- [docs/inventario-plantas.md](../inventario-plantas.md) — el banco de pruebas de 4 plantas
- [docs/research/modelo-agronomico-riego.md](modelo-agronomico-riego.md) — KL, WUCOLS, `Zr`, y el estilo de este documento
