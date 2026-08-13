# Fuente de hechos de cuidado por especie: datasets y publicaciones descargables

> Investigación para el issue [#3](https://github.com/Joosle/Plantas/issues/3), hijo del mapa [#1](https://github.com/Joosle/Plantas/issues/1).
> Fecha: 2026-08-13. Fuentes primarias citadas con URL. Todo dato numérico de este documento
> se ha **verificado en vivo** contra el fichero o la API correspondiente en esa fecha.
> Alcance: **datasets y publicaciones descargables**. Las APIs REST comerciales (Perenual,
> Trefle, Plant.id y similares) quedan fuera; las cubre otro documento.
> Banco de pruebas: las cuatro plantas de `docs/inventario-plantas.md`, no recuentos totales.

---

## Resumen de lo que dicen las fuentes

| Pregunta | Respuesta corta | Fuente que la posee |
|---|---|---|
| ¿Hay una fuente estructurada con el `ks` de WUCOLS descargable? | **Sí.** JSON completo de 4.103 taxones, 2,8 MB, un GET sin clave | [`wucols.blob.core.windows.net`](https://wucols.blob.core.windows.net/wucols-export/meta/wucols-data.json) |
| ¿Con qué licencia? | **Ninguna abierta.** «Copyright © The Regents of the University of California… All rights reserved» | [WUCOLS, pie de página](https://wucols.ucdavis.edu/project-background) |
| ¿Cubre las 4 plantas de casa? | **Sí, las 4** (rosal y abeto sólo por agregado de género/tipo) | verificado, §1.4 |
| ¿Da algo más que agua? | Exposición y drenaje en texto libre, sólo en **1.619 de 4.103** fichas. Nada de poda ni abonado | verificado, §1.5 |
| ¿USDA PLANTS sirve? | **No.** De 7 taxones probados sólo 2 tienen ficha de características, y son los dos norteamericanos | verificado, §3 |
| ¿Quién da zona de rusticidad USDA por especie, descargable? | **PFAF** (7.400 especies, 50 $) y **SelecTree** (2.087 árboles, API abierta) | §2, §5 |
| ¿Existe un mapa de rusticidad oficial de España? | **No** publicado por AEMET ni por el Ministerio. Lo que circula son mapas de webs comerciales | §9 |
| ¿Cuál es entonces la vía honesta? | **Calcularla**: la zona USDA es, por definición, la media de las mínimas extremas anuales. Open-Meteo la sirve | §9.2, verificado |
| ¿Hay fuente citable de ventanas de poda/abonado en clima español? | **Para el olivo sí** (IFAPA, MAPA). **Para rosal y coníferas ornamentales, no** | §10 |
| ¿Basta alguna fuente estructurada sola? | **No.** Ninguna cubre el calendario de tareas no-hídricas, que es donde está el valor de este proyecto | §11 |

**Conclusión de altura**: hay exactamente **una** fuente estructurada que merece llamarse
primaria para este proyecto —WUCOLS—, y sólo responde a *una* pregunta (cuánta agua).
Todo lo demás que la app necesita decir (cuándo podar el rosal, cuándo abonar el olivo, si el
abeto aguanta el invierno aquí) **no existe como dataset**. O sale de documentos PDF de
extensión agraria española leídos y transcritos a mano, o no sale. Esto no invalida el diseño
—el LLM sigue redactando el consejo— pero sí **desplaza el trabajo**: la ficha de especie no
se rellena importando un CSV, se rellena a mano para cuatro plantas.

---

## 1. WUCOLS V — UC Davis / California Center for Urban Horticulture

Es la fuente que el modelo agronómico ya asumió
([`modelo-agronomico-riego.md` §2.3](./modelo-agronomico-riego.md)) para el factor `ks`.
Aquí se comprueba si de verdad se puede usar.

### 1.1 Formato y accesibilidad

El portal público es una SPA en
[`wucols-frontend.ucdavis.edu`](https://wucols-frontend.ucdavis.edu/), y el flujo
**oficialmente soportado** de extracción es manual: seleccionar ciudad, buscar, marcar plantas
una a una con «Select for my List» y pulsar «Export to Excel»
([instrucciones oficiales](https://wucols.ucdavis.edu/plant-search-instructions)). Es inútil
para poblar una base de datos.

Pero el bundle de la SPA revela dos endpoints que sirven **la base entera**:

```
GET https://wucols.blob.core.windows.net/wucols-export/meta/wucols-data.json
→ {"cachedBlobUrl":"https://wucols.blob.core.windows.net/wucols-export/
                    data%2Fwucols.2025-06-27T18%3A52%3A00.043Z.json"}

GET <cachedBlobUrl>
→ 2.851.392 bytes de JSON
```

Verificado el 2026-08-13: HTTP 200, sin clave, sin CORS de por medio, sin *rate limit*
aparente. El puntero de indirección lleva la **fecha del snapshot en el nombre del fichero**
(`2025-06-27`), lo que da versionado gratis: se guarda el `cachedBlobUrl` visto y se detecta
si cambió.

Estructura del fichero, verificada:

| Clave | Contenido |
|---|---|
| `regions` | 6 regiones climáticas de California |
| `plantTypes` | 12 tipos (árbol, arbusto, suculenta, palmera…) |
| `waterUseClassifications` | las 6 categorías con su **rango numérico de plant factor** |
| `cities` | 778 municipios → región |
| `plants` | **4.103** taxones |
| `photos` | 1.311 |

Cada planta:

```json
{
  "id": 3567,
  "botanicalName": "Abelia chinensis (Linnaea chinensis)",
  "commonName": "Chinese abelia",
  "types": ["S"],
  "culturalInformation": "",
  "waterUseByRegion": ["M","U","U","M","NA","NA"]
}
```

Y la tabla de categorías, **verbatim del propio fichero** — nótese que difiere ligeramente de
los valores que se anotaron en el modelo agronómico:

| Código | Nombre | `plantFactor` | % ET0 |
|---|---|---|---|
| `VL` | Very Low | `< 0.10` | `< 10` |
| `LO` | Low | `0.10-0.30` | `10-30` |
| `M` | Moderate | `0.40-0.60` | `40-60` |
| `H` | High | `0.70-0.90` | `70-90` |
| `U` | Unknown | N/A | N/A |
| `NA` | Not Appropriate for this Region | N/A | N/A |

> **Corrección al modelo agronómico.** `docs/research/modelo-agronomico-riego.md` §2.3 anota
> «Muy baja 0.1 · Baja 0.3 · Moderada 0.6 · Alta 0.8». Los **rangos** reales son los de
> arriba; 0.3 es el *techo* de «Low», no su valor. Si se toman puntos medios, salen
> **0.05 / 0.20 / 0.50 / 0.80**. La diferencia entre 0.3 y 0.2 para el aloe es un 33 % de la
> demanda calculada. Conviene corregirlo en la implementación.

Hay dos categorías que el modelo **no contempla y debe contemplar**: `U` (sin evaluar) y `NA`
(la región no es apta). Ambas deben desactivar el balance hídrico, no colapsar a un número.

### 1.2 Licencia

**Esto es el problema de WUCOLS.** No hay ningún texto de licencia abierta en ningún sitio del
portal. Lo único que hay es el pie de todas sus páginas:

> «Copyright © The Regents of the University of California, Davis campus. All rights reserved.»
> ([wucols.ucdavis.edu/project-background](https://wucols.ucdavis.edu/project-background),
> «Last update: June 6, 2025»)

Lectura honesta:

- **Uso personal con copia local**: no hay permiso *expreso*, pero tampoco cláusula que lo
  prohíba, y el propio flujo oficial invita a exportar a Excel y guardarlo en tu ordenador
  («Remember to Save and Name your Favorites list to your computer»,
  [instrucciones](https://wucols.ucdavis.edu/plant-search-instructions)). Para una PWA de una
  sola casa, sin redistribución, el riesgo práctico es despreciable.
- **Redistribuir el dump o publicar la app con la base embebida**: **no está autorizado**.
  Si el proyecto deja de ser privado, hay que escribir a `ahkiers@ucdavis.edu` y pedir permiso,
  o pasar a consultar en línea en vez de embeber.
- Consumir el endpoint del blob es **técnicamente trivial pero contractualmente indocumentado**.
  No hay contrato de API, no hay compromiso de estabilidad. Puede desaparecer sin aviso. El
  diseño debe tratarlo como **importación puntual**, no como dependencia en tiempo de ejecución.

### 1.3 Coste y vitalidad en 2026

Gratis. Y muy vivo:

- WUCOLS V es la 5.ª edición; la 4.ª es de 2014 (Costello & Jones), la 1.ª de 1992
  ([historial](https://wucols.ucdavis.edu/project-background)).
- El snapshot descargable lleva fecha **2025-06-27**; el portal, «Last update: June 6, 2025».
- El proyecto **está financiado por el Water Use Efficiency Office del California Department
  of Water Resources (DWR)** ([ibíd.](https://wucols.ucdavis.edu/project-background)), y las
  fichas registran aprobaciones regulatorias recientes. Del campo `culturalInformation` de
  *Olea europaea*, literal:

  > «All *Olea europaea* subspecies and cultivars are assigned the same water use value (Plant
  > Factor) as the species in this climate region unless quantitative or qualitative research
  > information provides a different water use value **as approved by DWR, January 2025**.»

  Que una agencia estatal apruebe los valores es la mejor señal de vitalidad que puede dar una
  base de datos de plantas: no es un proyecto de un doctorando, es un insumo regulatorio.

### 1.4 Prueba de cobertura — las cuatro plantas de casa

Consultado el 2026-08-13 contra el JSON descargado. Regiones: 1 North-Central Coastal,
2 Central Valley, 3 South Coastal, 4 South Inland, 5 High/Intermediate Desert, 6 Low Desert.

**Olivo — `Olea europaea ssp. & cvs.`, «olive», tipos [T, S]**

| R1 | R2 | R3 | R4 | R5 | R6 |
|---|---|---|---|---|---|
| `LO` | `LO` | `LO` | `LO` | `LO` | `LO` |

Cobertura perfecta y **sin ambigüedad regional**: `Low` en las seis regiones →
`ks ∈ [0.10, 0.30]`. Para un olivo arraigado en secano, que es el caso de casa, el dato
confirma lo que el inventario ya suponía.

**Aloe — no hay ficha de *Aloe vera*.** Hay **una sola** entrada de género:
`Aloe spp. & cvs.`, «aloe», tipo `Su` (suculenta):

| R1 | R2 | R3 | R4 | R5 | R6 |
|---|---|---|---|---|---|
| `LO` | `LO` | `LO` | `LO` | `LO` | `LO` |

`ks ∈ [0.10, 0.30]` uniforme. **Consecuencia de diseño**: el aloe del inventario está
identificado a especie (GBIF `2777724`) pero WUCOLS sólo lo tiene a género. El *lookup*
**debe subir por la taxonomía** cuando falle la especie; si no, la única planta de la casa con
decisión de riego real se queda sin `ks`.

**Rosal — `Rosa` a género.** Aquí WUCOLS hace exactamente lo contrario: **74 entradas**, casi
todas cultivares comerciales (`Rosa 'Radrazz' PP11836`, `Rosa 'Meiswetdom' PP21612`…), y
**ninguna entrada genérica `Rosa spp.`**. Distribución por región de las 74:

| Región | Reparto |
|---|---|
| 1 North-Central Coastal | M 39 · U 29 · LO 6 |
| 2 Central Valley | M 37 · LO 31 · U 4 · VL 1 · NA 1 |
| 3 South Coastal | U 26 · LO 26 · M 15 · NA 4 · VL 3 |
| 4 South Inland | U 39 · M 32 · LO 3 |
| 5 High/Interm. Desert | U 40 · M 32 · H 1 · NA 1 |
| 6 Low Desert | U 67 · M 5 · H 1 · NA 1 |

**Respuesta a «¿da algo útil sin especie?»: sí, y es defendible.** En la región 2 (Central
Valley, mediterráneo de verano seco y caluroso, el análogo menos malo del interior peninsular)
el 92 % de los cultivares evaluados son `M` o `LO`, y la moda es `M`. Tomar
**`ks = Moderate` (0.40–0.60)** para un rosal de jardín desconocido está respaldado por 68
evaluaciones concordantes, no por una intuición. La cola es real —hay un cultivar `VL` y otro
`H`— pero cae dentro del «factor ~2 entre bins» que el modelo agronómico ya declaró como su
resolución.

Esto es un hallazgo con valor propio: **la agregación a género en WUCOLS es estadísticamente
sostenible cuando hay decenas de cultivares evaluados**, y la app puede exponer la dispersión
(«68 de 74 cultivares de rosal son moderados») en vez de fingir un valor único.

**Abeto sin identificar — las cuatro hipótesis:**

| Taxón | R1 | R2 | R3 | R4 | R5 | R6 |
|---|---|---|---|---|---|---|
| *Abies alba* | **no está en WUCOLS** | | | | | |
| `Abies spp. (CA native and non-native)` | `M` | `M` | `M` | `NA` | `NA` | `NA` |
| `Abies pinsapo` (pinsapo, español) | `LO` | `LO` | `LO` | `NA` | `NA` | `NA` |
| *Picea abies* «Norway spruce» | `M` | `M` | `M` | `NA` | `NA` | `NA` |
| `Cupressus sempervirens ssp. & cvs.` | `LO` | `LO` | `LO` | `LO` | `LO` | `M` |
| `Thuja occidentalis ssp. & cvs.` | `M` | `M` | `M` | `M` | `M` | `M` |

Aquí está la prueba de por qué identificar el abeto importa: **el `ks` se mueve de `LO`
(0.10–0.30) a `M` (0.40–0.60) según cuál sea**, un factor 2,5 en la demanda hídrica. Es
literalmente el eje de mayor incertidumbre que el modelo agronómico identificó, y en esta
planta está abierto de par en par.

Dato adicional relevante: los tres géneros de conífera de montaña salen `NA` («Not Appropriate
for this Region») en las regiones áridas de California — el comité regional dice que **no
deberían plantarse ahí**. Eso es información de idoneidad, no de riego, y es exactamente la
clase de aviso que la app querría dar si el abeto resulta ser un *Picea abies* en Castilla.

**Marcador de cobertura: 4 de 4** (2 a especie exacta, 1 por agregado de género, 1 pendiente
de identificar pero cubierto en todas sus hipótesis salvo *Abies alba*).

### 1.5 Granularidad más allá del agua

`culturalInformation` está poblado en **1.619 de 4.103** fichas (39 %). Es HTML con texto
libre. Del olivo, íntegro:

> «*Olea europaea* (olive), evergreen tree, slow growing to 20-35 feet tall and wide, with
> gray-green, lance-shaped leaves… Native to the Mediterranean region… Fruit drop can be messy
> and trees can spread invasively… **Full sun, good drainage. Sunset: 8-9,11-24.**»

Lo aprovechable son tres cosas: **exposición** («Full sun»), **drenaje** y **zonas Sunset**
(el sistema climático de *Sunset Western Garden Book*, propio del oeste de EE. UU., **no
transferible a España** — no es USDA ni es Köppen).

**No hay ningún campo de poda, abonado, trasplante ni protección invernal.** Ni estructurado ni
en el texto. WUCOLS responde a una pregunta y sólo a una.

### 1.6 Join con GBIF

Los nombres de WUCOLS son sucios. Clasificando los 4.103:

| Forma | n | % |
|---|---|---|
| Binomio limpio (`Picea abies`) | 2.432 | 59,3 % |
| Cultivar entrecomillado (`Rosa 'Radrazz'`) | 564 | 13,7 % |
| Otro (con `var.`, autores, paréntesis) | 548 | 13,4 % |
| Agregado (`spp.` / `& cvs.`) | 480 | 11,7 % |
| Híbrido (`x`) | 77 | 1,9 % |
| Sólo género | 2 | 0,0 % |

Pero **el matcher de GBIF los digiere**. Verificado el 2026-08-13 contra
`api.gbif.org/v1/species/match`:

| Cadena WUCOLS | matchType | rango | usageKey |
|---|---|---|---|
| `Olea europaea ssp. & cvs.` | HIGHERRANK | SPECIES | **5415040** |
| `Aloe spp. & cvs.` | HIGHERRANK | GENUS | **2770879** |
| `Cupressus sempervirens ssp. & cvs.` | EXACT | SPECIES | 2684030 |
| `Thuja occidentalis ssp. & cvs.` | EXACT | SPECIES | 2684178 |
| `Picea abies` | EXACT | SPECIES | 5284884 |
| `Abies pinsapo` | EXACT | SPECIES | 2685464 |

Las claves 5415040 (olivo) y 8395064 (`Rosa`) **son exactamente las que el inventario ya tiene
registradas**. El join a GBIF funciona de verdad, no en teoría.

La única fricción real es la de dirección contraria, ya vista con el aloe: WUCOLS indexa a
género (`2770879`) lo que el inventario tiene a especie (`2777724`). **Regla de resolución
necesaria**: buscar por `usageKey` de especie; si falla, pedir a GBIF el `parentKey` y
reintentar a género; si falla, declarar sin cobertura.

---

## 2. SelecTree — Cal Poly, Urban Forest Ecosystems Institute

### 2.1 Formato y accesibilidad

No hay fichero de descarga, pero **hay una API JSON abierta y sin clave**, y su endpoint de
búsqueda **ignora el filtro de nombre y devuelve el catálogo entero**:

```
GET https://selectree.calpoly.edu/api/tree/search-by-name?name=<cualquier cosa>
→ 735.921 bytes, 2.087 árboles (id, familia, nombre aceptado, nombre común, foto)

GET https://selectree.calpoly.edu/api/tree/detail/{tree_id}
→ ficha completa, 70+ campos
```

Verificado el 2026-08-13. Descargar la base entera son 2.088 peticiones. Es viable como
importación puntual, no como consulta en vivo.

### 2.2 Granularidad — es la mejor de todas las fuentes revisadas

De `/api/tree/detail/970` (*Olea europaea*), campos reales:

```
usda_zone            [8, 9, 10]
sunset_zone          ["8","9","11".."24","H1","H2"]
water_use            "Low"
shade_tolerant       0
growth_rate_high     24            (pulgadas/año)
height_high / width_high   30 / 30 (pies)
root_damage_potential      "Moderate"
wind_resistance            "Medium Low"
salt_tolerance             "Moderate"
branch_strength_low/high   "Strong" / "Strong"
soil_ph_low / soil_ph_high 3 / 7
foliage_type               "Evergreen"
biogenic_emissions         "Low"
```

**Zona USDA numérica por especie** es justo lo que ninguna otra fuente estructurada da con
esta limpieza. Advertencia: los campos `soil_ph_*` son incoherentes entre fichas
(*Abies alba* devuelve 1–4, *Aloe* 5–4 con el mínimo por encima del máximo); parecen índices
codificados, no pH, y **no están documentados**. No usarlos.

### 2.3 Prueba de cobertura

| Taxón | ¿está? | `usda_zone` | `water_use` |
|---|---|---|---|
| *Olea europaea* | **sí** | 8, 9, 10 | Low |
| *Abies alba* | **sí** | 4–8 | High |
| *Picea abies* | **sí** | 3–8 | Medium |
| *Cupressus sempervirens* | **sí** | 7–10 | Medium |
| *Thuja occidentalis* | **sí** | 3–8 | Medium |
| *Rosa* (cualquiera) | **no — 0 resultados** | — | — |
| *Aloe vera* | **no** (sólo *Aloe barberae* y *Aloe* 'Hercules', que son arbóreos) | — | — |

**Marcador: 2 de 4** (olivo y abeto en sus 4 hipótesis). Es un catálogo de **árboles urbanos**;
por construcción no tendrá nunca ni rosales ni suculentas de maceta. Cubre exactamente la
mitad del inventario de esta casa y nunca cubrirá la otra mitad.

Discrepancia digna de mención: SelecTree da *Abies alba* `water_use = High`; WUCOLS da
`Abies spp.` = `Moderate`. No usan la misma escala ni la misma metodología. **No se pueden
mezclar en el mismo campo.**

### 2.4 Licencia y vitalidad

No se ha localizado ningún texto de términos de uso ni de licencia en el portal ni en la API.
Como en WUCOLS, es una universidad pública estadounidense sin declaración explícita. Los campos
`memo` y `schoolyard_use_notes` tienen una prosa uniforme y genérica que sugiere generación
automática reciente; **no se ha podido verificar su procedencia**, así que ese texto no debe
tratarse como hecho citable. Los campos categóricos sí.

---

## 3. USDA PLANTS Database — NRCS

### 3.1 La prueba de cobertura lo descarta antes que ninguna otra consideración

Verificado el 2026-08-13 contra `plantsservices.sc.egov.usda.gov/api`:

| Consulta | Símbolo | `HasCharacteristics` |
|---|---|---|
| *Olea europaea* | OLEU | **False** |
| *Aloe vera* | ALVE2 | **False** |
| *Abies alba* | ABAL3 | **False** |
| *Cupressus sempervirens* | CUSE2 | **False** |
| `Rosa` | (no resuelve el género; devuelve *Rottboellia sanguinea*) | **False** |
| *Picea abies* | PIAB | True |
| *Thuja occidentalis* | THOC2 | True |

**2 de 7.** Y no es casualidad: PLANTS es una base de **plantas de conservación de Estados
Unidos**, y los dos taxones con ficha son precisamente los dos que se plantan en Norteamérica.
El olivo, el aloe, el abeto europeo y el ciprés mediterráneo están en el catálogo nomenclatural
pero **sin ni un solo atributo agronómico**.

**Marcador: 1 de 4** (sólo el abeto, y sólo en dos de sus cuatro hipótesis).

### 3.2 Lo que sí da, cuando da

Es una pena, porque la ficha es excelente. *Thuja occidentalis*
(`GET /api/PlantCharacteristics/15299`) devuelve **81 características**, de las cuales las
accionables:

```
Temperature, Minimum (°F)     -33          → −36 °C
Drought Tolerance             Low
Moisture Use                  Medium
Shade Tolerance               Medium
pH, Minimum / Maximum         5.2 / 7.0
Precipitation, Minimum/Máx.   35 / 55      (pulgadas/año)
Root Depth, Minimum (inches)  30
Frost Free Days, Minimum      100
Salinity Tolerance            Medium
CaCO3 Tolerance               High
Fertility Requirement         Medium
Anaerobic Tolerance           Medium
Active Growth Period          Spring and Summer
```

`Temperature, Minimum (°F)` es un **umbral absoluto en grados**, no una zona — mejor dato de
rusticidad que cualquier mapa. `Root Depth, Minimum` alimenta directamente el `Zr` del balance
en suelo. `Precipitation, Minimum` es un chequeo de idoneidad de sitio de primera.

Si esta ficha existiera para el olivo, sería la fuente primaria del proyecto. No existe.

### 3.3 Licencia, formato, vitalidad

- **Licencia**: dominio público de EE. UU. Política general del USDA: «Most information on
  USDA websites is in the public domain and can be freely distributed or copied»
  ([USDA, Policies and Links](https://www.usda.gov/about-usda/policies-and-links)). Es la
  licencia más limpia de todo este documento: copia local, redistribución y derivados sin
  restricción.
- **Descarga masiva**: la página de descargas
  ([plants.sc.egov.usda.gov/home/downloads](https://plants.sc.egov.usda.gov/home/downloads))
  es una SPA Angular; el «Complete PLANTS Checklist» histórico estaba en
  `plantsorig.sc.egov.usda.gov/dl_all.html`, **host que ya no resuelve por DNS** (verificado:
  `ENOTFOUND`). El checklist descargable es nomenclatura, no características; las
  características se obtienen ficha a ficha por la API.
- **Vitalidad**: el dominio `plants.usda.gov` redirige a `plants.sc.egov.usda.gov`, y hay
  restos de al menos tres generaciones de portal (`plantsorig`, `adminplants`, el actual). La
  API responde, pero el conjunto huele a mantenimiento mínimo.

**Veredicto**: la mejor licencia y la peor cobertura. Descartado como fuente primaria; útil
como *fallback* de dominio público para las pocas especies que cubra.

---

## 4. USDA/ARS GRIN-Taxonomy

Nomenclatura y germoplasma: «all families and genera of vascular plants and over 46,000
species», enfocado en «economic plants and their relatives»
([USDA-ARS GRIN](https://www.ars-grin.gov/)). Está publicado en GBIF como dataset
([GRIN Taxonomy](https://www.gbif.org/dataset/66dd0960-2d7d-46ee-a491-87b9adcfe7b1)), es
dominio público, y está muy vivo.

**No aporta nada a este proyecto.** No tiene atributos de cuidado. Y su función —resolver un
nombre a un taxón canónico— ya la cumple GBIF, que es lo que el proyecto usa. Descartado sin
más análisis.

---

## 5. PFAF — Plants For A Future

### 5.1 Formato, coste y licencia

- **Descarga masiva sí**, de pago: la base de **7.400 plantas templadas** en **Excel, CSV y
  DB**, edición doméstica **50 $**, comercial 150 $
  ([PFAF, descargas](https://plantsforafuture.com/temperate-plant-database/),
  [tienda](https://pfaf.org/user/shop.aspx)).
- **Licencia**: Creative Commons con atribución, **no comercial** y **compartir igual**. Para
  un proyecto personal, encaja sin fricción.
- Consulta web gratuita en [pfaf.org](https://pfaf.org/), ficha por ficha.

### 5.2 Prueba de cobertura — la mejor de todas

Verificado el 2026-08-13 leyendo las fichas web:

| Taxón | USDA hardiness | Sombra | Suelo / humedad |
|---|---|---|---|
| *Olea europaea* | **8–10** | «It cannot grow in the shade» | ligero/medio/pesado, bien drenado, tolera sequía |
| *Aloe vera* | **9–11** | «It cannot grow in the shade» | ligero/medio, bien drenado, tolera sequía |
| *Abies alba* | **5–8** | «can grow in full shade (deep woodland)» | prefiere suelo húmedo; no tolera contaminación |
| *Picea abies* | **2–7** | no tolera sombra | prefiere húmedo o encharcado; suelos ácidos |
| *Cupressus sempervirens* | **7–11** | no tolera sombra | bien drenado, tolera sequía |
| *Thuja occidentalis* | **3–7** | semisombra o sol | húmedo o encharcado, tolera muy alcalino |
| *Rosa canina* | 3–7 | semisombra o sol | tolera vientos fuertes, no exposición marítima |

**Marcador: 3,5 de 4.** Cubre olivo, aloe y las cuatro hipótesis de abeto **con zona de
rusticidad numérica**, que es exactamente el dato que falta en WUCOLS. Falla en el rosal por la
misma razón que WUCOLS acierta y al revés: PFAF es una base de **especies silvestres útiles**,
así que tiene *Rosa canina* pero **ningún cultivar de jardín**. Aplicar los datos de
*R. canina* a un híbrido de té sería un error de bulto —el escaramujo silvestre es mucho más
rústico y mucho menos exigente que un rosal moderno injertado.

### 5.3 Vitalidad y granularidad

El sesgo de PFAF es su propósito: es una base de **plantas comestibles y de uso**, y por eso
sus campos más ricos son «Edibility Rating», «Medicinal Rating», usos. Los campos de cultivo
son categóricos y consistentes (rusticidad, sombra, textura de suelo, pH, humedad, exposición
marítima, tolerancia a contaminación), pero **la sección de cuidados es prosa libre**, y la
mayor parte del contenido procede de referencias bibliográficas de los años 70–90 (las
citas `[50]`, `[76]`, `[7, 65]` visibles en las fichas). No hay fecha de última revisión por
ficha. **Trátese como fuente estable pero no actualizada.**

No da ventanas de poda ni de abonado.

---

## 6. Kew — POWO y WCVP

El *World Checklist of Vascular Plants* se descarga entero como fichero delimitado por `|` en
[`sftp.kew.org/pub/data-repositories/WCVP/`](http://sftp.kew.org/pub/data-repositories/WCVP/),
versión **v14 de 28 de mayo de 2025**
([Kew, About WCVP](https://powo.science.kew.org/about-wcvp)), y está publicado con licencia
abierta en GBIF ([dataset WCVP](https://www.gbif.org/dataset/f382f0ce-323a-4091-bb9f-add557f3a9a2))
y en Catalogue of Life. Descrito en *Scientific Data*
([Govaerts et al. 2021](https://www.nature.com/articles/s41597-021-00997-6)).

Es **taxonomía y distribución nativa**, no cuidados. Igual que GRIN: no responde a ninguna
pregunta que este proyecto tenga, y el papel de resolución de nombres ya lo cubre GBIF —que a
su vez ingiere WCVP—. Descartado.

Nota de precisión que sí importa: la distribución nativa de POWO **sí** es útil de forma
indirecta. Saber que *Olea europaea* es nativa de la cuenca mediterránea y *Picea abies* del
norte y centro de Europa es la mejor heurística barata de «¿esta planta está en su sitio en
mi jardín?». Pero eso ya lo da GBIF sin fuente adicional.

---

## 7. RHS — hardiness ratings y Plant Finder

### 7.1 La escala H1a–H7

Es el sistema del Royal Horticultural Society, introducido en 2012, y su virtud es que
**se define por temperatura absoluta, no por geografía**
([RHS hardiness ratings](https://www.rhs.org.uk/plants/trials-awards/award-of-garden-merit/rhs-hardiness-rating)):

| Rating | Temperatura | Definición |
|---|---|---|
| H1a | > 15 °C | invernadero cálido todo el año |
| H1b | 10–15 °C | fuera sólo en verano, mejor como planta de interior |
| H1c | 5–10 °C | fuera en verano en casi todo el Reino Unido |
| H2 | 1–5 °C | «Tolerant of low temperatures but will not survive being frozen» |
| H3 | −5 a 1 °C | rústica en costa y zonas suaves salvo inviernos duros |
| H4 | −10 a −5 °C | «Hardy through most of the UK apart from inland valleys» |
| H5 | −15 a −10 °C | rústica en casi todo el RU incluso en inviernos severos |
| H6 | −20 a −15 °C | «Hardy throughout the UK and northern Europe» |
| H7 | < −20 °C | «Hardy in the severest European continental climates» |

**Esto es más transferible a España que las zonas USDA**, y por una razón concreta: un rating
H4 dice «aguanta hasta −10/−5 °C», y eso es una afirmación sobre la planta que **no depende de
dónde estés**. Las zonas USDA son también umbrales de temperatura, pero llegan al usuario
envueltas en un mapa de Estados Unidos, y esa envoltura es la que no viaja.

### 7.2 Accesibilidad: nula

**No hay descarga.** Ni el RHS Plant Finder ni las fichas de plantas ofrecen fichero, API
pública ni términos de reutilización; la propia página de ratings no menciona descarga ni
licencia. Extraer los ratings requeriría *scraping* de un sitio con copyright del RHS, lo que
está fuera de lo que este proyecto debe hacer.

**Uso realista**: la **escala** es de dominio conceptual y se puede usar como vocabulario
interno de la app (mapear a H1a–H7 los umbrales que vengan de otras fuentes). Los **valores por
especie del RHS, no**.

---

## 8. TRY y LEDA

**TRY Plant Trait Database** (Max Planck Institute for Biogeochemistry): desde la versión 5 los
datos son **CC-BY** por defecto, con la salvedad de que «access to else unpublished data may be
restricted temporarily on request»
([TRY, MPI-BGC](https://www.bgc-jena.mpg.de/en/try-datenbank-fifth-version)). El acceso es por
**solicitud con propuesta** ([formulario](https://www.try-db.org/TryWeb/Prop0.php)), no
descarga directa.

**LEDA Traitbase**: ~3.000 especies del noroeste de Europa, 26 rasgos, acceso libre en
[leda-traitbase.org](https://www.leda-traitbase.org/). Última publicación de referencia, 2008.

**Ambas quedan descartadas por el mismo motivo, y conviene decirlo con claridad**: sus rasgos
son **ecológicos funcionales** —área foliar específica, masa de semilla, altura del dosel,
capacidad de dispersión, persistencia del banco de semillas—. Sirven para modelar ecosistemas.
**No contienen una sola instrucción de cuidado.** Que un rosal tenga tal SLA no le dice a nadie
cuándo podarlo. Aparecen en la lista de candidatos por asociación de ideas («base de datos de
plantas»), no porque respondan a la pregunta.

---

## 9. Rusticidad aplicable a España peninsular

Esta es la pregunta (a), y la respuesta corta es incómoda: **el mapeo honesto no es un mapeo,
es un cálculo.**

### 9.1 Lo que hay, y por qué no vale

**USDA Plant Hardiness Zone Map.** Su definición, literal, es
«the average annual extreme minimum winter temperature, displayed as 10-degree F zones and
5-degree F half zones» ([USDA PHZM](https://planthardiness.ars.usda.gov/)), edición de 2023 que
sustituye a la de 2012. **Cubre sólo Estados Unidos.** No hay mapa USDA de España porque el
USDA no cartografía España.

**No existe mapa oficial español de rusticidad.** Se ha buscado en AEMET y en el Ministerio y
no aparece. Lo que AEMET sí publica es otra cosa, y es mejor de lo que parece: el
[Atlas Climático Ibérico](https://www.aemet.es/documentos/es/conocermas/recursos_en_linea/publicaciones_y_estudios/publicaciones/Atlas-climatologico/Atlas.pdf)
(conjunto con el servicio meteorológico portugués) y los
[Mapas climáticos de España 1981-2010 y ETo 1996-2016](https://www.aemet.es/documentos/es/conocermas/recursos_en_linea/publicaciones_y_estudios/publicaciones/MapasclimaticosdeEspana19812010/MapasclimaticosdeEspana19812010.pdf),
que incluyen **clasificación de Köppen-Geiger** y **temperatura mínima media del mes más frío**
georreferenciadas. Es climatología oficial, pero **no es rusticidad**: la mínima *media* del mes
más frío no es la mínima *extrema*, y la diferencia es precisamente lo que mata plantas.

Los mapas de «zonas de rusticidad de España» que circulan por internet son de webs comerciales
de jardinería y de agregadores como `plantmaps.com`. **Ninguno declara metodología, periodo de
datos ni fuente.** No son citables y no deben usarse.

**Zonas Sunset** (las que WUCOLS pone en `culturalInformation`, y SelecTree en `sunset_zone`)
son del oeste de Estados Unidos y **no tienen ninguna traducción a España**. Ignorarlas.

**Rivas-Martínez.** Existe un equivalente español genuino y con más fundamento que las zonas
USDA: el *Worldwide Bioclimatic Classification System*
(Rivas-Martínez, Rivas-Sáenz & Penas, 2011,
[globalbioclimatics.org](http://www.globalbioclimatics.org)), con sus **termotipos**
(termomediterráneo, mesomediterráneo, supramediterráneo…) calculados sobre índices térmicos.
Es la clasificación estándar en la fitosociología ibérica. **Pero no sirve para esta app**: no
existe ninguna base de datos de ornamentales que etiquete especies por termotipo. Un dato de
sitio sin dato de planta con el que compararlo no decide nada.

### 9.2 Lo que sí se puede hacer, y está verificado

La zona USDA es **una fórmula, no un mapa**. Si la definición es «media de la mínima extrema
anual», y el proyecto ya consume Open-Meteo, entonces se calcula.

Verificado el 2026-08-13 contra el archivo histórico de Open-Meteo (ERA5), Madrid centro,
30 años completos 1995–2024:

```
GET https://archive-api.open-meteo.com/v1/archive
    ?latitude=40.4&longitude=-3.7
    &start_date=1995-01-01&end_date=2024-12-31
    &daily=temperature_2m_min&timezone=Europe/Madrid

→ mínima extrema por año: −2.3, −3.1, −2.2, −2.6, −3.7, …
→ media de las 30 mínimas extremas anuales = −3.64 °C
→ mínima absoluta del periodo           = −11.1 °C
```

−3,64 °C cae en **USDA 9b** (−3,9 a −1,1 °C). Y en la escala RHS, la planta necesitaría al
menos **H3** (−5 a 1 °C) para un año normal.

**Pero la mínima absoluta de esos 30 años fue −11,1 °C**, que es territorio de **zona 8a**.
Ahí está todo el problema de las zonas de rusticidad condensado en dos números: una planta
elegida para 9b muere en el año malo, y el año malo llegó al menos una vez en 30.

### 9.3 Qué error introduce esto — sé escéptico

Tres fuentes de error, en orden de tamaño:

1. **La media oculta la cola.** 7,5 °C separan la media de las mínimas extremas de la mínima
   absoluta en la serie de Madrid. Una zona de rusticidad es una **apuesta al año medio**, no
   una garantía. La app **no debe decir «tu planta es rústica aquí»**; debe decir «en un año
   normal aguanta; en el peor de los últimos 30 años no».
2. **ERA5 suaviza extremos.** El reanálisis es una malla de decenas de km que promedia sobre
   celdas; **las heladas de irradiación en vaguadas y jardines cerrados son locales y más
   frías** que la celda. El sesgo va en la dirección peligrosa: optimista. Contra esto, la
   defensa correcta no es sofisticar el clima sino ser conservador: **restar un margen (2–3 °C)
   antes de decidir**, y avisar por pronóstico de helada, que es lo que el modelo agronómico ya
   contempla.
3. **La transferencia California → España, que es un problema distinto y peor.** WUCOLS **no
   es rusticidad**, es consumo de agua, y su regionalización responde a la demanda evaporativa
   de seis comarcas californianas. Extrapolar `ks` a España se apoya en que ambos son climas
   mediterráneos —correcto en primer orden, y ya declarado como supuesto no validado en
   `modelo-agronomico-riego.md` §6.9.2—. **Pero la regionalización no se puede trasladar**:
   no hay ninguna correspondencia establecida entre «South Inland» y, pongamos, La Mancha. Lo
   único defendible es elegir **una** región californiana como referencia y declararlo.
   Recomendación: **Central Valley (región 2)** para interior peninsular y **South Coastal
   (región 3)** para litoral mediterráneo, con dos razones — es donde WUCOLS tiene menos
   valores `U` y `NA`, y son las de régimen térmico más parecido. **Es una elección del
   proyecto, no un hecho de la fuente.**

### 9.4 Recomendación sobre rusticidad

- **Dato de sitio**: calcularlo desde Open-Meteo archive con la definición del USDA (§9.2).
  Una vez, al dar de alta la vivienda. Guardar los 30 valores, no sólo la media, para poder
  decir «el peor año fue −11 °C».
- **Dato de planta**: `usda_zone` de **SelecTree** para árboles; rango de rusticidad de
  **PFAF** para el resto; `Temperature, Minimum (°F)` de **USDA PLANTS** cuando exista, que es
  el mejor de los tres por ser un umbral y no una zona.
- **Vocabulario de la app**: la escala **RHS H1a–H7**, porque está definida en grados y
  admite traducir a ella cualquiera de los tres orígenes anteriores.
- **Lo que no se hace**: no se importa ningún «mapa de rusticidad de España», porque no
  existe ninguno con procedencia declarada.

---

## 10. Tareas no-hídricas: poda, abonado, trasplante, protección

Esta es la pregunta (b), y en este inventario es **la mayor parte del valor**: 3 de 4 plantas
están arraigadas y no se riegan.

### 10.1 El hallazgo central

**Ninguna de las fuentes estructuradas de §1–§8 contiene ventanas de poda o abonado.**
Ni WUCOLS, ni SelecTree, ni PFAF, ni USDA PLANTS, ni GRIN, ni WCVP, ni TRY, ni LEDA. Ni un
campo. Comprobado ficha a ficha en las cuatro que tienen datos de cultivo.

Esto no es una laguna del muestreo: es estructural. Las bases de datos de plantas se construyen
para **seleccionar** especies (¿cabe aquí?, ¿gasta poca agua?, ¿aguanta el frío?), no para
**mantenerlas**. El calendario de mantenimiento vive en literatura de extensión agraria, en PDF,
por cultivo, y en prosa.

### 10.2 Olivo — sí hay fuente española, buena y citable

**Poda: IFAPA (Junta de Andalucía), vía SERVIFAPA.** Es el servicio de extensión agraria
español con más material publicado sobre olivar. Serie completa y descargable:

- [Introducción a la Poda del Olivo](https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/c8d82392-142d-499b-9764-b95291db93f2)
- [Poda de Formación](https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/4e793d32-2aa3-47af-ae3d-9b41663f3c2b) (2014)
- [Poda de Producción](https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/0e496cd1-2448-4898-a1c7-998a71d87a6e) (2018)
- [Poda de Renovación](https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/07640e63-0216-48fb-a42c-dcf576290be9)

Descarga directa añadiendo `/download` al identificador; verificado (PDF, 2,2 MB, 13 páginas).

**Licencia, verbatim del propio PDF** — y es buena noticia:

> «Este documento está bajo Licencia Creative Commons. Reconocimiento-No comercial-Sin obra
> derivada. http://creativecommons.org/licenses/by-nc-nd/3.0/es»
> (Pérez, D., Vega, V., Hidalgo, J., Hidalgo, J. C. y Leyva, A., *Poda del Olivo: Poda de
> Producción*, IFAPA, Córdoba, febrero de 2018)

**CC BY-NC-ND 3.0 ES**: uso personal y copia local, sí, con atribución. Sin obra derivada
significa que no se puede publicar una versión modificada; citar y resumir para consumo propio,
sí.

**Aviso de aplicabilidad.** El contenido es agronomía de plantación comercial: habla de
volumen de copa óptimo «entre los 10.000 y 12.000 m³/ha» en riego y «6.000 y 8.000 m³/ha» en
secano, de intercepción de radiación y de rendimiento graso. **Para el olivo ornamental de un
jardín, el objetivo no es el rendimiento graso.** El principio transferible es el de iluminación
de la copa; las cifras por hectárea, no.

**Abonado: MAPA, *Guía práctica de la fertilización racional de los cultivos en España*.**
Publicación del Ministerio, con capítulo 27 dedicado al olivar
([Parte I](https://www.mapa.gob.es/dam/mapa/contenido/agricultura/publicaciones/01_fertilizacion-baja-.pdf),
[Parte II](https://www.lgseeds.es/media/guia-practica-fertilizacion-cultivos-ii.pdf)).
Verificado el 2026-08-13 extrayendo el texto del PDF; **da ventanas temporales explícitas**:

> «El fósforo y el potasio pueden incorporarse **en otoño**, si se aplican por separado, o
> **después de la recolección** si se aportan junto al nitrógeno. Cuando se aplican los tres
> elementos juntos, mediante un abono complejo, o se aplican sólo abonos nitrogenados es
> preferible hacer la aplicación **inmediatamente después de la recolección**, para aprovechar
> todas las lluvias primaverales…»

Y sobre abonado foliar, que es lo más aplicable a un árbol de jardín:

> «El olivo tiene una hoja que admite muy bien el abonado foliar; por tanto, **en secano** y
> siempre que sea posible, se recomienda aportar los fertilizantes vía foliar. […] Para la
> aportación de nitrógeno se puede utilizar urea cristalina… es preferible rebajar la
> concentración… y hacer **dos aplicaciones al 2,5 %. La aplicación debe hacerse en
> primavera**. Respecto al potasio, cuando se aplica nitrato potásico las concentraciones
> oscilan entre 1,25 % y 2,5 % y **preferiblemente debe utilizarse en el otoño**.»

Con la ventana fenológica de absorción foliar: las aplicaciones aprovechan las hojas jóvenes,
«lo que sucede **de abril a julio**».

**Esto es exactamente lo que la app necesita**: N foliar en primavera, K foliar en otoño,
ventana de hoja joven abril–julio, aporte al suelo tras la recolección. Citable, oficial,
español, y con la salvedad de que las dosis (tabla 27.7, en kg/ha de NPK 20-8-14) hay que
ignorarlas para un árbol suelto.

**Normativa adicional, muy actual.** El
[Reglamento Específico de Producción Integrada de Olivar](https://www.juntadeandalucia.es/boja/2026/153/4.html)
fue reaprobado por Orden de 30 de julio de 2026 (BOJA nº 153, 10/08/2026), sustituyendo al de
2008, **con entrada en vigor el 1 de enero de 2027**. Impone plan de abonado obligatorio y
análisis foliar anual en julio. Es normativa para explotaciones profesionales —no obliga a un
jardín— pero fija el estándar técnico español vigente y es la referencia más actualizada que
existe.

**Plagas y sanidad**: la
[Guía de gestión integrada de plagas: olivar](https://www.mapa.gob.es/dam/mapa/contenido/agricultura/temas/medios-de-produccion/productos-fitosanitarios/uso-sostenible-de-productos-fitosanitarios/guias-de-gestion-integrada-de-plagas/guiaolivar--2-.pdf)
del MAPA (2014, ISBN 978-84-491-1410-6) es descarga libre desde el Ministerio.

### 10.3 Rosal — no hay fuente española citable. Dígase claro

Se ha buscado ventana de poda de rosal en servicios de extensión agraria españoles, IFAPA,
IVIA, INIA/CSIC, universidades y publicaciones del Ministerio. **No se ha encontrado nada.**
Lo que devuelve la búsqueda son blogs de jardinería, tiendas de plantas y foros. Ninguno es
fuente primaria.

La razón es evidente en retrospectiva: **el rosal no es un cultivo**. Los servicios de extensión
agraria españoles publican sobre lo que da renta —olivar, vid, cítricos, hortícolas—, y la
jardinería ornamental doméstica no está en su mandato.

Las tres salidas posibles, todas imperfectas:

1. **RHS**, que sí tiene grupos de poda de rosales publicados y con autoridad, pero es
   **clima británico** y no es descargable ni reutilizable (§7.2). Trasladar «finales de
   invierno antes de la brotación» a España requiere desplazar la ventana según la zona, y ese
   desplazamiento **lo puede calcular la propia app** desde el clima real: es el mismo dato de
   Open-Meteo que ya se consume.
2. **NTJ — Normas Tecnológicas de Jardinería y Paisajismo**, de la Fundació de la Jardineria i
   el Paisatge ([ntjdejardineria.org](https://www.ntjdejardineria.org/)). Es **la** norma
   técnica española de jardinería ornamental, con series dedicadas a poda y a arbustos
   (NTJ 07F). Pero es **de pago y propietaria**, sin descarga libre ni licencia de
   reutilización. Citable como norma, no importable como dataset.
3. **Aceptar que el LLM lo redacte** sobre la regla fenológica genérica, declarándolo como tal.

### 10.4 Coníferas ornamentales — igual de vacío

Mismo resultado y peor: ni siquiera hay un RHS *pruning group* que aporte gran cosa, porque la
respuesta agronómica correcta para casi todas las coníferas es «**no se podan**» — no rebrotan
de madera vieja. Eso es un hecho fisiológico robusto, no una ventana de calendario, y es
probablemente lo más útil que la app puede decirle al usuario sobre su abeto.

### 10.5 Balance de la pregunta (b)

| Planta | Poda | Abonado | Fuente |
|---|---|---|---|
| Olivo | **sí, citable** | **sí, con ventanas** | IFAPA (CC BY-NC-ND) + MAPA |
| Rosal | **no** | **no** | — (RHS, clima ajeno, no reutilizable) |
| Coníferas | **no** (y probablemente no haga falta) | **no** | — |
| Aloe | **no** | **no** | — |

**Una de cuatro.** Y es la que menos falta hacía, porque un olivo arraigado en un jardín
tolera perfectamente que no se le toque.

---

## 11. Recomendación argumentada

### 11.1 Fuente primaria: WUCOLS V, y sólo para el agua

Es la única fuente que cumple las cinco condiciones a la vez: descargable de una sola petición,
gratuita, viva y respaldada por una agencia estatal, con salida numérica accionable, y
**cubriendo las 4 plantas de esta casa**. Ninguna otra lo hace.

Diseño concreto:

- **Importación puntual, no dependencia en vivo.** Descargar el JSON, versionarlo por la fecha
  del `cachedBlobUrl`, y guardarlo en el repo o en el almacenamiento local de la PWA. El
  endpoint del blob es indocumentado; tratarlo como frágil.
- **Región de referencia declarada**: Central Valley (2) para interior, South Coastal (3) para
  litoral. Elección del proyecto, no hecho de la fuente.
- **Usar los rangos, no los puntos medios inventados**: corregir `ks` a los valores de §1.1.
- **Resolver por GBIF con ascenso a género**: especie → si falla, `parentKey` → género.
- **`U` y `NA` desactivan el balance**, no colapsan a un número.
- **No redistribuir** mientras la licencia sea «All rights reserved».

### 11.2 Respaldo: PFAF para rusticidad y suelo, SelecTree para árboles

Cubren el hueco exacto que WUCOLS deja: **cuánto frío aguanta** y **cuánta sombra tolera**.

- **PFAF** (50 $, CC BY-NC-SA, CSV): cubre olivo, aloe y las cuatro coníferas con rango USDA.
  Es la compra más justificada del proyecto: 50 € que resuelven de golpe el campo de rusticidad
  para todo lo que no sea un cultivar.
- **SelecTree** (API abierta, 2.087 árboles): mejor granularidad que nadie para árboles —zona
  USDA, resistencia al viento, potencial de daño radicular, velocidad de crecimiento—, pero
  sólo árboles. Útil para el olivo y para el abeto cuando se identifique.
- **USDA PLANTS** como tercer recurso de dominio público cuando exista ficha, por su
  `Temperature, Minimum (°F)` y su `Root Depth`.

**Regla dura**: cada campo de la ficha de especie tiene **una sola fuente**, y la registra.
Nunca mezclar el `water_use` de SelecTree con el de WUCOLS en el mismo campo — escalas y
metodologías distintas (§2.3).

### 11.3 Qué se hace con lo que ninguna cubre

Tres huecos, tres tratamientos distintos:

1. **El cultivar de rosal.** WUCOLS da la moda de 74 cultivares (`Moderate`) y su dispersión.
   Se usa eso, y la app **muestra la dispersión**: «68 de 74 rosales evaluados son de consumo
   moderado». Honestidad barata y mejor que un número solo.
2. **El abeto sin identificar.** No se inventa nada. La ficha queda en `Unidentified` y la app
   muestra **el abanico**: `ks` entre `Low` y `Moderate` según cuál sea, y rusticidad entre
   zona 3 y zona 9. Eso convierte el hueco en un argumento para identificarlo, que es
   justamente lo que quiere el issue [#15](https://github.com/Joosle/Plantas/issues/15).
3. **Todo lo no-hídrico.** Se rellena **a mano**, planta a planta, citando el PDF de origen, y
   el LLM redacta el consejo semanal sobre esos hechos. Para el olivo hay IFAPA y MAPA. Para
   las otras tres, se declara que la ventana procede de regla fenológica genérica y no de
   fuente citable.

### 11.4 La conclusión que reencuadra el proyecto

**Ninguna fuente estructurada basta.** Hay que decirlo sin rodeos:

Existe una buena fuente para el agua (WUCOLS) y una aceptable para la rusticidad
(PFAF + SelecTree). **No existe ninguna fuente estructurada, en ningún idioma, para el
calendario de tareas de mantenimiento de plantas ornamentales.** Y en este inventario —3 de 4
plantas arraigadas, sin riego— **el calendario de tareas es el producto**.

Lo cual tiene una consecuencia liberadora más que deprimente. Con cuatro plantas, la ficha de
cuidados **no es un problema de datos, es un problema de redacción**: son cuatro fichas escritas
a mano con sus citas. La ingeniería de importación de WUCOLS —endpoints, joins, normalización de
nombres, ascenso a género— resuelve el `ks` de **una** planta que hoy se riega. Merece la pena
hacerla porque es barata y porque escala si el inventario crece, pero **no es el camino crítico
del valor**.

El camino crítico es: identificar el abeto, escribir cuatro fichas de tareas con fuente, y
calcular la rusticidad del sitio desde Open-Meteo (§9.2), que es una función de veinte líneas y
resuelve las alertas de helada para las cuatro plantas a la vez.

---

## Fuentes

**Primarias — datos de agua por especie**
- [WUCOLS V — California Center for Urban Horticulture, UC Davis](https://ccuh.ucdavis.edu/wucols) · [portal](https://wucols.ucdavis.edu/) · [antecedentes del proyecto y copyright](https://wucols.ucdavis.edu/project-background) · [instrucciones de exportación](https://wucols.ucdavis.edu/plant-search-instructions) · [buscador](https://wucols-frontend.ucdavis.edu/)
- [WUCOLS — puntero al snapshot descargable](https://wucols.blob.core.windows.net/wucols-export/meta/wucols-data.json) (endpoint interno, indocumentado; snapshot verificado 2025-06-27, 4.103 taxones)
- [SelecTree — Urban Forest Ecosystems Institute, Cal Poly](https://selectree.calpoly.edu/) · API: `/api/tree/search-by-name`, `/api/tree/detail/{id}` (2.087 árboles, verificado 2026-08-13)

**Primarias — atributos de planta**
- [USDA PLANTS Database — NRCS](https://plants.sc.egov.usda.gov/) · API `plantsservices.sc.egov.usda.gov/api` · [descargas](https://plants.sc.egov.usda.gov/home/downloads) · [política de dominio público del USDA](https://www.usda.gov/about-usda/policies-and-links)
- [Plants For A Future (PFAF)](https://pfaf.org/) · [base descargable, 7.400 especies templadas](https://plantsforafuture.com/temperate-plant-database/) · [tienda y precios](https://pfaf.org/user/shop.aspx)
- [USDA-ARS GRIN](https://www.ars-grin.gov/) · [GRIN Taxonomy en GBIF](https://www.gbif.org/dataset/66dd0960-2d7d-46ee-a491-87b9adcfe7b1)
- [Kew — About WCVP](https://powo.science.kew.org/about-wcvp) · [descarga SFTP](http://sftp.kew.org/pub/data-repositories/WCVP/) · [WCVP en GBIF](https://www.gbif.org/dataset/f382f0ce-323a-4091-bb9f-add557f3a9a2) · [Govaerts et al., *Scientific Data* 2021](https://www.nature.com/articles/s41597-021-00997-6)
- [TRY Plant Trait Database — MPI-BGC](https://www.bgc-jena.mpg.de/en/try-datenbank-fifth-version) · [solicitud de datos](https://www.try-db.org/TryWeb/Prop0.php)
- [LEDA Traitbase](https://www.leda-traitbase.org/)
- [Arbolapp — Real Jardín Botánico, CSIC](https://www.arbolapp.es/) (143 especies de árboles silvestres ibéricos; descripción, ecología y distribución, **sin datos de cultivo**)

**Primarias — rusticidad**
- [USDA Plant Hardiness Zone Map](https://planthardiness.ars.usda.gov/) (edición 2023; definición de zona; **sólo EE. UU.**)
- [RHS hardiness ratings H1a–H7](https://www.rhs.org.uk/plants/trials-awards/award-of-garden-merit/rhs-hardiness-rating) (introducidas en 2012; sin descarga ni licencia de reutilización)
- [AEMET — Atlas Climático Ibérico](https://www.aemet.es/documentos/es/conocermas/recursos_en_linea/publicaciones_y_estudios/publicaciones/Atlas-climatologico/Atlas.pdf) · [Mapas climáticos de España 1981-2010 y ETo 1996-2016](https://www.aemet.es/documentos/es/conocermas/recursos_en_linea/publicaciones_y_estudios/publicaciones/MapasclimaticosdeEspana19812010/MapasclimaticosdeEspana19812010.pdf) (Köppen-Geiger y mínima media del mes más frío; **no es rusticidad**)
- [Rivas-Martínez, Rivas-Sáenz & Penas (2011), *Worldwide Bioclimatic Classification System*](http://www.globalbioclimatics.org) (termotipos ibéricos; sin base de ornamentales que los use)
- [Open-Meteo Historical Weather API (ERA5)](https://open-meteo.com/en/docs/historical-weather-api) — usado para **calcular** la zona: Madrid 1995–2024, media de mínimas extremas anuales = −3,64 °C (USDA 9b), mínima absoluta −11,1 °C

**Primarias — tareas no-hídricas en clima español**
- IFAPA / SERVIFAPA (Junta de Andalucía), serie de poda del olivo — [Introducción](https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/c8d82392-142d-499b-9764-b95291db93f2) · [Formación](https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/4e793d32-2aa3-47af-ae3d-9b41663f3c2b) · [Producción](https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/0e496cd1-2448-4898-a1c7-998a71d87a6e) · [Renovación](https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/07640e63-0216-48fb-a42c-dcf576290be9) — licencia **CC BY-NC-ND 3.0 ES** declarada en el propio PDF
- MAPA — *Guía práctica de la fertilización racional de los cultivos en España*, cap. 27 (olivar): [Parte I](https://www.mapa.gob.es/dam/mapa/contenido/agricultura/publicaciones/01_fertilizacion-baja-.pdf) · [Parte II](https://www.lgseeds.es/media/guia-practica-fertilizacion-cultivos-ii.pdf)
- MAPA — [Guía de gestión integrada de plagas: olivar](https://www.mapa.gob.es/dam/mapa/contenido/agricultura/temas/medios-de-produccion/productos-fitosanitarios/uso-sostenible-de-productos-fitosanitarios/guias-de-gestion-integrada-de-plagas/guiaolivar--2-.pdf) (2014) · [índice de guías](https://www.mapa.gob.es/es/agricultura/temas/sanidad-vegetal/productos-fitosanitarios/guias-gestion-plagas)
- [Orden de 30 de julio de 2026, Reglamento Específico de Producción Integrada de Olivar (BOJA nº 153, 10/08/2026)](https://www.juntadeandalucia.es/boja/2026/153/4.html) (vigor 1/1/2027)
- [Normas Tecnológicas de Jardinería y Paisajismo (NTJ)](https://www.ntjdejardineria.org/) — Fundació de la Jardineria i el Paisatge; norma española de jardinería ornamental, **de pago, sin licencia de reutilización**

**No verificadas / descartadas por falta de procedencia**
- Mapas de «zonas de rusticidad de España» de webs comerciales y agregadores (`plantmaps.com` y similares): sin metodología, periodo ni fuente declarados. **No usar.**
- Fuentes de poda de rosal en español: sólo blogs comerciales. Ninguna primaria localizada.
- Campos `memo` y `schoolyard_use_notes` de SelecTree: prosa de origen no verificable; no tratar como hecho citable.
