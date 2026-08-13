# Tareas no hídricas de dos plantas reales: olivo y rosal

> Investigación de fuentes primarias sobre **poda, abonado, protección invernal/estival y trasplante**.
> Fecha: 2026-08-13. Todas las citas están verificadas contra el PDF o la página original (no contra resúmenes), salvo donde se indique lo contrario.
> Alcance: dos ejemplares concretos del inventario, ambos **arraigados en suelo**, en **España peninsular**:
> 1. **Olivo** (*Olea europaea*) ornamental de jardín particular, **que nadie riega**.
> 2. **Rosal** (*Rosa* sp.) de jardín, pegado a **fachada noroeste bajo alero** — poca lluvia directa, poco sol directo.
>
> Contexto previo que no se reinvestiga: ninguna de las ~20 bases de datos estructuradas de plantas evaluadas tiene campo de ventana de abonado. Esto es literatura agronómica leída y citada a mano.

---

## Jerarquía de fuentes usada

| Escalón | Definición | Qué se ha encontrado aquí |
|---|---|---|
| **A** | Publicación oficial española: MAPA, IFAPA/SERVIFAPA, BOJA, extensión agraria estatal o autonómica, universidad española | **Olivo: abundante.** IFAPA (2 documentos), MAPA (guía de fertilización), BOJA (Reglamento de PI de Olivar 2026). **Rosal: existe, pero es antigua** — dos *Hojas Divulgadoras* del Ministerio de Agricultura (1954 y 1980) |
| **B** | Extensión agraria de otro país con clima trasladable | Rosal: **RHS** (Reino Unido, clima **no** trasladable sin ajuste) y **UC ANR / Master Gardeners de San Luis Obispo** (California, clima mediterráneo, **sí** trasladable) |
| **C** | Viveros, blogs, medios | **No se ha usado ninguna fuente C.** No ha hecho falta: todas las tareas preguntadas tienen al menos una fuente A o B |

---

## Resumen: qué se puede afirmar y con qué granularidad

Leyenda de **granularidad**: `MES` = mes de calendario · `FEN` = fase fenológica observable · `CLIMA` = condición sobre el clima real, evaluable con datos horarios/diarios de Open-Meteo.

### Olivo (*Olea europaea*), en suelo, sin riego

| Tarea | Ventana que dice la fuente | Escalón | Granularidad | ¿Convertible a condición climática? |
|---|---|---|---|---|
| **Poda** | "cuando el olivo está en **parada vegetativa invernal**, normalmente después de la recolección" | **A** (IFAPA 2020) | FEN | **Parcialmente**. La fuente no da temperatura. Pero otra fuente A da el umbral de arranque vegetativo (10–12 °C), lo que permite *derivar* el cierre de ventana — ver §3 |
| **Poda fuera de plazo** | Se puede seguir podando con movimiento de savia; "peor cicatrización", pero "el daño causado en general no tiene importancia suficiente como para no recomendar la poda" | **A** (IFAPA 2020) | FEN | Sí (misma condición, invertida). **La penalización es leve** — dato importante para el tono de la app |
| **Frecuencia** | "La práctica habitual es la **poda bienal**" | **A** (IFAPA 2020) | — | No (es un intervalo, no una fecha) |
| **Abonado — ¿aplica?** | El método de la guía calcula la dosis **a partir de la cosecha esperada** (15–20 kg N por cada 1.000 kg de aceituna). Sin cosecha, la base de cálculo desaparece | **A** (MAPA 2010) | — | **N/A por diseño.** Ver §1.2: el caso "ornamental sin cosecha y sin riego" **no está cubierto por ninguna fuente A** |
| **Abonado — cuándo (si se hace)** | Al suelo: "inmediatamente después de la recolección, para **aprovechar todas las lluvias primaverales** y posibilitar el paso de los nutrientes a la solución del suelo" | **A** (MAPA 2010) | MES + CLIMA | **Sí, y es la mejor conversión de todo el documento**: la utilidad del abono depende de que llueva |
| **Abonado — regla de disparo** | "Los nutrientes incorporados al suelo, se ponen cerca de las raíces **cuando se prevén lluvias**, especialmente en el caso del nitrógeno" | **A** (BOJA 2026, práctica *recomendada*) | **CLIMA** | **Sí, directamente**: `precipitation_sum` del pronóstico |
| **Abonado foliar** | "La aplicación debe hacerse en primavera" (N, urea); K "preferiblemente debe utilizarse en el otoño". Se favorece "si la temperatura ambiental es suave, si la humedad ambiente es elevada" | **A** (MAPA 2010) | FEN + CLIMA | **Sí** (temperatura suave + HR alta son variables de Open-Meteo) |
| **Protección invernal** | Aguanta "hasta 10-12 ºC bajo cero en pleno reposo invernal" | **A** (MAPA 2010) | **CLIMA** | **Sí** (`temperature_2m_min`). En la mayor parte de España peninsular **nunca se alcanza** → la tarea normalmente no procede |
| **Protección estival** | "Soporta altas temperaturas en verano, **hasta 40 ºC si tiene humedad suficiente en el suelo**". Parada vegetativa a 35-38 ºC | **A** (MAPA 2010) | **CLIMA** | **Sí, y engancha con el modelo hídrico ya diseñado**: el umbral no es la temperatura sola, es temperatura **condicionada al agua del suelo** |
| **Trasplante** | — | — | — | **Nada.** No aplica a un ejemplar arraigado y ninguna fuente A/B lo trata |

### Rosal (*Rosa* sp.), en suelo, fachada NO bajo alero

| Tarea | Ventana que dice la fuente | Escalón | Granularidad | ¿Convertible a condición climática? |
|---|---|---|---|---|
| **Poda (remontantes)** | "desde **finales de diciembre a mitad de marzo**" | **A** (MAPA, HD 5-6/1980) | MES | No por sí sola |
| **Poda — ajuste climático** | "La **poda tardía** es aconsejable en aquellos climas donde sean de temer las **heladas tardías** que pueden destruir los brotes tiernos recién salidos" | **A** (HD 5-6/1980) | **CLIMA** | **Sí**: fecha de última helada / riesgo de helada en el pronóstico |
| **Poda — clima suave** | "En climas suaves, donde la brotación suele ser muy temprana, se debe podar **con cierta anticipación**" | **A** (HD 5-6/1980) | FEN + CLIMA | Sí, pero **sin umbral numérico**: la fuente no dice cuánto es "suave" |
| **Poda mediterránea, doble** | "Una, la poda clásica, **en enero**. Otra, bastante ligera, **hacia mitad de agosto**" | **A** (HD 5-6/1980) | MES | No |
| **Poda (no remontantes)** | "después de que hayan florecido, lo que ocurre, en general, en el mes de **julio**" | **A** (HD 5-6/1980) | FEN + MES | No |
| **Poda — intensidad** | Ramos finos ("grosor de un lápiz") a 2-3 yemas; gruesos ("grosor del dedo pulgar") a 5-6 yemas. En zona mediterránea "la poda debe ser más larga" | **A** (HD 5-6/1980) | FEN | No (es diagnóstico visual, no fecha) |
| **Abonado — cuándo** | "En el mes de **enero**, cada año, se da una cava profunda, enterrando una cantidad de estiércol descompuesto" | **A** (MAPA, HD 13-54/1954) | MES | No |
| **Abonado — dosis moderna** | 70 g/m² de abono de uso general o específico de rosal, "**twice a year: in March/April before flowering** [y] **in mid-summer after the first flush of flowers**" | **B** (RHS, clima británico) | MES + FEN | No. **Requiere ajuste climático explícito** (ver §2.2) |
| **Abonado — regla de disparo** | "Four weeks after pruning, **fertilize prior to rainfall or water deeply**" | **B** (UC ANR, clima mediterráneo) | FEN + **CLIMA** | **Sí, directamente**, y es la única fuente que reconoce que el abono **necesita agua para servir de algo** |
| **Protección invernal** | "La protección invernal **sólo se precisa en climas muy fríos**. Para el rosal en forma baja bastará, en general, un aporcado un poco alto" | **A** (HD 13-54/1954) | — | **Sí, por negación**: en España peninsular típica, la tarea **no procede** |
| **Protección estival** | Existe un "**reposo estival** que se produce naturalmente por los fuertes calores", que "puede favorecerse limitando y suprimiendo el abonado" | **A** (HD 5-6/1980) | FEN | Parcialmente: **sin umbral de temperatura**. Ver §5 |
| **Trasplante / plantación** | "Se puede plantar en **otoño, en noviembre-diciembre**, o en **primavera, de enero a marzo**. La plantación otoñal suele dar mejores resultados, con tal de que no se trate de un clima muy frío" | **A** (HD 13-54/1954) | MES | No |

**Titular**: el olivo está **sobradamente documentado por fuentes A españolas y vigentes** (2018, 2020, 2026). El rosal **sí tiene respaldo institucional español, pero de 1954 y 1980** — la búsqueda previa que no encontró nada estaba mirando en el sitio equivocado: el material vive en la biblioteca de *Hojas Divulgadoras* del MAPA, no en las publicaciones agrarias modernas. Ver §4 para el desmentido detallado.

---

## 1. Olivo (*Olea europaea*)

### 1.1 Poda

**Fuente principal (A):** IFAPA, *Manual Formativo: Poda del Olivar*, Sevilla, 2020, UD 1, §1.4 "Bases agronómicas".
[Ficha](https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/d5778c9f-41bd-4ec1-a1c7-2331a3215933) · [PDF](https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/d5778c9f-41bd-4ec1-a1c7-2331a3215933/download)

> *"El mejor momento para realizar la poda es cuando el olivo está en parada vegetativa invernal, normalmente después de la recolección cuando esta no es muy temprana. En este periodo se minimizan los daños sufridos por los cortes realizados además de disponer de todo el crecimiento vegetativo de primavera."*

Y sobre podar **fuera de plazo**, que es lo que preguntaba el encargo:

> *"En el caso de que por diversas circunstancias, se inicie el movimiento de savia y la poda aún no se haya finalizado, esta se debe seguir realizando. Los cortes realizados con movimiento de savia tienen peor cicatrización, pero debido al carácter rústico del olivo, el daño causado en general no tiene importancia suficiente como para no recomendar la poda. En el caso de cortes de gran diámetro, en época de movimiento de savia, se podría aplicar algún cicatrizante existente en el mercado."*

> *"Un inconveniente de los cortes en esta época tardía, independiente de la cicatrización, podría ser la proliferación de la Euzophera, para lo cual habría que vigilar la posible incidencia de la misma y tomar las medidas de control correspondientes."*

Frecuencia:

> *"La frecuencia con la que debe realizarse la poda ha sido objeto de algunos ensayos de campo a lo largo de los años. Ha sido difícil determinar una frecuencia adecuada de poda... La práctica habitual es la poda bienal con una intensidad que permita esta frecuencia."*

Y una operación estival, separada de la poda principal:

> *"En verano se realiza la práctica de 'desvareto', que consiste en podar las varetas (brotaciones en la base del tronco)."*

**Conclusión operativa**: la penalización por podar tarde es **leve y cualitativa** (peor cicatrización, riesgo de *Euzophera*). Es un caso donde la app **no debe alarmar**: "ya vas tarde, pero el olivo lo tolera" es la lectura fiel de la fuente.

**Objetivo de la poda (A):** IFAPA, *Poda del Olivo: Poda de Producción*, Córdoba, febrero de 2018.
[Ficha](https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/0e496cd1-2448-4898-a1c7-998a71d87a6e) · [PDF](https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/0e496cd1-2448-4898-a1c7-998a71d87a6e/download)

> *"La poda de producción tiene por objetivo alargar al máximo el periodo productivo del olivo, manteniendo un volumen de copa compatible con el medio productivo y con la correcta aplicación de las prácticas de cultivo. De esta manera se optimiza la cantidad de radiación solar interceptada por la hojas."*

Este documento **no contiene ninguna referencia a fechas, meses ni heladas** (verificado por búsqueda de texto sobre el PDF completo): es puramente criterio de corte. Lo relevante para un ejemplar ornamental es el aviso contra el aclareo excesivo:

> *"Es importante mantener el interior de la copa cubierto con ramas de poco vigor con el fin de impedir una entrada de luz excesiva que pueda provocar quemaduras en las ramas principales del olivo."*

Y una advertencia explícita para secano, que es el caso de este ejemplar:

> *"Para el olivar en secano, esta porosidad debería ser algo mayor, para evitar un exceso de transpiración."*
> (§ Aclareo, *Manual Formativo* 2020)

**Norma vigente (A):** Orden de 30 de julio de 2026, **Reglamento Específico de Producción Integrada de Olivar**, BOJA núm. 153, 10 de agosto de 2026.
[BOJA](https://www.juntadeandalucia.es/boja/2026/153/4.html) · [PDF oficial](https://www.juntadeandalucia.es/eboja/2026/153/BOJA26-153-00038-10902-01_00342132.pdf)

Del cuadro de prácticas de **PODA**:

- Obligatoria: *"Se realiza con la frecuencia necesaria, respetando la tendencia natural de la especie y variedad y manteniendo una relación hoja – madera equilibrada."*
- Prohibida: *"En olivar de almazara, podas severas que no respeten el equilibrio hoja-raíz o que abran excesivamente los árboles, dejando el interior de la copa expuesto al sol, excepto cuando sea necesario realizar la poda de renovación."*
- Recomendada: *"El periodo de poda se ajusta a los meses con menor nidificación o presencia de nidos."*

**Hallazgo que conviene declarar**: la norma vigente **no fija ninguna ventana de calendario para la poda**. La única restricción temporal que impone es **ecológica** (nidificación de aves), no fenológica. Quien esperase encontrar "poda: de X a Y" en el reglamento, no la va a encontrar.

### 1.2 Abonado — y si aplica siquiera

**Fuente (A):** MAPA (entonces MARM), *Guía práctica de la fertilización racional de los cultivos en España*, Parte II, cap. 27 "Abonado del olivar", por Casimiro García García (UPM), 2010, pp. 223-233.
[PDF Parte II](https://www.mapa.gob.es/dam/mapa/contenido/agricultura/publicaciones/02_fertilizacion-baja-.pdf) · [PDF Parte I](https://www.mapa.gob.es/dam/mapa/contenido/agricultura/publicaciones/01_fertilizacion-baja-.pdf)

#### ¿Aplica a un ejemplar ornamental que nadie riega? — La respuesta honesta es *no, tal cual*

Toda la guía calcula la dosis **desde la cosecha esperada**:

> *"El agricultor, si quiere atender las necesidades nutritivas del olivar, tendrá que establecer un programa de abonado racional basado en las extracciones de nutrientes por el olivo en relación a la producción esperada, la fertilidad del suelo, el estado de nutrición del árbol y los nutrientes aportados por las reservas contenidas en tallos y hojas viejas."*

Con la tabla 27.4 de extracciones: **N 15-20, P₂O₅ 4-5, K₂O 20-25 kg por cada 1.000 kg de aceituna recogida**.

En un olivo ornamental **no se recoge la aceituna** (o se recoge simbólicamente), y **la aceituna cae al suelo bajo el propio árbol**. La propia guía dice de la fracción no exportada:

> *"Las inflorescencias, botones florales y frutos pequeños recién cuajados, caídos al suelo durante el proceso de floración y cuajado, y las hojas viejas que se desprenden, suponen una reincorporación al suelo de nutrientes que, a medio y largo plazo, pueden ser aprovechados por el olivo."*

**Derivación del proyecto (no es cita)**: con extracción ≈ 0 y reciclado in situ de hoja y fruto, el término dominante del método de la guía se anula. **La ventana de abonado del olivar es una ventana de reposición de cosecha, y sin cosecha no hay qué reponer.** La app debería, por defecto, **no programar tarea de abonado** para un olivo ornamental arraigado, y decir por qué.

Refuerza esta lectura la propia guía al describir la historia del cultivo:

> *"El olivar antes no se abonaba y ahora se abona, pasando la fertilización de ser algo secundario a ser fundamental en una explotación olivarera."*

Es decir: el abonado del olivo es una técnica **de intensificación productiva**, no un requisito de supervivencia del árbol.

#### Si aun así se abona: cuándo, y por qué el "cuándo" es climático

> *"El fósforo y el potasio pueden incorporarse en otoño, si se aplican por separado, o después de la recolección si se aportan junto al nitrógeno. Cuando se aplican los tres elementos juntos, mediante un abono complejo, o se aplican sólo abonos nitrogenados es preferible hacer la aplicación inmediatamente después de la recolección, **para aprovechar todas las lluvias primaverales y posibilitar el paso de los nutrientes a la solución del suelo**."*

Esta es **la frase clave de todo el documento** para el diseño de la app: el motivo de la fecha **no es la fecha**, es que llueva. Un abono granulado en suelo seco no entra en solución y no hace nada.

El Reglamento de PI de Olivar 2026 lo convierte en práctica **recomendada** y en condición pura:

> *"Los nutrientes incorporados al suelo, se ponen cerca de las raíces cuando se prevén lluvias, especialmente en el caso del nitrógeno."*
> (BOJA 153/2026, cuadro ENMIENDAS Y FERTILIZACIÓN, columna "Recomendadas")

Y el reparto fenológico, también del reglamento vigente:

> *"Se recomienda aplicar el 70 % de las necesidades de N desde salida del reposo hasta endurecimiento del hueso. En el caso del K, aplicar el 70 % de las necesidades desde endurecimiento del hueso hasta final de campaña."*

#### Dosis, si la fuente la da

De la guía MAPA (olivar tradicional, aplicación al suelo, **con cosecha**):

| Nutriente | Dosis citada |
|---|---|
| N | *"En el olivar tradicional se recomienda aplicar entre **0,5 y 1 kg N/árbol**, sin superar, en todo caso, 150 kg N/ha."* |
| K₂O | *"El olivar responde bien a las aplicaciones de potasio que se sitúan entre **1 y 2 kg K₂O/árbol**."* |
| P₂O₅ | *"En caso de deficiencia se puede aplicar **0,5 kg P₂O₅/árbol**."* |

Límite legal más restrictivo, del reglamento de PI vigente (práctica **prohibida** superarlo): **70 kg N/ha y año en olivar tradicional de secano**.

#### Abonado foliar: la vía que la guía recomienda **precisamente para secano**

> *"El olivo tiene una hoja que admite muy bien el abonado foliar; por tanto, **en secano y siempre que sea posible, se recomienda aportar los fertilizantes vía foliar**."*

Con ventanas y condiciones:

> *"Para la aportación de nitrógeno se puede utilizar urea cristalina... es preferible rebajar la concentración hasta la mitad y hacer dos aplicaciones al 2,5%. **La aplicación debe hacerse en primavera**."*
> *"Respecto al potasio, cuando se aplica nitrato potásico las concentraciones oscilan entre 1,25% y 2,5% y **preferiblemente debe utilizarse en el otoño**."*
> *"La absorción foliar de los nutrientes se favorece si la **temperatura ambiental es suave**, si la **humedad ambiente es elevada** y si el olivo tiene una proporción importante de hojas jóvenes, lo que sucede **de abril a julio**."*

**Esto es oro para la app**: "temperatura suave + humedad alta" son `temperature_2m` y `relative_humidity_2m` de Open-Meteo. Es una tarea que **sí** se puede disparar por condición climática real y no por calendario. La guía no numera "suave" ni "elevada" — ese umbral habría que fijarlo el proyecto y declararlo como supuesto.

#### Consecuencia de abonar mal (dato con efecto sobre otra tarea)

> *"Un abonado nitrogenado excesivo no mejora la calidad del aceite ni la producción, **aumenta la sensibilidad a las heladas** y a las enfermedades y retrasa la maduración de los frutos."*

> *"El potasio... **Aumenta la resistencia del árbol a las heladas** y a las enfermedades criptogámicas."*

Y una prohibición de calendario, que la propia guía MAPA resume del Reglamento de PI de 2008:

> *"Está prohibido superar en secano 70 kg N/ha en olivar tradicional... También aplicar los fertilizantes **en diciembre y enero sobre suelo desnudo**."*

(**Aviso de vigencia**: esa prohibición de diciembre-enero procede del reglamento de 2008 citado en la guía de 2010. El reglamento de **2026** la sustituye por una remisión genérica: *"Aporte de fertilizantes nitrogenados durante los periodos de prohibición, establecidos en la normativa vigente"*, que son los programas de actuación de zonas vulnerables a nitratos. No se ha verificado el calendario concreto de esos programas, que además varía por CCAA.)

### 1.3 Protección invernal y estival

Umbrales, todos de la **misma fuente A** (MAPA 2010, cap. 27, "Exigencias climáticas y edáficas"):

> *"En las condiciones españolas el cultivo está limitado por el frío en las regiones más septentrionales del país. **Soporta altas temperaturas en verano, hasta 40 ºC si tiene humedad suficiente en el suelo, y hasta 10-12 ºC bajo cero en pleno reposo invernal.**"*

> *"La inducción floral se produce en el periodo de reposo estival y al final del invierno o comienzos de primavera se inicia la diferenciación de las yemas. **Con temperaturas primaverales de 10-12 ºC se inicia el desarrollo vegetativo**, las inflorescencias y la floración se producen **entre 15 y 18 ºC** sobre la madera del año anterior. **Cuando las temperaturas estivales llegan a 35-38 ºC tiene lugar una parada vegetativa.**"*

**Lectura para este ejemplar**:

- **Protección invernal: no procede** en la mayor parte de España peninsular. El umbral de daño (−10/−12 °C en pleno reposo) está por debajo de las mínimas habituales salvo en interior de meseta norte y zonas de montaña. Es una tarea que la app debería **generar sólo si el pronóstico la justifica**, no por calendario.
- **Protección estival: el umbral no es una temperatura, es una temperatura condicionada al agua**. "Hasta 40 ºC **si tiene humedad suficiente en el suelo**" — y este olivo **no lo riega nadie**. Es exactamente el punto donde la tarea no hídrica se acopla al modelo de balance hídrico ya diseñado en [`modelo-agronomico-riego.md`](./modelo-agronomico-riego.md): `Tmax ≥ 35 °C` **y** agotamiento alto del depósito de suelo → aviso; `Tmax ≥ 35 °C` con suelo con reservas → no.

**Lo que la fuente no dice**: cómo proteger. Ninguna fuente A consultada describe medidas de protección (mallas, acolchado, encalado de tronco) para un olivo de jardín. Ese consejo, hoy, **no tiene respaldo**.

### 1.4 Trasplante

**No aplica y no hay fuente.** El ejemplar está arraigado en suelo. Ninguna de las cuatro fuentes A del olivo (IFAPA 2018 ×2, IFAPA 2020, MAPA 2010, BOJA 2026) trata el trasplante de un olivo adulto ya establecido. Una búsqueda específica sólo devolvió contenido comercial de almazaras y viveros (escalón C), que se descarta por innecesario: **la recomendación correcta para un ejemplar arraigado es no trasplantarlo**, y esa recomendación no necesita cita porque no es una afirmación agronómica sino la ausencia de una tarea.

---

## 2. Rosal (*Rosa* sp.)

### 2.1 Poda

**Fuente (A):** Antonio Verdeguer Monge, Ingeniero Agrónomo del **Servicio de Extensión Agraria**, *Poda del rosal y de otros arbustos ornamentales*, Hoja Divulgadora núm. 5-6/80 HD, **Ministerio de Agricultura**, 1980. ISBN 84-341-0229-3.
[PDF](https://www.mapa.gob.es/ministerio/pags/biblioteca/hojas/hd_1980_05-06.pdf)

El documento declara explícitamente su alcance, que coincide con nuestro caso:

> *"...dedicando una atención especial al rosal, dada su excepcional difusión y popularidad, **sin entrar en la poda del rosal cultivado específicamente para flor cortada**."*

Sección **"Época de poda"**, citada íntegra por su valor:

> *"Los rosales no remontantes se deben podar después de que hayan florecido, lo que ocurre, en general, en el mes de julio."*
>
> *"En los rosales remontantes la época de poda varía según la región en que se cultiven y según la rusticidad de las plantas. Si trabajamos con muchas clases de rosales, debemos podar primero los más rústicos. Se empezaría con los Polyanthas y Floribundas para terminar con los híbridos de té."*
>
> *"**En general, la poda puede realizarse desde finales de diciembre a mitad de marzo. La poda tardía es aconsejable en aquellos climas donde sean de temer las heladas tardías que pueden destruir los brotes tiernos recién salidos.** En estos lugares es conveniente hacer una poda de espera o prepoda, en diciembre, consistente en recortar las ramas demasiado largas, 1 a 1,5 metros, dejando los rosales a una altura de 40-50 centímetros del suelo. De este modo, además de mejorar ligeramente el aspecto de los rosales durante el invierno, se disminuye la resistencia de las plantas al viento, reduciéndose el riesgo de que, en caso de vientos fuertes, se despeguen los injertos jóvenes en los rosales de pie alto y de que se descalcen los rosales bajos."*
>
> *"**En climas suaves, donde la brotación suele ser muy temprana, se debe podar con cierta anticipación.**"*
>
> *"**En las regiones de clima muy benigno, como la zona mediterránea, se aconseja hacer dos podas. Una, la poda clásica, en enero. Otra, bastante ligera, hacia mitad de agosto.** Esta última va precedida, en julio o agosto, de un período de reposo de los rosales. Este reposo estival que se produce naturalmente por los fuertes calores, puede favorecerse limitando y suprimiendo el abonado. De esta manera, con el reposo y la poda posterior se consigue una floración más abundante, y de calidad, en otoño."*

**Qué ocurre si se poda fuera de plazo**, según esta fuente:

- **Demasiado pronto** en zona con heladas tardías → *"heladas tardías que pueden destruir los brotes tiernos recién salidos"*. Es la única penalización explícita y es **de naturaleza climática, no de calendario**.
- **Demasiado tarde**, en clima suave con brotación temprana → la fuente no describe el daño; sólo dice que hay que anticiparse. **Sin respaldo para cuantificar la penalización.**

Objetivo e intensidad:

> *"Al podar un rosal, lo primero que hay que hacer es suprimir totalmente las ramas muertas y las muy debilitadas."*
>
> *"Los ramos débiles, con el grosor aproximado al de un lápiz, se podarán a dos-tres yemas. Los ramos vigorosos, del grosor del dedo pulgar, se podarán a cinco-seis yemas. Se cortará sobre una yema que 'mire' al exterior del rosal, con objeto de ensanchar el vaso y evitar que el brote salga hacia el centro de la planta."*

Y un detalle **directamente pertinente a un rosal que casi no recibe agua**: la fuente hace depender la longitud de poda del riego y el abonado que realmente vaya a recibir la planta.

> *"...los factores determinantes del estado de la planta: el vigor y el modo de vegetar la variedad, influidas por el clima en el lugar del emplazamiento (en sitios de clima suave, como la zona mediterránea, **la poda debe ser más larga**) y por **los cuidados culturales que se vayan a realizar, principalmente abonado y riego**."*

**Derivación del proyecto (no es cita)**: un rosal bajo alero, con poca lluvia y sin riego, será poco vigoroso; sus ramas tenderán al "grosor de lápiz"; por la propia regla de la fuente, **la poda correcta para él es corta (2-3 yemas)**, no la poda larga que la misma fuente recomienda genéricamente para zona mediterránea. Las dos frases apuntan en direcciones opuestas y la fuente no resuelve el conflicto: es **criterio a declarar**, no hecho citado.

**Contraste climático (B):** RHS, *Rose Pruning: Floribunda & Hybrid Tea Roses*.
[rhs.org.uk](https://www.rhs.org.uk/plants/roses/modern-bush/pruning-guide)

> *"Roses can be pruned during late winter when growth is just resuming, usually in mid-February in the south, but in northern and colder areas wait until March."*

**Ajuste climático necesario**: el "sur" del RHS es el sur de Inglaterra, más frío y de brotación más tardía que cualquier punto de España peninsular. Trasladar "mediados de febrero" a España sería **tarde** — y coincide con lo que ya dice la fuente española de 1980 ("en climas suaves... podar con cierta anticipación", "la poda clásica, en enero"). El RHS aporta el criterio fenológico útil (*"when growth is just resuming"*) pero **sus meses no son trasladables**.

**Contraste mediterráneo (B):** UC ANR / UC Master Gardeners de San Luis Obispo County (California, clima mediterráneo comparable).
[ucanr.edu](https://ucanr.edu/site/uc-master-gardeners-san-luis-obispo-county/pruning-rose-bushes)

> *"late winter. In San Luis Obispo County, this is December–January for coastal areas and January–February for inland areas."*
> *"Annual pruning improves overall health and stimulates new growth for larger blooms and a more attractive plant."*

Este ajuste (diciembre-enero en costa, enero-febrero en interior, clima mediterráneo) **converge con la Hoja Divulgadora española**: "la poda clásica, en enero". Dos fuentes independientes, una A española y una B de clima análogo, coinciden. Ese es el nivel de confirmación más alto que se ha conseguido en todo este documento.

### 2.2 Abonado — y el problema del alero

**Fuente (A):** Juan Gostinchar, *Cultivo del rosal*, Hoja Divulgadora núm. 13-54-H, **Ministerio de Agricultura**, Madrid, 1954.
[PDF](https://www.mapa.gob.es/ministerio/pags/biblioteca/hojas/hd_1954_13.pdf)

Mantenimiento anual del rosal ya plantado:

> *"Durante el verano hay que dar las labores y riegos necesarios. **En el mes de enero, cada año, se da una cava profunda, enterrando una cantidad de estiércol descompuesto inferior a la aportada la vez primera.**"*

Y en plantación (dosis de referencia, por **área** = 100 m²):

> *"Se hace un desfonde de unos 50-60 centímetros de profundidad, enterrando, a unos 30-35 centímetros, una cantidad de **300-400 kilogramos de estiércol por área**."*

Nótese que la propia fuente encadena "**riegos necesarios**" con el abonado: el modelo mental de 1954 ya presupone que el rosal se riega.

**Dosis moderna (B):** RHS, *Roses: growing guide*.
[rhs.org.uk](https://www.rhs.org.uk/plants/roses/growing-guide)

> *"Sprinkle general-purpose or rose fertiliser on the soil around the plant, at a rate of **70g per sq m (2oz per sq yd)**."*
> *"Feed twice a year: **in March/April before flowering** [y] **in mid-summer after the first flush of flowers** (especially for repeat-flowering types)."*
> *"Mulch the soil with well-rotted manure or garden compost, leaving a gap of 10cm (4in) around the base of the stems."*

**Ajuste climático**: "March/April before flowering" en Reino Unido corresponde en España peninsular a un adelanto de varias semanas (la floración del rosal en España es notablemente más temprana). El **mes** no es trasladable; la **fase** ("antes de la floración", "tras la primera oleada de flores") sí lo es. Éste es un ejemplo limpio de por qué la app debe almacenar la **fase**, no el mes, cuando la fuente ofrece ambas.

#### El punto crítico: ¿sirve de algo abonar un rosal que no recibe agua?

La única fuente encontrada que se hace esta pregunta explícitamente es de clima mediterráneo:

**(B)** UC ANR / Master Gardeners SLO:

> *"Four weeks after pruning, fertilize prior to rainfall or water deeply."*

Es decir: **el abonado se ancla a la poda (4 semanas después) y se condiciona a que llueva o a que se riegue en profundidad**. No es una fecha: es una secuencia + una condición climática.

Y coincide con lo que dicen, para el olivo, las dos fuentes A españolas de §1.2: los nutrientes se aportan *"para aprovechar todas las lluvias primaverales y posibilitar el paso de los nutrientes a la solución del suelo"* (MAPA 2010) y *"cuando se prevén lluvias"* (BOJA 2026).

**Derivación del proyecto (no es cita, pero descansa sobre tres fuentes)**: este rosal está **bajo alero, en fachada noroeste**, es decir, en el escenario `exposicion_lluvia = cubierto` del modelo hídrico. Recibe poca o ninguna lluvia directa. Por tanto:

1. Un abono **granulado o sólido** aplicado a su pie **no entrará en solución** y no llegará a la raíz. La tarea, tal como la describen las fuentes, **es inefectiva para este ejemplar concreto**.
2. La única forma de abonarlo con sentido es **acompañarlo de un riego profundo deliberado** — que es exactamente lo que dice la fuente B ("*or water deeply*"). La app debería emitir la tarea **como tarea compuesta**: "abonar **y** regar a fondo", nunca "abonar" a secas.
3. Alternativamente, la app puede no proponer abonado y decir por qué. Es defendible: ninguna fuente A o B afirma que un rosal de jardín arraigado **necesite** abonarse para sobrevivir; lo que afirman es que se abona para **más flor y mejor flor**.

**Ninguna fuente encontrada, de ningún escalón, trata el caso "planta de jardín bajo alero".** Es un hueco de la literatura, igual que ocurría con la lluvia efectiva en el modelo hídrico.

### 2.3 Protección invernal y estival

**Invernal (A):** HD 13-54/1954:

> *"Hay muchas variedades rústicas que resisten las temperaturas inferiores a 0 ºC. Sin embargo, en climas muy duros y en variedades sensibles, es necesario suplir su poca resistencia con las debidas protecciones invernales."*

> *"**La protección invernal sólo se precisa en climas muy fríos. Para el rosal en forma baja bastará, en general, un aporcado un poco alto.** Los rosales de tallo alto se desligan de sus tutores, se doblan hacia el suelo, cubriendo sus ramas con paja, ramas de pino o también con tierra."*

**Lectura**: en España peninsular típica, **la tarea de protección invernal del rosal no procede**. La fuente no da un umbral numérico de "clima muy frío" — sólo el listón implícito de "inferiores a 0 ºC" como algo que muchas variedades ya resisten. **La conversión a condición climática es posible pero el umbral lo pone el proyecto, no la fuente.**

**Estival (A):** HD 5-6/1980, ya citado:

> *"Este reposo estival que se produce naturalmente por los fuertes calores, puede favorecerse limitando y suprimiendo el abonado."*

Es decir: la respuesta española al calor estival del rosal **no es proteger, es dejar de abonar y dejarlo parar**. Es una recomendación accionable y sorprendentemente bien alineada con lo que una app puede hacer (suprimir tareas en vez de añadirlas). **Sin umbral de temperatura**: "fuertes calores" no está numerado.

**Un dato sobre la orientación**, pertinente a la fachada noroeste (A, HD 13-54/1954):

> *"La orientación se prefiere, en general, soleada, aunque el sol demasiado directo de nuestro Levante, puede empalidecer los colores claros."*

Una fachada **noroeste** recibe sol sólo de tarde y en verano. La fuente prefiere "soleada", así que la ubicación es **subóptima según la fuente**, pero no dice qué consecuencia tiene la falta de sol. Sin respaldo para más.

### 2.4 Trasplante / plantación

**(A)** HD 13-54/1954:

> *"Se puede plantar en otoño, en noviembre-diciembre, o en primavera, de enero a marzo. **La plantación otoñal suele dar mejores resultados, con tal de que no se trate de un clima muy frío, que podría ocasionar daños en las plantas todavía no bien arraigadas.**"*

Aplica a **plantación**, no a trasplante de ejemplar arraigado. Para el ejemplar del inventario, **la tarea no procede**; la cita se conserva por si en el futuro el usuario planta otro rosal.

---

## 3. Granularidad: qué se puede convertir en condición climática y qué no

Éste es el resultado que más afecta al diseño de la app. Se separa en tres cubos.

### 3.1 Directamente evaluables con Open-Meteo (sin inferencia añadida)

| Regla | Variable Open-Meteo | Fuente | Escalón |
|---|---|---|---|
| Abonar el olivo **cuando se prevean lluvias** | `precipitation_sum` (pronóstico 7-16 d) | BOJA 153/2026 | A |
| Abonar el rosal **antes de lluvia** o regando a fondo | `precipitation_sum` | UC ANR | B |
| Abonado foliar del olivo con **temperatura suave y humedad alta** | `temperature_2m`, `relative_humidity_2m` | MAPA 2010 | A (umbral por definir) |
| Riesgo de daño invernal en olivo: **< −10 °C en reposo** | `temperature_2m_min` | MAPA 2010 | A |
| Parada vegetativa estival del olivo: **35-38 °C** | `temperature_2m_max` | MAPA 2010 | A |
| Estrés estival grave del olivo: **40 °C sin humedad en el suelo** | `temperature_2m_max` **+** estado del depósito del modelo hídrico | MAPA 2010 | A |
| No podar el rosal antes de que pase el **riesgo de heladas tardías** | `temperature_2m_min` (pronóstico + climatología de última helada) | HD 5-6/1980 | A |

### 3.2 Convertibles sólo mediante una inferencia declarada del proyecto

| Regla original | Inferencia propuesta | Riesgo |
|---|---|---|
| "Podar el olivo en **parada vegetativa invernal**" (IFAPA, FEN) | La misma familia de fuentes A dice que *"con temperaturas primaverales de 10-12 ºC se inicia el desarrollo vegetativo"* (MAPA 2010). → La ventana de poda se **cierra** cuando la temperatura media sostenida supera ~10-12 °C | **Combina dos fuentes distintas**. Ninguna afirma que esto sea el criterio de cierre de poda. Es razonamiento del proyecto, y la penalización por equivocarse es leve (§1.1) |
| "Rosal: reposo estival por los **fuertes calores**" (HD 1980, FEN) | Umbral de `temperature_2m_max` fijado por el proyecto | La fuente no numera nada. Es un umbral **inventado** |
| "Rosal: protección invernal sólo en **climas muy fríos**" (HD 1954) | Umbral por el proyecto | Ídem |

### 3.3 No convertibles — son calendario y punto

- Rosal, poda de remontantes: **"finales de diciembre a mitad de marzo"**, "la poda clásica, **en enero**", "otra ligera hacia **mitad de agosto**".
- Rosal, abonado tradicional: **"en el mes de enero, cada año"**.
- Rosal, abonado moderno RHS: **"March/April"** y **"mid-summer"**.
- Rosal, plantación: **"noviembre-diciembre"** o **"de enero a marzo"**.
- Olivo, abonado foliar: **"en primavera"** (N), **"en el otoño"** (K).
- Olivo, muestreo foliar de diagnóstico: **"durante el mes de julio"** (BOJA 2026) / *"en la parada vegetativa del mes de julio"* (MAPA 2010).

Para estas, **la app no puede ser más lista que un calendario**, y fingir lo contrario sería falsa precisión. Lo honesto es mostrarlas como ventana de calendario y decir en la ficha que la fuente da un mes, no una condición.

---

## 4. Licencias de las fuentes citadas

Importa porque la app pretende **citar textualmente**.

| Fuente | Licencia declarada | ¿Se puede citar literalmente en la app? |
|---|---|---|
| IFAPA, *Poda del Olivo: Poda de Producción* (2018) | **Creative Commons Reconocimiento-No comercial-Sin obra derivada**, con URL explícita en el PDF: `creativecommons.org/licenses/by-nc-nd/3.0/es` → **CC BY-NC-ND 3.0 ES** | **Sí**, con atribución, si el uso **no es comercial**. "Sin obra derivada" obliga a citar sin alterar |
| IFAPA, *Manual Formativo: Poda del Olivar* (2020) | *"Este documento está bajo Licencia Creative Commons Reconocimiento-No comercial-Sin obra derivada"* — **sin versión ni URL** en el PDF | Sí con las mismas condiciones; **la versión concreta no está declarada** (asumir BY-NC-ND) |
| MAPA/MARM, *Guía práctica de la fertilización racional* (2010) | **Ninguna licencia abierta declarada.** © Ministerio; NIPO 770-10-150-4; ISBN 978-84-491-0997-3; Depósito Legal M-27226-2010 | Cita breve con atribución, amparada en el derecho de cita. **No** reproducción extensa |
| MAPA, *Hoja Divulgadora 5-6/80* (poda del rosal, 1980) | **Autorización explícita en el propio folleto**: *"Se autoriza la reproducción íntegra de esta publicación mencionando su origen: «Hojas Divulgadoras del Ministerio de Agricultura»."* ISBN 84-341-0229-3 | **Sí** — es la licencia más permisiva del lote. Ojo al matiz: autoriza la reproducción **íntegra**; para citas parciales se aplica el derecho de cita |
| MAPA, *Hoja Divulgadora 13-54-H* (cultivo del rosal, 1954) | Sin nota de licencia localizada en el PDF | Cita breve con atribución |
| BOJA, Orden 30-jul-2026, Reglamento de PI de Olivar | Diario oficial. Reutilización de información del sector público | **Sí** |
| RHS | © Royal Horticultural Society, sin licencia de reutilización | **Cita mínima con enlace.** No reproducir bloques |
| UC ANR / UC Master Gardeners | © Regents of the University of California, sin licencia abierta declarada | **Cita mínima con enlace** |

---

## 5. Lo que NO se ha podido respaldar

Se listan aparte porque son exactamente las afirmaciones que la app **no debe hacer** o debe hacer con la confianza al mínimo.

1. **Que un olivo ornamental arraigado necesite abonarse.** Ninguna fuente A lo dice. Toda la literatura de abonado del olivar es de reposición de cosecha y presupone extracción. Sin cosecha, el método no tiene entrada. *No se ha encontrado ninguna publicación española sobre nutrición del olivo ornamental.*
2. **Un umbral de temperatura para el "cierre" de la ventana de poda del olivo.** IFAPA dice "parada vegetativa invernal" y "movimiento de savia"; ninguna fuente lo traduce a grados. La traducción de §3.2 es del proyecto.
3. **Medidas concretas de protección invernal o estival de un olivo de jardín.** Las fuentes dan umbrales de daño, no protocolos de protección.
4. **Trasplante de olivo adulto arraigado.** No hay fuente A ni B. Sólo material comercial (escalón C), descartado.
5. **Una fuente española *moderna* sobre el rosal ornamental.** La búsqueda previa que "no encontró nada" **no era del todo correcta, pero casi**:
   - **Desmentido parcial**: sí existe material institucional español, y es fuerte — dos *Hojas Divulgadoras* del Ministerio de Agricultura (1954 y 1980), la de 1980 firmada por un Ingeniero Agrónomo del Servicio de Extensión Agraria y con autorización expresa de reproducción.
   - **Confirmación**: **no existe material vigente**. Se ha verificado que el catálogo de **Reglamentos Específicos de Producción Integrada de Andalucía** (28 cultivos) **no incluye rosal ni ornamentales**; la única normativa de flor es la de **flor cortada: clavel y miniclavel bajo abrigo**, que no cubre el rosal de jardín. IFAPA/SERVIFAPA no publica nada de rosal. La guía de fertilización del MAPA no tiene capítulo de ornamentales.
   - **Consecuencia**: para el rosal, las fuentes A disponibles tienen **46 y 72 años**. Son técnicamente sólidas (la fenología del rosal no ha cambiado) pero mencionan productos hoy en desuso (escorias Thomas, sulfato amónico) y no incorporan variedades modernas.
6. **El caso "planta de jardín bajo alero, con lluvia efectiva casi nula".** No lo trata ninguna fuente de ningún escalón. Es el mismo hueco ya identificado en el modelo hídrico: es un problema de vivienda, no de campo.
7. **La penalización de podar el rosal *demasiado tarde*.** La fuente dice que hay que anticiparse en clima suave, pero **no describe el daño**. No se puede afirmar nada cuantitativo.
8. **Cualquier umbral numérico de "clima muy frío", "fuertes calores", "temperatura suave" o "humedad elevada".** Las fuentes usan estos adjetivos sin numerarlos. Todos los números que la app ponga ahí son del proyecto.
9. **Vigencia del calendario de prohibición de fertilizantes nitrogenados.** La prohibición de "diciembre y enero sobre suelo desnudo" procede del reglamento andaluz de 2008 citado en la guía MAPA de 2010; el reglamento de 2026 remite a los programas de actuación de zonas vulnerables a nitratos, que varían por CCAA y no se han verificado.

---

## 6. Recomendación sobre el estándar de prueba

El proyecto ya prevé un campo `confianza` en la ficha de cuidados. Esta investigación sugiere que **una sola dimensión no basta**: hay dos ejes independientes, y colapsarlos pierde información que sí se tiene.

### 6.1 Qué se propone aceptar como cita suficiente

Para que una tarea no hídrica llegue al plan del usuario, debe cumplir **las tres**:

1. **Origen**: fuente de escalón A o B, identificada con **URL estable + documento + sección o página**. No vale "según la literatura".
2. **Literalidad**: existe una **frase textual** de la fuente que sostiene la afirmación, almacenada junto a ella. Si hay que parafrasear porque la fuente no lo dice tan claro, es que la fuente no lo dice.
3. **Trazabilidad del salto**: si entre la frase y la regla implementada hay una inferencia (traducir fenología a grados, extrapolar de olivar productivo a olivo ornamental, extrapolar de California a España), esa inferencia se guarda como **campo aparte** y es visible.

### 6.2 Los dos ejes propuestos

**Eje 1 — `confianza_fuente`** (¿de quién viene?)

| Valor | Criterio |
|---|---|
| `alta` | Fuente A española **y vigente** (≤ 15 años o norma en vigor). Ej.: poda del olivo (IFAPA 2020), abonado del olivo (BOJA 2026) |
| `media` | Fuente A española **antigua** (> 25 años), o fuente B de clima mediterráneo trasladable. Ej.: todo el rosal (HD 1954/1980); UC ANR |
| `baja` | Fuente B de clima **no** trasladable con ajuste declarado (RHS), o fuente única sin corroboración |
| `sin_respaldo` | Todo lo del §5. **No se muestra como tarea**; como mucho, como nota informativa marcada |

**Eje 2 — `granularidad`** (¿cómo de listo puede ser el disparo?)

| Valor | Significado | Qué hace la app |
|---|---|---|
| `condicion_clima` | La fuente expresa una condición sobre el clima | Evaluar contra Open-Meteo. **Aquí la app aporta valor real** |
| `fenologia` | La fuente expresa una fase (parada vegetativa, tras la floración) | Aproximar por calendario **y pedir confirmación visual al usuario** |
| `mes` | La fuente da un mes | Mostrar como ventana de calendario, **sin fingir inteligencia** |
| `derivada` | La regla climática la ha construido el proyecto combinando fuentes | Evaluar, pero **etiquetar visiblemente** y bajar `confianza_fuente` un escalón |

### 6.3 Qué debe **bajar** la confianza

Reglas concretas, aplicables sin criterio subjetivo:

- **Fuente > 25 años** → baja un escalón. (Afecta a **todo** el rosal.)
- **Extrapolación de contexto productivo a ornamental** → baja un escalón. (Afecta a **todo** el abonado del olivo: la guía habla de olivar de almazara.)
- **Extrapolación climática entre países** sin validación → baja un escalón, y el ajuste debe estar escrito. (RHS → España.)
- **Umbral numérico puesto por el proyecto donde la fuente usa un adjetivo** → baja un escalón y se marca como supuesto.
- **La tarea presupone riego o fertirrigación y el ejemplar no se riega** → **no baja: se suprime la tarea** y se explica. Éste es el caso más frecuente y más grave: gran parte de la literatura de abonado está escrita para fertirrigación.
- **Regla derivada de combinar dos fuentes** (§3.2) → nunca puede ser `alta`.

### 6.4 Aplicación a los dos ejemplares

| Planta | Tarea | `confianza_fuente` | `granularidad` | ¿Se muestra? |
|---|---|---|---|---|
| Olivo | Poda | `alta` | `fenologia` (+`derivada` para el cierre) | **Sí**, con tono suave (penalización leve) |
| Olivo | Abonado al suelo | `sin_respaldo` para "necesita abonarse"; `media` para "si abonas, hazlo antes de lluvia" | `condicion_clima` | **Como nota, no como tarea recurrente** |
| Olivo | Abonado foliar | `media` (A vigente, pero contexto productivo) | `condicion_clima` | Opcional, avanzado |
| Olivo | Protección invernal | `alta` | `condicion_clima` | **Sólo si el pronóstico lo dispara** |
| Olivo | Protección estival | `alta` | `condicion_clima` (acoplada al balance hídrico) | **Sí** |
| Olivo | Trasplante | `sin_respaldo` | — | **No** |
| Rosal | Poda | `media` (A de 1980, corroborada por B mediterránea) | `mes` + `condicion_clima` para heladas tardías | **Sí** |
| Rosal | Abonado | `media`/`baja` | `mes`, con disparo `condicion_clima` de UC ANR | **Sí, pero como tarea compuesta "abonar + regar a fondo"** por el alero |
| Rosal | Protección invernal | `media` | `condicion_clima` con umbral del proyecto | **Normalmente no procede** |
| Rosal | Reposo estival (suprimir abonado) | `media` | `fenologia`, sin umbral | Sí, como supresión de tarea |
| Rosal | Trasplante | `media` pero **no aplica** (arraigado) | `mes` | **No** |

---

## 7. Límites y supuestos declarados

1. **Este documento no cubre fitosanitarios.** Repilo, verticilosis, mosca del olivo, oídio y mancha negra del rosal quedan fuera por alcance, aunque las fuentes A los tratan extensamente y varias prácticas de poda están motivadas por ellos.
2. **Todo lo del olivo procede de literatura de olivar productivo** (almazara/mesa, secano o riego, densidad 80-1.500 árboles/ha). Su traslado a **un árbol aislado ornamental** es una extrapolación del proyecto. Es razonable en poda (la fisiología es la misma) y **cuestionable en abonado** (el método está anclado a la cosecha).
3. **Todo lo del rosal procede de dos folletos de 1954 y 1980.** La fenología no ha cambiado; los productos fertilizantes citados sí (escorias Thomas, sulfato amónico). **No usar las fórmulas de 1954 como recomendación de compra.**
4. **Las citas del RHS y de UC ANR se han obtenido de la página web**, no de un PDF versionado. Ese contenido puede cambiar sin aviso. Conviene guardar fecha de consulta (2026-08-13) junto a la cita.
5. **Ningún umbral de "temperatura suave", "humedad elevada", "fuertes calores" o "clima muy frío" está numerado por las fuentes.** Cualquier número que aparezca en el código lo pone el proyecto.
6. **La regla que cierra la ventana de poda del olivo por temperatura (§3.2) combina dos fuentes que no se refieren la una a la otra.** Es la inferencia más frágil del documento. Su mitigación es que la penalización por equivocarse es leve, según la propia IFAPA.
7. **No se ha verificado el calendario vigente de prohibición de aportes nitrogenados** en zonas vulnerables a nitratos, que varía por comunidad autónoma. Para un jardín particular es probablemente irrelevante, pero la app no debería citar la prohibición de "diciembre y enero" como si estuviera vigente.
8. **La única variedad concreta que importaría no se conoce.** El tipo de rosal (híbrido de té, floribunda, trepador remontante o no remontante) **cambia la ventana de poda de enero a julio**. La app **debe** preguntar por el tipo o degradar el consejo a genérico; sin ese dato, la ventana de poda del rosal no está determinada.
9. **La orientación noroeste bajo alero no está cubierta por ninguna fuente.** Sus dos efectos (poca lluvia efectiva, poco sol directo) se tratan aquí por derivación desde el modelo hídrico y desde una preferencia genérica de la fuente de 1954 por la exposición soleada.

---

## Fuentes

**Escalón A — España, vigentes**

- IFAPA (Junta de Andalucía), *Manual Formativo: Poda del Olivar*, Sevilla, 2020, 38 pp. Coord. Daniel Pérez Mohedano. Serie Frutales / Formación. Licencia CC Reconocimiento-No comercial-Sin obra derivada.
  [Ficha SERVIFAPA](https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/d5778c9f-41bd-4ec1-a1c7-2331a3215933) · [PDF](https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/d5778c9f-41bd-4ec1-a1c7-2331a3215933/download) — §1.4 Bases agronómicas (época y frecuencia de poda)
- IFAPA, *Poda del Olivo: Poda de Producción*, Córdoba, febrero de 2018, 13 pp. Pérez, D.; Vega, V.; Hidalgo, J.; Hidalgo, J. C.; Leyva, A. **CC BY-NC-ND 3.0 ES** (URL declarada en el PDF).
  [Ficha](https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/0e496cd1-2448-4898-a1c7-998a71d87a6e) · [PDF](https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/0e496cd1-2448-4898-a1c7-998a71d87a6e/download)
- IFAPA, *Poda del Olivo: Poda de Formación* — [ficha](https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/4e793d32-2aa3-47af-ae3d-9b41663f3c2b) · IFAPA, *Poda del Olivo: Poda de Renovación* — [ficha](https://www.juntadeandalucia.es/agriculturaypesca/ifapa/servifapa/registro-servifapa/07640e63-0216-48fb-a42c-dcf576290be9) (consultadas; no aportan ventanas temporales)
- **Orden de 30 de julio de 2026**, por la que se aprueba el **Reglamento Específico de Producción Integrada de Olivar**. BOJA núm. 153, 10 de agosto de 2026.
  [BOJA](https://www.juntadeandalucia.es/boja/2026/153/4.html) · [PDF](https://www.juntadeandalucia.es/eboja/2026/153/BOJA26-153-00038-10902-01_00342132.pdf) — cuadros PODA y ENMIENDAS Y FERTILIZACIÓN; Plan de Abonado
- Junta de Andalucía, [catálogo de Reglamentos Específicos de Producción Integrada por cultivos](https://www.juntadeandalucia.es/organismos/agriculturapescaaguaydesarrollorural/areas/agricultura/sanidad-vegetal/paginas/produccion-integrada-regl.html) — **verificado: 28 cultivos, ninguno ornamental ni rosal**

**Escalón A — España, históricas**

- MARM/MAPA, *Guía práctica de la fertilización racional de los cultivos en España*, 2010. NIPO 770-10-150-4, ISBN 978-84-491-0997-3.
  [Parte I](https://www.mapa.gob.es/dam/mapa/contenido/agricultura/publicaciones/01_fertilizacion-baja-.pdf) · [Parte II](https://www.mapa.gob.es/dam/mapa/contenido/agricultura/publicaciones/02_fertilizacion-baja-.pdf) — **cap. 27, "Abonado del olivar"**, Casimiro García García (ETSIA-UPM), pp. 223-233
- Antonio Verdeguer Monge (Servicio de Extensión Agraria), *Poda del rosal y de otros arbustos ornamentales*, Hoja Divulgadora **núm. 5-6/80 HD**, Ministerio de Agricultura, 1980. ISBN 84-341-0229-3. *Reproducción íntegra autorizada citando el origen.*
  [PDF](https://www.mapa.gob.es/ministerio/pags/biblioteca/hojas/hd_1980_05-06.pdf)
- Juan Gostinchar, *Cultivo del rosal*, Hoja Divulgadora **núm. 13-54-H**, Ministerio de Agricultura, Madrid, 1954.
  [PDF](https://www.mapa.gob.es/ministerio/pags/biblioteca/hojas/hd_1954_13.pdf)

**Escalón B — extensión extranjera (con ajuste climático declarado)**

- UC ANR / UC Master Gardeners of San Luis Obispo County (California, clima mediterráneo), *Pruning Rose Bushes*, Lisa Mowery — [ucanr.edu](https://ucanr.edu/site/uc-master-gardeners-san-luis-obispo-county/pruning-rose-bushes). Consultado 2026-08-13
- RHS (Reino Unido, **clima no trasladable sin ajuste**), *Roses: growing guide* — [rhs.org.uk](https://www.rhs.org.uk/plants/roses/growing-guide) · *Rose Pruning: Floribunda & Hybrid Tea Roses* — [rhs.org.uk](https://www.rhs.org.uk/plants/roses/modern-bush/pruning-guide). Consultados 2026-08-13

**Escalón C** — ninguna fuente de este escalón se ha usado en el documento.

**Documento hermano de este proyecto**

- [`modelo-agronomico-riego.md`](./modelo-agronomico-riego.md) — de él se toman el balance hídrico del suelo (para el umbral estival del olivo) y el concepto de `exposicion_lluvia` (para el rosal bajo alero)
