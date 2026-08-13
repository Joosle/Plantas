# Avisos oficiales de AEMET sin API key

Resuelve el ticket [Alta de API key de AEMET OpenData](https://github.com/Joosle/Plantas/issues/12).

**Resultado: no hay key, y no hace falta.** El único papel que
[Fuente meteorológica para España peninsular](https://github.com/Joosle/Plantas/issues/4)
le reservó a AEMET —los avisos oficiales— se cubre entero por un canal público, sin alta,
sin key y **en un host distinto del que está caído**.

Verificado el 2026-08-13.

## 1. El portal de OpenData está caído, y no es cosa nuestra

El ticket pedía solicitar la key en `opendata.aemet.es`. No se pudo: el host no acepta
conexiones.

| Camino de red | Resultado |
| --- | --- |
| `curl` desde esta máquina | sin conexión TCP (timeout a los 21 s) |
| Chrome del usuario (red doméstica real) | la pestaña rebota a `newtab`, no carga |
| Fetcher de Anthropic (otra red, otro país) | `connect ECONNREFUSED 212.128.97.177:443` |

El DNS **sí** resuelve (`opendata.aemet.es` → `212.128.97.177`), y `www.aemet.es` responde
`200` desde los tres caminos. Es decir: no es el DNS, no es nuestra red y no es el
navegador — **el servicio de OpenData no está escuchando en el 443**.

Lo relevante para el diseño no es el corte de hoy sino su **persistencia**: la
investigación de #4 ya registró que «AEMET estuvo completamente inaccesible durante toda
la investigación», y eso fue días antes. Dos investigaciones independientes, separadas en
el tiempo, encuentran el mismo host muerto. Sumado a los cortes documentados que AEMET ha
reconocido públicamente en el pasado, la conclusión es que **AEMET OpenData no puede estar
en ninguna ruta de la que dependa una alerta**, que es exactamente lo que #4 dedujo por
otras razones.

## 2. El canal que sí funciona

AEMET publica los avisos del Plan Meteoalerta en **RSS/Atom + CAP** desde `www.aemet.es`,
que es un host distinto y está sano. Índice de canales:
<https://www.aemet.es/es/rss_info/avisos/esp>.

Dos formas de consumirlo, ambas probadas hoy:

| Recurso | URL | Tamaño | Contenido |
| --- | --- | --- | --- |
| Feed RSS nacional | `https://www.aemet.es/documentos_d/eltiempo/prediccion/avisos/rss/CAP_AFAE_RSS.xml` | 241 KB | 449 avisos **amarillo/naranja/rojo** de toda España |
| Paquete completo | `https://www.aemet.es/documentos_d/eltiempo/prediccion/avisos/cap/Z_CAP_C_LEMM_<timestamp>_AFAE.tar.gz` | 251 KB | **626** ficheros CAP: los 449 anteriores **más 177 de nivel verde** |

La URL del `.tar.gz` lleva el timestamp de la tirada, así que **no es fija**: se lee del
primer `<item>` del feed RSS, que sí lo es. Una petición para el feed, otra para el
paquete: **dos llamadas al día**, encajadas en la misma tanda que Open-Meteo.

### El paquete gana al feed, y por una razón de diseño

El RSS es un **subconjunto estricto** del `.tar.gz` (comprobado: 0 avisos del RSS faltan en
el paquete). Los 177 ficheros extra son los de **nivel verde** — declaraciones explícitas
de «aquí no hay aviso de este fenómeno hoy».

Eso no es relleno: es la diferencia entre **«no hay alerta»** y **«no me he podido
descargar los avisos»**, que es la distinción que este mapa lleva legislando desde #4 (la
escalera de degradación del clima rancio), #9 (el silencio como evidencia con caducidad) y
#10 (la edad visible del plan). Con el feed a secas, un fallo de red se parece a un día
tranquilo. Con el paquete, un día tranquilo **está firmado**. **Se consume el `.tar.gz`.**

## 3. Formato de un aviso

CAP 1.2 (`urn:oasis:names:tc:emergency:cap:1.2`), UTF-8 válido, bilingüe: dos bloques
`<info>` por fichero, `es-ES` y `en-GB`. Campos que el motor necesita, todos presentes:

| Campo CAP | Ejemplo real de hoy | Uso |
| --- | --- | --- |
| `<event>` | `Aviso de temperaturas máximas de nivel amarillo` | Titular |
| `<severity>` | `Moderate` / `Minor` | Estándar CAP |
| `<eventCode>` `AEMET-Meteoalerta fenomeno` | `AT;Temperaturas máximas` | **Código de fenómeno**, apto para `enum` |
| `<parameter>` `AEMET-Meteoalerta nivel` | `amarillo` \| `naranja` \| `verde` | El nivel real de AEMET — más fino que `severity` |
| `<parameter>` `AEMET-Meteoalerta parametro` | `TA;Temperatura máxima;36 °C` | **El umbral numérico que disparó el aviso** |
| `<parameter>` `AEMET-Meteoalerta probabilidad` | `40%-70%` | Confianza declarada |
| `<onset>` / `<expires>` | `2026-08-14T13:00+02:00` / `...T20:59:59+02:00` | **Ventana con hora**, no día suelto |
| `<area><polygon>` | polígono de la zona | Filtro geográfico |
| `<area><geocode>` `AEMET-Meteoalerta zona` | `681903` | Id estable de zona |

`<onset>`/`<expires>` con hora y `expires` propio es justo lo que
[el modelo de dominio](https://github.com/Joosle/Plantas/issues/7) pidió de una `Alert`:
**caduca sola y nunca se arrastra**. Viene caducada de origen; no hay que inventarle vida útil.

### Hay dos campos de código, y confundirlos cuesta caro

`fenomeno` (en `<eventCode>`) y `parametro` (en `<parameter>`) **no son lo mismo**, aunque
ambos empiecen por dos letras y un punto y coma:

- **`fenomeno`** es *de qué* avisa: `AT;Temperaturas máximas`.
- **`parametro`** es *la magnitud medida y el umbral que se cruza*:
  `TA;Temperatura máxima;36 °C`, `P1;Precipitación acumulada en una hora;15 mm`.

El segundo es el hallazgo útil: **el aviso trae el número, no sólo el color**. Un
`36 °C` explícito se puede comparar con el umbral de una planta; un «naranja» no.

**Pero no siempre viene.** De la tirada de hoy (626 ficheros):

| Fenómeno | Trae umbral numérico |
| --- | --- |
| `AT` máximas, `PR` lluvias, `NI` nieblas | **Sí**, en todos los avisos activos |
| `TO` tormentas | **No** — 134 avisos amarillos y naranjas, ninguno con número |
| Cualquiera en nivel `verde` | No (no ha disparado nada) |

Es decir: el umbral es **opcional y depende del fenómeno**. Quien lo consuma tiene que
tratarlo como campo anulable, no como garantía.

### Vocabulario completo observado

13 fenómenos en la tirada de hoy: `AT` máximas, `BT` mínimas, `PR` lluvias, `TO` tormentas,
`NE` nevadas, `NI` nieblas, `VI` vientos, `VS` polvo en suspensión, `CO` costeros,
`AL` aludes, `DH` deshielos, `GA` galernas, `RI` rissagas.

Niveles: `amarillo` 372, `naranja` 77, `verde` 177. **Hoy no hay ningún `rojo`** — existe en
el Plan Meteoalerta, pero no se ha observado en vivo, así que el `enum` debe incluirlo sin
que este documento pueda enseñar un ejemplo.

Para este proyecto importan **`BT` (heladas)** y **`AT` (ola de calor)** —los dos disparadores
que el mapa citó desde el principio— y `PR`/`TO` como entrada al balance hídrico.

Detalle de cobertura: los avisos verdes se emiten por CCAA y fenómeno (19 de cada uno de los
8 fenómenos generales), mientras que los locales —`CO` costeros, `AL` aludes, `GA` galernas,
`RI` rissagas— sólo aparecen donde aplican. **Un fenómeno sin fichero verde no es un fallo**:
significa que ahí no aplica. En la Alcarria de Guadalajara aparecieron los 8 generales.

## 4. Prueba end-to-end contra la ubicación real

Con el punto de la casa (`40.70021, -3.42541`, ver `docs/inventario-plantas.md`) se hizo
**point-in-polygon contra los 626 ficheros CAP** del paquete de hoy.

Resultado: **exactamente una zona** contiene el punto — sin ambigüedad, sin solapes.

> **Zona `681903` — Alcarria de Guadalajara**

Avisos vigentes para esa zona en la tirada de las 17:34 UTC del 2026-08-13:

| Fenómeno | Nivel | Umbral | Prob. | Desde | Hasta |
| --- | --- | --- | --- | --- | --- |
| `AT` Temperaturas máximas | 🟡 amarillo | **37 °C** | 40–70 % | 13-08 13:00 | 13-08 20:59 |
| `AT` Temperaturas máximas | 🟡 amarillo | **37 °C** | 40–70 % | 14-08 13:00 | 14-08 20:59 |
| `PR` Lluvias | 🟡 amarillo | **15 mm/h** | 40–70 % | 15-08 14:00 | 15-08 21:59 |
| `TO` Tormentas | 🟡 amarillo | — (no trae) | 40–70 % | 15-08 14:00 | 15-08 21:59 |
| Los 8 fenómenos generales | 🟢 verde | — | — | 15-08 00:00 | 15-08 23:59 |

**Un aviso amarillo de máximas activo hoy mismo sobre la casa**, y no un caso sintético:
la ruta completa —descargar, descomprimir, localizar la zona, leer nivel, umbral y ventana—
está probada de punta a punta con dato real.

Dos cosas que este resultado enseña de paso:

- **El umbral local es 37 °C**, no un número genérico: AEMET calibra por zona (hoy hay
  avisos de máximas desde 34 °C hasta 40 °C según dónde). El aviso ya viene adaptado a lo
  que es anómalo *aquí*, que es más de lo que el proyecto podría deducir solo.
- **`TO` confirma en vivo el hueco del umbral**: mismo día, misma zona, mismo nivel
  amarillo que `PR`, y sin número. No es un fallo de la descarga.

### La zona se resuelve una vez, no en cada ejecución

El polígono decide la zona, pero la zona **no cambia**: es una constante de la casa. Se
fija `681903` en la configuración y en ejecución basta filtrar por `<geocode>`, que es
comparación de cadenas. El point-in-polygon fue el trabajo de *hoy*, para averiguar el
código; **no entra en el código de producción**. Si algún día hubiera una segunda
ubicación, se repite este cálculo a mano.

## 5. Consecuencias

1. **La key de AEMET no se pide.** El ticket se cierra sin alta. Nada del alcance actual
   la necesita, y pedirla ni siquiera es posible hoy.
2. **Enmienda a #4.** Donde decía «AEMET OpenData como respaldo para avisos oficiales y
   observación de estación», ahora: **avisos por el canal CAP público de `www.aemet.es`,
   sin key**. La *observación de estación* se cae del todo — #4 ya la había vaciado de
   contenido (sin radiación, ventana de 12–24 h, retraso de 4 días en climatológicos) y
   era lo único que exigía OpenData.
3. **AEMET deja de ser un riesgo de credencial** y pasa a ser sólo un riesgo de
   disponibilidad, cubierto por la escalera de degradación que #4 ya definió. Sin key que
   caduque, sin cuota que agotar, sin correo de alta que perder.
4. **Entrada para [Alertas de helada y calor](https://github.com/Joosle/Plantas/issues/19)**:
   ese ticket puede contrastar el umbral *calculado* desde la rusticidad con el aviso
   *oficial* de AEMET para la misma zona y el mismo día. No son la misma señal —AEMET avisa
   por riesgo a personas, no a plantas— pero tenerlas lado a lado es más de lo que había.

## Fuentes

- AEMET — Canales RSS/Atom de avisos: <https://www.aemet.es/es/rss_info/avisos/esp>
- AEMET — Avisos de fenómenos meteorológicos adversos: <https://www.aemet.es/es/eltiempo/prediccion/avisos>
- AEMET — AEMET OpenData (descripción del API con key): <https://www.aemet.es/es/datos_abiertos/AEMET_OpenData>
- AEMET — Nota legal (condiciones de reutilización): <https://www.aemet.es/es/nota_legal>
- OASIS — Common Alerting Protocol 1.2, el estándar de los ficheros: `urn:oasis:names:tc:emergency:cap:1.2`
- Open-Meteo — API de geocodificación usada para el punto: <https://geocoding-api.open-meteo.com/v1/search>
