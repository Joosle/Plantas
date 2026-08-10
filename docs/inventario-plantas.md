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
