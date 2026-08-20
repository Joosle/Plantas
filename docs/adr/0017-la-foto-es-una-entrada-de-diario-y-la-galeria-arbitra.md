# La foto es una entrada de diario, y la galería es donde se arbitra

Una foto es un **cuarto tipo de `JournalEntry`**, `Photo`, junto a `CareAction`, `Observation` y `LifecycleEvent`. No lleva campo de propósito. Y la comparación entre dos fotos no es un álbum: es la única superficie de este sistema que un no experto sabe arbitrar, así que es donde se ratifica una propuesta sobre `u`.

El [ADR 0015](0015-la-foto-se-analiza-al-subirla-y-el-diario-es-la-junta.md) decidió que el LLM analiza la foto y el [ADR 0016](0016-las-fotos-viven-en-r2-sin-tope-y-a-2576-px.md) dónde vive el archivo, pero entre los dos quedó un hueco: el `PhotoRef` de 0016 no colgaba de nada. Este ADR lo cuelga.

## `Photo` se gana el tipo por la regla de admisión, no por comodidad

El [ADR 0003](0003-regla-de-admision-del-vocabulario-del-diario.md) fijó que algo entra en el vocabulario cerrado **si el motor lo lee o el plan puede mandarlo**. La foto cumple **las dos mitades**: el plan la pide ([#34](https://github.com/Joosle/Plantas/issues/34)) y el motor la lee (ADR 0015). Es el segundo caso que las cumple a la vez, después de `shelter` en [#19](https://github.com/Joosle/Plantas/issues/19).

La alternativa —la foto como adjunto de otra entrada— era más barata y estuvo cerca de ganar. Pierde porque obliga a inventar una entrada anfitriona para la foto que no acompaña a nada, y porque la foto **es** el hecho: cuando fotografías una planta, el hecho registrado es que la miraste ese día y quedó constancia de cómo estaba. Un adjunto describe algo; aquí no hay ese algo aparte.

## Un solo tipo para los tres trabajos, y sin campo de propósito

Hoy una foto hace tres cosas distintas: la periódica de estado, la que contesta una pregunta fenológica ([ADR 0010](0010-la-pregunta-es-la-tarea.md)) y la de identificación que va a Pl@ntNet ([#5](https://github.com/Joosle/Plantas/issues/5)). Las tres son `Photo`. Dos mecanismos para el mismo archivo habrían dejado la galería viendo sólo una parte del jardín.

**El propósito no se guarda.** Se deriva de la entrada hermana que el análisis produjo: `Observation` de autor `system` si se analizó el estado, `LifecycleEvent` si se identificó, `Reading` de `phenology` si contestó una pregunta. Es la cuarta vez que este mapa decide lo mismo —el estado del `PlanItem` en [ADR 0002](0002-plan-inmutable-y-diario-derivado-de-la-checklist.md), el arraigo en [ADR 0008](0008-emplazamiento-y-arraigo-son-ejes-distintos.md), el rasgo remontante en [#25](https://github.com/Joosle/Plantas/issues/25)— y por cuarta vez la razón es que un derivado guardado es un derivado que puede mentir.

### Lo que casi obliga a guardarlo

El ADR 0016 le puso al `PhotoRef` una bandera *analizada / pendiente* y encargó al **cron del lunes barrer las pendientes**. Ese barrido corre desacoplado de la subida: el contexto que sabía qué analizador tocaba ya no existe, y sin propósito en la fila el cron tiene una foto y ninguna instrucción.

Se cierra sin campo nuevo declarando que **sólo el análisis de estado es diferible**:

- **Identificación** es interactiva por construcción — #5 devuelve una **lista cerrada de candidatos** entre los que eliges. Una lista que nadie está mirando el lunes no sirve de nada, así que si falla, falla en tu cara y se reintenta cuando tú quieras.
- **Respuesta fenológica** hoy **no tiene analizador**: el ADR 0010 fijó que mientras no haya modelo de visión la respuesta sigue siendo el sí/no y la foto es sólo prueba adjunta.
- **Estado** es la única asíncrona, y es la que el ADR 0015 mandó correr en `ctx.waitUntil` con la valla de que un fallo guarda la foto igual.

Con eso *pendiente* significa exactamente una cosa y el barrido no pregunta nada.

## La foto que nadie pidió

El ADR 0015 escribió su lógica dando por supuesto que *«quien te pide la foto es el propio plan»*. Falta el caso real: pasas por delante del aloe, tiene mala cara, y le haces una foto. Existe, es una `Photo` normal y va por el mismo analizador.

El riesgo no es guardarla sino **que entre en la serie sin distinguirse**. El análisis del ADR 0015 compara la foto nueva contra sus propias descripciones anteriores; si las espontáneas se mezclan con las periódicas, la serie deja de ser «cómo va esta planta» y pasa a ser «cómo se veía las veces que me preocupé», que leída como tendencia miente siempre en la misma dirección. Es la forma que [#13](https://github.com/Joosle/Plantas/issues/13) ya cazó en otro sitio: *bajo hábito el modelo aprendería tu calendario y te lo devolvería como consejo*.

No hace falta mecanismo nuevo, por dos razones:

- **La valla ya existe.** El [ADR 0007](0007-lo-que-se-mide-no-se-ajusta.md) exige 3 hechos concordantes **con al menos una señal negativa**. Una serie de fotos-de-preocupación es toda del mismo signo, así que por sí sola no mueve `u`.
- **El discriminador ya está en el modelo.** Una foto periódica contesta un `PlanItem`; una espontánea, no. Derivado, como el propósito. Lo único que se añade es que el análisis **reciba** cuál está mirando, para no leer un muestreo sesgado como tendencia.

Restricción que sale de aquí para #34: **la foto de referencia de la temporada nunca es una espontánea**, porque la referencia tiene que ser la normal.

## La galería, y por qué no es un álbum

Una foto sola no dice nada; dos a un año de distancia dicen mucho. La comparación es una **galería por planta**, colgada de la ficha —que ya es superficie desde que [#9](https://github.com/Joosle/Plantas/issues/9) hizo que editarla genere un `LifecycleEvent`—, así que no compite con la checklist que el ADR 0002 puso en el centro.

**Lo que la licencia es la enmienda de #25.** La regla original de [#11](https://github.com/Joosle/Plantas/issues/11) —*la foto sólo arbitra si la evidencia cae de los dos lados*— la habría descalificado por asimétrica: una planta que se ve mal está mal, pero una que se ve bien puede estar a dos días de venirse abajo. #25 debilitó la regla a *¿termina al menos una rama, y la otra degrada a un estado definido en vez de a una conjetura?*, y con esa pasa: «se ve peor que en marzo» termina, y «se ve igual» no es una conjetura sino el estado definido **el modelo no se está equivocando de forma visible**.

Y de ahí sale su segundo trabajo. El ADR 0007 apoyó la calibración de `u` en que el sistema propone y tú **ratificas**, y [#22](https://github.com/Joosle/Plantas/issues/22) estableció después el hecho que ese ADR no tenía delante: **no eres experto**. Ratificar un número que no sabes juzgar es teatro. Ratificar «¿se ve peor que en la foto de marzo?» no lo es. La galería es el sustrato que le faltaba a esa ratificación; **quién propone el ajuste sigue siendo la pregunta de [#33](https://github.com/Joosle/Plantas/issues/33)** y este ADR no la contesta.

### El par sale de los hechos citados

Cuando una propuesta enlaza a la galería, las dos fotos son **las de sus `citedFacts`** —el catálogo que el [ADR 0006](0006-el-llm-supervisa-y-el-codigo-propone.md) ya obliga al código a validar—, no «referencia contra última». Un par razonable pero ajeno al argumento sería la interfaz cometiendo por su cuenta el fallo que el ADR 0006 prohibió: que lo que se ve y lo que se decidió no coincidan.

Si ningún hecho citado viene de una foto, **no hay enlace**. Es correcto y no un borde: significa que esa propuesta no se apoya en nada visual. Para el olivo, el abeto y el rosal no puede ocurrir, porque la foto es su único canal.

## Considered Options

- **La foto como adjunto de una `JournalEntry`.** Ningún tipo nuevo, el vocabulario cerrado intacto. Rechazada: obliga a inventar una entrada anfitriona vacía para la foto espontánea, y la foto no describe un hecho aparte — lo es.
- **Un campo `purpose` cerrado en la entrada.** Resuelve el barrido del lunes y blinda la prohibición de #5 de un plumazo. Rechazada por guardar un derivado, el cuarto caso que el mapa lleva tres veces rechazando; el barrido se cierra más barato acotando qué es diferible.
- **El propósito vive en la cola de análisis, no en la foto.** Deja la entrada limpia y admite diferir los tres analizadores. Rechazada: añade una pieza con estado propio que puede desincronizarse de la foto, para habilitar dos diferimientos que nadie necesita.
- **`Photo` sólo para la periódica**, con identificación y respuesta fenológica como adjuntos. Cambio más pequeño. Rechazada: dos mecanismos para el mismo archivo y una galería que no ve todas las fotos de la planta.
- **La espontánea fuera de la serie de evidencia.** Serie garantizada limpia. Rechazada: tira justo las fotos que hiciste porque había algo que ver, y el sesgo que evitaba ya lo contiene la regla de la señal negativa del ADR 0007.
- **Sin vista humana: las fotos son archivo y sólo las lee el LLM.** Honesto con que el ADR 0015 ya dio a la máquina lo que necesitaba. Rechazada: deja el *«todas, para siempre»* del ADR 0016 sin nadie que mire, y a la ratificación del ADR 0007 sin nada que enseñar.
- **La comparación embebida en el plan, aparte de la galería.** No te saca del sitio donde decides. Rechazada: dos maneras de enseñar exactamente lo mismo.

## Consequences

- **Enmienda a [#7](https://github.com/Joosle/Plantas/issues/7) y [#9](https://github.com/Joosle/Plantas/issues/9)**: el diario tiene cuatro tipos de entrada, no tres. `JournalEntry = CareAction | Observation | LifecycleEvent | Photo`.
- **Enmienda al ADR 0015**: su supuesto de que la foto la pide siempre el plan se cae, y el análisis pasa a recibir si la foto es periódica o espontánea para no leer un muestreo sesgado como tendencia.
- **Enmienda al ADR 0016**: el `PhotoRef` es la carga de una entrada `Photo` y no una fila suelta; su bandera *pendiente* pasa a tener un único significado, y el barrido del lunes queda especificado.
- Aparece una **segunda pantalla**, la galería en la ficha. Es la primera superficie de esta app que existe para mirar y no para decidir — y sólo se gana el sitio porque además arbitra.
- La ratificación del ADR 0007 deja de ser abstracta, pero **no queda cerrada**: #33 sigue debiendo quién propone el ajuste y qué se ratifica exactamente. Lo que este ADR aporta es que ya hay algo concreto que enseñar.
- El olivo, el abeto y el rosal —las tres que nunca se riegan— pasan a tener una vista donde su única evidencia es legible por ti y no sólo por el modelo.
