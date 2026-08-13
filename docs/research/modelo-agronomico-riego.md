# Modelo agronómico: de clima a "regar o no regar"

> Investigación para el issue [#6](https://github.com/Joosle/Plantas/issues/6), hijo del mapa [#1](https://github.com/Joosle/Plantas/issues/1).
> Fecha: 2026-08-07. Fuentes primarias citadas con URL + capítulo/ecuación.
> Alcance: plantas de **exterior** (maceta y suelo) en vivienda de España peninsular, sin sensores de humedad.
> Las de interior quedan fuera del modelo hídrico climático por decisión del mapa (especie + calendario estacional).

---

## Resumen de lo que dicen las fuentes

| Pregunta | Respuesta corta | Fuente que la posee |
|---|---|---|
| ¿Hay que calcular ET0 a mano? | **No.** Open-Meteo publica `et0_fao_evapotranspiration` (FAO-56 PM) diaria, gratis, sin API key | [Open-Meteo docs](https://open-meteo.com/en/docs) |
| ¿AEMET OpenData da ET0? | **No.** Da observación, predicción y valores climatológicos; no ET0 | [AEMET OpenData](https://www.aemet.es/en/datos_abiertos/AEMET_OpenData) |
| ¿Hay ET0 oficial española? | **Sí, SiAR** (MAPA), ET0 Penman-Monteith diaria de +500 estaciones. Observación, no predicción | [MAPA SiAR](https://www.mapa.gob.es/es/desarrollo-rural/temas/gestion-sostenible-regadios/sistema-informacion-agroclimatica-regadio/presentacion) |
| ¿ERA5/Copernicus? | ERA5 crudo no; **AgERA5** sí publica *reference evapotranspiration* FAO-56 diaria a 0.1° | [Catálogo FAO/AgERA5](https://data.apps.fao.org/catalog/iso/f22813e9-679e-4864-bd92-d48f5dfc436c) |
| ¿FAO-56 cubre ornamentales? | **No.** El capítulo 9 no aborda vegetación ornamental ni de paisaje | [FAO-56 cap. 9](https://www.fao.org/4/x0490e/x0490e0f.htm) |
| ¿Qué se usa entonces? | El **coeficiente de paisaje KL = ks · kd · kmc** (WUCOLS / UC ANR) | [UC ANR, Simplified Landscape Irrigation Demand](https://ucanr.edu/sites/default/files/2016-09/248814.pdf) |
| ¿Kc sirve para macetas? | **Mal.** La literatura da Kc de ornamentales en contenedor entre <1 y >5 | [Water 2019, 11(10), 2070](https://doi.org/10.3390/w11102070) |
| ¿Qué error mete Hargreaves-Samani en España? | RMSE anual 0.52–0.69 mm/d; muy malo en invierno (>30 %) | [NHESS 20, 859 (2020)](https://nhess.copernicus.org/articles/20/859/2020/) |
| ¿Lluvia pequeña cuenta? | **No.** P < 0.2·ET0 se evapora entera y se puede ignorar | [FAO-56 cap. 7](https://www.fao.org/4/x0490e/x0490e0c.htm) |

**Conclusión de altura**: el eslabón débil de este modelo **no es la atmósfera** (ET0 viene servida y con ±10 % de error), sino la planta (KL cuantizado en saltos de ~2×) y el sustrato (volumen y agua disponible con ±50 %). Por tanto un modelo de "depósito" (bucket) simple entrega la mayor parte del valor; sofisticar la meteorología es optimizar el término que menos pesa.

---

## 1. ET0 y Penman-Monteith

### 1.1 Qué exige FAO-56

La ecuación FAO Penman-Monteith es la **ecuación 6** de FAO-56 ([cap. 2](https://www.fao.org/4/x0490e/x0490e06.htm)):

```
        0.408 Δ (Rn − G) + γ · (900 / (T + 273)) · u2 · (es − ea)
ET0 =  ───────────────────────────────────────────────────────────
                    Δ + γ (1 + 0.34 u2)
```

Con las variables y unidades que el propio capítulo define:

| Símbolo | Significado | Unidad |
|---|---|---|
| ET0 | evapotranspiración de referencia | mm día⁻¹ |
| Rn | radiación neta en la superficie del cultivo | MJ m⁻² día⁻¹ |
| G | flujo de calor del suelo | MJ m⁻² día⁻¹ |
| T | temperatura media diaria del aire a 2 m | °C |
| u2 | velocidad del viento a 2 m | m s⁻¹ |
| es − ea | déficit de presión de vapor | kPa |
| Δ | pendiente de la curva de presión de vapor | kPa °C⁻¹ |
| γ | constante psicrométrica | kPa °C⁻¹ |

La superficie de referencia es *"a hypothetical reference crop with an assumed crop height of 0.12 m, a fixed surface resistance of 70 s m⁻¹ and an albedo of 0.23"* ([FAO-56 cap. 2](https://www.fao.org/4/x0490e/x0490e06.htm)). **Esto importa**: ET0 no es "lo que gasta tu planta", es lo que gastaría un césped bien regado de 12 cm. Todo lo demás son coeficientes sobre esa referencia.

Entradas meteorológicas exigidas: temperatura máx/mín, humedad (presión de vapor real), radiación solar o duración de insolación, y viento a 2 m.

### 1.2 ¿Se puede evitar calcularla? Sí

**Open-Meteo** expone `et0_fao_evapotranspiration` como variable diaria y horaria, descrita en su documentación como *"ET₀ Reference Evapotranspiration of a well watered grass field"*, calculada por FAO-56 Penman-Monteith ([Open-Meteo docs](https://open-meteo.com/en/docs)). Verificado en vivo el 2026-08-07 contra la API pública:

```
GET https://api.open-meteo.com/v1/forecast
    ?latitude=40.4&longitude=-3.7
    &daily=et0_fao_evapotranspiration,precipitation_sum,temperature_2m_max,temperature_2m_min
    &timezone=Europe/Madrid

→ "et0_fao_evapotranspiration":[7.49, 7.42, 7.21]   // mm/día, Madrid, agosto
   "temperature_2m_max":[38.3, 37.8, 35.2]
```

7.5 mm/día en Madrid en agosto es el orden de magnitud correcto para meseta interior en verano. La variable es directamente utilizable.

Condiciones de uso, relevantes para el diseño ([Open-Meteo docs](https://open-meteo.com/en/docs), [términos](https://open-meteo.com/en/terms)):

- **Sin API key** para uso no comercial.
- Licencia **CC-BY 4.0** → hay que atribuir a Open-Meteo en la app.
- Límites del nivel gratuito: **10.000 llamadas/día**, 5.000/hora, 600/min. Para un usuario doméstico con una llamada diaria, sobra por cuatro órdenes de magnitud.
- Horizonte: 7 días por defecto, **hasta 16 días**; `past_days` hasta 92 días hacia atrás. Esto permite reconstruir el balance hídrico si la app estuvo días sin abrirse.
- Resolución sobre Europa: modelos de 1–2,5 km seleccionados automáticamente por localización.

**Recomendación**: Open-Meteo es la fuente por defecto. Es la única de las tres candidatas que combina ET0 lista, **predicción** (imprescindible para el plan semanal y las alertas), gratuidad y cero fricción de registro.

### 1.3 Las otras fuentes plausibles para España

**AEMET OpenData** es una API REST gratuita con acceso a predicción, observación y valores climatológicos, con la única condición de citar a AEMET como autor ([AEMET OpenData](https://www.aemet.es/en/datos_abiertos/AEMET_OpenData)). **No publica ET0** en ninguna de sus familias de datos. Habría que calcularla desde las variables crudas, y la predicción municipal no incluye radiación de forma consistente. Sigue siendo interesante como fuente de **avisos oficiales** (helada, ola de calor), que es el otro entregable del mapa, pero no para el balance hídrico.

**SiAR** (Sistema de Información Agroclimática para el Regadío, MAPA) es la fuente española canónica: red de **más de 500 estaciones agroclimáticas** (>360 del Ministerio, >160 de las CCAA), que mide temperatura, humedad, viento, radiación solar y precipitación, y **calcula ET0 por Penman-Monteith** en registros semihorarios, diarios, semanales y mensuales, con API de acceso programático ([MAPA — presentación SiAR](https://www.mapa.gob.es/es/desarrollo-rural/temas/gestion-sostenible-regadios/sistema-informacion-agroclimatica-regadio/presentacion), [portal SiAR](https://eportal.mapa.gob.es/websiar/)). Es una fuente **excelente para validar** el ET0 de Open-Meteo en la comarca del usuario, y para reconstruir el histórico reciente. Su límite estructural: es observación, **no predicción**, y depende de que haya estación cerca. No sirve para el plan semanal.

**ERA5 / Copernicus**: ERA5 crudo publica `pev` (potential evaporation), que **no es ET0 FAO-56** y no debe usarse como sustituto. Lo que sí sirve es **AgERA5**, los indicadores agrometeorológicos derivados de ERA5 publicados por C3S/ECMWF, que incluyen *reference evapotranspiration* diaria, global, a 0.1° (~10 km), preparada explícitamente *"according to the FAO Penman-Monteith method as described in FAO Irrigation and Drainage Paper 56"* ([catálogo FAO/AgERA5 diario](https://data.apps.fao.org/catalog/iso/f22813e9-679e-4864-bd92-d48f5dfc436c)). Es reanálisis: latencia de días, resolución gruesa, y acceso vía CDS con registro. **Sobredimensionado** para esta app.

### 1.4 Métodos reducidos: si algún día no hay ET0

FAO-56 dedica el [capítulo 3](https://www.fao.org/4/x0490e/x0490e07.htm) a datos faltantes. Lo relevante:

- **Humedad ausente** (ec. 48): asumir que el punto de rocío se acerca a la mínima diaria → `ea = e°(Tmin)`. En zonas áridas conviene restar 2–3 °C a Tmin antes.
- **Radiación ausente** (ec. 50, fórmula de Hargreaves): `Rs = kRs · √(Tmax − Tmin) · Ra`, con **kRs ≈ 0.16 para interior** y **≈ 0.19 para costa**.
- **Viento ausente**: *"a value of 2 m/s can be used as a temporary estimate. This value is the average over 2000 weather stations around the globe"*, con el límite u2 ≥ 0.5 m/s.
- **Hargreaves-Samani** (ec. 52), ET0 solo con temperaturas:

  ```
  ET0 = 0.0023 · (Tmedia + 17.8) · (Tmax − Tmin)^0.5 · Ra        [mm/día]
  ```

  con **Ra expresada en mm/día** (= 0.408 × Ra en MJ m⁻² d⁻¹).

FAO-56 avisa de dos sesgos propios: la ec. 52 *"has a tendency to underpredict under high wind conditions (u2 > 3 m/s) and to overpredict under conditions of high relative humidity"*, y que *"daily estimates should not be utilized as true daily estimates but only in averages over the period under consideration"*. Y afirma que rellenar huecos y usar Penman-Monteith sale *"somewhat more accurate"* que aplicar directamente Hargreaves-Samani.

**Cuánto error, en España concretamente.** El estudio de la cuenca del Duero sobre 49 estaciones, 2000–2018, comparando modelos de datos reducidos contra FAO-56 PM completo ([NHESS 20, 859, 2020](https://nhess.copernicus.org/articles/20/859/2020/)):

- A escala **anual**: RMSE 0.52–0.69 mm/día, NSE > 0.88, R² > 0.91, sesgo ±4 %. Aceptable.
- A escala **estacional se rompe**: en invierno *"no model showed good performance"*, con RMSE relativo por encima del 30 %. Primavera 17–20 %, verano 12–16 %, otoño 22–32 %.

En el sur de España, Hargreaves subestimó en promedio **0.69 mm/día en estaciones costeras** y sobrestimó **0.13 mm/día en interior** ([Assessing Reference Evapotranspiration by the Hargreaves Method in Southern Spain](https://www.researchgate.net/publication/245288847_Assessing_Reference_Evapotranspiration_by_the_Hargreaves_Method_in_Southern_Spain) — nota: resumen secundario, la afirmación no ha podido verificarse contra el PDF original; tratar como indicativa).

**Lectura práctica**: un error de ±0.6 mm/día sobre un ET0 estival de 7 mm/día es un **9 %**. Puesto al lado de la incertidumbre del factor de especie (ver §2), es ruido. Hargreaves-Samani es un fallback offline perfectamente digno, **con la salvedad de invierno**, que es justamente cuando el riego importa poco.

---

## 2. Coeficiente de cultivo (Kc) y el salto a ornamentales

### 2.1 Qué es Kc en FAO-56

La [ecuación 58](https://www.fao.org/4/x0490e/x0490e0b.htm) es todo el concepto:

```
ETc = Kc · ET0
```

Kc integra en un solo número las cuatro diferencias entre el cultivo real y el césped de referencia: altura, albedo, resistencia del dosel y evaporación desde el suelo. La **Tabla 12** de FAO-56 tabula tres valores por cultivo — `Kc ini`, `Kc mid`, `Kc end` — que se interpolan a lo largo de las cuatro etapas fenológicas. Órdenes de magnitud: Kc ini 0.3–1.05, Kc mid 0.85–1.20, Kc end 0.25–1.10. Las **ecuaciones 62 y 65** corrigen Kc mid y Kc end por clima local (u2, RHmin, altura h).

El propio capítulo advierte que las duraciones de etapa *"may vary substantially from region to region"* y que los valores de tabla son aproximaciones que requieren observación local.

### 2.2 FAO-56 no cubre ornamentales — y lo dice por omisión

El [capítulo 9](https://www.fao.org/4/x0490e/x0490e0f.htm) ("ETc for natural, non-typical and non-pristine vegetation") es lo más cerca que llega FAO-56, y aporta dos herramientas genuinamente útiles para plantas leñosas dispersas:

- **Ecuación 97** (vía índice de área foliar): `Kcb mid = Kc min + (Kcb full − Kc min)(1 − exp[−0.7·LAI])`
- **Ecuación 98** (vía cobertura del suelo): estima Kcb mid a partir de la fracción efectiva de suelo cubierto `fc eff` y la altura de planta, pensada para árboles y arbustos de dosel disperso, con aproximación esférica para árboles aislados.

Pero **no hay sección sobre vegetación ornamental, de jardín o urbana**. Este es el hueco que hay que cubrir con otra literatura.

### 2.3 La respuesta correcta para jardín: el coeficiente de paisaje KL

La University of California desarrolló el método del **coeficiente de paisaje**, publicado por UC ANR ([Simplified Landscape Irrigation Demand Estimation](https://ucanr.edu/sites/default/files/2016-09/248814.pdf)):

```
ETL = KL · ET0
KL  = ks · kd · kmc
```

Donde:

| Factor | Qué captura | Valores |
|---|---|---|
| **ks** — especie | necesidad hídrica relativa de la especie | Muy baja `<0.10` · Baja `0.10–0.30` · Moderada `0.40–0.60` · Alta `0.70–0.90` (ver corrección abajo) |
| **kd** — densidad | cuánto dosel/cobertura hay realmente | Baja 0.5 · Media 0.75 · Alta 1.0 |
| **kmc** — microclima | exposición: sombra vs. pared sur reflectante y ventosa | Bajo 0.5 · Medio 0.75 · Alto 1.25 |

El factor de especie `ks` se resuelve consultando **WUCOLS** (Water Use Classification of Landscape Species), mantenido por el California Center for Urban Horticulture de UC Davis, que clasifica **más de 3.500 especies ornamentales** en categorías muy baja / baja / moderada / alta ([WUCOLS, UC ANR](https://ucanr.edu/sites/WUCOLS)).

**Esto es exactamente lo que este proyecto necesita**: una fuente estructurada, citable, con cobertura de miles de ornamentales, cuya salida es un número entre 0.1 y 0.8 que multiplica a ET0. Encaja con la restricción del mapa ("los hechos duros por especie salen de una fuente estructurada citable"): la ficha de especie almacena una categoría WUCOLS, no un número inventado por el LLM.

> **Corrección (2026-08-13, [ticket #3](https://github.com/Joosle/Plantas/issues/3)).** Los valores puntuales
> `0.1 / 0.3 / 0.6 / 0.8` que este documento anotaba **no son los del fichero real de WUCOLS**. El volcado
> oficial (`wucols-data.json`, 4.103 taxones, snapshot de 2025-06-27) define `plantFactor` como **rangos**:
> VL `<0.10`, LO `0.10–0.30`, M `0.40–0.60`, H `0.70–0.90`. Los antiguos valores siguen sirviendo como punto
> medio operativo, pero hay que saber que son una elección del proyecto dentro de un rango, no el dato.
>
> Además existen **dos categorías que este modelo no contemplaba** y que deben **desactivar el balance
> hídrico** en lugar de caer a un número por defecto:
>
> - `U` — especie **no evaluada** en esa región.
> - `NA` — especie **no apta** para esa región.
>
> Ambas caen en la regla de §"Faltan datos": no dar litros, sólo "toca revisar". Detalle y verificación en
> [`fuente-cuidados-datasets.md`](./fuente-cuidados-datasets.md).

**Advertencias que hay que declarar**:

1. WUCOLS está calibrado para **California**, con seis regiones climáticas propias. España peninsular tiene climas mediterráneos análogos (que es precisamente por qué tantas ornamentales son compartidas), pero la extrapolación no está validada por la fuente. Es una asunción del proyecto, no un hecho de WUCOLS.
2. Las categorías son **bins gruesos**. De "baja" (0.3) a "moderada" (0.6) hay un factor 2. Esa es la resolución real del modelo en el eje planta.
3. El propio documento de UC señala que las condiciones concretas del sitio y la fase de establecimiento pueden justificar apartarse de los coeficientes estándar.

### 2.4 Para maceta, Kc se rompe

Aquí hay que ser explícito porque es el error de diseño más fácil de cometer. En un cultivo en campo, ETc = Kc · ET0 con todo referido a **área de suelo**. En una maceta, si se normaliza por el **área de la boca del tiesto**, el "Kc" resultante es absurdo: la planta transpira desde un dosel que puede ser 3× el área del tiesto, y además está aislada, así que recibe advección lateral (el "efecto oasis" / clothesline effect) que un cultivo en manto no recibe.

La literatura lo confirma: en ornamentales leñosas en contenedor, *"crop coefficients reported in literature for container woody ornamentals range from <1 to >5"*, y los consumos diarios medidos (DWU) estuvieron entre **8,8 y 17,3 mm/día** según taxón y tratamiento ([Water 2019, 11(10), 2070](https://doi.org/10.3390/w11102070) — verificado vía metadatos y resumen del editor; el texto completo devolvió 403 y no ha podido leerse directamente).

**Consecuencia de diseño**: para maceta **no se trabaja en mm sino en litros**, y el área que multiplica a ET0 es el **área proyectada del dosel**, no la del tiesto:

```
ET_planta [L/día] = KL · ET0 [mm/día] · A_dosel [m²]
```

(1 mm sobre 1 m² = 1 L, así que la conversión es directa). Esto evita el falso Kc>1 y hace el modelo geométricamente honesto.

---

## 3. Balance hídrico en maceta: por qué cambia todo

### 3.1 El sustrato de contenedor no se comporta como suelo

Fuentes primarias de horticultura de contenedor ([USDA-ARS / HortScience, *Water and Air Relations in Propagation Substrates*, DOI 10.21273/HORTSCI14145-19](https://www.ars.usda.gov/ARSUserFiles/50820500/GPRG/2019PublicationsandSummaries/2019_Water%20and%20Air%20Relations%20in%20Propagation%20Substrates.pdf)):

- **Capacidad de contenedor (CC)**: el agua máxima que retiene el sustrato una vez cesa el drenaje gravitacional. Es el análogo de "capacidad de campo" pero **no es lo mismo**: depende de la geometría del tiesto.
- **Porosidad de aire (AFP)**: el espacio que drena por gravedad. Óptimo 10–20 % v/v.
- **Agua fácilmente disponible (EAW)**: agua retenida entre capacidad de contenedor y aproximadamente **−10 kPa**. En el marco clásico de de Boodt & Verdonck (1972), EAW es el agua liberada entre −1 y −5 kPa (10–50 cm de columna de agua).
- **Agua no disponible**: por debajo del punto de marchitez permanente, ≈ −1.500 kPa.

Y la diferencia estructural, textual de la fuente: los sustratos de contenedor desarrollan una **capa freática colgada (perched water table)** por efecto de las paredes del tiesto — el agua se acumula en el fondo en vez de drenar libremente como en un perfil de suelo. **Los tiestos altos sufren menos** este efecto; los bajos y anchos retienen desproporcionadamente en la base.

La consecuencia práctica es que **la altura del tiesto cambia cuánta agua útil tiene**, a igualdad de volumen y sustrato. Un modelo que solo mire "litros de maceta" está ignorando esto — y es aceptable ignorarlo, pero hay que declararlo.

### 3.2 Por qué el agotamiento se mide en días, no en semanas

Aritmética de orden de magnitud, con las cifras de arriba:

- Maceta de 10 L nominales → ~7 L de sustrato (el resto es drenaje, borde libre, cepellón).
- EAW típica de un sustrato de turba de calidad: **≈ 20–30 % v/v**. Tomando 25 %: **≈ 1,75 L de agua fácilmente disponible**.
- Planta con dosel de 0,3 m² (≈ 60 cm de diámetro), KL = 0,6, en Madrid en agosto con ET0 = 7,5 mm/día:
  `ET = 0,6 × 7,5 × 0,3 = 1,35 L/día`

**El depósito se agota en poco más de un día.** Ese es el resultado central de esta sección y justifica todo el ticket: en maceta, en verano peninsular, el margen de error entre "bien" y "muerta" son horas, no semanas.

Compárese con suelo: FAO-56 ([cap. 8, ec. 82](https://www.fao.org/4/x0490e/x0490e0e.htm)) da `TAW = 1000 (θFC − θWP) · Zr`, con `1000(θFC − θWP)` de **90 mm/m para arena franca, 120 para arcillo-limoso, 170 para limo**. Un arbusto con 0,5 m de raíz en suelo franco tiene ~75 mm de agua total disponible, y con p = 0,5 unos **37 mm fácilmente disponibles**: a 4 mm/día de ETc eso son **9 días**. Un orden de magnitud más de margen.

**Regla de diseño**: maceta y suelo necesitan **el mismo modelo conceptual (un depósito que se vacía) pero parámetros y cadencias distintos**, y macetas pequeñas en verano deben poder generar avisos diarios, no semanales.

---

## 4. Lluvia efectiva

### 4.1 Lo que dice FAO

FAO-56 aporta la regla operativa más útil y más simple ([cap. 7](https://www.fao.org/4/x0490e/x0490e0c.htm)):

> *"Daily precipitation in amounts less than about 0.2 ET_o is normally entirely evaporated and can usually be ignored in the K_e and water balance calculations."*

Con ET0 = 7 mm/día en verano, **cualquier lluvia por debajo de 1,4 mm no cuenta**. Esto elimina de un plumazo el falso positivo más común ("llovió, no riegues") ante un chubasco simbólico.

El mismo capítulo da los parámetros del suelo superficial: **REW** (agua fácilmente evaporable) 2–11 mm según textura, **TEW = 1000(θFC − 0,5 θWP)·Ze** con Ze = 0,10–0,15 m, y `Kr = (TEW − De)/(TEW − REW)` para la fase 2 de secado. Y que **la lluvia moja el 100 % de la superficie (fw = 1,0)**, frente a goteo (0,3–0,4).

FAO-56 [cap. 8, ec. 85](https://www.fao.org/4/x0490e/x0490e0e.htm) da el balance de la zona radicular completo:

```
Dr,i = Dr,i−1 − (P − RO)i − Ii − CRi + ETc,i + DPi
```

donde P = precipitación, RO = escorrentía, I = riego neto infiltrado, CR = ascenso capilar, ETc = evapotranspiración del cultivo, DP = percolación profunda, Dr = agotamiento de la zona radicular. Para el caso doméstico, CR ≈ 0 y DP se resuelve por saturación (todo lo que exceda de llenar el depósito se pierde).

### 4.2 Los métodos USDA-SCS y por qué no encajan aquí

FAO Riego y Drenaje nº 25 ([cap. II](https://www.fao.org/4/x5560e/x5560e03.htm)) recoge los métodos clásicos de lluvia efectiva:

- **USDA Soil Conservation Service**: tabulado (Tabla 8) en función de la **lluvia mensual media** y el uso consuntivo, corregido por un factor según la lámina neta de riego (Tabla 7, factores 0,620–1,070 para láminas distintas de los 75 mm estándar). Restricción clave: *"the monthly effective rainfall cannot exceed the rate of consumptive use"*.
- **Porcentaje fijo**: p. ej. 70 % de la lluvia estacional media.
- **Fórmulas empíricas** de tipo estadístico (método Sastry), basadas en media y desviación típica de la lluvia diaria.

La misma fuente cuantifica cuánta lluvia se pierde de verdad: en su ejemplo de balance diario, **solo 279 mm de 625 mm (45 %) resultaron efectivos**, el resto se fue en percolación profunda y drenaje.

**Veredicto**: el método SCS es **mensual** y está diseñado para dimensionar campañas de riego agrícola, no para decidir si regar un tiesto el martes. Aquí no sirve como algoritmo, pero sirve como **calibración de expectativa**: es razonable que menos de la mitad de la lluvia acabe siendo útil.

### 4.3 La geometría doméstica: el problema real

Ninguna fuente agronómica cubre esto porque es un problema de la vivienda, no del campo. Hay que resolverlo geométricamente y **declararlo como asunción del proyecto**:

**A pleno campo (planta en suelo, cielo abierto)**: la lluvia cae sobre el área radicular. `P_ef = P` menos escorrentía y menos el exceso sobre la capacidad. Es el caso FAO-56 estándar.

**En maceta**: la lluvia entra solo por la **boca del tiesto**. El área de captación no es el área del dosel, ni el área de suelo — es **A_boca**. Y la relación se invierte respecto al consumo: la planta transpira desde A_dosel, que suele ser mayor que A_boca. Con dosel de 0,3 m² sobre tiesto de 0,07 m² (30 cm de diámetro), la maceta capta ~4 veces menos agua de la que necesitaría para compensar su propio consumo. **Una lluvia de 10 mm aporta 0,7 L a una planta que gasta 1,35 L/día**: medio día de respiro, no una semana.

Peor: el dosel actúa de paraguas sobre su propio tiesto. Una planta frondosa intercepta lluvia que gotea **fuera** del tiesto. Esto se modela con un factor de intercepción, y su valor honesto es "no lo sabemos" — se propone descartar los primeros 1–2 mm de cada evento como interceptados.

**Bajo alero, porche o balcón cubierto**: la lluvia efectiva es **cero o casi**. Es la clase de detalle que un usuario sabe y un modelo no. Debe ser un campo del perfil de la planta (`exposicion_lluvia`: abierto / parcial / cubierto), no una inferencia.

---

## 5. Qué precisión es honesta

**Sí: un modelo simple entrega la mayor parte del valor.** El argumento, con los números de las secciones anteriores:

Descomponiendo el error del cálculo `ET_planta = KL · ET0 · A`:

| Término | Incertidumbre realista | Origen de la cifra |
|---|---|---|
| ET0 (Open-Meteo, PM completo) | ~±10 % | modelo NWP a 1–2,5 km sobre punto |
| ET0 (Hargreaves-Samani, fallback) | ±0,5–0,7 mm/d ≈ ±10 % en verano, >30 % en invierno | [NHESS 20, 859](https://nhess.copernicus.org/articles/20/859/2020/) |
| **KL (categoría WUCOLS)** | **factor ~2** entre bins contiguos (0.3 vs 0.6) | [UC ANR](https://ucanr.edu/sites/default/files/2016-09/248814.pdf) |
| **Área de dosel (estimada por el usuario)** | **±50 %** | no medida, declarada |
| **EAW del sustrato real** | **±50 %** (20–30 % v/v en turba buena; mucho menos en tierra de saco compactada) | [USDA-ARS](https://www.ars.usda.gov/ARSUserFiles/50820500/GPRG/2019PublicationsandSummaries/2019_Water%20and%20Air%20Relations%20in%20Propagation%20Substrates.pdf) |
| Lluvia efectiva bajo alero | binaria y desconocida sin input del usuario | — |

Los términos dominantes son **KL, área y sustrato**, todos ellos del lado de la planta y todos con incertidumbre de factor 1,5–2. Mejorar el ET0 de Hargreaves a Penman-Monteith reduce un error del 10 % en un término que ya está multiplicado por otro con un error del 100 %. **Es optimización del término equivocado.**

Por eso:

- Un modelo de "días desde el último riego ajustados por temperatura y lluvia" **sí captura el 80 % del valor**, porque el 80 % de las decisiones domésticas son binarias y groseras: en agosto riega casi cada día, en enero casi nunca, si llovió 20 mm espera.
- Pero cuesta lo mismo hacerlo bien: **Open-Meteo regala ET0**, así que la versión "correcta" (bucket con ET0) no es más cara de implementar que la versión heurística. La complejidad no está en la meteorología.
- Lo que **no** hay que hacer es fingir precisión: no decir "riega 1,35 L", sino "toca regar" + una cantidad redondeada con banda ("~1,5 L, hasta que drene por abajo").

**Regla de honestidad para la app**: el modelo decide **cuándo**, y da el **cuánto** solo como orden de magnitud. El "cuánto" preciso en maceta tiene una respuesta agronómica mejor que cualquier número calculado: regar hasta que drene por los agujeros (llenar a capacidad de contenedor), que además lava sales.

---

## Modelo propuesto

Dos modos con el **mismo esqueleto** (un depósito que se vacía), parámetros distintos.

### 6.1 Entradas

| Entrada | Símbolo | Origen | Frecuencia |
|---|---|---|---|
| ET de referencia diaria | `ET0` [mm/d] | Open-Meteo `daily=et0_fao_evapotranspiration` (pasado + pronóstico 16 d) | 1 llamada/día |
| Precipitación diaria | `P` [mm] | Open-Meteo `daily=precipitation_sum` | ídem |
| Temp. máx/mín | `Tmax`,`Tmin` [°C] | Open-Meteo (alertas de helada/calor + fallback HS) | ídem |
| Lat/lon | `φ`, `λ` | perfil de la vivienda (una vez) | — |
| Categoría hídrica de especie | `ks` ∈ {VL, LO, M, H} → punto medio del rango; `U`/`NA` desactivan el balance | ficha de especie ← WUCOLS | estática |
| Densidad de plantación | `kd` ∈ {0.5, 0.75, 1.0} | ficha de la planta (declarado por el usuario) | estática |
| Microclima | `kmc` ∈ {0.5, 0.75, 1.25} | ubicación de la planta (sombra / normal / pared sur o ventoso) | estática |
| Tipo de emplazamiento | `maceta` \| `suelo` | ficha de la planta | estática |
| Volumen de maceta | `V_pot` [L] | ficha de la planta | estática |
| Diámetro de boca | `D_boca` [m] | ficha de la planta (o derivado de V_pot) | estática |
| Diámetro de dosel | `D_dosel` [m] | ficha de la planta, revisable | estacional |
| Exposición a la lluvia | `f_lluvia` ∈ {1.0 abierto, 0.4 parcial, 0.0 cubierto} | ficha de la planta | estática |
| Profundidad radicular (suelo) | `Zr` [m] | ficha de especie (0.3 herbácea / 0.5 arbusto / 1.0 árbol) | estática |
| Textura del suelo (suelo) | → `AWC` [mm/m] | perfil del jardín; por defecto franco = 150 | estática |
| **Eventos de riego** | `I` | **diario del usuario** (esto resetea el balance) | evento |

### 6.2 Geometría derivada

```
A_dosel = π · (D_dosel / 2)²                    [m²]
A_boca  = π · (D_boca  / 2)²                    [m²]
V_sub   = 0.70 · V_pot                          [L]   // supuesto: 70% del nominal es sustrato
```

### 6.3 Coeficiente de paisaje

```
KL = clamp(ks · kd · kmc, 0.05, 1.2)
```

(UC ANR: `KL = ks · kd · kmc`. El clamp evita valores degenerados por combinaciones extremas.)

### 6.4 Demanda diaria

```
Maceta:  ET_i [L]   = KL · ET0_i [mm] · A_dosel [m²]
Suelo:   ETc_i [mm] = KL · ET0_i [mm]
```

### 6.5 Lluvia efectiva

```
// Regla FAO-56 cap.7: ignorar chubascos que se evaporan enteros
si P_i < 0.2 · ET0_i  →  P_util = 0
si no                 →  P_util = max(0, P_i − 1.5)     // 1.5 mm interceptados por el dosel

Maceta:  R_i [L]  = P_util · A_boca [m²] · f_lluvia
Suelo:   R_i [mm] = P_util · f_lluvia
```

### 6.6 Balance — MACETA (en litros)

```
W_max = V_sub · θ_EAW                     // θ_EAW = 0.25 por defecto (turba); 0.15 si "tierra de saco"
S_0   = W_max                             // al registrar un riego: S ← W_max

S_i = clamp( S_{i−1} + R_i + I_i − ET_i , 0 , W_max )

d_i = 1 − S_i / W_max                     // fracción de agotamiento, 0..1
```

**Disparo de riego**: cuando `d_i ≥ u_especie`, con

| Tolerancia de la especie | `u` |
|---|---|
| Sensible a la sequía (helechos, hortensias, la mayoría de anuales) | 0.35 |
| Media (por defecto) | 0.50 |
| Tolerante / crasas / mediterráneas leñosas | 0.70 |

**Cantidad recomendada**: `S` litros hasta capacidad, redondeado al medio litro, y siempre con el consejo cualitativo "hasta que salga agua por los agujeros".

**Predicción**: se proyecta la misma recursión sobre el pronóstico de 7–16 días de Open-Meteo → el primer día con `d ≥ u` es el "próximo riego previsto" del plan semanal.

### 6.7 Balance — SUELO (en mm, FAO-56 cap. 8)

```
TAW = AWC · Zr                            // ec. 82; AWC en mm/m: arena franca 90, arcillo-limoso 120, limo 170, franco ≈150
p   = clamp(p_tab + 0.04 · (5 − ETc_i), 0.1, 0.8)     // ajuste FAO-56 de p por demanda
RAW = p · TAW                             // ec. 83

// ec. 85 simplificada: CR = 0, DP y RO se resuelven por saturación
Dr_i = clamp( Dr_{i−1} − R_i − I_i + ETc_i , 0 , TAW )
```

`p_tab` por defecto **0.5** (FAO-56 da 0.30 para raíces someras con ETc alto y 0.70 para raíces profundas con ETc bajo; el ajuste por ETc de arriba recoge esa dependencia).

**Disparo de riego**: cuando `Dr_i ≥ RAW`. **Cantidad**: `Dr_i` mm sobre el área regada.

**Coeficiente de estrés** (para el texto del consejo, no para el disparo), FAO-56 ec. 84:

```
Ks = 1.0                                      si Dr ≤ RAW
Ks = (TAW − Dr) / (TAW · (1 − p))             si Dr > RAW
```

`Ks < 0.6` → el consejo debe subir de tono ("estrés hídrico probable").

### 6.8 Fallback sin ET0 (offline / API caída)

Hargreaves-Samani, FAO-56 ec. 52, con Ra calculada por astronomía (FAO-56 Anexo 2, ec. 21–25), sin necesidad de ningún dato externo más que lat, día del año y las temperaturas:

```
J   = día del año (1..365)
dr  = 1 + 0.033 · cos(2π J / 365)
δ   = 0.409 · sin(2π J / 365 − 1.39)
ωs  = arccos(−tan φ · tan δ)                          // φ = latitud en radianes
Ra  = (24·60/π) · 0.0820 · dr · [ ωs·sin φ·sin δ + cos φ·cos δ·sin ωs ]   [MJ m⁻² d⁻¹]
Ra_mm = 0.408 · Ra                                     [mm/d]

ET0_HS = 0.0023 · (Tmedia + 17.8) · √(Tmax − Tmin) · Ra_mm
```

**Marcar el resultado como estimado** en la UI. No usar en invierno para decisiones (RMSE relativo > 30 %, [NHESS 20, 859](https://nhess.copernicus.org/articles/20/859/2020/)); en invierno degradar a consejo genérico.

### 6.9 Supuestos declarados

1. El **diario del usuario es la verdad**: cada riego registrado resetea el depósito a lleno. Si el usuario no registra, el modelo deriva y hay que decírselo.
2. **WUCOLS (California) se extrapola a España peninsular** por analogía de clima mediterráneo. No validado por la fuente.
3. El **70 % del volumen nominal de la maceta** es sustrato.
4. El sustrato tiene **25 % v/v de agua fácilmente disponible** (turba de calidad). Sustratos compactados o "tierra de saco" pueden bajar a 15 % o menos.
5. Se ignora la **capa freática colgada** y el efecto de la altura del tiesto, aunque la fuente USDA-ARS lo documenta como significativo.
6. Se ignora el **ascenso capilar** (CR = 0) — correcto para maceta, aproximado para suelo.
7. La lluvia se reparte uniformemente; no hay escorrentía superficial modelada en suelo (se absorbe en el clamp a TAW).
8. Se descartan **1,5 mm por evento** como interceptados por el dosel — número elegido, no medido.
9. El ET0 de Open-Meteo en el punto de la vivienda representa el microclima del jardín. **No lo hace**: patios, muros y sombras cambian la demanda localmente; eso es lo que `kmc` intenta absorber, groseramente.

### 6.10 Límites declarados — qué NO puede saber este modelo

- **El estado real del sustrato.** Sin sensor, el balance es una simulación abierta. Basta un riego no registrado, una fuga en el tiesto o una compactación para desviarlo. **Mitigación**: pedir confirmación periódica ("¿estaba seco cuando regaste?") y ofrecer un botón de "reset: acabo de comprobar y está húmedo/seco".
- **La salud del sistema radicular.** Raíces enrolladas, pudrición o cepellón deshidratado hidrofóbico (que repele el agua y la deja escurrir por las paredes) cambian todo y son invisibles al modelo.
- **La exposición real al sol.** El número de horas de sol directo que recibe *ese rincón* no está en ninguna API. `kmc` es un parche de tres valores.
- **El riego por debajo de la superficie** (platos, autorriego, mechas).
- **Especies con fisiología distinta**: crasas y CAM no siguen la lógica de ET0 (transpiran de noche, resisten agotamiento total). El modelo debe **desactivarse** para ellas y dar solo el calendario estacional.
- **Plantas recién trasplantadas o en establecimiento**: consumo y volumen radicular en flujo; el modelo no aplica durante ~4–8 semanas.
- **Interior**: fuera de alcance por decisión del mapa. El clima exterior no entra.

**Cuándo degradar a consejo genérico en vez de dar un número** — reglas explícitas:

| Condición | Acción |
|---|---|
| Faltan `V_pot`, `D_dosel` o categoría WUCOLS | No dar litros. Solo "toca revisar" + cómo comprobar con el dedo |
| Especie crasa / CAM / bulbo en reposo | Desactivar balance. Calendario estacional |
| Menos de 8 semanas desde trasplante | Consejo de establecimiento, no balance |
| ET0 procede de Hargreaves-Samani **y** es invierno | Consejo cualitativo, sin cifra |
| Más de 14 días sin ningún registro en el diario | Marcar el balance como "no fiable", pedir comprobación manual |
| Alerta de helada activa | El riego pasa a segundo plano; prevalece el consejo de protección |

**Y en todos los casos**: el número va acompañado del test del dedo. Es gratis, es más fiable que el modelo, y mantiene al usuario calibrando el sistema.

### 6.11 Plan por fases

**v1 — el depósito (ya con ET0).** No hay razón para posponer ET0: Open-Meteo la sirve gratis y sin clave, y el coste de integración es una llamada HTTP. v1 implementa §6.2–6.7 con parámetros por defecto agresivamente simples: `ks` de WUCOLS, `kd = kmc = 0.75` (medios), `θ_EAW = 0.25`, `u = 0.5`. Alertas de helada/calor desde `Tmax`/`Tmin`. Sin calibración.

**v2 — geometría y microclima.** Se abren `kd`, `kmc`, `f_lluvia` y la tolerancia `u` como campos editables de la ficha de planta. Se añade la proyección a 7–16 días para el plan semanal y el "próximo riego previsto". Se añade el fallback Hargreaves-Samani (§6.8) para funcionamiento offline.

**v3 — calibración con el diario.** Cuando haya histórico suficiente, comparar el intervalo de riego que **predice** el modelo con el que el usuario **practica** de hecho, y ajustar un factor de corrección por planta:

```
c_planta = mediana( intervalo_real / intervalo_predicho )
ET_corregida = ET_modelo / c_planta
```

Es un solo parámetro por planta, interpretable, acotado a [0.5, 2.0], y convierte el diario en la señal de realimentación que sustituye al sensor que no hay. **Esta es la fase que más precisión aporta por línea de código**, y solo es posible porque el diario ya es el estado del sistema.

**Fuera de plan (declarado como no-objetivo)**: dual Kc (Kcb + Ke, FAO-56 cap. 7), fenología por etapas con Kc ini/mid/end, y ecuaciones 97/98 de cobertura de suelo. Aportan precisión en el término que ya no domina el error (§5).

---

## Fuentes

**Primarias — FAO**
- FAO Irrigation and Drainage Paper 56, *Crop evapotranspiration* — [índice](https://www.fao.org/4/x0490e/x0490e00.htm)
  - [Cap. 2 — Ecuación FAO Penman-Monteith](https://www.fao.org/4/x0490e/x0490e06.htm) (ec. 6, superficie de referencia)
  - [Cap. 3 — Datos meteorológicos](https://www.fao.org/4/x0490e/x0490e07.htm) (ec. 48, 50, 52; datos faltantes)
  - [Cap. 6 — Kc simple](https://www.fao.org/4/x0490e/x0490e0b.htm) (ec. 58, 62, 65; Tabla 12)
  - [Cap. 7 — Kc dual](https://www.fao.org/4/x0490e/x0490e0c.htm) (ec. 73, 74; Tabla 19; regla 0.2·ET0; fw)
  - [Cap. 8 — ETc bajo estrés hídrico](https://www.fao.org/4/x0490e/x0490e0e.htm) (ec. 82, 83, 84, 85; TAW, RAW, p, Ks)
  - [Cap. 9 — Vegetación no típica](https://www.fao.org/4/x0490e/x0490e0f.htm) (ec. 97, 98; ausencia de ornamentales)
- FAO Irrigation and Drainage Paper 25, *Effective rainfall in irrigated agriculture* — [Cap. II, medición de la lluvia efectiva](https://www.fao.org/4/x5560e/x5560e03.htm) (método USDA-SCS, Tablas 7 y 8)

**Primarias — fuentes de datos**
- [Open-Meteo — documentación de la API de predicción](https://open-meteo.com/en/docs) · [términos y licencia](https://open-meteo.com/en/terms) (CC-BY 4.0, 10.000 llamadas/día)
- [AEMET OpenData](https://www.aemet.es/en/datos_abiertos/AEMET_OpenData) · [portal](https://opendata.aemet.es/)
- [MAPA — SiAR, presentación](https://www.mapa.gob.es/es/desarrollo-rural/temas/gestion-sostenible-regadios/sistema-informacion-agroclimatica-regadio/presentacion) · [portal web SiAR](https://eportal.mapa.gob.es/websiar/)
- [AgERA5 — Reference evapotranspiration diaria, catálogo FAO](https://data.apps.fao.org/catalog/iso/f22813e9-679e-4864-bd92-d48f5dfc436c)

**Primarias — ornamentales y contenedor**
- [University of California ANR — Simplified Landscape Irrigation Demand Estimation](https://ucanr.edu/sites/default/files/2016-09/248814.pdf) (KL = ks·kd·kmc y valores numéricos)
- [WUCOLS — Water Use Classification of Landscape Species, UC ANR / UC Davis](https://ucanr.edu/sites/WUCOLS)
- [USDA-ARS / HortScience — *Water and Air Relations in Propagation Substrates*, DOI 10.21273/HORTSCI14145-19](https://www.ars.usda.gov/ARSUserFiles/50820500/GPRG/2019PublicationsandSummaries/2019_Water%20and%20Air%20Relations%20in%20Propagation%20Substrates.pdf) (capacidad de contenedor, AFP, EAW, capa freática colgada)
- [*Water Conserving Irrigation Practices, Plant Growth, Seasonal Crop Coefficients, and Nutrition of Container-Grown Woody Ornamentals*, Water 2019, 11(10), 2070](https://doi.org/10.3390/w11102070) (DWU 8,8–17,3 mm/d; Kc de contenedor <1 a >5)

**Primarias — validación en España**
- [NHESS 20, 859–878 (2020) — *Estimation of evapotranspiration by FAO Penman-Monteith temperature and Hargreaves-Samani models... Duero basin (Spain)*](https://nhess.copernicus.org/articles/20/859/2020/)

**Secundarias — usadas solo como indicación, no verificadas contra el original**
- *Assessing Reference Evapotranspiration by the Hargreaves Method in Southern Spain* (cifras de sesgo costa/interior obtenidas de resumen; el PDF original no fue accesible)
- Metadatos editoriales de Water 2019, 11(10), 2070 — el texto completo devolvió HTTP 403; las cifras de DWU y Kc proceden del resumen del editor y deberían reverificarse antes de fijar constantes en código.
