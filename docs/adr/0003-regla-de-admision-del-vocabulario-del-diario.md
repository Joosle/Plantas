# El vocabulario del diario se decide por regla, no por lista

Un `Verb` de `CareAction` —y una etiqueta de `Observation`— existe **si y sólo si alguna regla del motor lo lee, o el plan puede llegar a mandarlo**. Lo que no cumple ninguna de las dos cosas no se añade al vocabulario: se escribe como texto libre dentro de una `Observation`.

La regla importa más que la lista que produce. El vocabulario va a crecer —cuando [#3](https://github.com/Joosle/Plantas/issues/3) diga qué cuidados hay documentados, cuando entre la primera planta de interior— y sin un criterio escrito cada verbo nuevo vuelve a ser una discusión desde cero sobre si merece estar. Con criterio, la respuesta se calcula.

El diario tiene además una restricción heredada del [ADR 0002](0002-plan-inmutable-y-diario-derivado-de-la-checklist.md): toda tarea que el plan sepa mandar necesita su `CareAction`, o habrá ítems imposibles de cerrar. La regla la absorbe: "el plan puede mandarlo" es literalmente una de sus dos mitades.

## Lo que la regla admite hoy

| Verbo | Por qué entra |
| --- | --- |
| `water` | lo **lee** el balance hídrico ([#6](https://github.com/Joosle/Plantas/issues/6)): resetea el depósito |
| `mulch` | lo **lee**: el acolchado cambia la evaporación del suelo |
| `fertilize` | lo manda el plan (calendario estacional) |
| `prune` | lo manda el plan |
| `deadhead` | lo manda el plan, con cadencia semanal en floración — que no es la de `prune`, anual |
| `treat` | lo manda el plan tras una `Observation` con etiqueta `pest` o `disease` |
| `repot` | lo manda el plan |
| `protect` | lo manda el plan ante alerta de helada u ola de calor |
| `harvest` | lo manda el plan (la aceituna del olivo del inventario) |

Etiquetas de `Observation`, por el mismo criterio: `pest` y `disease` (sugieren `treat`), `frost_damage` y `sun_damage` (dicen si la alerta acertó), `flowering` (activa la cadencia de `deadhead`), `fruiting` (activa `harvest`), `yellowing` y `wilting` (entran en el diagnóstico del consejo).

`Reading` es el mismo mecanismo para juicios con escala. Hoy existe uno solo: humedad del sustrato, `seco | húmedo`.

**Lo que la regla deja fuera hoy**: limpiar hojas, desbrozar, entutorar, airear el sustrato. Ninguna regla los lee y ningún plan los manda — limpiar hojas es gesto de interior, y el inventario no tiene interior. Entran el día que algo los lea.

## Considered Options

- **Texto libre con etiquetas inferidas por el LLM.** Es de largo lo más natural de escribir, y por eso tentador. Rechazado porque pone el estado del sistema a merced de una extracción que puede fallar **en silencio**: si un día no saca `pest` de "unos bichos blancos", el motor no ve la plaga y nadie se entera. Además rompe la auditabilidad que el [ADR 0002](0002-plan-inmutable-y-diario-derivado-de-la-checklist.md) compró al congelar el plan — releer marzo en agosto no reproduce lo que el LLM entendió en marzo.
- **Etiquetas libres creadas por el usuario.** Máximamente fiel a cómo uno piensa su propio jardín, pero ninguna regla puede leerlas y la deriva es inevitable: `#plaga`, `#plagas` y `#bichos` como tres cosas distintas.
- **Vocabulario cerrado pero extensible por el usuario.** Un verbo añadido a mano no lo lee ninguna regla ni lo manda ningún plan, así que por construcción no pasa la regla de admisión: es una `Observation` con traje de acción. La extensibilidad no compra nada y confunde el contrato.
- **Lista fija de nueve, sin regla escrita.** Misma lista de partida, menos que especificar. Rechazada porque el vocabulario es justo lo que va a crecer, y sin criterio cada crecimiento es una discusión nueva.

## Consequences

- **`mulch` cobra una deuda contra [#6](https://github.com/Joosle/Plantas/issues/6)**: se admite porque el motor lo lee, pero el informe agronómico no modela el acolchado en ninguna parte. Con 3 de las 4 plantas del inventario en suelo, no es un caso marginal. Ticket propio.
- El **cajón de nota libre está siempre**, junto a cualquier verbo: la regla acota lo que el motor lee, no lo que el usuario puede escribir.
- `CareAction(water)` admite una cantidad cualitativa opcional. Ausente significa riego a fondo y resetea el depósito a lleno (`S ← W_max`, el supuesto que ya hacía [#6](https://github.com/Joosle/Plantas/issues/6)); `poco` hace un reset parcial. La fricción aparece sólo cuando te apartas del caso normal.
