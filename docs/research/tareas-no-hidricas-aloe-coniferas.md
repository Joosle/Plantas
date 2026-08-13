# Tareas no hídricas: poda, abonado, protección y trasplante

> Investigación de tareas **no hídricas** para dos casos reales del inventario: un *Aloe vera* en maceta en terraza suroeste, y una conífera ornamental de jardín **sin identificar**.
> Fecha: 2026-08-13. Fuentes primarias citadas con URL, cita literal y licencia cuando está declarada.
> Alcance: España peninsular, vivienda particular. Complementa a [`modelo-agronomico-riego.md`](modelo-agronomico-riego.md), que cubre sólo el eje hídrico.
> Contexto heredado: ya se comprobó que **ninguna base de datos estructurada de plantas tiene campo de ventana de abonado**. Todo lo de aquí es literatura leída y citada a mano.

---

## Convenciones de esta ficha

**Escalón de fuente** (se declara en cada afirmación):

| Escalón | Qué es | Qué se ha encontrado aquí |
|---|---|---|
| **A — fuerte** | Publicación oficial española: MAPA, CCAA, IFAPA, IVIA, INIA/CSIC, diputaciones, universidades españolas, jardines botánicos públicos | Tres fichas/ensayos de Aloe vera (Murcia, Córdoba, Lanzarote). **Cero** sobre coníferas ornamentales de jardín |
| **B — media** | Extensión agraria comparable: RHS (Reino Unido), UC ANR (California), Colorado State Extension, Missouri Botanical Garden, Morton Arboretum | Casi todo lo de coníferas y todo lo de maceta |
| **C — débil** | Viveros, blogs, medios | Sólo se usa marcado, y con explicación de por qué no hay nada mejor |

**Granularidad honesta** (decide si la app puede hacer algo inteligente con el dato):

| Marca | Significado | ¿La app puede evaluarlo? |
|---|---|---|
| 🌡 | **Condición climática**: temperatura, helada, ola de calor, sequía prolongada | **Sí**, con Open-Meteo horario |
| 🌱 | **Fase fenológica u observación**: "cuando la inflorescencia supere las hojas", "cuando salgan raíces por los agujeros" | **No sola**: hay que preguntar al usuario. Convertible en *check* del diario |
| 📅 | **Mes de calendario**: "abril a septiembre" | **No**. Es un recordatorio, no una decisión |

---

## Resumen por planta y tarea

### Aloe vera (GBIF 2777724) — maceta 25×24 cm (~7,5 L sustrato, 0,049 m² de boca), terraza SO

| Tarea | Ventana / condición | Escalón | Granularidad | Fuera de plazo |
|---|---|---|---|---|
| **Trasplante** (cambio de maceta) | Primavera; también inicio de verano. Disparador real: raíces asomando por los agujeros o enrolladas en el cepellón | B (RHS, UC MG) | 🌱 + 📅 | Otoño/invierno: sustrato nuevo se queda encharcado → pudrición |
| **Cadencia de trasplante** | Cada **3–5 años** (regla general RHS) o **2–4 años** (crasas, UC MG). Maceta nueva **≤ 1/3 mayor**; en tiestos < 25 cm, +2,5–5 cm de ancho y hondo | B | 📅 | Maceta demasiado grande → exceso de sustrato húmedo → pudrición |
| **Abonado en maceta** | 1 vez al mes, **abril–septiembre**; abono líquido pobre en N (o rico en K tipo tomate); **nada en invierno** | B (RHS, UC MG) | 📅 | Abonar en reposo invernal = acumulación de sales; exceso = mal enraizamiento |
| **Abonado en suelo (referencia, NO aplica a maceta)** | N + P; compost a la plantación; 20 g/planta de sulfato amónico a los 2 meses | **A** (Cabildo de Lanzarote) | 📅 | — |
| **"Poda": despunte de flor** | Cortar el escapo desde la base **cuando la inflorescencia supere la altura de las hojas y antes de que florezca** | **A** (CARM Murcia) | 🌱 | La inflorescencia resta vigor a las hojas |
| **"Poda": desahijado** | Separar hijuelos **cuando alcancen ≥ 20 cm**; al menos **cada dos años** | **A** (CARM Murcia + Cabildo Lanzarote) | 🌱 | Los hijuelos compiten por agua y nutrientes y retrasan la planta madre |
| **Protección invernal — daño** | No aguanta heladas prolongadas por debajo de **−2 °C**; "no soporta heladas" | **A** (CARM Murcia, Cabildo Lanzarote) | 🌡 | Células reventadas, hoja translúcida, pudrición |
| **Protección invernal — crecimiento** | Cubrir con manta térmica en invierno dio **+37 % de peso fresco de hoja** (717 g vs 457 g) en Córdoba | **A** (Diputación de Córdoba) | 🌡 | No mata, pero frena el año siguiente |
| **Protección estival** | Óptimo 20–25 °C; se adapta a 18–40 °C; olas de calor → **parada de crecimiento** reversible. Sombra de tarde en picos de verano | **A** (CARM) + B (UC MG) | 🌡 | Parada de crecimiento; deshidratación y quemadura si además hay sol duro sin aclimatar |
| **Aclimatación al sol de terraza SO** | Transición gradual a pleno sol tras vivero o invernada | **C** | 🌱 | **No respaldado** — ver §5 |

### Conífera ornamental de jardín, sin identificar ("abeto"), arraigada en suelo, campo abierto

| Tarea | Ventana / condición | Escalón | Granularidad | Fuera de plazo |
|---|---|---|---|---|
| **Regla dura de poda** | **No cortar nunca más allá de la parte verde con acículas.** Salvo el tejo, las coníferas no rebrotan de madera vieja | B (RHS, CSU, Morton) | 🌱 (visual) | **Calvo permanente e irreversible** |
| **Ventana de poda (RHS, clima atlántico)** | "entre abril y finales de agosto"; poda ligera segura en **abril, junio y principios de agosto** | B | 📅 | Pardeado del follaje; más muerte regresiva podando en otoño (sobre todo octubre) |
| **Ventana de poda (Norteamérica continental)** | **Finales de invierno / principios de primavera, antes de la brotación**; evitar finales de verano y otoño | B (CSU, Morton) | 📅 + 🌱 | Poda tardía estimula brotación tierna que no endurece |
| **Ventana sintetizada para España peninsular** | Salida del invierno a primavera; **no podar en sequía prolongada ni ola de calor**; evitar otoño | Inferencia del proyecto sobre B | 🌡 + 📅 | Ver §4.2: es una síntesis, no una cita |
| **Poda de reducción de tamaño** | Máximo **un tercio de la altura, en abril**; por encima de eso el seto/porte "puede no volver a rellenar" | B (RHS) | 📅 | Queda plano y pelado arriba, para siempre |
| **Abonado de ejemplar arraigado** | "La mayoría de las coníferas prosperan sin abonado adicional"; basta el acolchado anual | B (RHS) | — | Innecesario; sólo si el crecimiento es pobre → granulado de uso general **a finales de invierno** |
| **Protección invernal** | "La mayoría de las coníferas son totalmente rústicas"; sólo unas pocas son sensibles a la helada (p. ej. *Araucaria cunninghamii*) | B (RHS) | 🌡 | Normalmente **no hay tarea** |
| **Protección estival / riego** | Los 2 primeros años tras plantar necesita riego regular; **ya establecida se apaña salvo sequías prolongadas y olas de calor** | B (RHS) | 🌡 | Pardeado por sequía/vientos secos |
| **Fuente española oficial** | **No existe** publicación de extensión agraria española sobre poda/abonado de coníferas ornamentales de jardín. La norma de referencia (NTJ 14C Parte 2) es de pago | — | — | Ver §4.1 |

---

## 1. Aloe vera en maceta — el detalle citado

### 1.1 Las tres fuentes españolas de escalón A (y qué NO cubren)

Hay tres publicaciones de administración pública española sobre *Aloe vera*. Las tres son de **cultivo comercial en suelo**, no de maceta doméstica. Se citan porque son lo único de escalón A que existe, y porque lo que dicen sobre **frío, calor, floración e hijuelos** es fisiología de la planta y sí se traslada a la maceta. Lo que dicen sobre **abonado por hectárea** no se traslada.

**(A-1) Región de Murcia — *Cultivo de Aloe Vera en la comarca del Campo de Cartagena***
David Garrido Gómez, Oficina Comarcal Agraria Cartagena-Oeste. Edita: Comunidad Autónoma de la Región de Murcia, Consejería de Agua, Agricultura, Ganadería y Pesca. Depósito Legal MU-196-2018. Programa de Innovación Tecnológica, Cultivos Industriales nº 39.
Licencia declarada: *"Se autoriza la reproducción total o parcial citando la fuente"*.
[PDF en el portal de la CARM](https://www.carm.es/web/integra.servlets.Blob?ARCHIVO=39.-+Cultivo+de+Aloe+Vera+en+la+comarca+del+Campo+de+Cartagena.pdf&TABLA=ARCHIVOS&CAMPOCLAVE=IDARCHIVO&VALORCLAVE=152285&CAMPOIMAGEN=ARCHIVO&IDTIPO=60&RASTRO=c498%24m1259%2C20538)

**(A-2) Diputación de Córdoba — *Caracterización del cultivo y manejo de Aloe vera (Aloe vera (L.) Burm f.) en la provincia de Córdoba*** (2016)
Antonio Jiménez Luque, Manuel Díaz Buenestado, Antonio Martínez Parlón (Centro Agropecuario Provincial, Diputación de Córdoba); Juan Enrique Castillo García (ETSIAM, Universidad de Córdoba); José Manuel Romero Zea.
Licencia: **no declarada** en el documento.
[PDF](https://www.dipucordoba.es/wp-content/uploads/2020/11/8ensayoaloevera.pdf) *(el servidor devuelve 403 a peticiones sin `Referer`; se descargó con cabecera de navegador)*

**(A-3) Cabildo de Lanzarote — *Fichas técnicas de cultivos de Lanzarote: Aloe***, Servicio Insular Agrario, enero 2012.
Licencia: **no declarada**.
[PDF](https://www.cabildodelanzarote.com/documents/35307/78325/ficha_tecnica_del_cultivo_de__aloe_vera.pdf/6d484d29-6142-b195-ec78-c48a7d17793d?t=1620721613744)

### 1.2 Umbrales de temperatura — protección invernal

La cifra dura, de Murcia (A-1, §6.2):

> *"conviene comentar que en la zona del ensayo no han tenido lugar periodos adversos de heladas, aspecto este crucial para la supervivencia del Aloe, ya que **no aguanta bien las heladas prolongadas con temperaturas inferiores a −2 ˚C**. Tampoco le van bien los cambios bruscos de temperatura entre el día y la noche."*

Lanzarote (A-3) lo dice sin matices, más conservador:

> *"La temperatura óptima oscila entre los 19 y los 25ºC. **No soporta heladas.**"*

Missouri Botanical Garden, como planta de contenedor (escalón B), da un mínimo **muy** por encima de la helada:

> *"Winter hardy to USDA Zone 10-12. […] Tolerates 50F to 60F degree nights in summer with a **minimum of 40F degrees in winter**. Plants taken outside for summer should be protected from rain, and should be brought indoors in early fall before night temperatures begin to dip below 50F degrees."*
> — [MOBOT Plant Finder, *Aloe vera*](https://plantfinder.mobot.org/PlantFinderDetails.aspx?kempercode=b628). Licencia: © Missouri Botanical Garden, no declarada como abierta.

El RHS, en su ficha de aloes de interior:

> *"They dislike humid atmospheres and **cannot cope with freezing temperatures**."*
> — [RHS, *Aloe plants including Aloe vera*](https://www.rhs.org.uk/getmedia/d02059a1-4e2c-45c3-956b-da5cc90db7f1/Indoor-plants-resources.pdf) (PDF). Licencia: © RHS, Registered Charity 222879/SC038262.

**Lectura para la app.** Hay tres umbrales distintos y no se contradicen, describen cosas distintas:

| Umbral | Qué significa | Fuente | Acción |
|---|---|---|---|
| **+4,4 °C (40 °F)** | mínimo *confortable* para planta de contenedor bajo cubierto | MOBOT (B) | demasiado conservador para una terraza peninsular; ignorable |
| **0 °C** | "no soporta heladas" | Lanzarote (A) | 🌡 **aviso**: proteger o entrar |
| **−2 °C prolongado** | daño / muerte documentada | Murcia (A) | 🌡 **alarma**: daño probable |

Los dos últimos son directamente evaluables con `temperature_2m_min` de Open-Meteo. La maceta agrava el caso: el cepellón se enfría mucho más que el suelo, así que el umbral efectivo para maceta es **más severo** que el de plantación en campo — pero **ninguna fuente lo cuantifica**, ver §4.

### 1.3 Protección invernal: cuánto se gana, no sólo cuánto se pierde

El ensayo de Córdoba (A-2) es el hallazgo más útil, porque es español, cuantitativo, y en clima **mediterráneo subtropical** (invierno tipo *Citrus* según Papadakis) — es decir, un invierno sin heladas severas, comparable a media España peninsular costera y del sur. Compara plantas **cubiertas con manta térmica** durante el invierno frente a plantas a la intemperie:

| Variable | Cubierta | Expuesta | Diferencia |
|---|---|---|---|
| Peso fresco de hoja | **717 g** | 457 g | +37 % |
| Longitud de hoja | **68,7 cm** | 56,8 cm | +21 % |
| Nº de hijuelos por unidad experimental | **65** | 51 | +27 % |

> *"El peso fresco medio de la hoja de Aloe vera muestra diferencias significativas de crecimiento entre las plantas cubiertas durante el invierno y las expuestas a la intemperie, siendo el peso medio de las hojas de las plantas cubiertas un 37% superior al correspondiente a las plantas expuestas al frío. **Por tanto el crecimiento de las plantas se ve directamente afectado por la temperatura que soportan durante el invierno.**"*

Y en conclusiones:

> *"Las unidades experimentales correspondientes a plantas protegidas durante el invierno presentan un mayor desarrollo vegetativo que las expuestas a las temperaturas invernales. El número de hijuelos por unidad experimental es superior en las unidades experimentales protegidas durante el invierno."*

**Esto cambia el mensaje de la app.** La protección invernal del Aloe **no es sólo un seguro contra la muerte por helada**: en un invierno cordobés *sin heladas*, cubrir dio un tercio más de hoja. El consejo honesto no es "protege si baja de 0 °C" sino "protege si quieres que crezca". Granularidad: 🌡 (la fuente no da un umbral de activación, sólo el contraste cubierto/expuesto durante todo el invierno) — así que **el umbral de disparo es una decisión del proyecto**, no un dato.

### 1.4 Protección estival e insolación en terraza suroeste

Murcia (A-1) da el rango y observa el efecto de las olas de calor:

> *"la planta se adapta a temperaturas que oscilan entre los 18 y 40 ˚C con humedades relativas entre 65-85%. Para el desarrollo de sus hojas requiere una temperatura óptima entre 20 y 25 ˚C y soporta bien las elevadas temperaturas, aunque en el ensayo se ha podido observar que en determinados momentos **las olas de calor han propiciado una parada en el crecimiento, reactivándose una vez pasado el período extremo**."*

Esto es 🌡 y perfectamente evaluable: `temperature_2m_max > 40 °C` sostenido → el usuario debe saber que la parada de crecimiento es **normal y reversible**, y que no debe reaccionar abonando ni regando más.

Sobre **quemadura por sol directo**, la única fuente por encima de escalón C es UC Master Gardeners of Orange County (B, clima mediterráneo californiano, trasladable a terraza peninsular):

> *"Providing afternoon shade during high summer temperatures helps reduce stress"* … *"excessive sunlight can lead to signs of dehydration and sunburn; relocating potted plants to shaded areas can prevent this."*
> — [UC Master Gardeners of Orange County, *Succulents*](https://ucanr.edu/site/uc-master-gardeners-orange-county/succulents-orange-county). Licencia: © UC Regents, no declarada como abierta.

Una terraza **suroeste** es exactamente el caso peor: recibe el sol de tarde, que coincide con el máximo diario de temperatura. La combinación "sol de tarde + ola de calor" es la que la fuente señala. Convertible a condición: 🌡 `temperature_2m_max` alto **y** orientación SO declarada en el perfil → sugerir sombreo o retirada en las horas centrales-tarde.

MOBOT sitúa la planta en *"Full sun"* / *"Best sited in full sun, but this plant seems to adapt to some part shade"*, y NC State Extension acepta tanto *"Full sun (6 or more hours)"* como *"Partial Shade"*. Es decir: **pleno sol es la posición correcta**; el problema no es el sol sino el pico de calor sobre una maceta pequeña.

### 1.5 Trasplante en maceta — cadencia, señales y época

Ninguna fuente española habla de Aloe vera en maceta. Todo esto es escalón B.

**Época y señales** — RHS, guía de trasplante de macetas:

> *"Spring, as plants start into growth, is generally the best time to repot and prune roots."* … *"Early to midsummer is also suitable for repotting many plants"* (evitar otoño e invierno).
> Señal: raíces *"spiralling around the bottom and sides of the rootball"* o *"growing out through the holes"*; esa congestión *"can prevent water draining properly and lead to root rot."*
> — [RHS, *How to repot a plant*](https://www.rhs.org.uk/container-gardening/how-to-repot-a-plant)

**Tamaño de la maceta nueva** (crítico para una crasa):

> *"the new pot should be no more than one third larger than the current one"*; para tiestos de menos de 25 cm, uno *"2.5-5cm (1-2in) deeper and wider than the previous one."*

Y el RHS, en la guía específica de cactus y crasas, da la razón:

> *"Avoid repotting into too large a pot as the excess volume of soil can stay too wet and cause rot."*
> — [RHS, *How to grow cacti and succulents*](https://www.rhs.org.uk/plants/types/cacti-succulents/houseplants/growing-guide)

**Cadencia**:

- RHS, regla general: *"Repotting once every three to five years is suitable for most plants."*
- UC Master Gardeners, específico para crasas: cambiar el sustrato *"every 2 to 4 years"*, y *"The best time to divide or transplant is at the start of their growth period, typically in spring or fall."*

**Sustrato** — RHS, cactus y crasas:

> *"Proprietary peat-free cactus compost, or make your own using 2 parts by volume of peat-free John Innes No 2, with 1 part horticultural grit or sharp sand mixed in."*

RHS, ficha específica de aloes: *"Use multipurpose peat-free compost mixed with 50% sand and/or grit"*.

**Aplicado a esta maceta concreta (25 × 24 cm)**: está justo en el límite de la regla RHS. La maceta siguiente sería de **28–30 cm de boca**, no más. Con ~7,5 L de sustrato y una crasa de crecimiento lento, la cadencia realista es el extremo alto de la horquilla: **cada 3–4 años**, y siempre con la señal (raíces por los agujeros) por delante del calendario.

**Nota de diseño**: el trasplante interacciona con el modelo hídrico. `modelo-agronomico-riego.md` §6.10 ya declara que el balance no aplica durante ~4–8 semanas tras un trasplante. El registro de trasplante en el diario debe, por tanto, **desactivar el disparo de riego**, no sólo anotarse.

### 1.6 Abonado en maceta — el caso distinto

Aquí hay que separar tres cosas que se confunden con facilidad.

**(a) Lo que dice la fuente española, que es de suelo y NO aplica a la maceta.**

Lanzarote (A-3) es la única que da dosis:

> *"Para hacer un abonado adecuado se aconseja partir de un análisis de suelo para corregir los elementos deficientes con abonado de fondo […]. El aloe se fertiliza a base de **nitrógeno y fósforo**. Es conveniente la aplicación de **compost en el momento de la plantación**. A los dos meses de plantación se aplicará **20 gr/planta de sulfato amónico**."*

Córdoba (A-2) usa humus de lombriz *"dos veces al año, primavera y otoño, […] a una dosis de 0,50 kg/planta"*, sobre un fondo de 20.000 kg/ha de estiércol de oveja fermentado.

Murcia (A-1) es el más informativo, por lo contrario:

> *"En cuanto a la fertilidad del terreno, **el Aloe se adapta a terrenos pobres en nutrientes, lo que permite ahorrar en abonado**. En nuestro ensayo apenas se ha utilizado de forma muy puntual un abono orgánico ecológico sin nitrógeno"* … *"es **menos exigente en fertilización nitrogenada** que los cultivos tradicionales."*

**(b) Lo que sí aplica a maceta.** Dos fuentes B coinciden en la ventana y discrepan en el producto:

- RHS, ficha de aloes: *"To encourage good growth and flowering, **feed once a month during the growing season (April–September)**, using a liquid feed. **Don't feed in the winter.**"*
- RHS, cactus y crasas: *"Feed once a month during the growing season (April–September), using either a **liquid feed high in potassium such as a tomato fertiliser**, or a specialist liquid cacti feed."*
- UC Master Gardeners: *"Use any balanced, non-synthetic fertilizer—fish emulsion (½ strength) works well"*, *"at half the recommended rate once a month; **avoid winter fertilizing when most succulents are dormant**"*; para maceta, *"a **low-nitrogen fertilizer (i.e. 15-15-15) at ¼ strength** with each watering"*.

**Discrepancia real, y cómo se resuelve**: RHS dice "rico en potasio (tomate)", UC dice "equilibrado y bajo en nitrógeno". No son lo mismo, pero **coinciden en el vector**: relativamente poco N. Y eso concuerda con la fuente española de escalón A (Murcia: poco exigente en N). La recomendación defendible es **abono líquido pobre en nitrógeno, a mitad o cuarto de dosis de etiqueta, una vez al mes de abril a septiembre**.

**(c) Por qué maceta ≠ suelo aquí.** En suelo, la reserva de nutrientes del perfil y el estiércol de fondo cubren años. En 7,5 L de sustrato, con riegos que **drenan por abajo** (que es la práctica correcta según `modelo-agronomico-riego.md` §5, porque además lava sales), los nutrientes solubles se van con el agua de drenaje. Es exactamente por eso que la maceta necesita un aporte periódico que el suelo no necesita, y por eso una dosis por hectárea no se puede "dividir" para dar una dosis por maceta.

Granularidad: **📅 pura**. Abril–septiembre es un mes de calendario, no una condición climática. La app **no puede hacer nada inteligente con esto** más allá de un recordatorio mensual estacional. Es el ejemplo canónico de tarea no convertible.

### 1.7 "Poda" del Aloe: no es poda, son dos operaciones fenológicas

El Aloe no se poda. Lo que existe, y las dos fuentes españolas de escalón A lo describen igual, son dos labores con **disparador observable**, no fecha:

**Despunte de flor** (CARM Murcia, §7.3):

> *"La inflorescencia si está muy desarrollado puede restarle vigor al desarrollo de las hojas del Aloe, por lo que habrá de eliminarlas mediante un corte desde su base **cuando la inflorescencia supere la altura de las hojas y antes de que florezcan**."*

**Desahijado** (CARM Murcia, §7.3):

> *"Los hijos de la planta pueden llegar a desarrollar competencia por los nutrientes y el agua por lo que repercutirá en la producción (retrasándola). Una planta de Aloe puede generar a los 18 meses de vida como máximo dos hijuelos y a partir del segundo año pueden generarse 4 ó 5. […] se ha procedido a desahijar, **y siempre y cuando, alcancen una altura mínima de 20 cm**."*

Cabildo de Lanzarote (A-3) añade cadencia y una advertencia de manejo:

> *"**Deshije**: Consiste en extraer los hijuelos **por lo menos cada dos años**, después de un corte o cosecha de hoja. Si al retirar los hijuelos se destapa la raíz de la planta madre, ésta debe cubrirse inmediatamente para evitar pérdidas de la planta por deshidratación o retraso en el crecimiento."*

RHS describe el mismo desahijado como propagación, con el detalle de qué hacer con la madre:

> *"Carefully separate the small offset plant from the parent, if possible with some roots attached."* … *"Trim the ends of the roots on the parent plant with secateurs or sharp scissors and put it back in its pot with fresh peat-free compost."*

**Lectura para la app**: estas dos son 🌱 puras y son **las mejores tareas no hídricas de todo este documento**, porque el disparador es algo que el usuario *ve* ("¿asoma un tallo floral por encima de las hojas?", "¿hay hijuelos de un palmo?"). Se convierten en preguntas del diario, no en fechas. Y en maceta el desahijado es doblemente relevante: los hijuelos consumen el mismo depósito de 7,5 L que la madre.

Además, en maceta el desahijado y el trasplante son la **misma operación física** (hay que sacar el cepellón para ambos), así que conviene agruparlos en una sola tarea de primavera.

---

## 2. Conífera ornamental sin identificar — el detalle citado

### 2.1 La regla dura: no rebrota de madera vieja

Es la afirmación más importante de todo este documento, y es la que **no depende de la especie** (con excepciones nombradas). El RHS la enuncia dos veces:

> *"**With the exception of yew (Taxus baccata), conifers have little or no capacity to regrow from old wood.**"*
> — [RHS, *Conifers: brown patches*](https://www.rhs.org.uk/problems/conifers-brown-patches)

> *"**Most conifers (apart from yew) do not respond well to renovation, as they do not re-shoot from old wood.**"* … *"Examples of conifer hedges intolerant of hard pruning include Cupressus, Chamaecyparis and × Cuprocyparis leylandii (Leyland cypress)."*
> — [RHS, *Hedges: renovation*](https://www.rhs.org.uk/plants/types/hedges/renovation)

> *"Most conifers will not regrow from brown dead wood if you prune into this; **notable exceptions include yew and less reliably Thuja**."*
> — [RHS, *How to grow conifers*](https://www.rhs.org.uk/plants/types/conifers/growing-guide)

Colorado State University Extension da la razón mecánica y la consecuencia, que es lo que hay que decirle al usuario:

> *"**New needles will not grow from branches without needles.**"*
> *"Because new growth comes ONLY from the growing tips, branches cannot be pruned back into wood without needles. **If the shrub is pruned back to bare wood, it will have a permanent bare spot.**"*
> — [CMG GardenNotes #617, *Pruning Evergreens*](https://csfs.colostate.edu/wp-content/uploads/2024/01/Pruning-Evergreens.pdf). Autores: David Whiting y Carol O'Meara, CSU Extension. Revisado 2018/2023.
> Licencia declarada: *"CMG GardenNotes may be reproduced, without change or additions, for nonprofit educational use with attribution."* © Colorado State University Extension.

El Morton Arboretum lo llama *dead zone* y explica por qué crece:

> *"If pruning cuts are made into this area, there are no buds to sprout and fill in the gap."* — y esa zona muerta **se agranda** cuando se recorta a tijera en vez de podar selectivamente.
> — [Morton Arboretum, *How to prune evergreen trees and shrubs*](https://mortonarb.org/plant-and-protect/tree-plant-care/plant-care-resources/how-to-prune-evergreen-trees-and-shrubs/). Licencia: © The Morton Arboretum, no declarada como abierta.

**Esto es lo que la app puede decir hoy, sin conocer la especie, y es un consejo de alto valor**: el error es irreversible y el usuario no puede saberlo por intuición. Granularidad 🌱 pero **visual e inmediata**: "no cortes más allá de donde hay acículas verdes". No necesita clima, no necesita fecha, no necesita especie.

### 2.2 Ventana de poda: aquí las fuentes divergen, y la divergencia es climática

**RHS (Reino Unido, clima atlántico templado y húmedo):**

> *"conifers are best pruned **between April and the end of August**"* … *"**Browning of foliage is more likely if you prune at other times.**"*
> — [RHS, *How to grow conifers*](https://www.rhs.org.uk/plants/types/conifers/growing-guide)

> *"**Trimming two or three times, in April, June and early August, is usually relatively safe.**"*
> *"die-back appeared to be slightly more common after **autumn trimming (mainly October)**."*
> *"**Trimming in the summer during times of plant stress, such as prolonged drought or hot, dry spells may also be a factor.**"*
> — [RHS, *Conifers: brown patches*](https://www.rhs.org.uk/problems/conifers-brown-patches)

**CSU Extension (Colorado, continental seco):** para *Picea*, *Abies* y *Pseudotsuga*, *"**Prune late winter or early spring.**"*; para *Juniperus* y *Thuja*, *"They may be pruned at any time except during subzero weather or **late summer**. Needles form a waxy layer in summer that prevents sunburn of the needles. **The best time is early spring, prior to new growth.**"* Y sobre el recorte a tijera: *"While shearing is quick and easy, it is not recommended, **especially after midsummer**."*

**Morton Arboretum:** *"before new growth starts in the spring"* para la mayoría; *"**late summer and fall pruning should be avoided**"*.

**La divergencia es real**: el RHS mete el verano dentro de la ventana buena; los norteamericanos lo excluyen a partir de mediados. La explicación más plausible es climática — un agosto inglés no es un agosto peninsular. Y el propio RHS, en la página de manchas pardas, **ya excluye el verano cuando hay sequía o calor**, que en España es la norma y no la excepción.

**Síntesis del proyecto** (marcada como inferencia, no como cita): para España peninsular, la ventana defendible es **de la salida del invierno a comienzos de verano**, con dos condiciones climáticas de veto:

- 🌡 **veto por calor/sequía**: no podar durante ni justo antes de una ola de calor o una racha seca prolongada (RHS lo señala explícitamente como factor de muerte regresiva).
- 📅 **veto de otoño**: evitar octubre (RHS: más muerte regresiva) y finales de verano (CSU/Morton: brotación tardía que no endurece).

Esta es la única afirmación de todo el documento donde el proyecto **compone** dos fuentes B de climas distintos. Debe ir marcada como tal en la app.

### 2.3 Poda de reducción de tamaño

> *"**Reduce the height by up to one-third in April**"* … *"**Hedges reduced in height by more than one-third may not fill out, remaining flat and bare at the top.**"*
> — [RHS, *Hedges: renovation*](https://www.rhs.org.uk/plants/types/hedges/renovation)

Alternativa que el RHS ofrece en lugar de la renovación: *"thin out the side branches, removing selected branches right back to the trunk but leaving the others intact."*

CSU añade la regla estructural para un ejemplar aislado en el centro del jardín, que es exactamente este caso:

> *"On evergreen trees, **avoid pruning the central leader (trunk)**. This results in the development of multiple leaders that are prone to wind and snow damage."*
> *"**Never allow codominant trunks** (trunks of similar size) to develop."*
> *"In situations where the branch must be pruned back past the needles, **remove it back to the trunk**."*

Y una advertencia de diseño relevante para un árbol en campo abierto: *"small to midsize evergreen trees, with their pyramidal form, generally look best with their lowest branches allowed to drape to ground level"* — es decir, **la tarea por defecto de un abeto de jardín en campo abierto es no podar nada**.

### 2.4 Abonado de un ejemplar arraigado que nadie riega

La respuesta del RHS es que **no hay tarea**:

> *"**Most conifers will thrive without additional feeding** and annual mulching should provide any additonal nutrients required."*

Y sólo si el crecimiento es pobre:

> *"you can apply a **general-purpose granular fertiliser in late winter** at the rate recommended on the packet"*

(La misma página distingue explícitamente el caso de maceta, que sí necesita: *"liquid feeding from early spring until late summer"* — no aplica aquí.)

Riego, que es lo colindante y sí tiene condición climática:

> *"During the **first two years** after planting, new conifers need regular watering to ensure the soil stays moist, particularly in dry and hot spells."*
> *"**Once established, most cope without watering, except in prolonged droughts and heatwaves**"*

**Lectura para la app**: para una conífera arraigada de la que nadie se ocupa, el consejo correcto es **"no abones"**, con un disparador de excepción (🌱 crecimiento pobre / acículas descoloridas) que lleva a una ventana 📅 (finales de invierno). Es una tarea *negativa*, y eso es una respuesta válida y útil: evita que el usuario haga daño.

Existe además consenso de extensión agraria norteamericana de que los árboles y arbustos establecidos en suelo favorable no necesitan abonado salvo que un análisis de suelo muestre deficiencia (University of Maryland, UNH, Clemson, UMN). **No se ha podido verificar ninguna de esas páginas contra el original** — la de UMN devolvió 403 y las demás sólo se vieron en resúmenes de búsqueda. Se deja constancia y **no se cita como respaldo**; el peso lo lleva el RHS, que sí se leyó.

### 2.5 Protección invernal y estival

**Invierno — normalmente no hay tarea:**

> *"**Most conifers are fully hardy and can be planted outdoors** however; a few are frost tender such as Araucaria cunninghamii and should be grown with protection over winter"*
> — [RHS, *How to grow conifers*](https://www.rhs.org.uk/plants/types/conifers/growing-guide)

No hay umbral numérico en la fuente. Es 🌡 sólo en el sentido de "rústica", sin cifra. Para un jardín de España peninsular, la probabilidad de que una conífera ornamental corriente necesite protección invernal es baja, pero **no se puede afirmar sin conocer el género**: *Araucaria* es el contraejemplo que la propia fuente nombra.

**Verano y estrés ambiental — sí hay condición climática:**

> *"many brown patches are likely to result from adverse growing conditions such as **drought, frost, waterlogging or cold, drying winds**, all of which could inhibit regeneration from the trimmed foliage."*
> — [RHS, *Conifers: brown patches*](https://www.rhs.org.uk/problems/conifers-brown-patches)

CSU añade el efecto de la poda repetida sobre la resistencia invernal: *"Frequently sheared plants are more prone to show needle browning and dieback from winter cold and drying winds."*

**Plagas**: el RHS documenta que el pulgón del ciprés (*Cinara cupressivora*) estuvo *"associated with half of the cases of brown patches investigated"* en un proyecto conjunto RHS–East Malling Research. Afecta a Cupressaceae (*Cupressus*, *Chamaecyparis*, *Thuja*, *Juniperus*), no a *Abies* ni *Picea*. **Otra cosa que depende de saber el género.**

### 2.6 Qué es estable en el grupo y qué exige saber la especie

Esta es la pregunta que decide si la app puede decir algo útil antes de identificar la planta. La respuesta corta: **la regla de seguridad es estable; casi todo lo demás no lo es.**

| Afirmación | ¿Estable en "conífera ornamental"? | Excepciones nombradas por la fuente |
|---|---|---|
| No rebrota de madera sin acículas → corte irreversible | **Sí, muy estable** | *Taxus* (tejo) rebrota; *Thuja* "de forma menos fiable" |
| El líder central no se corta | **Sí** | — |
| Un ejemplar aislado en campo abierto no necesita poda | **Sí** | — |
| No necesita abonado si crece bien | **Sí** | — |
| Rústica en invierno en España peninsular | **Probable, no seguro** | *Araucaria cunninghamii* y otras sensibles |
| Ventana de poda | **No** | *Pinus*: sólo pinzar velas en primavera. *Picea/Abies*: final de invierno–primavera. *Juniperus/Thuja*: casi cualquier época salvo finales de verano. *Taxus*: poda fuerte antes de julio |
| Tolerancia a poda de reducción | **No** | *Picea/Abies*: toleran si no se pasa de las acículas. *Pinus*: no, "do not shear pines". Cultivares enanos (*Picea glauca* 'Conica', *P. mariana* 'Nana', *P. abies* 'Nidiformis'): *"rather intolerant of pruning"* |
| Método de poda | **No** | *Pinus*: pinzar la "vela" con los dedos, un tercio, sin tijera. *Picea/Abies*: cortar a yema lateral. *Juniperus/Thuja*: cortar a rama lateral ascendente |
| Susceptibilidad al pulgón del ciprés | **No** | Sólo Cupressaceae |

Las citas que sostienen la mitad derecha de la tabla, todas de CSU Extension (B):

> *"Spruce (Picea spp.), fir (Abies spp.), and Douglas fir (Pseudotsuga menziesii) generally need little to no pruning when planted in the right place. […] Because these species produce some side buds, branch tips can be removed encouraging side bud growth. **Prune late winter or early spring.**"*

> *"Because **pines produce few side buds, they are intolerant of more extensive pruning**. If the terminal bud on a branch is removed, growth on that shoot is stopped […] **Do not shear pines.** (Cut needles will stay cut and not grow back.)"*

> *"Very slow-growing species, like the dwarf Alberta spruce […] are **rather intolerant of pruning**."*

> *"Junipers and arborvitae **growing in the shade are rather intolerant of pruning** due to slow growth rates."*

Y el Morton Arboretum sobre el tejo, la excepción que confirma por qué importa la identidad: *"Yew: Uniquely tolerant—New growth will develop on old wood, allowing heavy pruning if done before July."*

### 2.7 El nombre común no resuelve nada

Un dato incómodo para el diseño: **"abeto" en español no identifica ni el género**. Se aplica corrientemente a *Abies* (abeto verdadero), a *Picea abies* (llamado "abeto rojo" o "falso abeto", que es el árbol de Navidad habitual en España), y coloquialmente a cualquier conífera piramidal de jardín, incluidas Cupressaceae (*Cupressus*, *Chamaecyparis*, *Thuja*). Y la tabla de §2.6 muestra que la diferencia entre *Abies*, *Picea* y *Thuja* cambia el método de poda entero.

Las fuentes que documentan esta confusión de nomenclatura son de escalón **C** (fichas divulgativas y prensa). No hay nada mejor porque es un problema de uso del lenguaje, no de agronomía; no hay literatura de extensión sobre cómo llama la gente a sus árboles. Se marca como C y se usa **sólo** como argumento de diseño, nunca como afirmación agronómica.

---

## 3. Convertibilidad a condición climática

La app tiene datos horarios de Open-Meteo. Esto es lo que puede y no puede convertir.

### Sí convertibles (🌡)

| Condición | Variable Open-Meteo | Regla | Fuente |
|---|---|---|---|
| Aviso de helada para el Aloe | `temperature_2m_min` | ≤ 0 °C → proteger o entrar | A (Lanzarote) |
| Alarma de daño para el Aloe | `temperature_2m_min` | ≤ −2 °C sostenido → daño probable | A (Murcia) |
| Ola de calor sobre el Aloe | `temperature_2m_max` | > 40 °C → avisar de parada de crecimiento **normal**; no reaccionar | A (Murcia) |
| Sol de tarde + calor en terraza SO | `temperature_2m_max` + orientación declarada | sombrear en horas de tarde | B (UC MG) |
| Veto de poda de conífera por estrés | sequía prolongada / ola de calor | **no podar** | B (RHS) |
| Riego de rescate de conífera establecida | sequía prolongada / ola de calor | única circunstancia con tarea de riego | B (RHS) |

### No convertibles (📅) — recordatorios estacionales, nada más

- Abonado del Aloe: abril–septiembre, mensual.
- Trasplante del Aloe: primavera.
- Ventana de poda de conífera: primavera; evitar otoño.
- Abonado correctivo de conífera: finales de invierno.

### Convertibles a **pregunta del diario** (🌱) — las más valiosas

- ¿Asoman raíces por los agujeros de la maceta? → trasplante.
- ¿Hay un tallo floral por encima de las hojas? → despunte de flor (antes de que abra).
- ¿Hay hijuelos de ≥ 20 cm? → desahijado.
- ¿La conífera crece poco o tiene acículas descoloridas? → único disparador de abonado.

**Observación de diseño**: en tareas no hídricas, el eje fenológico 🌱 pesa **más** que el climático 🌡, al revés que en el riego. Casi todo lo accionable con fundamento se dispara por algo que el usuario ve, no por algo que la API dice. La app debería tratar el diario como el sensor principal de este módulo.

---

## 4. Lo que no se ha podido respaldar

### 4.1 No hay extensión agraria española sobre ornamentales de jardín — confirmado

Se buscó explícitamente en MAPA, IFAPA/SERVIFAPA, IVIA, Comunidad de Madrid y Junta de Andalucía material sobre poda o abonado de **coníferas ornamentales de jardín**. **No existe.** Lo que hay es:

- **Producción y sanidad de cultivos con renta.** Las coníferas aparecen como especie forestal o de vivero, no como planta de jardín particular.
- **Legislación, no agronomía.** La Comunidad de Madrid regula que está *"prohibida la poda drástica, indiscriminada y extemporánea de todo árbol protegido por esta Ley"* y que *"la poda se realizará a juicio del técnico competente"* ([Comunidad de Madrid, Arbolado urbano](https://www.comunidad.madrid/servicios/urbanismo-medio-ambiente/arbolado-urbano)). Esto es escalón A y es **cierto**, pero no da ventana, ni objetivo, ni umbral: no es utilizable por la app.
- **Una norma técnica española que sí lo cubre, pero es de pago.** Las *Normas Tecnológicas de Jardinería y Paisajismo* **NTJ 14C Parte 2:1998 — "Mantenimiento del arbolado: Poda"** y el *Manual Práctico nº 9 — "Los trabajos de poda del arbolado urbano"* (2002) incluyen una sección específica **"27. La poda de las coníferas"**. El índice es público; el contenido no ([ntjdejardineria.org](https://www.ntjdejardineria.org/ntj/mp9-los-trabajos-de-poda-del-arbolado-urbano/)). La única cita pública que se pudo extraer es genérica: *"Las operaciones de poda que deben efectuarse dependen de la especie, de la situación, de la función que desempeñan, de la edad, etc."* — que, irónicamente, **confirma la tesis de §2.6**.

**Conclusión declarada**: el escalón A español está **vacío** para coníferas ornamentales domésticas. Todo el §2 de este documento se apoya en escalón B (RHS, CSU, Morton). Es una carencia estructural del dominio, no un fallo de búsqueda.

### 4.2 La ventana de poda de coníferas para España es una síntesis, no una cita

Ninguna fuente dice "poda tu conífera en España en tal ventana". Lo de §2.2 combina RHS (abril–agosto, clima atlántico) con CSU/Morton (fin de invierno–primavera, evitar final de verano) usando como puente la propia advertencia del RHS sobre sequía y calor. **Es una inferencia del proyecto.** Si la app la muestra, debe etiquetarla como tal.

### 4.3 Aloe vera en maceta: cero respaldo español

Las tres fuentes A españolas son de **plantación comercial en suelo**. No hay ninguna publicación pública española sobre *Aloe vera* en maceta doméstica. Todo lo de §1.5 y §1.6 (trasplante, cadencia, tamaño de maceta, abonado líquido) es escalón **B** británico y californiano.

### 4.4 El efecto de la maceta sobre el umbral de frío: no cuantificado por nadie

Es físicamente cierto que un cepellón de 7,5 L se enfría mucho más rápido y más profundo que el suelo del campo, y por tanto los −2 °C de la fuente murciana (medidos sobre plantación en suelo) **sobreestiman** la tolerancia real de esta maceta concreta. **Ninguna fuente cuantifica el desplazamiento del umbral.** Si la app aplica un margen de seguridad para maceta, será un número inventado por el proyecto y debe declararse.

### 4.5 Aclimatación al sol y quemadura solar del Aloe: sólo escalón C

Sobre el caso concreto de una terraza suroeste — cuánto sol directo tolera un Aloe recién comprado, cuántos días de transición hacen falta, a qué temperatura empieza la quemadura — **no se ha encontrado ninguna fuente A ni B**. Lo que hay es abundante material de blogs y viveros que repite protocolos de 2–3 semanas y umbrales de 38 °C, sin ninguna medición detrás. **Se marca como C y no se recomienda usarlo como consejo cuantitativo.** Lo único defendible es la afirmación cualitativa de UC Master Gardeners (§1.4) sobre sombra de tarde.

Por qué no hay nada mejor: la quemadura solar de crasas ornamentales no tiene impacto económico, así que ni la extensión agraria ni los jardines botánicos han publicado ensayos sobre ella.

### 4.6 Datos que se buscaron y no existen

- **Dosis de abono para una maceta concreta de Aloe.** Todas las fuentes A dan kg/ha o g/planta *en campo*; las B dan "media dosis de etiqueta" sin especificar concentración. No hay un número defendible en g o ml para 7,5 L.
- **Resultado del ensayo de fertilización de Córdoba.** El estudio declara la fertilización en cobertera (humus de lombriz, 0,50 kg/planta, primavera y otoño) como variable independiente en Material y Métodos, pero **ni los Resultados ni las Conclusiones publican su efecto**. Sólo se reportan riego y manejo del suelo. Es decir: el único ensayo español que midió el abonado del Aloe **no publicó el dato**.
- **Umbral de temperatura para suspender el abonado.** "No abonar en invierno" es 📅 en todas las fuentes; ninguna lo ata a una temperatura, que es lo que la app necesitaría para hacerlo bien.
- **Frecuencia de trasplante específica de *Aloe vera*.** Sólo hay reglas generales (3–5 años para plantas de maceta, 2–4 para crasas). Nada específico de la especie.

---

## 5. Qué puede decir la app de una planta sin identificar

Este es el caso normal al añadir una planta, no la excepción. La conífera del jardín lo ilustra: el usuario sabe "es un abeto", y eso ni siquiera fija el género (§2.7).

### 5.1 El principio: separar lo que protege de lo que optimiza

De todo lo investigado, las afirmaciones se parten limpiamente en dos:

**Consejos de seguridad — no dependen de la especie, sólo del grupo funcional.** Son irreversibles si se incumplen, y por tanto son los de mayor valor por unidad de certeza:

- *No cortes más allá de donde hay acículas verdes: no volverá a brotar y el hueco es permanente.*
- *No cortes el eje principal.*
- *No reduzcas más de un tercio de la altura.*
- *Si crece bien, no la abones.*
- *No podas durante ola de calor o sequía prolongada.*

Estos cinco se pueden dar **hoy**, con cita, sin conocer la especie. Cuatro de los cinco son consejos **negativos** — cosas que no hacer.

**Consejos de optimización — exigen la especie.** Ventana exacta de poda, método (pinzar velas vs. cortar a yema lateral vs. recortar), tolerancia a la reducción, susceptibilidad a plagas, necesidad de protección invernal. Aquí la app **no debe adivinar**: la tabla de §2.6 muestra que el método correcto para un pino es dañino para una tuya y viceversa.

### 5.2 Propuesta de modelo: nivel de confianza taxonómica

En vez de tratar "sin identificar" como un estado de error, tratarlo como un **nivel** con contenido propio:

| Nivel | Lo que la app sabe | Lo que puede decir |
|---|---|---|
| **Grupo funcional** ("conífera ornamental de jardín") | Fisiología compartida | Los 5 consejos de seguridad. Ventana de poda **amplia y con veto climático**. "No abonar" por defecto |
| **Género** (*Abies*, *Picea*, *Thuja*, *Cupressus*, *Pinus*, *Taxus*…) | Método y ventana de poda; tolerancia a reducción; plagas del género | Todo lo anterior + método concreto + ventana estrecha + aviso de plaga |
| **Especie / cultivar** | Rusticidad, porte final, cultivares enanos intolerantes | Todo + umbral de frío + si es un enano que no se debe podar |

El salto que más valor aporta **no es especie → cultivar, sino grupo → género**. Y el género de una conífera es identificable por foto con relativa fiabilidad (cono erecto vs. colgante, acícula vs. escama), mucho más que la especie. **La app debería pedir el género, no la especie.**

### 5.3 Consecuencias de diseño

1. **La ficha de planta necesita un campo de grupo funcional independiente del taxón**, poblable sin identificación: `conífera ornamental`, `crasa/CAM`, `arbusto mediterráneo leñoso`, `herbácea de temporada`. Es lo que engancha el conjunto de consejos de seguridad.
2. **Los consejos negativos son consejos de primera clase.** "No la abones" y "no cortes ahí" son resultados válidos de una tarea, no huecos por rellenar. Este documento produce más consejos negativos que positivos, y eso es la respuesta correcta para un jardín doméstico.
3. **Cada afirmación lleva su escalón y su granularidad en el modelo de datos**, no sólo en esta ficha. Sin eso, la síntesis de §2.2 (inferencia del proyecto) queda indistinguible de la cita del RHS.
4. **No rellenar con blogs.** Cuando sólo hay escalón C —§4.5, la aclimatación al sol— la app debe decir "no lo sé con fundamento" y ofrecer la observación cualitativa, en vez de inventar "dos semanas de transición".

---

## 6. Límites y supuestos declarados

1. **El escalón A español sobre coníferas ornamentales domésticas está vacío** (§4.1). Todo el §2 descansa en RHS (clima atlántico) y extensión norteamericana (clima continental). El ajuste climático a España peninsular **no está validado por ninguna fuente**.
2. **Las tres fuentes A de Aloe vera son de cultivo en suelo con riego por goteo y objetivo de rendimiento en hoja.** Sus umbrales fisiológicos (frío, calor, floración, hijuelos) se trasladan a la maceta; sus dosis de abonado **no**.
3. **El ensayo de Córdoba mide crecimiento, no supervivencia.** El +37 % con manta térmica se obtuvo en un invierno cordobés sin heladas severas. No dice qué pasa a −2 °C, ni con qué umbral activar la protección.
4. **El umbral de frío en maceta es más severo que el de suelo, y en cuánto no se sabe** (§4.4).
5. **La ventana de poda de coníferas para España es una síntesis del proyecto** (§4.2), no una cita.
6. **La discrepancia RHS (rico en K) vs. UC (bajo en N) sobre el abono del Aloe se resolvió por convergencia de vector, no por una fuente que arbitre.**
7. **El abonado es intrínsecamente 📅.** Ninguna fuente lo ata a temperatura ni a fenología. La app no puede hacerlo mejor que un recordatorio estacional, y fingir lo contrario sería inventar.
8. **"Abeto" no identifica ni el género** (§2.7), y el respaldo de esa afirmación es escalón C. Se usa como argumento de diseño de UI, no como hecho agronómico.
9. **Ninguna fuente cubre "terraza suroeste"** como microclima. Es geometría doméstica, el mismo hueco que `modelo-agronomico-riego.md` §4.3 declara para la lluvia.
10. **La poda de un ejemplar arraigado que nadie riega y nadie mira es, casi siempre, ninguna tarea.** El resultado más probable y mejor respaldado de este módulo para la conífera es que la app **no genere ninguna tarea**, y explique por qué. Eso hay que diseñarlo como éxito, no como vacío.

---

## Fuentes

**Escalón A — administración pública española**

- [Comunidad Autónoma de la Región de Murcia — *Cultivo de Aloe Vera en la comarca del Campo de Cartagena*](https://www.carm.es/web/integra.servlets.Blob?ARCHIVO=39.-+Cultivo+de+Aloe+Vera+en+la+comarca+del+Campo+de+Cartagena.pdf&TABLA=ARCHIVOS&CAMPOCLAVE=IDARCHIVO&VALORCLAVE=152285&CAMPOIMAGEN=ARCHIVO&IDTIPO=60&RASTRO=c498%24m1259%2C20538) — David Garrido Gómez, Oficina Comarcal Agraria Cartagena-Oeste; Consejería de Agua, Agricultura, Ganadería y Pesca; Programa de Innovación Tecnológica, Cultivos Industriales nº 39; DL MU-196-2018. **Licencia: "Se autoriza la reproducción total o parcial citando la fuente".** (umbral −2 °C, rango 18–40 °C, óptimo 20–25 °C, olas de calor, despunte de flor, desahijado, fertilización mínima)
- [Diputación de Córdoba — *Caracterización del cultivo y manejo de Aloe vera en la provincia de Córdoba*, 2016](https://www.dipucordoba.es/wp-content/uploads/2020/11/8ensayoaloevera.pdf) — Centro Agropecuario Provincial + ETSIAM, Universidad de Córdoba. **Licencia no declarada.** (ensayo de invierno con manta térmica: +37 % peso de hoja, +21 % longitud, +27 % hijuelos)
- [Cabildo de Lanzarote, Servicio Insular Agrario — *Fichas técnicas de cultivos de Lanzarote: Aloe*, enero 2012](https://www.cabildodelanzarote.com/documents/35307/78325/ficha_tecnica_del_cultivo_de__aloe_vera.pdf/6d484d29-6142-b195-ec78-c48a7d17793d?t=1620721613744) — **Licencia no declarada.** (óptimo 19–25 °C, "no soporta heladas", N+P, compost a la plantación, 20 g/planta de sulfato amónico, deshije cada dos años)
- [Comunidad de Madrid — Arbolado urbano](https://www.comunidad.madrid/servicios/urbanismo-medio-ambiente/arbolado-urbano) — prohibición de poda drástica y extemporánea. Legislación, no agronomía utilizable.

**Escalón B — extensión y jardines botánicos de clima trasladable**

- [RHS — *How to grow conifers*](https://www.rhs.org.uk/plants/types/conifers/growing-guide) (ventana abril–agosto, madera vieja, abonado, riego de establecimiento, rusticidad)
- [RHS — *Conifers: brown patches*](https://www.rhs.org.uk/problems/conifers-brown-patches) (excepción del tejo, abril/junio/agosto, muerte regresiva de octubre, sequía y calor, *Cinara cupressivora*)
- [RHS — *Hedges: renovation*](https://www.rhs.org.uk/plants/types/hedges/renovation) (renovación, un tercio en abril, *Cupressus*/*Chamaecyparis*/×*Cuprocyparis*)
- [RHS — *How to repot a plant*](https://www.rhs.org.uk/container-gardening/how-to-repot-a-plant) (primavera, señales, un tercio mayor, 3–5 años)
- [RHS — *How to grow cacti and succulents*](https://www.rhs.org.uk/plants/types/cacti-succulents/houseplants/growing-guide) (abonado mensual abril–septiembre, sustrato, maceta no demasiado grande, invernada 8–10 °C)
- [RHS — *Aloe plants including Aloe vera*](https://www.rhs.org.uk/getmedia/d02059a1-4e2c-45c3-956b-da5cc90db7f1/Indoor-plants-resources.pdf) (PDF; abonado, sustrato 50 % arena/grava, separación de hijuelos, no soporta congelación)
  Todas las anteriores: **© Royal Horticultural Society, Registered Charity 222879/SC038262. Sin licencia abierta declarada.**
- [Colorado State University Extension — CMG GardenNotes #617, *Pruning Evergreens*](https://csfs.colostate.edu/wp-content/uploads/2024/01/Pruning-Evergreens.pdf) — Whiting & O'Meara, rev. 2018/2023. **Licencia: "may be reproduced, without change or additions, for nonprofit educational use with attribution", © CSU Extension.** (grupos por hábito de ramificación, ventanas por género, líder central, cultivares enanos, recorte a tijera)
- [The Morton Arboretum — *How to prune evergreen trees and shrubs*](https://mortonarb.org/plant-and-protect/tree-plant-care/plant-care-resources/how-to-prune-evergreen-trees-and-shrubs/) — **Licencia no declarada.** (zona muerta, diferencias por género, tejo antes de julio)
- [Missouri Botanical Garden Plant Finder — *Aloe vera*](https://plantfinder.mobot.org/PlantFinderDetails.aspx?kempercode=b628) — **Licencia no declarada.** (zona 10–12, mínimo 40 °F en invierno, pleno sol, propagación por hijuelos)
- [UC Master Gardeners of Orange County — *Succulents*](https://ucanr.edu/site/uc-master-gardeners-orange-county/succulents-orange-county) — **© UC Regents, licencia no declarada.** (abonado bajo en N a media/cuarta dosis, cambio de sustrato cada 2–4 años, dividir en primavera u otoño, sombra de tarde, quemadura)
- [NC State Extension Plants — *Aloe vera*](https://plants.ces.ncsu.edu/plants/aloe-vera/) (pleno sol o semisombra, zonas 10–12)

**Norma técnica española, citada pero no accesible**

- NTJ 14C Parte 2:1998, *Mantenimiento del arbolado: Poda*, y Manual Práctico nº 9, *Los trabajos de poda del arbolado urbano* (2002), sección 27 "La poda de las coníferas" — [índice público](https://www.ntjdejardineria.org/ntj/mp9-los-trabajos-de-poda-del-arbolado-urbano/). **De pago; contenido no verificado.**

**Escalón C — sólo como argumento de diseño, nunca como hecho agronómico**

- Material divulgativo español y de prensa sobre la confusión *Abies* / *Picea* / "abeto" (§2.7).
- Blogs y viveros sobre aclimatación al sol y quemadura del Aloe (§4.5). **No usados como respaldo cuantitativo.**

**Consultadas y NO verificables (no se citan como respaldo)**

- University of Minnesota Extension, *Fertilizing evergreens* — HTTP 403 y bloqueo anti-bot; sólo visto en resumen de búsqueda.
- University of Maryland, UNH, Clemson y NC State sobre abonado de árboles establecidos — sólo resúmenes de búsqueda, no leídas contra el original.

