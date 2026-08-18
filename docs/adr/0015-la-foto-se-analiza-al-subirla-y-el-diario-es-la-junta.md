# La foto se analiza al subirla y su conclusión entra en el diario, no en el plan

Una foto no viaja nunca al prompt del plan semanal. Al subirse se analiza por su cuenta y lo que sale de ese análisis es una **`Observation` fechada, de autor `system`**, en el diario. El cron del lunes lee el diario —texto— y decide la semana. Las dos pasadas están unidas por el diario y por nada más.

Esto es la regla del mapa llevada hasta el final: *el diario es el estado del sistema*. Si una evidencia no está en el diario, el motor no la ve; y como toda evidencia acaba en el diario, la decisión semanal se toma siempre sobre la misma superficie —entradas fechadas— venga de donde venga.

## Por qué el LLM sí mira las fotos

El [ADR 0007](0007-lo-que-se-mide-no-se-ajusta.md) repartió la calibración en dos mandos y dejó a las fotos y comentarios como la **única evidencia que mueve `u`** — y, para las tres plantas que nunca se riegan, como el **único falsador que existe**: la predicción es «ningún riego previsto en el horizonte» y no hay fecha que la desmienta. El olivo es mudo por construcción.

El dueño de este jardín no es experto en fitopatología. Un canal de evidencia que sólo un experto sabe leer no es un canal: es un archivo de fotos bonitas. O lo lee el LLM, o `u` no se mueve nunca.

Esto **no contradice** al [ADR de identificación](../../CONTEXT.md) ni a la prohibición de [#5](https://github.com/Joosle/Plantas/issues/5) de que el LLM identifique por foto, y la distinción importa: identificar tiene una respuesta **verificable contra GBIF**, así que dejar al LLM inventar el nombre desperdiciaba una fuente citable mejor que él. «Esta hoja está parda en un 30 % del follaje» no tiene fuente citable ninguna. Ahí el LLM no compite con una autoridad mejor — es la única que hay.

## La línea base es texto, no píxeles

El síntoma útil casi nunca está en una foto sola: «el olivo tiene hojas pardas» puede ser lo normal en ese olivo desde hace tres años. Lo que informa es el **cambio**. El análisis recibe por tanto la foto nueva **más sus propias descripciones anteriores del diario**, y compara imagen contra texto: *«hace tres semanas anoté puntas pardas en un 10 % de las hojas; hoy veo un 30 %»*.

Tres consecuencias que lo justifican frente a pasarle la serie de imágenes:

- El coste es **constante en el tiempo** en lugar de crecer con el histórico.
- El análisis es **auditable**: puedes leer en qué se apoyó. Con comparación de píxeles no puedes.
- El modelo **no puede reescribir en silencio** lo que dijo en mayo.

La excepción es el cambio muy lento, de un año para otro, donde la descripción de hace doce meses la escribió un modelo distinto con otro criterio. Para eso existe la **foto de referencia**: una por planta y temporada, marcada, que entra siempre como imagen junto a la nueva. Cuatro plantas × cuatro temporadas = 16 imágenes vivas, coste plano.

## Al subir, no el lunes

Hay una pescadilla: el plan del lunes necesita el análisis hecho, pero **quien te pide la foto es el propio plan** — la checklist es la interfaz ([ADR 0002](0002-plan-inmutable-y-diario-derivado-de-la-checklist.md)). Analizar dentro del cron del lunes significaría que la foto que te pide el lunes no se analiza hasta el lunes siguiente.

El análisis corre por tanto **al subir la foto**, en segundo plano del propio Worker (`ctx.waitUntil`), y la `Observation` se fecha con el `occurredAt` de la foto, no con el del análisis. Se descarta meterlo en el cron diario: ese camino es **sin LLM por diseño** ([#10](https://github.com/Joosle/Plantas/issues/10)), para que la fragilidad del modelo no se lleve por delante las alertas de helada.

**Si el análisis falla, la foto se guarda igual** y queda marcada como no analizada; el cron del lunes barre las pendientes antes de redactar. El fallo del LLM nunca pierde una foto ni bloquea una subida.

## Lo que el LLM no hace: mover `u`

Describir lo que se ve y mover un parámetro físico no son la misma operación, y aquí se separan a propósito. Si el mismo actor **produce** la evidencia y la **interpreta**, el lazo que el ADR 0007 abrió deliberadamente se cierra sobre sí mismo y ya no queda nada fuera.

Con el reparto de este ADR siguen habiendo dos canales independientes: el LLM describe, y lo que mueve `u` es la **comparación entre esa descripción y lo que el modelo predecía**. Quién ejecuta esa comparación queda abierto, y es una posible enmienda al ADR 0007 — porque ese ADR apoyó la calibración en una ratificación humana experta que no existe.

Lo que sí produce el análisis, vía el plan, es el verbo `inspect`: «revisa el olivo, puede estar pasando sed». Es la salida útil para un no experto —no un parámetro, sino ir a mirar— y entra por la regla de admisión de [#9](https://github.com/Joosle/Plantas/issues/9) sin inventar nada.

## Considered Options

- **La foto entra en el prompt del plan semanal.** Una sola pasada, menos piezas. Rechazada: el plan pasaría a decidir sobre dos superficies distintas (diario y píxeles), y lo que el modelo vio en la foto no quedaría escrito en ninguna parte auditable.
- **El LLM describe y además propone el ajuste de `u`.** Más automático y más útil para un no experto. Rechazada por lo dicho: es el productor de la evidencia interpretándose a sí mismo.
- **Sólo tú y Pl@ntNet miran las fotos** (el LLM nunca). Coherente con #5 leído en bruto. Rechazada: deja `u` sin ningún canal que un no experto sepa operar, y devuelve al olivo a la mudez.
- **Comparar la serie de imágenes cada semana.** Máxima fidelidad. Rechazada por coste creciente, por no ser auditable y por permitir al modelo contradecir en silencio lo que dijo antes.
- **Analizar en lote en el cron del lunes.** Encaja en los dos crons de #10 sin abrir un tercer camino. Rechazada: ata el análisis a una hora que no es la de la foto, y con la cola offline una foto puede aparecer un miércoles a las tres.

## Consequences

- Aparece un **tercer camino de ejecución con LLM**, además de los dos crons de #10: la subida de foto. Es el primero que corre en respuesta a algo que pulsas, así que necesita su propia valla de fallo (la foto se guarda aunque el análisis muera).
- El diario gana entradas de **autor `system`**. Hasta ahora todo lo escribías tú o se autogeneraba sin juicio (`LifecycleEvent`). Una `Observation` opinada y automática es nueva, y obliga a decidir si se ciñe a las etiquetas cerradas de #9 o convive con texto llano.
- La cronología del diario puede **desordenarse respecto a la escritura**: tu entrada llega al instante y la del LLM días después si estuviste sin cobertura. Se lee bien porque ambas se ordenan por `occurredAt`, que es la separación que #9 ya había fijado para otro motivo.
- **La foto pasa a ser una fuente, no un adorno**, y eso arrastra al backup: una copia que guarda las conclusiones y tira los datos que las produjeron no permite auditar nada.
