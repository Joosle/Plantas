# Acolchado en maceta, y cuánto dura un acolchado

> Investigación para [#20](https://github.com/Joosle/Plantas/issues/20). Mitad "maceta y caducidad" del ticket.
> La mitad "suelo" está en [`acolchado-suelo-fao56.md`](acolchado-suelo-fao56.md).
>
> Contexto: [`modelo-agronomico-riego.md`](modelo-agronomico-riego.md), secciones **6.6 (balance MACETA)** y **6.10 (límites declarados)**.

## Resumen de lo que dicen las fuentes

1. **En maceta, el acolchado no hace nada medible.** Y no es una inferencia: hay un experimento con lisímetros diseñado exactamente para esta pregunta (Gilman, Beeson y Meador, 2012). Resultado: **ninguna diferencia de evaporación entre contenedores acolchados y sin acolchar** en los tres días posteriores al riego. El primer día seco los acolchados evaporaron **más**.

2. **La razón es que en maceta manda la transpiración.** Un segundo estudio, sobre tuya gigante en contenedor, lo dice sin rodeos: el acolchado no afectó al contenido de agua del sustrato, *"lo que subraya que la transpiración era la componente principal de la pérdida de agua del sustrato"*.

3. **La geometría del aloe lo remata.** La boca del tiesto son 0,049 m². Cualquier efecto se aplica sobre una superficie minúscula, y compite contra la pérdida por las paredes y el drenaje, que el modelo tampoco representa.

4. **Muy por debajo del ruido ya declarado.** La sección 6.10 del modelo declara **±50 %** de incertidumbre en modo maceta por desconocimiento del sustrato. Un efecto que la mejor medida disponible no distingue de cero no compite con eso.

5. **Un acolchado sí caduca, y en escala de 1–3 años.** Se degrada, se compacta y pierde espesor; por debajo de ~5 cm deja de trabajar. La vida útil depende del material (corteza fina 1–2 años; astilla gruesa 3–5), pero **las fuentes con cifras concretas de reposición son comerciales, no de extensión** — hallazgo negativo que conviene registrar.

**Conclusiones adelantadas**: **no modelar el acolchado en maceta**, y modelar `mulch` como **estado con vencimiento** (no como evento que decae), con **12 meses** por defecto.

---

## A. El modo maceta

### A.1 La medida directa

**Gilman, E.F., Beeson, R.C., Meador, D. (2012).** *Impact of Mulch on Water Loss from a Container Substrate and Native Soil.* Arboriculture & Urban Forestry 38(1):18–23.

Diseño: lisímetros de contenedores de 360 L rellenos de suelo nativo o de sustrato de vivero (60 % corteza de pino : 30 % turba : 10 % arena), cubiertos con acolchado de corteza de pino o descubiertos, midiendo la pérdida de agua tras el riego.

Resultados:

| Hallazgo | Cifra |
|---|---|
| Lisímetros con **suelo**: diferencia acolchado vs. no acolchado | **Ninguna**, en ningún periodo de tres días consecutivos tras el riego |
| Lisímetros con **sustrato**, primer día seco tras riego | El **acolchado evaporó más** que el descubierto |
| Lisímetros con **sustrato**, segundo día | El descubierto evaporó más (se compensa) |
| Evaporación acumulada a día 2, sustrato | **Idéntica** en ambos |
| Evaporación acumulada a día 3, sustrato | 0,5 L más en el no acolchado |

El resultado del **primer día** es el interesante y el contraintuitivo: el acolchado mojado por el propio riego evapora desde sí mismo, y ese agua ya no está disponible. Es el mismo mecanismo de intercepción que en suelo penaliza las lluvias pequeñas (§4 del documento hermano), pero en maceta se dispara **con cada riego**, no sólo con la lluvia. En un tiesto que se riega a menudo, esa penalización se cobra a menudo.

> **Salto inferencial declarado.** El experimento usa contenedores de **360 L** para simular un cepellón plantado; la maceta del inventario tiene ~**7,5 L de sustrato**. La relación superficie/volumen es mucho más desfavorable en la maceta pequeña, de modo que el peso relativo de la evaporación superficial **es mayor** ahí. Es decir: el estudio podría estar subestimando el efecto para nuestro caso. Aun así se recomienda no modelarlo, y las razones de A.2–A.4 sostienen esa recomendación con independencia de este salto.

### A.2 Qué fracción de la pérdida es evaporación en maceta

El segundo estudio ataca justo esto: *Effect of Mulching on Plant and Weed Growth, Substrate Water Content, and Temperature in Container-grown Giant Arborvitae*. Conclusión relevante: el acolchado **no alteró el contenido de agua del sustrato**, y los autores lo atribuyen a que **la transpiración era la componente dominante** de la pérdida.

Con planta vigorosa y dosel que sobresale del tiesto, la transpiración domina y la superficie de sustrato está además sombreada por el propio dosel — que ya es, gratis, medio acolchado.

**El caso del aloe merece una comprobación aparte**, porque la premisa "la transpiración domina" es justo la que podría no cumplirse:

- *Aloe vera* es **CAM**: abre estomas de noche, cuando el déficit de presión de vapor es bajo, y su transpiración es **muy inferior** a la de una planta C3 de dosel equivalente. El modelo ya lo recoge por la vía de `ks` (WUCOLS categoría muy baja) → `KL` pequeño.
- Con `T` pequeña, **la evaporación del sustrato pasa a ser una fracción proporcionalmente mayor** de la pérdida total. En una crasa en maceta, la superficie de sustrato expuesta puede perder tanto o más que la planta.

Así que, en contra de la conclusión del estudio de tuya, **para el aloe la componente evaporativa sí podría ser dominante**. Esto es lo más cerca que está el acolchado en maceta de justificarse.

> **Salto inferencial declarado.** No se ha encontrado ninguna medida del reparto `E`/`T` en *Aloe vera* en maceta. El razonamiento anterior es **mecanístico, no medido**: se apoya en que CAM implica menor transpiración diurna, hecho bien establecido, pero el reparto concreto en un tiesto de 25 cm no está publicado que se haya encontrado. **Confianza baja.**

### A.3 La competencia: paredes y drenaje

Antes de optimizar la superficie conviene ver contra qué compite:

- La maceta del inventario es de **terracota** — material poroso: una fracción sustancial de la pérdida ocurre **a través de la pared**, que tiene mucha más superficie que la boca. Un acolchado en superficie no toca esa componente en absoluto.
- El modelo (6.6) tampoco representa la pérdida por pared; está absorbida dentro del ±50 % declarado.
- La terraza es **suroeste**: la pared del tiesto recibe sol directo de tarde, que es el escenario que más favorece la pérdida por pared frente a la superficial.

Es decir: acolchar la superficie de este tiesto es **optimizar la componente pequeña mientras la grande queda sin modelar**. Añadir la primera al modelo sin la segunda no mejora la exactitud; la desplaza.

### A.4 Veredicto sobre maceta

| Criterio | Resultado |
|---|---|
| ¿Hay medida directa? | Sí, y da **cero diferencia** (Gilman et al. 2012) |
| ¿Supera el ±50 % declarado en 6.10? | **No, ni de lejos** |
| ¿Compite con componentes no modeladas (pared, drenaje)? | Sí, y pierde |
| ¿Hay una vía por la que podría importar? | Sí: crasa CAM de baja transpiración — pero **sin medir**, confianza baja |

**Recomendación: no modelar el acolchado en modo maceta.** La sección 6.6 no se toca. Si el usuario registra `mulch` sobre una planta en maceta, la entrada **se guarda** (el diario nunca rechaza lo que el usuario hizo de verdad) pero **no altera el balance**.

Esto conviene que sea explícito en el código y no un olvido: el efecto es **cero por decisión documentada**, no cero por no haberlo implementado.

---

## B. La caducidad

### B.1 El umbral, que es lo que convierte esto en un problema temporal

Del documento hermano (§3.1): WSU FS160E recomienda **10–15 cm** de astilla para efecto pleno y admite **5 cm** en climas frescos y lluviosos. Por debajo de ~5 cm el acolchado deja de romper la continuidad capilar y su efecto sobre la evaporación se pierde.

Un acolchado, por tanto, no "se gasta" de golpe: **se adelgaza hasta cruzar un umbral**, y ahí deja de contar. Eso es lo que hay que representar.

### B.2 Velocidad de degradación por material

| Material | Vida útil declarada | Reposición |
|---|---|---|
| Paja, restos herbáceos, hoja | Meses — la más rápida | Cada temporada |
| Corteza fina / triturado fino | **1–2 años** | Cada 1–2 años |
| Astilla de arbolista, corteza gruesa | **3–5 años** | Al bajar de ~7 cm |
| Gravilla, volcánica, mineral | No se degrada | Nunca (pero ver abajo) |

Práctica de reposición habitual: **añadir 2–5 cm de material fresco cada primavera**.

> **Hallazgo negativo, y hay que decirlo.** Estas cifras proceden mayoritariamente de **fuentes comerciales de paisajismo**, no de extensión universitaria ni de literatura revisada. No se ha encontrado una tasa de descomposición de acolchado orgánico **medida en clima mediterráneo peninsular**. Confianza **baja**, y el rango 1–5 años es amplio precisamente por eso.
>
> Dos correcciones cualitativas que sí se pueden razonar sobre ese rango, ambas de signo contrario: el verano mediterráneo, seco y caluroso, **frena** la descomposición microbiana (que necesita humedad) respecto a los climas templados húmedos de donde salen las cifras; pero la radiación UV intensa **acelera** la degradación física y el viento desplaza el material ligero. No se ha encontrado fuente que resuelva el neto.

### B.3 La forma temporal: evento que decae, estado con vencimiento, o nada

Las tres opciones del ticket:

**(a) Evento puntual con decaimiento continuo** (exponencial o lineal). Físicamente es lo más fiel: la capa adelgaza de forma gradual. Pero exige una **vida media con fuente**, y B.2 dice que no la hay con confianza decente: se estaría poniendo una curva precisa sobre un número que es un rango de 1 a 5 años. Da falsa precisión, y además obliga a mantener estado continuo por planta para un efecto que en el mejor caso vale −12,5 % de `ETc`. **Rechazada.**

**(b) Estado con vencimiento** — el acolchado está activo desde el `mulch` hasta que vence o hasta el siguiente `mulch`. Es un escalón, no una curva: menos fiel a la física, pero **honesto con la incertidumbre** (un umbral difuso representado como un umbral, no como una curva inventada) y, sobre todo, **produce una pregunta al usuario**, que es información nueva que entra al sistema. **Recomendada.**

**(c) Nada.** Sería consistente si el efecto en suelo también fuera cero, pero no lo es: hay regla FAO citable. **Rechazada** para suelo; es exactamente lo que se recomienda para maceta (§A.4).

### B.4 Por qué (b) encaja con lo que este proyecto ya hace

Es el patrón **"evidencia con caducidad"** que el mapa ya usa dos veces: [#4](https://github.com/Joosle/Plantas/issues/4) le puso escalera de degradación al clima rancio, y [#9](https://github.com/Joosle/Plantas/issues/9) la reutilizó para el silencio del diario. Un `mulch` es un hecho fechado que pierde validez con el tiempo, y cuya pérdida de validez **el sistema no puede observar** — sólo el usuario, mirando el suelo.

Al vencer, la respuesta idiomática no es apagar el efecto en silencio: es **preguntar**. Un ítem de checklist de un toque —"¿el acolchado del olivo sigue puesto y con grosor?"— que:

- si el usuario dice sí → renueva el vencimiento sin pedir un `mulch` nuevo;
- si dice no → desactiva el estado y, si procede, el plan puede mandar `mulch`, que ya es un verbo del vocabulario;
- y en cualquier caso **genera entrada de diario sola**, por el mecanismo del [ADR 0002](../adr/0002-plan-inmutable-y-diario-derivado-de-la-checklist.md): responder al ítem *es* registrar.

Esto además cierra un cabo suelto: el vocabulario tiene `mulch` como verbo que el **plan puede mandar**, no sólo que el motor lee. Con el vencimiento, `mulch` pasa a cumplir **las dos mitades** de la regla de admisión del [ADR 0003](../adr/0003-regla-de-admision-del-vocabulario-del-diario.md) — lo cual refuerza su permanencia en el vocabulario con independencia de lo flojo que resulte su efecto numérico.

### B.5 El número por defecto, y la fricción

**Recomendado: 12 meses.**

Justificación: cae dentro del rango de todos los materiales orgánicos plausibles salvo la paja; coincide con la práctica de reposición anual en primavera; y **una pregunta al año por planta acolchada es fricción despreciable** — con el inventario actual, como mucho 3 preguntas al año en total.

**No preguntar el material.** El proyecto minimiza la fricción del diario, y afinar 12 meses a 18 o a 36 según material compraría precisión sobre un efecto de −12,5 % cuya incertidumbre de partida ya es mayor que la corrección. Si algún día el usuario responde repetidamente "sigue puesto", el propio historial dirá que su acolchado dura más, sin haber preguntado nunca.

**Una excepción sí merece la pena a coste cero**: si el usuario acolcha con **material mineral** (gravilla, volcánica), no caduca nunca. Pero eso no es una pregunta nueva — es una nota libre en la entrada de `mulch`, que el cajón de texto libre ya admite siempre. No se diseña para ello hasta que aparezca.

---

## Recomendación para el modelo

### En maceta

**No se modela.** La sección 6.6 queda intacta. `CareAction(mulch)` sobre una planta en maceta se registra en el diario y **no altera el balance**, por decisión documentada (Gilman et al. 2012: diferencia no detectable; y muy por debajo del ±50 % ya declarado en 6.10).

### Forma temporal de `mulch` (aplicable al modo suelo)

```
// Estado derivado del diario, no columna persistida:
// una planta tiene acolchado activo si existe CareAction(mulch)
// con occurredAt dentro de los últimos MULCH_TTL

MULCH_TTL = 12 meses

acolchado_activo(planta, fecha) =
    existe CareAction(mulch) sobre planta
    con  fecha − occurredAt  <  MULCH_TTL

// Al vencer: el plan emite un ítem de confirmación de un toque.
//   "sigue puesto"  → renueva el vencimiento (nueva entrada de diario)
//   "ya no"         → estado inactivo; el plan puede mandar mulch
```

Coherente con que **el diario es el estado del sistema**: no hace falta guardar un flag, se deriva de las entradas — igual que el resto del modelo.

### Parámetros por defecto

| Parámetro | Valor | Confianza |
|---|---|---|
| Efecto del acolchado en maceta | **0** | **Alta** — medida directa que no lo distingue de cero |
| Forma temporal | Estado con vencimiento | **Alta** — decisión de diseño, coherente con #4 y #9 |
| `MULCH_TTL` | **12 meses** | **Baja-media** — el rango de fuentes es 1–5 años, y son comerciales |
| Espesor umbral bajo el cual no cuenta | ~5 cm | **Media** — WSU FS160E, clima distinto |
| ¿Se pregunta el material? | No | Decisión de fricción |

### Límites declarados

- **`MULCH_TTL` no tiene fuente fuerte.** Es el parámetro más flojo de los dos documentos. Se mitiga porque su vencimiento **no apaga nada en silencio: pregunta**, así que un valor equivocado cuesta una pregunta de más o de menos, no un error de balance.
- **No se sabe si el acolchado sigue puesto** salvo que el usuario lo diga. Ningún dato externo lo observa.
- **El caso de la crasa CAM en maceta queda sin resolver**: es la única vía por la que el acolchado en maceta podría importar, y no hay medida. Si algún día se quiere cerrar, es una medida casera (dos tiestos, una báscula, una semana), no una búsqueda bibliográfica más.
- **No cubre acolchado mineral ni plástico**, cuya caducidad es otra (ninguna, y por degradación UV, respectivamente).

---

## Fuentes

- **Gilman, E.F., Beeson, R.C., Meador, D.** (2012). *Impact of Mulch on Water Loss from a Container Substrate and Native Soil*. Arboriculture & Urban Forestry 38(1):18–23. https://hos.ifas.ufl.edu/woody/documents/articles/EFG2012a.pdf (resumen: https://hort.ifas.ufl.edu/woody/abstracts/efg/EFG2012a.shtm)
- ***Effect of Mulching on Plant and Weed Growth, Substrate Water Content, and Temperature in Container-grown Giant Arborvitae***. https://www.researchgate.net/publication/236593064
- **Efficacy of Bio-based Liquid Mulch on Weed Suppression and Water Conservation in Container Nursery Production**. Journal of Environmental Horticulture 35(3):103. https://jeh.kglmeridian.com/view/journals/jenh/35/3/article-p103.xml
- **Chalker-Scott, L.** *Using Arborist Wood Chips as a Landscape Mulch*. WSU Extension Fact Sheet FS160E. https://pubs.extension.wsu.edu/product/using-arborist-wood-chips-as-a-landscape-mulch-home-garden-series/
- **Chalker-Scott, L.** *Wood chip mulch: Landscape boon or bane?* WSU Puyallup. https://wpcdn.web.wsu.edu/wp-puyallup/uploads/sites/403/2015/03/wood-chips.pdf
- **CMG GardenNotes #245** — *Mulching*. Colorado State University Extension. https://cmg.extension.colostate.edu/Gardennotes/245.pdf
- **Hallazgo negativo registrado**: no se localizó ninguna fuente de extensión universitaria ni revisada por pares con **tasa de descomposición o vida útil de acolchado orgánico medida en clima mediterráneo peninsular**. Las cifras de 1–5 años del §B.2 provienen de literatura comercial de paisajismo y se marcan con confianza baja.
