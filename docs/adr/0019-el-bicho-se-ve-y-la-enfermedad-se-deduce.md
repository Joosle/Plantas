# El bicho se ve y la enfermedad se deduce: `pest` sobrevive, `disease` cae al texto libre

El [ADR 0017](0017-el-analisis-describe-aspecto-y-nunca-diagnostica.md) vetó `pest` y `disease` al análisis de foto y dejó un agujero a propósito: ninguna de las cuatro plantas de la casa tenía forma de registrar una plaga que el motor leyera. La pregunta que quedaba era si un clasificador **especializado** —con fuente citable, como Pl@ntNet le quitó al LLM la identificación de especie en [#5](https://github.com/Joosle/Plantas/issues/5)— podía escribir lo que el generalista no.

**No puede, y el motivo no es el que parecía.**

## Lo que faltaba era la refutación, no la especialización

En #5, Pl@ntNet ganó por **dos** cosas y no por una: devuelve una clave citable (`gbif.id`, `powo.id`) **y** el dueño puede desmentirla, porque comparar un ejemplar con un nombre y unas fotos de referencia no exige ser botánico. Con la patología sólo se cumple la primera. El [ADR 0015](0015-la-foto-se-analiza-al-subirla-y-el-diario-es-la-junta.md) dejó fijado que el dueño de este jardín no es experto en fitopatología, así que **un falso positivo no lo puede desmentir nadie de esta casa** — y `treat` es echar producto sobre una planta que puede estar sana.

Un clasificador especializado compra una **etiqueta mejor** sobre un **veredicto igual de indesmentible**. Eso no es lo que faltaba.

El corte importa porque es el que hace la decisión robusta: si el criterio fuese sólo «fuente especializada», habría que discutir a ciegas qué umbral de confianza compra el derecho a etiquetar. Con la refutabilidad como criterio no hace falta llegar a esa discusión.

## Los hechos no rescatan a ningún candidato

Los tres candidatos localizados, verificados contra su documentación (agosto de 2026):

| Candidato | Cobertura | Coste | Sobre este jardín |
| --- | --- | --- | --- |
| Pl@ntNet `/v2/diseases/identify` | **65 especies de cultivo** (marzo 2026) | Gratis, 1 crédito de los 500/día de [#14](https://github.com/Joosle/Plantas/issues/14) | **0 de 4** |
| Kindwise `crop.health` | 288 clases sobre **23 cultivos comestibles** | 0,01–0,05 €/petición | **0 de 4** |
| Kindwise `plant.health` | **548 clases**, ornamentales y de interior | 0,05 €/petición, **sin capa gratuita** | Plausible, **73 % en top-3** |

Ni olivo, ni rosal, ni laurel, ni conífera aparecen en la lista de hospedadores de Pl@ntNet, que además se autolimita en su propia documentación a *«informative advice»* y explícitamente *«not intended to provide information on treatments»* — con lo que el enlace a `treat`, que era el motivo de la pregunta, lo corta la propia fuente.

`plant.health` —candidato que el ticket no tenía localizado— es el único que plausiblemente cubriría el jardín, y aparece justo donde `crop.health` fallaba: apunta a ornamentales y plantas de interior. No cambia la decisión, y de paso muestra por qué el criterio de la refutabilidad era el bueno: acierta **tres de cada cuatro veces en el top-3**, sin publicar top-1.

**Ninguno cita la atribución.** Pl@ntNet devuelve un **código EPPO**, que es una clave real y unible a una autoridad fitosanitaria — la forma correcta, la misma que ganó en #5. Pero el código identifica al **patógeno**, no es evidencia de que esta planta lo tenga. Kindwise adjunta licencia y URL de fuente, y son de la descripción (Wikipedia) de la enfermedad y de las imágenes de referencia: te citan *qué es* la roya, no *que tu rosal la tenga*.

Por lo mismo **no vuelve la opción intermedia** que el ADR 0017 aparcó —sugerir la etiqueta sin confirmar y que el dueño la ratifique—: volvía sólo si había fuente citable detrás, y no la hay. Y ratificar es justamente el acto que el ADR 0015 le niega.

## Las dos etiquetas no eran la misma cosa

Aplicada la línea del ADR 0017 con consistencia en vez de por bloque, `pest` y `disease` se separan:

- **`pest` es aspecto.** Pulgón, cochinilla, orugas: el bicho **se ve en la superficie de la planta**. «Tiene pulgón» describe lo que hay, no deduce una causa. Cae del lado bueno de la línea.
- **`disease` es atribución de causa.** Nadie ve un hongo: se ven manchas y se **deduce**. No es observable, y negar ese acto al dueño no es distinto de negárselo al modelo — el problema nunca fue **quién** lo dice, sino que la atribución de causa no se ve.

Así que `disease` cae al **texto libre dentro de una `Observation`**, que es exactamente el escape hatch que el [ADR 0003](0003-regla-de-admision-del-vocabulario-del-diario.md) dejó puesto para lo que no tiene verbo ni etiqueta. Lo que hoy se escribiría como `disease` se registra como `yellowing` más la nota. **El vocabulario de #9 pasa de 8 etiquetas a 7.**

## `treat` deja de estar huérfano, y el plan pregunta en vez de mandar

Con `pest` viva y escrita **a mano**, la objeción de la regla de admisión se disuelve: `pest` → `treat` es una regla que el motor sí lee, igual que `flowering` → `deadhead` en el ADR 0003. `treat` se queda en el vocabulario.

Pero el motor sabe dos cosas —se vio un bicho el día X, y desde entonces no hay `treat` registrado— y **no sabe la tercera**: con qué se trata. Elegir el producto es fitopatología otra vez. Un plan que diga «trata el rosal» manda exactamente lo que el ADR 0017 no quería mandar, sólo que con la firma del dueño en el desencadenante.

La forma que encaja ya existe: el `PlanItem` del [ADR 0010](0010-la-pregunta-es-la-tarea.md), con **las dos mitades congeladas juntas** — la pregunta y la acción que sigue:

> «Mira el rosal: ¿siguen los pulgones?» → `[sí, tratado]` · `[ya no están]`

El motor pregunta por lo observable; **el producto lo elige el dueño**, que es donde estaba la competencia todo el tiempo.

Eso resuelve además **cómo se apaga el episodio**. Una etiqueta `pest` sin contra-señal deja al rosal marcado como infestado para siempre. «Ya no están» es una `Observation` que lo cierra sin borrar nada, igual que la refutación del ADR 0017. Y el re-chequeo lo da la escalera del [ADR 0004](0004-el-silencio-del-diario-es-evidencia-con-caducidad.md): el silencio tras un `pest` **sí** tiene cadencia esperada, a diferencia del silencio no-hídrico general que #9 dejó sin mecanismo.

## Considered Options

- **Adoptar Pl@ntNet `/diseases`.** Gratis, misma cuota ya dada de alta, y la forma correcta (clave EPPO, no cadena). Cae por cobertura antes que por doctrina: 65 especies de cultivo, ninguna de las cuatro de la casa. Aunque cubriera, su documentación se niega a hablar de tratamientos.
- **Adoptar Kindwise `plant.health`.** El único que cubriría el jardín. Cae por la refutabilidad —el criterio de este ADR— y, de haberse aceptado, habría obligado a fijar un umbral sobre un 73 % en top-3 sin top-1 publicado, pagando 0,05 € por consulta sin capa gratuita.
- **Etiqueta sugerida y ratificada por el dueño.** La intermedia que aparcó el ADR 0017. Vuelve sólo con fuente citable detrás; no la hay. Y ratificar un diagnóstico es el acto que el ADR 0015 niega.
- **Rulear plagas y enfermedades enteras fuera del alcance.** Simétrico y limpio, pero tira `pest` —que sí es observable— junto con `disease`, y deja a `treat` sin ninguna regla que lo dispare, rozando la regla de admisión de #9 por el otro lado.
- **Mantener `pest` sin que el motor la lea.** No es una opción intermedia: una etiqueta que el motor no lee y el plan no manda **incumple la regla de admisión**, así que equivale a rulear `pest` fuera del vocabulario también.

## Consequences

- **El vocabulario del ADR 0003 baja a 7 etiquetas**: `pest`, `frost_damage`, `sun_damage`, `flowering`, `fruiting`, `yellowing`, `wilting`. Los 9 verbos siguen intactos, `treat` incluido.
- **`disease` no se sustituye por nada**: se registra como texto libre en una `Observation`, normalmente junto a `yellowing`. Es una pérdida aceptada — el motor no podrá agregar ni comparar episodios de enfermedad entre semanas.
- **Enmienda pendiente al ADR 0017.** Si `pest` es aspecto visible, el análisis de foto puede escribirla: serían **siete** etiquetas y no seis. No se resuelve aquí; queda en ticket propio.
- **El rosal es hoy el único candidato realista** a disparar este mecanismo. El olivo y el laurel llevan años sin intervención y la conífera está sin identificar.
- **Ninguna dependencia nueva**: no entra ningún proveedor de pago en la spec, y la cuota de Pl@ntNet sigue dedicada por entero a identificación de especie.
