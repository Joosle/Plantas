# Acolchado en el balance hídrico: el modo suelo

> Investigación para [#20](https://github.com/Joosle/Plantas/issues/20). Mitad "suelo" del ticket.
> La mitad "maceta y caducidad" está en [`acolchado-maceta-y-persistencia.md`](acolchado-maceta-y-persistencia.md).
>
> Contexto: el modelo hídrico ya decidido vive en [`modelo-agronomico-riego.md`](modelo-agronomico-riego.md).
> Este documento sólo toca la sección **6.7 (balance SUELO, en mm)**.

## Resumen de lo que dicen las fuentes

1. **FAO-56 sí modela el acolchado, y da un número.** No está en el capítulo del coeficiente dual, que es donde uno lo busca — está en el **capítulo 11**, sobre ET en periodos sin cultivo. La regla es: *reducir `TEW` un **5 % por cada 10 %** de superficie de suelo efectivamente cubierta por un acolchado orgánico* (y análogamente `Kc ini`). A cobertura total, eso es **−50 % del agua evaporable**, no −100 %: la propia FAO satura el efecto en la mitad.

2. **El acolchado toca `E`, no `T`.** Reduce la evaporación directa desde el suelo; no toca la transpiración de la planta. Por tanto **su techo no es `ETc`, sino la fracción de `ETc` que es evaporación de suelo**. En olivar mediterráneo esa fracción medida ronda el **23 %** en verano y sube mucho en plantaciones jóvenes o de dosel ralo.

3. **Multiplicar `KL` sería un error de modelado**, y es justo la tentación obvia. `KL` agrega `E` y `T`; un multiplicador sobre `KL` recorta también la transpiración, que el acolchado no toca. Si se engancha, hay que engancharlo sobre la componente evaporativa.

4. **El hallazgo incómodo**: en un verano mediterráneo sin riego y sin lluvia, la capa superficial ya está seca — `De ≥ TEW`, luego `Kr = 0` y `E ≈ 0`. **El acolchado no puede ahorrar una evaporación que ya no está ocurriendo.** Su trabajo real es conservar la lluvia de otoño-invierno hacia la primavera, que es exactamente la estación en la que estas plantas no están estresadas y el motor no iba a mandar regar de todos modos.

5. **Tiene signo negativo en lluvias pequeñas.** El propio acolchado intercepta y retiene agua que antes llegaba al suelo. Las cifras publicadas van de **11–23 % de la lluvia bruta retenida** en dosis moderadas hasta absorción **total** de un chubasco de 25 mm en capas gruesas. En clima mediterráneo, dominado por eventos pequeños, esto no es un detalle.

**Conclusión adelantada**: el verbo `mulch` **sí tiene un enganche defendible y con fuente** (la regla de FAO-56 cap. 11), así que no incumple la regla de admisión del [ADR 0003](../adr/0003-regla-de-admision-del-vocabulario-del-diario.md) — pero el efecto sobre *la decisión de regar* en este inventario concreto es **pequeño y a contratiempo**. Ver [Recomendación para el modelo](#recomendación-para-el-modelo).

---

## 1. Qué dice FAO-56, exactamente

### 1.1 El coeficiente dual, que es donde vive `E`

FAO-56 cap. 7 parte `Kc` en dos:

```
Kc = Kcb + Ke                      // basal (transpiración) + evaporación de suelo
Ke = Kr · (Kc_max − Kcb) ≤ few · Kc_max        // ec. 71
```

La evaporación se agota desde un depósito superficial propio, independiente del depósito radicular:

```
TEW = 1000 · (θ_FC − 0.5 · θ_WP) · Ze          // ec. 73   [mm]
Ze  = 0.10 … 0.15 m                             // capa evaporante
Kr  = (TEW − De,i−1) / (TEW − REW)              // ec. 74, etapa 2
```

`REW` (agua fácilmente evaporable, etapa 1 sin restricción), tabla 19:

| Textura | `REW` [mm] |
|---|---|
| Arena | 2–7 |
| Arena franca | 4–8 |
| Franco-arenoso | 6–10 |
| Franco | 8–10 |
| Franco-limoso / limo | 8–11 |
| Franco-arcillo-limoso | 8–11 |
| Arcillo-limoso | 8–12 |
| Arcilla | 8–12 |

`few` es la fracción de suelo **expuesta y humedecida**, de donde sale casi toda la evaporación.

La mecánica importa para lo que viene: **`E` no depende de cuánta agua haya en el perfil, sino de cuánta quede en los primeros 10–15 cm**, y se apaga sola (`Kr → 0`) cuando esa capa se seca.

### 1.2 Dónde aparece el acolchado

No en el cap. 7. Está en el **cap. 11 — "ETc during non-growing periods"**, sección *Surface covered with dead vegetation*:

> "Where the ground surface has a plant residue or other dead organic mulch cover […] then the surface will respond similarly to a surface covered by mulch."

> "In this case, Kc can be set equal to Kc ini as predicted from figures 29 and 30, but the value for **Kc ini can be reduced by about 5% for each 10% of soil surface that is effectively covered by an organic mulch**."

> "Therefore, total evaporation losses will be less than the TEW predicted from Equation 73. This can be accounted for by **reducing the value for TEW by 5% for each 10% of soil surface that is effectively covered by an organic mulch**."

Es decir, con `C` = fracción de suelo cubierta (0..1):

```
TEW_mulch = TEW · (1 − 0.5 · C)          // FAO-56 cap. 11
```

**Tres lecturas que conviene no saltarse:**

- El efecto **satura en −50 %** aunque la cobertura sea total. FAO no acepta que un acolchado anule la evaporación.
- Reduce el **depósito evaporable**, no la tasa instantánea. Físicamente: bajo el acolchado el suelo se seca **más despacio y hasta menos profundidad**, no "deja de evaporar".
- Es una regla **para acolchado orgánico**. FAO trata el plástico aparte y con otros números (cap. 6, nota 16 de la tabla 12, para piña) — no aplica aquí.

**Confianza**: alta. Fuente primaria, vigente, y es *la* referencia normativa del resto del modelo de este proyecto.

---

## 2. El puente al modelo agregado

El problema real de este proyecto: la sección 6.7 **no tiene `TEW`**. Tiene un depósito único (`Dr`, `TAW`, `RAW`) y un coeficiente **agregado** `KL = ks · kd · kmc` de UC ANR, que mezcla `E` y `T` en un solo número. La regla de FAO-56 apunta a una variable que no existe en nuestro modelo.

Opciones evaluadas:

### (a) Multiplicador sobre `KL` o sobre `ETc` — **rechazada tal cual**

```
ETc = KL · ET0 · (1 − 0.5 · C)        // ✗
```

Es la más fácil y la más equivocada. `KL` incluye la transpiración, y **el acolchado no reduce la transpiración**: la planta sigue tirando del agua del perfil profundo, que el acolchado no toca. Aplicar −50 % aquí exagera el ahorro por un factor de ~4 (ver §3). Rechazada.

### (b) Reducir `TEW` — **correcta pero exige abrir el dual**

Es literalmente lo que dice FAO. Requiere partir `KL` en `Kcb + Ke`, mantener un segundo depósito superficial (`De`, `TEW`, `REW`, `few`, `Kr`) y actualizarlo a diario. Eso es **doblar el modelo hídrico** —y `KL` de UC ANR no viene partido en basal y evaporativo, así que habría que inventar el reparto, que es justo el número que no tenemos con fuente. El coste no lo paga el beneficio (§3). Rechazada por ahora, no por incorrecta.

### (c) Tocar `p` / `RAW` — **rechazada, es un error conceptual**

`p` es la fracción de agotamiento tolerable **antes de que la planta sufra estrés**: es un parámetro de la *planta*, no de la superficie del suelo. El acolchado no cambia cuánto aguanta un olivo antes de estresarse. Usar `p` como palanca metería el efecto en el sitio equivocado y contaminaría además el `Ks` del texto del consejo. Rechazada.

### (d) Tocar la lluvia efectiva — **necesaria, pero de signo contrario**

Es real y va **en contra** del ahorro: ver §4. No es la palanca del beneficio, es la corrección que acompaña a cualquiera de las anteriores.

### (e) La recomendada: multiplicador sobre `ETc` **acotado por la fracción evaporativa**

Conserva la forma agregada del modelo y respeta la física, a cambio de un parámetro declarado (`f_E`, la fracción de `ETc` que es evaporación de suelo):

```
ETc_mulch = KL · ET0 · (1 − f_E · 0.5 · C)
```

Con `C = 1` (acolchar es un acto deliberado: se cubre el alcorque entero) y `f_E ≈ 0.25` (§3):

```
ETc_mulch ≈ ETc · 0.875          // −12,5 %
```

Es la traducción mínima honesta de la regla de FAO-56 a un modelo de un solo depósito. **El error que introduce**: trata el ahorro como constante a lo largo del año, cuando en realidad se concentra en los días siguientes a una lluvia y es **cero** cuando la capa superficial ya está seca (§5). Sobreestima en verano y subestima justo tras la lluvia.

---

## 3. La magnitud: cuánto puede ahorrar como máximo

El techo del acolchado es `f_E`, la fracción de `ETc` que es evaporación de suelo. Medidas en olivar mediterráneo:

| Situación | `E` como fracción de ET | Fuente |
|---|---|---|
| Olivar intensivo, verano mediterráneo (microlisímetros, SE Portugal) | **23 %** | Paço et al., *Water* 11(12):2529 |
| Olivar en goteo, adulto (36 % cobertura de suelo), estacional | 4–12 % | Bonachela et al., *Irrigation Science* |
| Olivar en goteo, joven (5 % cobertura), estacional | 18–43 % | ídem |

> **Salto inferencial declarado.** Todas estas cifras son de **olivar productivo, regado o en secano de plantación**, medidas sobre superficie de suelo desnudo entre calles. Trasladarlas a *un* olivo ornamental aislado en un jardín doméstico supone que el reparto `E`/`T` depende sobre todo de la cobertura de dosel, no del manejo. Es un supuesto razonable —es el mecanismo físico— pero **no está medido para este caso**.
>
> El inventario juega en contra de la cifra baja: un ejemplar aislado tiene cobertura de dosel **local** alta bajo la copa pero el suelo alrededor expuesto; el rango 18–43 % de la plantación joven de dosel ralo probablemente describe mejor el alcorque de un ornamental que el 4–12 % del olivar adulto cerrado.

**Valor recomendado**: `f_E = 0.25`, con rango honesto **0.15–0.40**.

Combinado con la regla de FAO (`−0.5 · C`), el ahorro máximo sobre `ETc` queda:

| `f_E` | Ahorro sobre `ETc` a cobertura total |
|---|---|
| 0.15 | −7,5 % |
| **0.25** | **−12,5 %** |
| 0.40 | −20 % |

Para contraste, las cifras que circulan en divulgación de jardinería —"el acolchado reduce la evaporación un 70 %", "reduce el riego hasta un 50 %"— **no son comparables**: hablan de la evaporación *de la superficie desnuda*, no de la ET total de la planta, y proceden de fuentes de extensión sin la medida detrás. La confusión entre las tres magnitudes (**reducción de `E` ≠ reducción de `ET` ≠ ahorro de riego**) es el error más común de la literatura divulgativa sobre acolchado, y es exactamente el error que este proyecto no debe cometer al escribir el consejo al usuario.

### 3.1 Espesor mínimo eficaz

El efecto no es binario: depende del espesor de la capa. WSU Extension (Chalker-Scott, FS160E, *Using Arborist Wood Chips as a Landscape Mulch*) recomienda **10–15 cm (4–6")** de astilla de arbolista para supresión de adventicias y retención de humedad plenas, y admite que **5 cm (2")** puede bastar en climas de lluvia frecuente o veranos frescos — lo cual **no** describe el clima mediterráneo peninsular.

> **Salto inferencial declarado.** FS160E es de clima del Pacífico Noroeste. Su recomendación de espesor se traslada aquí porque el mecanismo (romper la continuidad capilar y el intercambio turbulento en superficie) no es climático, pero la advertencia "en climas más lluviosos 5 cm bastan" se lee al revés en España: **aquí el extremo alto del rango es el relevante**.

Consecuencia de diseño: por debajo de ~5 cm el efecto se pierde. Esto es lo que hace que el acolchado tenga **caducidad**, y se trata en el documento hermano.

---

## 4. El efecto de signo contrario: intercepción de lluvias pequeñas

El acolchado es una esponja intercalada entre la lluvia y el suelo. El agua que retiene se evapora **desde el acolchado**, sin llegar nunca a la zona radicular.

Cifras publicadas:

- Con dosis moderada (8 t/ha): **11–23 % de la lluvia bruta retenida**, 7–16 % absorbida; al aumentar la densidad, retención y absorción **más que se duplican**.
- En capas gruesas de material fino (15 cm de serrín), un chubasco de **25 mm puede quedar retenido íntegramente**, con penetración al suelo dudosa.

Esto importa mucho aquí por dos razones:

1. **El modelo ya tiene el mecanismo.** La sección 6.5 descarta chubascos con `P_i < 0.2 · ET0_i` y resta 1,5 mm de intercepción por dosel. El acolchado **sube ese umbral y ese sustraendo** — no necesita maquinaria nueva.
2. **El clima mediterráneo está hecho de eventos pequeños.** Buena parte de la precipitación anual llega en episodios de pocos mm, que son precisamente los que un acolchado se traga enteros. En un año de lluvias débiles y frecuentes, el balance neto del acolchado sobre el agua que llega a la raíz **puede ser negativo**.

**Recomendación**: si se modela el ahorro de `E`, es obligatorio modelar también la penalización de intercepción, o el modelo mentirá sistemáticamente a favor del acolchado.

```
// con acolchado activo
interceptación: P_util = max(0, P_i − 4.0)       // frente a 1.5 mm sin acolchar
```

> **Salto inferencial declarado, y el más flojo de este documento.** El valor 4,0 mm es **construido**, no citado: parte del 1,5 mm de dosel que ya usaba el modelo y le suma la capacidad de retención de una capa de astilla de ~10 cm inferida de los porcentajes de arriba. **No hay fuente que dé este número para acolchado de corteza en jardín doméstico mediterráneo.** Confianza baja. Si se implementa, va marcado como parámetro estimado.

---

## 5. El hallazgo que decide el ticket: el ahorro llega a destiempo

Esto no estaba en la pregunta y es lo que más pesa en la respuesta.

La evaporación de suelo es **limitada por la oferta**. En la mecánica de FAO-56 (§1.1), en cuanto el agotamiento de la capa superficial alcanza `TEW`, `Kr = 0` y `E = 0`: el suelo seco es su propio acolchado. En España peninsular, un suelo sin regar en julio lleva semanas con los primeros 15 cm secos.

De ahí:

- **En verano, sin riego y sin lluvia, `E ≈ 0` con acolchado y sin él.** El ahorro del acolchado es ~0 justo en la estación en que el motor decide si regar.
- **El ahorro real ocurre en las semanas siguientes a cada lluvia**, sobre todo en otoño-invierno-primavera — la estación en que el depósito está lleno, `Dr` está lejos de `RAW` y **el motor no iba a mandar regar de ninguna manera**.

Ahora crúcese con el inventario ([`inventario-plantas.md`](../inventario-plantas.md)) y con el hallazgo de [#2](https://github.com/Joosle/Plantas/issues/2): las 3 plantas en suelo son **ejemplares arraigados que hoy no se riegan**. Para ellas, el balance hídrico no manda ninguna acción; sirve, como mucho, para el tono del consejo vía `Ks`.

**Por tanto: el acolchado es agronómicamente bueno para estas plantas —conserva la reserva invernal, que es de donde tiran en verano— pero su efecto sobre la salida que el motor produce hoy es próximo a nulo.** El valor de modelarlo aparece el día que haya una planta en suelo que sí se riegue, o que se quiera un `Ks` fino de estrés estival.

---

## 6. Otros efectos, y si el modelo debe capturarlos

| Efecto | ¿Real? | ¿Al modelo? |
|---|---|---|
| Reducción de evaporación de suelo | Sí, con regla FAO citable | **Sí**, es el único enganche defendible |
| Intercepción de lluvias pequeñas | Sí, y en contra | **Sí**, obligatorio si se modela el anterior |
| Supresión de adventicias que competían por agua | Sí | **No.** El modelo no representa adventicias; en jardín doméstico su consumo ya está implícito en `kd`. Nota cualitativa en el consejo, no ecuación |
| Moderación de temperatura del suelo | Sí | **No.** El modelo no tiene temperatura de suelo. Podría entrar algún día por el lado de la protección ante helada, no por el balance hídrico |
| Mayor infiltración / menos escorrentía | Sí | **No.** La sección 6.7 ya resuelve escorrentía por saturación y `CR = 0`. Meterlo exigiría un modelo de infiltración que no existe |
| Aporte de materia orgánica, cambio de `AWC` a largo plazo | Sí, en años | **No.** Fuera de la escala temporal del modelo |

---

## Recomendación para el modelo

**`mulch` sobrevive a la regla de admisión del [ADR 0003](../adr/0003-regla-de-admision-del-vocabulario-del-diario.md), pero por poco, y no por donde se creía.**

Hay número citable y de fuente primaria (FAO-56 cap. 11: `TEW` −5 % por cada 10 % de cobertura), así que **no procede retirar el verbo**: la deuda que [#9](https://github.com/Joosle/Plantas/issues/9) cobró contra [#6](https://github.com/Joosle/Plantas/issues/6) se puede pagar. Lo que la investigación cambia es **cuánto vale pagarla ahora**.

### La ecuación propuesta (modo suelo únicamente)

```
// Estado: la planta tiene acolchado activo (ver documento hermano para la caducidad)

f_E  = 0.25              // fracción de ETc que es evaporación de suelo; rango 0.15–0.40
C    = 1.0               // cobertura del alcorque; acolchar es acto deliberado
m    = 1 − f_E · 0.5 · C = 0.875

// Demanda (sección 6.4, modo suelo)
ETc_i = KL · ET0_i · m

// Lluvia efectiva (sección 6.5) — la penalización que acompaña
si P_i < 0.2 · ET0_i  →  P_util = 0
si no                 →  P_util = max(0, P_i − 4.0)      // 1.5 mm sin acolchar
```

`p`, `RAW`, `TAW` y `Ks` **no se tocan**.

### Parámetros por defecto

| Parámetro | Valor | Confianza |
|---|---|---|
| Reducción de `TEW` por cobertura total | −50 % | **Alta** — FAO-56 cap. 11, literal |
| `f_E` (fracción evaporativa de `ETc`) | 0.25 (0.15–0.40) | **Media** — medido en olivar, extrapolado a ornamental aislado |
| Efecto neto sobre `ETc` | **−12,5 %** | **Media** |
| Intercepción con acolchado | 4,0 mm | **Baja** — número construido, sin fuente directa |
| Espesor mínimo para que aplique | ≥ 5 cm | **Media** — WSU FS160E, clima distinto |

### Límites declarados — qué NO puede saber este modelo

- **No sabe si el acolchado está bien puesto.** Espesor, material y contacto con el tronco no se preguntan. Se asume una capa razonable; un acolchado de 2 cm no hace nada y el modelo no se enterará.
- **Reparte el ahorro uniformemente en el año**, cuando físicamente se concentra tras las lluvias y es nulo con la capa superficial seca (§5). Es el error conocido de plegar el dual a un multiplicador constante.
- **`f_E` no está medido para un ornamental aislado**, sólo extrapolado de olivar.
- **La intercepción es una estimación sin fuente.** Es el eslabón débil, y va en contra del efecto principal — es decir, el modelo puede estar restando un beneficio real con un número inventado.
- **No modela plástico ni acolchado mineral** (gravilla, volcánica). La regla de FAO-56 citada es explícitamente para orgánico. La gravilla no se degrada, así que además rompería la caducidad del documento hermano.

### Fase recomendada

**Fase 2, no fase 1.** Razón: por §5, con el inventario actual —3 plantas en suelo que no se riegan— el efecto sobre la salida del motor es prácticamente nulo, y el eslabón más flojo (la intercepción) puede anularlo. El verbo se **conserva** en el vocabulario y se **registra** desde el día uno; la ecuación entra cuando exista una planta en suelo bajo riego, o cuando se quiera afinar el `Ks` del texto estival.

Alternativa honesta y más barata que conviene poner sobre la mesa al decidir: **declarar hoy `mulch` como verbo registrado-pero-no-leído**, con esta investigación como justificación de por qué se queda, y ascenderlo a leído cuando llegue esa planta. Eso obligaría a matizar el ADR 0003, que hoy lo justifica por "el motor lo lee".

---

## Fuentes

- **FAO-56** — Allen, R.G., Pereira, L.S., Raes, D., Smith, M. (1998). *Crop evapotranspiration — Guidelines for computing crop water requirements*. FAO Irrigation and Drainage Paper 56.
  - Índice: https://www.fao.org/4/x0490e/x0490e00.htm
  - Cap. 6 — ETc, coeficiente único `Kc`: https://www.fao.org/4/x0490e/x0490e0b.htm
  - Cap. 7 — ETc, coeficiente dual (`TEW` ec. 73, `Kr` ec. 74, `Ke` ec. 71, tabla 19 `REW`): https://www.fao.org/4/x0490e/x0490e0c.htm
  - **Cap. 11 — ETc en periodos sin cultivo (la regla del acolchado)**: https://www.fao.org/4/x0490e/x0490e0h.htm
- **Paço, T.A. et al.** (2019). *Measuring and Modelling Soil Evaporation in an Irrigated Olive Orchard to Improve Water Management*. Water 11(12):2529. https://www.mdpi.com/2073-4441/11/12/2529
- **Bonachela, S. et al.** *Soil evaporation from drip-irrigated olive orchards*. Irrigation Science. https://link.springer.com/article/10.1007/s002710000030
- **Chalker-Scott, L.** *Using Arborist Wood Chips as a Landscape Mulch*. WSU Extension Fact Sheet FS160E. https://pubs.extension.wsu.edu/product/using-arborist-wood-chips-as-a-landscape-mulch-home-garden-series/
- **Quantifying the hydrological impact of soil mulching across rainfall regimes and mulching layer thickness**. Journal of Hydrology (2022). https://www.sciencedirect.com/science/article/abs/pii/S0022169422000981 *(consultado sólo por resumen indexado; el texto completo está tras muro de pago — de aquí salen los rangos de retención de lluvia del §4, con la reserva correspondiente)*
- **IFAPA / Junta de Andalucía** — *Guía de cubiertas vegetales en olivar*: https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/26479626-ed74-411b-9ac8-0da05770418c/download — *consultada; describe beneficios de cubierta y restos de poda (más infiltración, menos escorrentía y evaporación) en términos **cualitativos**, sin una cifra de reducción de evaporación trasladable. Se registra el hallazgo negativo: **no se encontró cifra oficial española de reducción de evaporación por acolchado en olivar**.*
