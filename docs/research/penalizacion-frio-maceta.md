# Cuánto menos frío resiste una maceta que el mismo ejemplar en el suelo

> Investigación para el issue [#31](https://github.com/Joosle/Plantas/issues/31), hijo del mapa [#1](https://github.com/Joosle/Plantas/issues/1).
> Fecha: 2026-08-19. Fuentes primarias citadas con autor, año y URL.
> Alcance: la penalización por emplazamiento en maceta sobre el umbral de helada de [#19](https://github.com/Joosle/Plantas/issues/19), colgando del `placement` de [#16](https://github.com/Joosle/Plantas/issues/16).
> Aplica hoy a **una** planta del inventario: el aloe de la terraza, maceta de plástico de ~7,5 L de sustrato útil.

---

## Veredicto

**No entra como número en el modelo. Se queda como marca cualitativa sobre `placement`, exactamente donde [#19](https://github.com/Joosle/Plantas/issues/19) la dejó provisionalmente.**

Y no por falta de literatura —hay bastante, y buena— sino por tres razones que la literatura misma impone, en orden de peso:

1. **La regla de «una o dos zonas USDA menos» no tiene estudio detrás.** La repiten servicios de extensión universitaria (Illinois, Penn State), lo cual la sube por encima de la web comercial, pero **ninguno la cita ni la deriva**: dan el mecanismo (la raíz es menos rústica, el cepellón es poca masa) y luego sueltan el número sin procedencia. Es el mismo desenlace que [#3](https://github.com/Joosle/Plantas/issues/3) con los mapas de rusticidad de España.
2. **La literatura primaria no penaliza un umbral: lo sustituye por otro.** No mide «cuánto menos aguanta la maceta»; mide la **temperatura letal de raíz** por especie, como valor absoluto, sobre planta en contenedor. Es un parámetro distinto y mejor, no un descuento sobre el aéreo.
3. **Y sobre el aloe, el único ejemplar al que aplicaría, la penalización no mueve nada**, porque su parte aérea muere muy por encima de cualquier temperatura letal de raíz publicada. El eslabón que se rompe primero es la hoja, no el cepellón. Ver §5.

---

## Resumen de lo que dicen las fuentes

| Pregunta del ticket | Respuesta corta | Fuente que la posee |
|---|---|---|
| ¿Hay fuente primaria que cuantifique la penalización? | **No como penalización.** Sí hay temperatura letal de raíz absoluta, medida | [Havis 1976, HortScience 11(4):385-386](https://njaes.rutgers.edu/fs528/); [Steponkus, Good y Wiest 1976](https://auf.isa-arbor.com/content/2/3/51) |
| ¿En zonas, en °C, o en temperatura de cepellón? | **En temperatura de cepellón**, y la exposición se mide en **horas bajo umbral**, no en ΔT | [Good, Steponkus y Wiest 1976, *Arboriculture & Urban Forestry* 2(3):51-52](https://auf.isa-arbor.com/content/2/3/51) |
| ¿De dónde sale «una o dos zonas»? | De extensión, **sin cita ni derivación** | [Illinois Extension](https://extension.illinois.edu/blogs/good-growing/2020-11-18-overwintering-potted-plants) |
| ¿Depende del volumen? | **Sí, y el mecanismo se conoce** (calor latente de fusión del agua del sustrato). **No hay curva ni umbral publicados** | [Good et al. 1976](https://auf.isa-arbor.com/content/2/3/51) |
| ¿Depende del material y del color? | **Medido para el calor, no para el frío** | [Agronomy 10(4):484 (2020)](https://www.mdpi.com/2073-4395/10/4/484); [Horticulturae 6(2):22 (2020)](https://www.mdpi.com/2311-7524/6/2/22) |
| ¿Apoyada en suelo o en baldosa? | **Sin medir.** La práctica de *enterrar* la maceta recupera el tampón del suelo, pero se enuncia sin números | [Penn State Extension](https://extension.psu.edu/overwintering-plants-in-containers) |
| ¿La fuente distingue raíz de parte aérea? | **Sí, y es su hallazgo central**: hasta 28 °C de diferencia dentro de un mismo individuo | [OSU *Ornamentals Northwest* 9(2)](https://agsci.oregonstate.edu/sites/agscid7/files/horticulture/osu-nursery-greenhouse-and-christmas-trees/onn090210.pdf) |

---

## 1. La pregunta estaba mal planteada, y la literatura lo dice

El ticket pedía un descuento: cuántos grados o cuántas zonas restarle a la rusticidad de la ficha cuando la planta está en maceta. La literatura de viverismo **no trabaja así**, y lleva cincuenta años sin hacerlo.

Lo que publica es una tabla de **temperatura que mata la raíz**, por especie, medida sobre planta de vivero en contenedor. El apéndice de la Oregon State University recoge unas 150 entradas de ese tipo, atribuidas a Havis (1976), Steponkus, Good y Wiest (1976), Briggs (1980) y Gouin (1977). Rangos observados: de −5 °C (cornejos, jaras, romero) a −23,3 °C (rododendro de Catawba, híbridos 'P.J.M.').

La diferencia no es cosmética. Un descuento sobre el umbral aéreo **arrastra el error del umbral aéreo**; una temperatura letal de raíz es una medida independiente, sobre el tejido que de verdad se congela en un tiesto. Quien tiene el número bueno lo publica como absoluto.

## 2. La raíz no es la parte aérea, y la diferencia es enorme

Es el hallazgo que reordena todo lo demás. Del documento de la OSU, citando a Steponkus, Good y Wiest (1976):

> *Within a single plant, the cold tolerance of the roots and shoots can differ as much as 50 °F: in* Pyracantha coccinea *Roem. 'Lalandei', stems can survive −15 °F when fully acclimated. Older, woody roots are killed at 2 °F. Young roots are killed at 22 °F.*

En °C: tallo **−26,1**, raíz leñosa madura **−16,7**, raíz joven **−5,6**. **20,5 °C de hueco** entre el tallo y la raíz joven del mismo arbusto. La ficha de rusticidad que consultaría un aficionado da el −26 °C.

Y el motivo es fisiológico, no accidental: **la raíz no entra en dormancia**. Su endurecimiento es función pura de la temperatura, sin señal fotoperiódica, y se pierde entero **en 24 horas** ante un episodio cálido, mientras recuperarlo cuesta días. Consecuencia operativa que la OSU subraya: *cold damage might occur at any time during the year*. La raíz de una maceta puede estar en su punto más vulnerable en pleno enero, después de una semana templada.

Esto explica *por qué* existe la intuición de la penalización —es real y es grande— y a la vez **por qué no se puede expresar como un descuento fijo**: no es un desplazamiento del umbral aéreo, es otro umbral, de otro tejido, con otra dinámica temporal.

## 3. El número que sí existe, y no es un ΔT: son horas

La única comparación directa maceta-contra-suelo encontrada, y es exactamente lo que pedía el ticket. Good, Steponkus y Wiest, *Arboriculture & Urban Forestry* 2(3):51-52 (1976), invierno 1973/74 en Ithaca (Nueva York):

- Suelo de campo a **10 cm de profundidad**: nunca bajó de **−6,1 °C**.
- Contenedor de **2 galones (7,6 L)**: estuvo por debajo de esa temperatura **más de 500 horas**, y entre **−15 y −12,2 °C** unas **70 horas**.

Léase con cuidado, porque dice dos cosas y sólo una es la esperada:

**La esperada.** El suelo no bajó nunca de donde la maceta pasó tres semanas acumuladas. El tampón del suelo es tan grande que en ese invierno **el suelo no llegó a alcanzar el mínimo de la maceta ni una sola vez**.

**La inesperada, y la que decide este ticket.** La penalización **no se expresa como una diferencia de temperatura**. Se expresa como **dosis**: horas por debajo de un umbral. Un modelo que reste 3 °C al umbral de la planta está midiendo la magnitud equivocada — el daño de raíz en la literatura va con cuánto tiempo se pasa abajo, no con cuánto se baja en el punto más frío de una noche.

Corroborado por el otro lado: McNeil y Duncan (1983) en Kentucky, recogidos por la OSU. Noche del 16 de enero con **aire a −31,1 °C**. Contenedores **sin protección**: el cepellón bajó a **−16,1 °C**. Es decir, **15 °C por encima del aire** en el pico de un episodio extremo. La maceta *sí* tampona un pico puntual —el calor latente de fusión del agua del sustrato, 79,7 cal/g, lo paga— y lo que no tampona es la **duración**. Un modelo de ΔT sobre el mínimo pronosticado habría acertado justo al revés.

## 4. Volumen, material, color y apoyo: lo que se sabe y lo que no

**Volumen: el mecanismo sí, la curva no.** El tampón lo paga el agua del sustrato al congelarse (calor de fusión), así que escala con la **masa de agua retenida**, no con el volumen del tiesto ni con su masa. Ninguna fuente publica curva ni umbral de volumen. Nota afortunada para este proyecto: los **7,5 L** del aloe son casi exactamente los **7,6 L** del contenedor de Ithaca — si algún día hiciera falta el número, el ejemplar de casa está en el tamaño que se midió.

Corolario práctico que sí está citado, y que importa más que el volumen: **el sustrato seco no tampona**. Kentucky y Oregón coinciden en que mantener humedad en el cepellón durante el invierno es el factor individual más importante para reducir el daño de raíz. Un tiesto seco pierde el mecanismo entero.

**Material y color: medidos para el calor, no para el frío.** La literatura moderna con instrumentación mide el **extremo cálido**: los contenedores negros mantienen el cepellón por encima de 38 °C y de 46 °C un 15–17 % más de tiempo que los blancos o los de autorrepicado (Agronomy 10(4):484). Es dato bueno y es de otra estación. Del lado frío sólo aparecen efectos de segundo orden —forro de poliestireno y maceta de tejido, del orden de 2–3 °C sobre las mínimas del cepellón—, citados en resúmenes de la línea de trabajo de la ISHS sobre tipo de contenedor y tratamiento invernal que **no se han podido leer en el original** (acceso cerrado); se anotan como indicio, no como hecho citable. Nada sobre color en frío.

Ese reparto es informativo por sí solo: **para el aloe en Guadalajara, el color del tiesto está mejor documentado como problema de julio que como problema de enero**, lo cual encaja con que [#19](https://github.com/Joosle/Plantas/issues/19) ya dejara la vulnerabilidad al calor colgando de `placement`.

**Apoyo en suelo o en baldosa: sin medir.** No se ha encontrado ninguna medición. Lo que sí está en toda la literatura de extensión es la práctica de **enterrar** la maceta hasta el borde para recuperar el tampón del suelo — que es el mecanismo de §3 enunciado al revés, y sin cifras.

## 5. Y sobre el aloe, nada de esto cambia el disparador

Todo lo anterior es literatura de **vivero de leñosa templada**: arces, cornejos, acebos, rododendros, coníferas. El apéndice de la OSU tiene *Abies*, *Picea*, *Juniperus*, *Buxus* — y **no tiene ni *Rosa*, ni *Olea*, ni *Aloe***. Sólo una de las cuatro plantas de casa (el abeto, cuando se identifique) cae dentro del universo muestral, y está en el suelo.

Y la que está en maceta rompe el supuesto que hace útil toda la construcción. La penalización por maceta importa cuando **la parte aérea es mucho más rústica que la raíz**: el piracanta aguanta −26 °C arriba y muere a −5,6 °C abajo, así que en el suelo vive y en el tiesto no, y ahí el emplazamiento decide. **En el aloe la desigualdad va al revés.** *Aloe vera* es una crasa CAM sin aclimatación al frío: su tejido aéreo se daña **en el entorno de 0 °C** — el intervalo citable con procedencia lo posee el `CareFact` de PFAF que registró [#18](https://github.com/Joosle/Plantas/issues/18), y esta investigación **no encontró ninguna fuente primaria mejor**: la búsqueda de temperatura letal de *Aloe vera* devuelve exclusivamente web comercial, de la que #3 ya prohibió beber.

Con la hoja muriendo cerca de 0 °C y las temperaturas letales de raíz publicadas entre −5 y −23 °C, **la raíz nunca es el eslabón que se rompe primero**. La alerta se dispara por el umbral aéreo, y le da igual si la maceta está 3 °C más fría: para cuando el cepellón se acerca a su límite, la planta lleva días muerta por arriba.

**El emplazamiento sigue importando en este ticket, pero por la otra puerta**: no cambia *cuándo* se avisa, cambia *qué se puede hacer* — la maceta es la única de las cuatro plantas que se puede meter dentro, que es justo la asimetría que [#19](https://github.com/Joosle/Plantas/issues/19) convirtió en tarea de temporada.

## 6. Qué se lleva el modelo

- **No hay parámetro nuevo.** No se resta nada al umbral de helada por estar en maceta. `placement` no gana un número.
- **La marca cualitativa sobre `placement` se queda, y ahora está fundamentada**: la maceta no cambia el umbral, cambia el repertorio de acciones y —eso sí, con cita— la vulnerabilidad al calor.
- **La ausencia se declara, no se rellena.** Igual que en [#3](https://github.com/Joosle/Plantas/issues/3): la regla de «una o dos zonas» **no entra** en la ficha ni en el código. Si algún día la interfaz quiere decir algo sobre el frío de la maceta, dice el mecanismo («el cepellón se congela entero; el suelo no»), no un número inventado.
- **Un aviso guardado para cuando el inventario cambie.** El día que entre en casa una **leñosa templada en maceta** —un arce japonés, un boj, una conífera enana en tiesto— esta investigación **sí tiene número que aplicar**, y no es un descuento: es la temperatura letal de raíz absoluta de la tabla de Havis/Steponkus, como `CareFact` propio con su cita, resuelto por grupo funcional al estilo de [#18](https://github.com/Joosle/Plantas/issues/18). Hoy no hay ninguna, y por eso no se modela.
- **Si alguna vez se modela, se modela como dosis**, no como ΔT: horas de cepellón bajo umbral, no grados bajo el mínimo pronosticado. Que es, de paso, el único sitio donde el `past_days` de Open-Meteo que fijó [#4](https://github.com/Joosle/Plantas/issues/4) tendría un segundo cliente.

---

## Fuentes

- Good, G.L., Steponkus, P.L. y Wiest, S.C. (1976). *Winter Protection of Containerized Ornamental Plants*. **Arboriculture & Urban Forestry** 2(3):51-52. <https://auf.isa-arbor.com/content/2/3/51> — la comparación Ithaca 1973/74 suelo-contra-contenedor, y temperaturas letales de raíz madura y joven.
- Havis, J.R. (1976). *Root hardiness of woody ornamentals*. **HortScience** 11(4):385-386. — la tabla fundacional de temperaturas letales de raíz. Consultada a través de sus reproducciones en extensión (Rutgers, Kentucky, Oregon State); el original no está en acceso abierto.
- Steponkus, P.L., Good, G.L. y Wiest, S.C. (1976). *Root hardiness of woody plants*. **American Nurseryman** 144(6):16, 76-79. — el contraste tallo/raíz del piracanta.
- Oregon State University, *Ornamentals Northwest Newsletter* 9(2), **Overwintering Container-Grown Plants**. <https://agsci.oregonstate.edu/sites/agscid7/files/horticulture/osu-nursery-greenhouse-and-christmas-trees/onn090210.pdf> — síntesis con Apéndice 1 (≈150 temperaturas letales de raíz), fisiología de la aclimatación radicular, y los datos de McNeil y Duncan (1983) en Kentucky.
- Rutgers NJAES, **FS528: Management of Container Nursery Plant Material During Cold Weather**. <https://njaes.rutgers.edu/fs528/> — tablas de temperatura letal de raíz para leñosas y vivaces.
- University of Kentucky Nursery Crop Extension, **Overwintering Nursery Crops**. <https://nursery-crop-extension.mgcafe.uky.edu/content/overwintering-nursery-crops> — *«roots rarely, if ever, will survive temperatures below −10 °F, therefore root hardiness should be our primary concern»*.
- Illinois Extension, **Overwintering potted plants** (18-11-2020). <https://extension.illinois.edu/blogs/good-growing/2020-11-18-overwintering-potted-plants> — enuncia la regla de las dos zonas **sin cita ni derivación**; es la prueba de la ausencia.
- Penn State Extension, **Overwintering Plants in Containers**. <https://extension.psu.edu/overwintering-plants-in-containers> — la práctica de enterrar la maceta.
- Agronomy 10(4):484 (2020), *Container Color and Compost Substrate Affect Root Zone Temperature and Growth of 'Green Giant' Arborvitae*. <https://www.mdpi.com/2073-4395/10/4/484> — el efecto del color, medido en el extremo cálido.
- Horticulturae 6(2):22 (2020), *Container Type and Substrate Affect Root Zone Temperature and Growth of 'Green Giant' Arborvitae*. <https://www.mdpi.com/2311-7524/6/2/22>
- Agriculture 12(12):2165 (2022), *Effects of Nursery Container Color and Spacing on Root Zone Temperatures of 'Soft Touch' Holly*. <https://www.mdpi.com/2077-0472/12/12/2165>
