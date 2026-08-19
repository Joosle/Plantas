# Inventario de plantas reales

Las plantas que hay hoy en la casa. Es el banco de pruebas contra el que se juzga
cualquier fuente de cuidados y cualquier camino de identificación: la pregunta no es
"¿cuántas especies cubre?" sino "¿cubre **éstas**?".

Resuelve el ticket [Inventariar las plantas reales de casa](https://github.com/Joosle/Plantas/issues/2).
**Lista abierta** — se añaden plantas conforme aparezcan; los números de abajo se recalculan.

Vocabulario (`Plant`, `Site`, `Taxon`, `Unidentified`) según `CONTEXT.md`.

Última actualización: 2026-08-10.

## Sites

| Site | Interior/exterior | Sustrato | Orientación | Lluvia |
| --- | --- | --- | --- | --- |
| Jardín frontal este | exterior | suelo | este | libre |
| Jardín frontal noroeste | exterior | suelo | noroeste | libre |
| Fachada noroeste (bajo alero) | exterior | suelo | noroeste, pegado a muro | alero parcial, pero le llega lluvia |
| Terraza posterior | exterior | maceta | suroeste | libre |

`Fachada noroeste` es un `Site` propio y no una nota sobre el jardín noroeste: el rosal
está pegado al muro con alero encima y el abeto en campo abierto a pocos metros, así que
la lluvia que reciben y su microclima (`kmc`) difieren. Es el criterio de `CONTEXT.md`
aplicado tal cual — un `Site` es un conjunto de condiciones uniformes, no un recinto.

## Plants

| Nombre casero | Taxon | Rango | GBIF key | Site | Plantada |
| --- | --- | --- | --- | --- | --- |
| El olivo del jardín este | *Olea europaea* L. | especie | [5415040](https://www.gbif.org/species/5415040) | Jardín frontal este | ya estaba (vino con la casa) |
| El abeto | — **Unidentified** | — | — | Jardín frontal noroeste | ya estaba (vino con la casa) |
| El rosal de la fachada | *Rosa* L. | género | [8395064](https://www.gbif.org/species/8395064) | Fachada noroeste (bajo alero) | desconocida |
| El aloe de la terraza | *Aloe vera* (L.) Burm.f. | especie | [2777724](https://www.gbif.org/species/2777724) | Terraza posterior | desconocida |

### Los dos números

- **4 Plant, 4 taxones distintos.** Ninguna repetida — no hay dos ejemplares de lo mismo.
- **Identificación**: 2 a especie (olivo, aloe), 1 a género (rosal), **1 `Unidentified`** (abeto).

El rosal a género **no es un fallo de identificación**: los rosales de jardín son
cultivares híbridos sin especie limpia, así que género *es* el rango terminal correcto.
El único hueco real es el abeto.

Y bajar de rango tampoco daría lo que hace falta: el dato que decide la poda del rosal
—remontante o no— **no lo sirve ningún rango**, porque varía entre cultivares del mismo
género y no hay fuente que lo publique por taxón. Sólo se obtiene mirando el arbusto (ver
más abajo). Es el primer caso del inventario donde identificar mejor no ayudaría en nada.

### Detalle por planta

**El olivo del jardín este.** Árbol arraigado, en suelo, sin riego. Nombre común
inequívoco en España; match GBIF `EXACT`, confianza 97.

**El abeto.** `Unidentified`. "Abeto" en español de jardín cubre *Abies*, *Picea abies*
(lo que suele venderse como abeto de Navidad) y con frecuencia se aplica mal a
*Cupressus* o *Thuja*, que sólo se le parecen. **Necesita foto** — es el caso de prueba
número uno para
[Probar el camino de identificación contra las plantas reales de casa](https://github.com/Joosle/Plantas/issues/15).

**El rosal de la fachada.** *Rosa* sp., cultivar desconocido. En suelo, pegado al muro
noroeste con alero parcial. Es la única planta con microclima de pared: sombra de lluvia
parcial y calor reflejado por la fachada — los dos factores que el `kmc` de WUCOLS existe
para capturar.

#### Remontante o no: sin determinar, y su poda es julio hasta que se sepa

El tipo de rosal decide su poda, y **este ejemplar no tiene el dato**. La fuente oficial
que localizó
[Calendario de tareas no-hídricas: poda, abonado y protección con cita](https://github.com/Joosle/Plantas/issues/17)
—Hoja Divulgadora 5-6/80 del Ministerio de Agricultura— trata los dos tipos por separado:
los **no remontantes** se podan *«después de que hayan florecido, lo que ocurre, en
general, en el mes de julio»*; los **remontantes**, dentro de la ventana general de
*«finales de diciembre a mitad de marzo»*.

**Corrección: «enero» no aplica a esta casa.** La fuente no da un mes para los remontantes,
da una ventana con tres modificadores condicionados, y el mes sale sólo del tercero: *«en
las regiones de clima muy benigno, como la zona mediterránea, se aconseja hacer dos podas.
Una, la poda clásica, en enero»*. El Casar está a **824 m**
([Alta de API key de AEMET OpenData](https://github.com/Joosle/Plantas/issues/12)) con una
cola fría de **−11,1 °C**
([Alertas de helada y calor](https://github.com/Joosle/Plantas/issues/19)): no es clima muy
benigno, y le toca el **segundo** modificador, que empuja al lado contrario — *«la poda
tardía es aconsejable en aquellos climas donde sean de temer las heladas tardías que pueden
destruir los brotes tiernos recién salidos»*. La ventana aplicable si resulta remontante es
el **extremo tardío**: de finales de febrero a mediados de marzo. Arrastrar «enero» hasta
aquí era la *extrapolación climática* que el propio #17 mandaba penalizar, cometida sobre
su propia cita.

**Mientras el tipo esté indeterminado, la poda que se emite es la de julio.** No es un
empate al que se le tira una moneda: los dos errores cuestan cosas muy distintas.

| Se poda… | y resulta ser… | coste |
| --- | --- | --- |
| Marzo | no remontante | **la floración entera del año** — la fuente manda podarlo *después* de florecer, así que podar antes se lleva por delante la única floración que tiene |
| Julio | remontante | **parte de una sola oleada** — vuelve a florecer sobre brote nuevo, y la fuente llega a recomendar una poda ligera estival para este tipo (en climas benignos, no aquí) |

Julio es el lado barato, así que es el defecto declarado. *(El mecanismo de la madera vieja
es **salto inferencial**, no cita: la fuente prescribe el cuándo, no el porqué. La
asimetría se sostiene igual sin él, porque «podar antes de que florezca» contradice la
instrucción literal.)*

**Cómo se determina, y cuándo.** El criterio es si vuelve a florecer, y no todas las fechas
valen lo mismo:

- **Otoño (septiembre–noviembre)** — la señal fuerte y la más próxima. La fuente asocia a
  los remontantes *«una floración más abundante, y de calidad, en otoño»*.
- **Segunda oleada tras la de primavera (finales de mayo–junio)** — concluyente.
- **Agosto** — **débil, y no sirve como negativo**: la propia fuente describe un *«reposo
  estival que se produce naturalmente por los fuertes calores»*, así que un remontante
  puede estar sin flor ahora mismo.

El cierre es asimétrico a propósito: **un solo sí lo cierra para siempre** —una planta que
refloreció es remontante, y eso no caduca—, mientras que *no remontante* sólo se concluye
tras una **temporada completa** de noes en esas ventanas. Es la misma forma que
[Cómo entra el acolchado en el balance hídrico](https://github.com/Joosle/Plantas/issues/20)
y [Plantas arraigadas que no se riegan](https://github.com/Joosle/Plantas/issues/16) ya
adoptaron: termina preguntando, no venciendo.

**Dónde vive el dato: en ninguno de los dos sitios que se propusieron.** No es campo de la
`Plant` ni fila de la tabla de hechos citables de
[La tabla de cuidados curada](https://github.com/Joosle/Plantas/issues/18), porque **no
tiene ni tendrá cita**: ninguna fuente sirve «remontante» para el género *Rosa* —varía
dentro de él— y su procedencia es haber mirado *este* arbusto. Se descompone en dos piezas
que ya existen:

- Las **observaciones** son `Observation` con `reading` (`floración: sí | no`, fechada),
  que es lo que `CONTEXT.md` define como *un juicio sobre un estado*, y el motor ya lee.
- El **rasgo es derivado** de ellas, con tres estados (`remontante` / `no remontante` /
  `indeterminado`), igual que el arraigo de #16 es derivado y no un campo.

El grupo funcional de #17/#18 sigue en juego, pero como **consumidor y no como almacén**:
el consejo de poda cuelga de `rosal-remontante` / `rosal-no-remontante` con su cita, y el
rasgo derivado de la `Plant` es lo que elige a cuál pertenece. Las dos mitades de la
disyuntiva del ticket eran ciertas sobre objetos distintos.

**El aloe de la terraza.** Maceta de plástico, **25 cm de diámetro de boca × 24 cm de
alto**. Derivados para el modo maceta del modelo hídrico:

- Volumen del tiesto ≈ **8,6 L** (tronco de cono, base estimada a 0,7 × boca);
  sustrato útil descontando ~2,5 cm de borde libre ≈ **7,5 L**.
- Área de boca, que es por donde entra la lluvia en el modo maceta ≈ **0,049 m²**.
- La base estimada es una suposición: si el dato importa al calibrar, se mide.

## Lo que esta lista dice del proyecto

**Hoy no se riega nada.** Los dos jardines viven sólo de la lluvia y ambos árboles vinieron
con la casa, así que están arraigados. De las 4 plantas, 3 están en suelo, arraigadas y sin
aporte de agua; la única con decisión de riego real es el aloe, que además es la más
resistente a la sequía de la casa (`ks` muy bajo en WUCOLS).

Consecuencia para el mapa: **el motor de riego semanal, sobre el inventario de hoy, tiene
una planta sobre la que actuar.** No invalida lo decidido en
[Fuente meteorológica](https://github.com/Joosle/Plantas/issues/4) ni en
[Modelo agronómico](https://github.com/Joosle/Plantas/issues/6) —el clima sigue haciendo
falta para las alertas de helada y de ola de calor, que sí aplican a las cuatro—, pero
desplaza el peso del valor del día uno hacia **alertas y tareas de calendario** (poda del
rosal y del olivo, abonado, protección) y lejos del plan de riego.

También invierte una suposición del modelo agronómico: se esperaba que el modo maceta
(litros) fuera el caso común y el modo suelo (mm) el minoritario. Es al revés, 3 a 1.

**Cobertura previsible de la fuente de cuidados.** Olivo, rosal y aloe son plantas de
paisaje clásicas y deberían estar en WUCOLS; el abeto lo estará también en cuanto se sepa
cuál es. Es una expectativa, no una comprobación — la comprueba
[Fuente de hechos de cuidado por especie](https://github.com/Joosle/Plantas/issues/3).

## Verificación GBIF

Consultado el 2026-08-10 contra `api.gbif.org/v2/species/match` (COL XR), el camino que
fijó [De un nombre casero a un nombre científico consultable](https://github.com/Joosle/Plantas/issues/5).

| Consulta | matchType | Confianza | Resultado |
| --- | --- | --- | --- |
| `Olea europaea` | EXACT | 97 | `5415040` ACCEPTED, especie |
| `Aloe vera` | EXACT | 97 | `2777724` ACCEPTED, especie |
| `Rosa` | EXACT | 92 | `8395064` ACCEPTED, género |
| `abeto` | **NONE** | — | sin resultado |

`abeto` devolviendo `NONE` es la confirmación en vivo, sobre una planta de esta casa, de
que **la tabla local de alias español → nombre científico es obligatoria** y no un adorno.
Aquí no falla en un caso de laboratorio: falla en la primera planta que no sabemos nombrar.
