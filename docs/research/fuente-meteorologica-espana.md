# Fuente meteorológica para España peninsular

Responde a: ¿qué servicio meteorológico usamos para alimentar el motor de riego/cuidado (histórico reciente + pronóstico + ET0) en una ubicación doméstica de España peninsular, con coste cero y uso personal?

---

## Recomendación

**Fuente primaria: Open-Meteo.** Es la única candidata que, en una sola llamada y sin clave, entrega **exactamente el conjunto de variables que el motor necesita** —Tmáx, Tmín, precipitación acumulada, humedad relativa, viento, radiación solar y **ET0 FAO-56 ya calculada**— tanto para los **días pasados** como para el **pronóstico**, en la misma rejilla y con la misma unidad. Concretamente `et0_fao_evapotranspiration` está documentada como variable diaria («Daily sum of ET₀ Reference Evapotranspiration») y horaria en el Forecast API ([Open-Meteo, docs](https://open-meteo.com/en/docs)) y también en el Historical Forecast API ([Open-Meteo, historical-forecast-api](https://open-meteo.com/en/docs/historical-forecast-api)) y en el Historical Weather API ([Open-Meteo, historical-weather-api](https://open-meteo.com/en/docs/historical-weather-api)).

**Respaldo / segunda opinión: AEMET OpenData**, para dos cosas concretas y sólo para esas:
1. **Avisos oficiales de fenómenos adversos** (helada, ola de calor) — son la autoridad legal en España y se emiten «any time an adverse weather event is issued» ([datos.gob.es, high-value meteorological datasets](https://datos.gob.es/en/blog/high-value-meteorological-datasets)).
2. **Observación real de estación** (últimas ~12–24 h) como comprobación de que no ha llovido de verdad, cuando la rejilla y la realidad discrepan.

AEMET **no sirve** como fuente de histórico reciente para el motor: sus valores climatológicos diarios se «normally update once a day **with a delay of four days**» ([datos.gob.es](https://datos.gob.es/en/blog/high-value-meteorological-datasets)), lo que deja un agujero justo en la ventana que importa (los últimos 7 días). Trampa **confirmada**.

**Descartados como fuente del motor:** Meteostat (histórico, no pronóstico, y sin ET0), Copernicus/CDS ERA5 directo (misma latencia de ~5 días y un flujo de descarga asíncrono desproporcionado para esto), SIAR (ET0 oficial agronómica excelente, pero sin API pública documentada; útil sólo como calibración manual puntual).

### Cadencia de consulta propuesta

| Cuándo | Llamada | Qué se pide | Caché |
|---|---|---|---|
| 1×/día, ~06:15 hora local | `GET https://api.open-meteo.com/v1/forecast` | `past_days=10`, `forecast_days=10`, daily = `temperature_2m_max,temperature_2m_min,precipitation_sum,et0_fao_evapotranspiration,shortwave_radiation_sum,relative_humidity_2m_mean,wind_speed_10m_max` + timezone `Europe/Madrid` | Persistir **cada día como fila propia** en BD local; el pasado se congela, el futuro se sobrescribe |
| 1×/día (opcional, misma tanda) | AEMET `avisos` para la provincia | avisos vigentes | 6 h |
| Bajo demanda / máx. 2×/día | AEMET observación de la estación más cercana | precipitación y T de las últimas horas | 1 h |

Eso son **1–3 llamadas al día**, contra un límite de «less than 10'000 API calls per day» ([Open-Meteo, terms](https://open-meteo.com/en/terms)). Margen de sobra: incluso reintentando cada 10 minutos todo un día no se roza el techo.

Decisiones concretas:
- **Una sola llamada** cubre pasado y futuro: `past_days` sobre el Forecast API evita tener que hablar con el archivo ERA5. Trampa **confirmada**: `past_days` admite hasta **92** días ([Open-Meteo, docs](https://open-meteo.com/en/docs)), y el Historical Forecast API existe además como archivo dedicado del pronóstico de alta resolución ([Open-Meteo](https://open-meteo.com/en/docs/historical-forecast-api)). Ninguno de los dos sufre el retraso de 5 días de ERA5.
- **Ventana de pasado 10 días** (no 7): da colchón si la app pasa unos días sin abrirse y permite recalcular el balance hídrico desde cero de forma idempotente.
- **Ventana de pronóstico 10 días**: las alertas de helada y ola de calor pierden valor más allá de 5–7 días, pero el coste marginal de pedir 10 es cero.
- **Nunca llamar en caliente desde la UI.** El plan semanal se genera desde el snapshot cacheado. Si la llamada diaria falla, el motor trabaja con el snapshot de ayer y lo marca como obsoleto.
- **Plantas de interior**: no consumen ninguna de estas variables; su planificación no debe bloquearse nunca por un fallo meteorológico.

---

## Candidato 1 — Open-Meteo

### Histórico reciente y latencia

Tres caminos, y el hueco entre el archivo y el pronóstico **sí está cubierto**:

- **Forecast API con `past_days`** (`https://api.open-meteo.com/v1/forecast`): parámetro `past_days`, valor máximo **92**; `forecast_days` máximo **16** ([Open-Meteo, docs](https://open-meteo.com/en/docs)). Sin latencia apreciable: son las salidas del modelo ya emitidas, cosidas al pronóstico vivo.
- **Historical Forecast API** (`https://historical-forecast-api.open-meteo.com/v1/forecast`): «Archived high-resolution weather forecasts», construido cosiendo ejecuciones sucesivas del modelo en series horarias continuas; disponible desde 2021–2022 según modelo (ECMWF IFS HRES desde enero de 2017), con actualizaciones cada 1–6 h según modelo ([Open-Meteo, historical-forecast-api](https://open-meteo.com/en/docs/historical-forecast-api)).
- **Historical Weather API / archivo ERA5** (`https://archive-api.open-meteo.com/v1/archive`): ERA5 a 0.25° (~25 km) y ERA5-Land a 0.1° (~11 km), **actualizados a diario con 5 días de retraso**; el ECMWF IFS incorporado desde 2017 (9 km) se actualiza cada 6 h sin retraso ([Open-Meteo, historical-weather-api](https://open-meteo.com/en/docs/historical-weather-api)).

Para el motor: **usar `past_days` en el Forecast API**, no el archivo. El archivo llega tarde para «¿regué de más esta semana?».

El **Climate API** (CMIP6 downscaled a 10 km, 1950–2050) **no vale** aquí: la propia doc avisa de que los datos de años recientes «should not be mistaken for actual measurements, as it serves the purpose of model validation rather than showing actual past weather» ([Open-Meteo, climate-api](https://open-meteo.com/en/docs/climate-api)).

### ET0

**Directa, no hay que calcularla.** `et0_fao_evapotranspiration` aparece como variable **diaria** («Daily sum of ET₀ Reference Evapotranspiration», en mm) y horaria en el Forecast API ([Open-Meteo, docs](https://open-meteo.com/en/docs)), y como variable diaria disponible tanto en el Historical Forecast API ([fuente](https://open-meteo.com/en/docs/historical-forecast-api)) como en el Historical Weather API ([fuente](https://open-meteo.com/en/docs/historical-weather-api)).

Aun así, **todos los insumos de Penman-Monteith FAO-56 están expuestos** por si queremos recalcular o auditar: `shortwave_radiation_sum` (MJ/m², «The sum of solar radiation on a given day»), `temperature_2m_max`/`min`, `relative_humidity_2m_mean`, `wind_speed_10m_max` ([Open-Meteo, docs](https://open-meteo.com/en/docs)). Ojo: el viento se publica a **10 m**, no a 2 m; si alguna vez recalculamos ET0 a mano hay que aplicar la conversión logarítmica de FAO-56.

### Granularidad espacial

Dato de **rejilla**, no de estación. Cobertura europea con «1 - 11 km» de resolución según modelo, y «Best Match» selecciona automáticamente el modelo de mayor resolución aplicable a cada punto ([Open-Meteo, docs](https://open-meteo.com/en/docs)). Modelos que cubren Europa: ICON (DWD) 2–11 km, IFS/AIFS (ECMWF) 9–25 km, AROME (Météo-France) 1–25 km, UKMO 2–10 km ([Open-Meteo, docs](https://open-meteo.com/en/docs)). **AEMET no figura entre las fuentes de Open-Meteo** ([Open-Meteo, ecmwf-api](https://open-meteo.com/en/docs/ecmwf-api)), así que sobre España peninsular lo habitual será ICON-EU / IFS.

Precisión realista en un jardín concreto:
- **Temperatura, humedad, viento, radiación**: buenas. Son campos suaves; un píxel de 7–11 km representa razonablemente la parcela, con sesgo si hay desnivel o efecto urbano.
- **Precipitación**: **la variable débil**. Una tormenta convectiva de verano en la Península puede descargar 20 mm en un pueblo y 0 en el de al lado; una rejilla de 7–11 km suaviza eso. El motor no debe tratar `precipitation_sum` como una medida, sino como una **señal**: umbral generoso para «no riegues» y, si se quiere afinar, contrastar con la observación de estación AEMET o con un pluviómetro casero.

### Cuota

«Less than 10'000 API calls per day», «5'000 per hour», «600 per minute» para el uso gratuito no comercial ([Open-Meteo, terms](https://open-meteo.com/en/terms)). El repositorio oficial confirma que **no hace falta API key** para uso no comercial y que la política es de uso justo: quien supere 10.000 peticiones diarias debe contactar ([Open-Meteo, GitHub](https://github.com/open-meteo/open-meteo)). Con 1–3 llamadas diarias, irrelevante.

### Licencia

- **Datos: CC BY 4.0.** «You accept to the CC-BY 4.0 licence, as specified in the licence conditions» ([Open-Meteo, terms](https://open-meteo.com/en/terms)). Atribución exigida, con el texto recomendado **«Weather data by Open-Meteo.com»** ([Open-Meteo, GitHub](https://github.com/open-meteo/open-meteo)).
- **Uso no comercial**: «Non-commercial use is defined as elaborated by creative commons», e incluye explícitamente sitios privados sin suscripciones ni anuncios y **domótica personal** ([Open-Meteo, terms](https://open-meteo.com/en/terms)). Nuestro caso encaja de lleno.
- **Código: AGPLv3** ([Open-Meteo, GitHub](https://github.com/open-meteo/open-meteo)) — sólo relevante si algún día auto-alojáramos la instancia.
- **Datos subyacentes**: proceden de servicios meteorológicos nacionales (NOAA GFS/HRRR, DWD ICON, Météo-France, ECMWF IFS, JMA, GEM, MET Norway) ([Open-Meteo, GitHub](https://github.com/open-meteo/open-meteo)); ECMWF pasó a open-data el 1 de octubre de 2025 bajo CC-BY 4.0 ([Open-Meteo, ecmwf-api](https://open-meteo.com/en/docs/ecmwf-api)). Para ERA5, la licencia del CDS es «CC-BY licence» ([Copernicus CDS, ERA5](https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels)).

**Acción práctica**: poner «Datos meteorológicos de Open-Meteo.com (CC BY 4.0)» en el pie o en la pantalla de información de la PWA. Es todo lo que hace falta.

### Fiabilidad

**Sin SLA, y lo dicen sin ambages**: «Open-Meteo assumes no responsibility for any inaccuracies or omissions in the data, their availability... The accuracy and completeness of the data products and their uninterrupted provision are not guaranteed by Open-Meteo, and they are provided without any warranty» ([Open-Meteo, terms](https://open-meteo.com/en/terms)). Servicio «as is» / «as available», sin compromiso de disponibilidad.

Cliente sensato: caché persistente en disco del último snapshot correcto, reintento con backoff (3 intentos, 1 min / 5 min / 30 min), y degradación explícita (ver §5).

### Forma de la API

- Endpoint: `GET https://api.open-meteo.com/v1/forecast?latitude=..&longitude=..&daily=..&past_days=10&forecast_days=10&timezone=Europe%2FMadrid`
- **Autenticación: ninguna** para uso no comercial ([Open-Meteo, GitHub](https://github.com/open-meteo/open-meteo)).
- Respuesta: **JSON en una sola petición**, con arrays paralelos (`daily.time[]`, `daily.precipitation_sum[]`, …). Sin paso intermedio ni URLs temporales.

---

## Candidato 2 — AEMET OpenData

> **Nota metodológica**: `opendata.aemet.es` fue **inaccesible desde el entorno de investigación** (conexión rechazada, tanto vía WebFetch como vía `curl`, en varios intentos). Lo que sigue se apoya en fuentes oficiales alcanzables: la web institucional de AEMET (`aemet.es`), el catálogo oficial `datos.gob.es` y el catálogo de datos abiertos del MITECO. **Las rutas exactas de los endpoints deben verificarse contra el Swagger de AEMET (`https://opendata.aemet.es/dist/index.html`) antes de implementar.**

### Histórico reciente y latencia

Aquí está la trampa, y **se confirma**. Hay que distinguir dos productos completamente distintos:

- **Observación convencional (tiempo actual)**: datos horarios de las estaciones, con ventana corta. El catálogo oficial describe «datos horarios de las últimas 12 horas de todas las estaciones meteorológicas», actualización continua, y para las automáticas medidas en tiempo real cada 10 minutos ([MITECO, dataset observación](https://catalogo.datosabiertos.miteco.gob.es/catalogo/dataset/7e58cef7-5cca-4d7e-9b8b-8bf853c9903e); [datos.gob.es, dataset observación](https://datos.gob.es/en/catalogo/e05068001-datos-de-observaciones-medidos-por-estaciones-meteorologicas)). datos.gob.es cifra la actualización de la observación convencional en «twice an hour» ([datos.gob.es](https://datos.gob.es/en/blog/high-value-meteorological-datasets)). Los mensajes SYNOP/TEMP cubren 24 h y los CLIMAT 40 días ([datos.gob.es, dataset observación](https://datos.gob.es/en/catalogo/e05068001-datos-de-observaciones-medidos-por-estaciones-meteorologicas)).
  → **Sirve para "ahora mismo", no para "los últimos 7 días"**, salvo que la app se encargue de sondear a diario y construirse su propio histórico.

- **Valores climatológicos diarios (validados)**: «normally updated **once a day with a delay of four days**» ([datos.gob.es, high-value datasets](https://datos.gob.es/en/blog/high-value-meteorological-datasets)). El conjunto se etiqueta además como «Datos provisionales», con cobertura desde 1900 hasta la actualidad ([MITECO, observaciones validadas](https://catalogo.datosabiertos.miteco.gob.es/catalogo/dataset/2d48e936-95a2-4904-af7e-59d912d002ec)).
  → **Inútil para la ventana de 7 días del motor.** Con 4 días de retraso, más de la mitad de la ventana llega vacía. Trampa confirmada.

También se confirma la distinción **predicción por municipio vs observación de estación**: son familias separadas del catálogo. La predicción municipal a 7 días se publica en XML «al menos actualizadas diariamente», y la predicción horaria por municipio cubre hora a hora hasta 48 h por delante, actualizándose **cuatro veces al día** ([AEMET, catálogo de datos abiertos](https://www.aemet.es/es/datos_abiertos/catalogo); [datos.gob.es, predicción por municipios 7 días](https://datos.gob.es/es/catalogo/ea0022545-prediccion-por-municipios-7-dias)). La predicción municipal es un **producto post-procesado por municipio**, no una medida: excelente para alertas, no comparable con una observación.

### ET0

**No la ofrece.** No aparece ET0 ni evapotranspiración en el catálogo de AEMET OpenData ([AEMET, catálogo](https://www.aemet.es/es/datos_abiertos/catalogo); [datos.gob.es, high-value datasets](https://datos.gob.es/en/blog/high-value-meteorological-datasets)). Habría que calcularla, y aquí falla el insumo clave: la observación convencional publica «hourly variables on precipitation, wind, humidity, pressure, and temperature» ([datos.gob.es](https://datos.gob.es/en/blog/high-value-meteorological-datasets)) — es decir, **sin radiación solar global (Rs)**. AEMET mantiene productos de «Radiación y ozono» dentro de vigilancia del clima, pero la propia página de servicios climatológicos no documenta disponibilidad ni número de estaciones ([AEMET, datos climatológicos](https://www.aemet.es/es/serviciosclimaticos/datosclimatologicos)); en el catálogo abierto lo que sí figura como producto de radiación es el **índice UV previsto**, que no es Rs ([AEMET, catálogo](https://www.aemet.es/es/datos_abiertos/catalogo)).

→ Penman-Monteith FAO-56 completo **no es viable** sólo con AEMET OpenData. Como mucho, una aproximación tipo Hargreaves (sólo Tmáx/Tmín + radiación extraterrestre calculada), que es un método degradado.

### Granularidad espacial

**Estación concreta**, no rejilla, para la parte de observación. AEMET OpenData da acceso a datos climatológicos de «casi un millar» de estaciones. Con ~1.000 estaciones en todo el territorio, la separación típica es de decenas de kilómetros: en una ubicación doméstica, la estación más cercana puede estar a 5 km o a 40 km, y en zonas de montaña la diferencia es grande. Para **precipitación**, una estación real es *más* fiable que una rejilla **si está cerca**, y *menos* fiable si está lejos — con lluvia convectiva, una estación a 30 km no dice nada sobre el jardín.

La predicción por municipio, en cambio, sí está adaptada al municipio y es la unidad natural para alertas.

### Cuota

AEMET aplica limitación por API key y por minuto. No he podido verificar la cifra exacta contra el portal (inaccesible); **hay que confirmarla en el Swagger**. Además, la consulta de climatologías diarias está acotada en rango de fechas por petición (del orden de 10–15 días por llamada, a verificar). Para 1–3 llamadas diarias, cualquiera de esos límites es holgado.

### Licencia

Reutilización **permitida, también comercial**, con atribución obligatoria. El aviso legal de AEMET exige: si se distribuye sin modificar, incluir «© AEMET» o indicar «Información elaborada por la Agencia Estatal de Meteorología»; si se genera un servicio de valor añadido, mencionar «Fuente: AEMET» o «Información elaborada utilizando, entre otras, la obtenida de la Agencia Estatal de Meteorología»; mantener el logo de AEMET en los productos que lo lleven integrado; no desnaturalizar el sentido de la información; y **no indicar, insinuar ni sugerir que AEMET participa, patrocina o apoya la reutilización** ([AEMET, nota legal](https://www.aemet.es/es/nota_legal)). Marco legal: Ley 37/2007 (modificada por Ley 18/2015) y Real Decreto 1495/2011 ([AEMET, nota legal](https://www.aemet.es/es/nota_legal)). En el catálogo, los conjuntos figuran como **CC BY 4.0** ([MITECO, dataset observación](https://catalogo.datosabiertos.miteco.gob.es/catalogo/dataset/7e58cef7-5cca-4d7e-9b8b-8bf853c9903e)), aunque datos.gob.es anticipa una revisión de licencias hacia posiblemente CC BY-SA 4.0 ([datos.gob.es](https://datos.gob.es/en/blog/high-value-meteorological-datasets)).

Nuestra app es personal y no comercial: cumple sin esfuerzo poniendo «Fuente: AEMET» junto a los avisos.

### Fiabilidad

**No publica SLA.** Y hay evidencia empírica en esta misma investigación: `opendata.aemet.es` estuvo **completamente inaccesible** durante todas las pruebas, mientras `aemet.es` respondía con normalidad. Sea bloqueo geográfico, corte o mantenimiento, el mensaje operativo es el mismo: **AEMET no puede ser la ruta crítica del motor**. Úsese como enriquecimiento opcional, con timeout corto (5 s) y fallo silencioso.

### Forma de la API — el patrón de dos pasos

AEMET OpenData es una **API REST con modelo de dos llamadas**, autenticada por `api_key` (se solicita indicando un email en el portal):

1. **Primera llamada** a la ruta del producto, con la `api_key` (en cabecera o query string). La respuesta **no contiene los datos**: es un JSON de control con, entre otros, un campo **`datos`** con una **URL temporal**, un campo `metadatos` con otra URL, y `estado`/`descripcion`.
2. **Segunda llamada** a esa URL `datos`, que devuelve el JSON real del producto (o el fichero, p. ej. `.tar.gz` para mensajes de observación).

Este patrón está confirmado en la descripción operativa del servicio: la primera petición devuelve un JSON con una URL temporal en el campo de datos y una segunda petición a esa URL recupera el conjunto real ([datos.gob.es, dataset observación](https://datos.gob.es/en/catalogo/e05068001-datos-de-observaciones-medidos-por-estaciones-meteorologicas)). Implicaciones para el cliente: **cada consulta cuesta 2 requests**, la URL `datos` **caduca** (no cachearla, cachear el JSON descargado), y hay que gestionar `estado != 200` en el paso 1.

Rutas que usaríamos (verificar en Swagger):

| Producto | Ruta (a verificar) |
|---|---|
| Predicción diaria por municipio (7 d) | `/api/prediccion/especifica/municipio/diaria/{municipio}` |
| Predicción horaria por municipio (48 h) | `/api/prediccion/especifica/municipio/horaria/{municipio}` |
| Observación, todas las estaciones | `/api/observacion/convencional/todas` |
| Observación, una estación | `/api/observacion/convencional/datos/estacion/{idema}` |
| Valores climatológicos diarios | `/api/valores/climatologicos/diarios/datos/fechaini/{ini}/fechafin/{fin}/estacion/{idema}` |
| Avisos de fenómenos adversos | `/api/avisos_cap/ultimoelaborado/area/{area}` |

Base: `https://opendata.aemet.es/opendata`. Formato de respuesta: JSON (algunos productos, imágenes o `tar.gz`).

---

## Candidato 3 — SIAR (MAPA), brevemente

El más interesante desde el punto de vista agronómico y el que menos encaja técnicamente.

- **Qué es**: el Sistema de Información Agroclimática para el Regadío, «una infraestructura que capta, registra y difunde los datos agroclimáticos necesarios para el cálculo de la demanda de agua en las zonas de regadío», con **más de 500 estaciones** (>360 del Ministerio, >160 de las CCAA) y 12 centros regionales más uno nacional ([MAPA, presentación SIAR](https://www.mapa.gob.es/es/desarrollo-rural/temas/gestion-sostenible-regadios/sistema-informacion-agroclimatica-regadio/presentacion)).
- **ET0: sí, y es la buena.** Cada estación mide exactamente los parámetros necesarios para calcular la evapotranspiración de referencia por el «método Penman Monteith (recomendado por FAO)»: temperatura y humedad del aire, velocidad y dirección del viento, radiación solar y precipitación ([MAPA](https://www.mapa.gob.es/es/desarrollo-rural/temas/gestion-sostenible-regadios/sistema-informacion-agroclimatica-regadio/presentacion)). Es decir: **radiación solar medida**, que es justo lo que le falta a AEMET.
- **Granularidad**: estación agroclimática, pensada para zonas regables. En zonas de regadío la densidad es buena; en zona urbana o de secano puede no haber estación cerca.
- **Acceso**: portal web de consulta (`eportal.mapa.gob.es/websiar`), con selección de estación, rango de fechas y variables incluyendo ET0. **La página institucional no documenta ninguna API ni servicio web** ([MAPA](https://www.mapa.gob.es/es/desarrollo-rural/temas/gestion-sostenible-regadios/sistema-informacion-agroclimatica-regadio/presentacion); [SIAR, portal](https://eportal.mapa.gob.es/websiar/Inicio.aspx)) — es un formulario ASPX, no un servicio para máquinas.
- **Licencia**: la página institucional **no declara condiciones de uso**; el dataset está listado en datos.gob.es ([datos.gob.es, SIAR](https://datos.gob.es/es/aplicaciones/sistema-de-informacion-agroclimatico-y-de-regadios-siar)). Para uso personal no hay riesgo, pero no hay texto de licencia que citar.
- **Veredicto**: **no integrar**. Sí usarlo **una vez, a mano**, para comparar la ET0 de Open-Meteo con la ET0 oficial de la estación SIAR más cercana durante un mes. Si hay sesgo sistemático, se corrige con un coeficiente en el motor. Eso es investigación de calibración, no una dependencia.

---

## Candidato 4 — Meteostat, brevemente

- **Qué es**: «an open platform which provides free access to historical weather and climate data», con biblioteca Python, API JSON y descargas masivas en CSV ([Meteostat, dev](https://dev.meteostat.net/)).
- **Sólo histórico**, no pronóstico → no cubre las alertas, que es la mitad del problema.
- **Sin ET0** ni radiación de forma general; agrega datos de estaciones de DWD, NOAA y otros, y «Meteostat does not own any meteorological data» ([Meteostat, licence](https://dev.meteostat.net/license.html)).
- **Licencia: CC BY 4.0**, incluso para uso comercial, con atribución del tipo «Source: Meteostat, Deutscher Wetterdienst» ([Meteostat, licence](https://dev.meteostat.net/license.html)).
- **Sin garantías**: «The data provided by Meteostat is not intended to be used in any context where human life or property may be at risk» ([Meteostat, terms](https://dev.meteostat.net/terms.html)).
- **Veredicto**: no aporta nada sobre Open-Meteo para este caso.

---

## Candidato 5 — Copernicus / CDS (ERA5), brevemente

- **Resolución**: «Reanalysis: 0.25° x 0.25° (atmosphere)» (~25 km); ERA5-Land a 0.1° (~11 km) ([Copernicus CDS, ERA5](https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels)).
- **Latencia**: ERA5T preliminar va **~5 días por detrás** del tiempo real, y esos datos pueden diferir de la versión final publicada 2–3 meses después ([Copernicus CDS](https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels)).
- **Acceso**: portal web o **CDS API**, requiere crear cuenta ([Copernicus CDS](https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels)). Es un flujo de encolado/descarga de ficheros, no un GET que devuelve JSON.
- **Licencia**: «CC-BY licence» ([Copernicus CDS](https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels)).
- **Veredicto**: **no integrar directamente.** La misma latencia que nos estorba, con mucha más fricción. Open-Meteo ya reexpone ERA5/ERA5-Land por HTTP simple si alguna vez hiciera falta ([Open-Meteo, historical-weather-api](https://open-meteo.com/en/docs/historical-weather-api)).

---

## Tabla comparativa: variables que necesita el motor × candidatos

| Variable | Open-Meteo | AEMET OpenData | SIAR | Meteostat |
|---|---|---|---|---|
| **T máx / T mín (pasado)** | Sí — `/v1/forecast` con `past_days`, `temperature_2m_max/min` | Sí, pero con **4 días de retraso** (valores climatológicos diarios); u observación horaria de últimas ~12 h | Sí (estación, portal web) | Sí (API JSON) |
| **T máx / T mín (pronóstico)** | Sí — `/v1/forecast`, `forecast_days` ≤16 | Sí — predicción municipal diaria (7 d) / horaria (48 h) | No | No |
| **Precipitación acumulada (pasado)** | Sí — `precipitation_sum` (rejilla, señal no medida) | Sí — observación de estación (medida real, si hay estación cerca) | Sí (estación) | Sí |
| **Precipitación (pronóstico)** | Sí — `precipitation_sum` diario / horario | Sí — predicción municipal | No | No |
| **Humedad relativa** | Sí — `relative_humidity_2m_mean` (diaria), `relative_humidity_2m` (horaria) | Sí — observación horaria | Sí | Parcial |
| **Viento** | Sí — `wind_speed_10m_max` (a **10 m**) | Sí — observación horaria | Sí (a 2 m, agronómico) | Sí |
| **Radiación solar** | Sí — `shortwave_radiation_sum` (MJ/m²) | **No** en observación convencional abierta | **Sí, medida** | No |
| **ET0 (FAO-56)** | **Sí, directa** — `et0_fao_evapotranspiration`, diaria y horaria, en forecast, historical-forecast y archive | **No** — y sin Rs no se puede calcular bien | **Sí, oficial** (Penman-Monteith FAO) | No |
| **Avisos oficiales (helada/calor)** | No (derivables de umbrales) | **Sí** — avisos de fenómenos adversos | No | No |
| **Latencia del histórico reciente** | Sin retraso (`past_days`) | **4 días** (climatológicos) / 12–24 h (observación) | Diario (portal) | Días |
| **Autenticación** | Ninguna | `api_key` + patrón de 2 pasos | Formulario web | Ninguna / API key |

---

## Riesgos y qué hacer cuando el servicio no responde

**Riesgo 1 — Open-Meteo cae o cambia.** No hay SLA de ningún tipo: el servicio se presta sin garantía de disponibilidad ni de continuidad ([Open-Meteo, terms](https://open-meteo.com/en/terms)).
→ *Mitigación*: el snapshot diario se persiste en la BD local. El motor **nunca** llama a la red en el camino de renderizado. Si la llamada de las 06:15 falla, se reintenta con backoff (1 min / 5 min / 30 min) y luego se abandona hasta el día siguiente.

**Riesgo 2 — Datos obsoletos usados como si fueran frescos.** Es el fallo silencioso más peligroso: recomendar «no riegues, va a llover» con un pronóstico de hace cinco días.
→ *Mitigación*: guardar `fetched_at` con cada snapshot y aplicar una **política de caducidad escalonada**:
  - ≤ 24 h: normal.
  - 24–72 h: el plan se genera igual, pero la UI muestra «datos meteorológicos del [fecha]».
  - \> 72 h: **el motor deja de emitir alertas** (helada, ola de calor, lluvia) y cae a un plan de riego **basado sólo en calendario y especie**, avisando de que va a ciegas. Las plantas de interior no se ven afectadas en ningún caso.

**Riesgo 3 — AEMET inaccesible.** Ya observado en esta investigación: `opendata.aemet.es` rechazó la conexión de forma sostenida.
→ *Mitigación*: AEMET es estrictamente opcional. Timeout de 5 s por cada uno de los dos pasos, sin reintentos agresivos, y ausencia de avisos ≠ ausencia de peligro (los umbrales propios sobre el pronóstico de Open-Meteo siguen generando alertas).

**Riesgo 4 — La URL `datos` de AEMET caduca.** El patrón de dos pasos devuelve una URL temporal.
→ *Mitigación*: nunca persistir la URL; descargar inmediatamente y cachear el **contenido**. Tratar el fallo del paso 2 como fallo de toda la consulta.

**Riesgo 5 — Precipitación de rejilla equivocada en tormenta convectiva.** El caso realista: el modelo da 8 mm, en el jardín no cayó nada, el motor salta el riego y la planta lo paga.
→ *Mitigación*: umbral **asimétrico**. Que llueva mucho según el modelo sólo *reduce* el riego, nunca lo cancela por completo más de un ciclo; y ofrecer en la UI un botón «no llovió aquí» que invalide el dato del día. Opcionalmente, contrastar con la estación AEMET más cercana antes de saltar un riego.

**Riesgo 6 — Sesgo de ET0 en microclima doméstico.** Un patio cerrado, una terraza orientada al sur o una maceta a la sombra no viven la ET0 de rejilla.
→ *Mitigación*: coeficiente de ajuste por planta/ubicación (factor de exposición) sobre la ET0. La calibración inicial puede hacerse comparando con la estación SIAR más cercana.

**Riesgo 7 — Incumplimiento de atribución.** CC BY 4.0 exige crédito ([Open-Meteo, terms](https://open-meteo.com/en/terms)) y AEMET exige fórmulas concretas y prohíbe sugerir su patrocinio ([AEMET, nota legal](https://www.aemet.es/es/nota_legal)).
→ *Mitigación*: pie fijo en la PWA con «Datos meteorológicos de Open-Meteo.com (CC BY 4.0)» y, si se muestran avisos, «Fuente: AEMET», sin logos ni redacciones que insinúen respaldo oficial.

**Riesgo 8 — Cambio de licencia en AEMET.** datos.gob.es anticipa que «current licences will be reviewed... possibly following the recommendation by adopting the license CC BY-SA 4.0» ([datos.gob.es](https://datos.gob.es/en/blog/high-value-meteorological-datasets)).
→ *Mitigación*: irrelevante para uso personal, pero conviene revisarlo si el proyecto se publicara alguna vez.

---

## Fuentes

**Open-Meteo**
- Forecast API (variables diarias/horarias, `past_days` máx. 92, `forecast_days` máx. 16, modelos y resoluciones en Europa): https://open-meteo.com/en/docs
- Términos y límites de cuota / licencia CC BY 4.0 / ausencia de garantía: https://open-meteo.com/en/terms
- Historical Forecast API: https://open-meteo.com/en/docs/historical-forecast-api
- Historical Weather API (ERA5 / ERA5-Land, retraso de 5 días): https://open-meteo.com/en/docs/historical-weather-api
- Climate API (CMIP6, no apto para pasado reciente): https://open-meteo.com/en/docs/climate-api
- ECMWF API (modelos y resoluciones, ECMWF open-data): https://open-meteo.com/en/docs/ecmwf-api
- Repositorio oficial (AGPLv3, CC BY 4.0 para datos, atribución «Weather data by Open-Meteo.com», sin API key): https://github.com/open-meteo/open-meteo

**AEMET**
- AEMET OpenData (descripción del servicio): https://www.aemet.es/es/datos_abiertos/AEMET_OpenData
- Catálogo de datos abiertos AEMET: https://www.aemet.es/es/datos_abiertos/catalogo
- Nota legal / condiciones de reutilización: https://www.aemet.es/es/nota_legal
- Datos climatológicos (servicios climáticos): https://www.aemet.es/es/serviciosclimaticos/datosclimatologicos
- Portal OpenData y Swagger (**inaccesible durante la investigación**): https://opendata.aemet.es/dist/index.html
- Portal OpenData, info del servicio (**inaccesible**): https://opendata.aemet.es/centrodedescargas/info

**Catálogos oficiales del Estado**
- datos.gob.es — conjuntos meteorológicos de alto valor (observación 2×/hora; **climatológicos con 4 días de retraso**; avisos; radar): https://datos.gob.es/en/blog/high-value-meteorological-datasets
- datos.gob.es — Datos de observaciones medidos por estaciones meteorológicas (ventana de 12–24 h, patrón de dos llamadas, CC BY 4.0): https://datos.gob.es/en/catalogo/e05068001-datos-de-observaciones-medidos-por-estaciones-meteorologicas
- datos.gob.es — Predicción por municipios, 7 días: https://datos.gob.es/es/catalogo/ea0022545-prediccion-por-municipios-7-dias
- MITECO — dataset de observación (frecuencias, cobertura, licencia): https://catalogo.datosabiertos.miteco.gob.es/catalogo/dataset/7e58cef7-5cca-4d7e-9b8b-8bf853c9903e
- MITECO — dataset «Datos climáticos: observaciones validadas»: https://catalogo.datosabiertos.miteco.gob.es/catalogo/dataset/2d48e936-95a2-4904-af7e-59d912d002ec

**SIAR**
- MAPA — presentación del SIAR (>500 estaciones, ET0 Penman-Monteith FAO, variables medidas): https://www.mapa.gob.es/es/desarrollo-rural/temas/gestion-sostenible-regadios/sistema-informacion-agroclimatica-regadio/presentacion
- Portal web SIAR: https://eportal.mapa.gob.es/websiar/Inicio.aspx
- datos.gob.es — ficha del SIAR: https://datos.gob.es/es/aplicaciones/sistema-de-informacion-agroclimatico-y-de-regadios-siar

**Meteostat**
- Documentación: https://dev.meteostat.net/
- Licencia (CC BY 4.0, atribución, no propiedad de los datos): https://dev.meteostat.net/license.html
- Términos de servicio: https://dev.meteostat.net/terms.html

**Copernicus / CDS**
- ERA5 single levels (0.25°, ERA5T con ~5 días de retraso, CDS API, licencia CC-BY): https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels
