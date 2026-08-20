# Nadie ratifica: el ajuste se aplica, y el síntoma es quien lo desmiente

El [ADR 0007](0007-lo-que-se-mide-no-se-ajusta.md) apoyó toda la calibración en que el sistema propone y **el usuario ratifica**. [#22](https://github.com/Joosle/Plantas/issues/22) estableció después un hecho que aquel ADR no tenía delante: **el usuario no es experto**. Este ADR retira la ratificación de los dos mandos y pone el control **detrás** de la aplicación, no delante.

La ratificación no cae por barata. Cae porque **nunca existió como valla**: pedirle a alguien que apruebe si un aloe debe regarse el doble es pedirle un juicio que no está en condiciones de emitir, y una firma sin autoridad no protege de nada — sólo reparte la culpa. El propio ADR 0007 rozó el problema al descartar «sin tope, la ratificación es el tope» (*«en tres años no se recuerda por qué se aceptó»*), pero lo diagnosticó como falta de memoria cuando era falta de competencia.

## Compara el código, no el LLM

[#22](https://github.com/Joosle/Plantas/issues/22) le prohibió al LLM mover `u` —si el mismo actor produce la evidencia y la interpreta, el lazo que el ADR 0007 abrió a propósito se cierra sobre sí mismo— y dejó abierto quién ejecuta la comparación entre lo que la foto describe y lo que el modelo predecía.

**La ejecuta el código.** No es una elección de prudencia: [#32](https://github.com/Joosle/Plantas/issues/32) cambió el terreno bajo aquella pregunta. La salida del análisis ya no es prosa, es una etiqueta de un conjunto de seis más una **extensión ordinal cerrada** de 3–4 pasos (`puntual | parte | mayoría`), que es un `Reading`. El código tiene los dos lados y la contradicción es una regla escrita:

- **Síntoma con reserva alta** — el modelo cree la planta cómoda y no lo está: `u` es demasiado alto, la planta sufre antes de lo modelado.
- **Reserva baja sostenida sin síntoma** — el modelo predice sufrimiento y la planta está impecable: `u` es demasiado bajo.

Dejarlo en manos del LLM del lunes habría cerrado el lazo un paso más allá y sin que se notara, y con el agravante del [ADR 0015](0015-la-foto-se-analiza-al-subirla-y-el-diario-es-la-junta.md): el análisis se realimenta con sus propias descripciones, así que estaría midiéndose contra su propio pasado dos veces.

## No hay puerta: hay ventana de seguimiento

El ajuste se **aplica** cuando la evidencia lo sostiene. Lo que antes era la ratificación pasa a ser un falsador posterior.

El falsador obvio no vale. «Se aplica y el síntoma desaparece» revierte *siempre* `yellowing`: una hoja que amarilleó no vuelve a verde. Pero el ordinal de #32 mide **extensión**, y la extensión sólo puede crecer o quedarse quieta. De ahí la regla:

> El ajuste se **revierte si el síntoma sigue avanzando en la escala** dentro de la ventana de seguimiento. Que se pare es aprobado; que suba es suspenso.

Vale en las dos direcciones: bajé `u` y la extensión sigue subiendo → no era `u`; subí `u` y aparece síntoma donde no lo había → tampoco.

**El lazo sólo puede suspender, nunca aprobar.** No hay grupo de control: si la planta mejora puede ser el ajuste o puede ser que se acabó agosto. El sistema no dice nunca «el ajuste funcionó», dice «no ha sido desmentido». Reclamar el mérito es el autoengaño que el ADR 0007 quiso evitar, y sin ratificación humana delante sería la única voz en la sala.

La ventana se cuenta en **observaciones, no en semanas** — la cadencia de fotos la fija [#34](https://github.com/Joosle/Plantas/issues/34), y hasta que exista, este ADR no puede ponerle el reloj.

La reversión automática hereda la revocación del ADR 0007 sin cambios: restaura el **valor exacto anterior**, así que no pelea con el tope por paso, y **queda como hecho fechado**. La revocación manual sigue siendo libre, sin evidencia y sin pasar por el LLM: es lo único de aquel ADR que este no toca.

## Al menos un hecho tiene que ser tuyo

El ADR 0007 exige 3 hechos fechados concordantes con **al menos una señal negativa**, y su razonamiento es de independencia: *«si riegas el aloe los domingos por costumbre, tres riegos concordantes no son evidencia: son tu calendario»*.

Ese mismo razonamiento, aplicado al canal de `u`, condena la evidencia que el ADR 0007 le asignó. Como el análisis se realimenta con sus descripciones anteriores (ADR 0015, confirmado por #32: una línea base falsa *«se hereda hasta ser el pasado oficial de la planta»*), **tres análisis de fotos consecutivas no son tres hechos: son la misma cadena leyéndose tres veces**. Es la inercia del calendario de los domingos con otro disfraz — y ya no hay ratificación que la pare.

Por tanto: **al menos uno de los tres hechos no puede salir del análisis automático.** Tiene que ser un comentario del usuario, del cajón que el [ADR 0006](0006-el-llm-supervisa-y-el-codigo-propone.md) pone en la línea por planta del plan vacío.

Esto no reintroduce por la puerta de atrás el juicio que acabamos de retirar. No se pide «¿debe regarse el doble?» sino «¿lo ves raro?»: **aspecto, no diagnóstico** — exactamente la línea que #32 le trazó al LLM, aplicada al humano. Es también dónde encaja la pregunta *«¿esta planta te preocupa?»*: en el lado de la **evidencia**, fechada, nunca como aprobación. Si contestara y además aprobara, el lazo se cerraría con la mano del usuario.

El mecanismo que produce ese hecho ya existe y no hay que diseñarlo: el ADR 0007 señala el síntoma contradictorio **el mismo día**, y #22 fijó que la salida útil para un no experto es `inspect` (*«revisa el olivo, puede estar pasando sed»*). El sistema manda mirar, el usuario mira y comenta.

**El precio, dicho en claro**: si el usuario no observa nunca, `u` no se mueve — y para las tres plantas que nunca se riegan ése es su único canal. Es una versión de la parálisis que el ADR 0007 temía, pero pide **observar** donde antes pedía **aprobar**, y observar sí está al alcance de un no experto.

## Una reversión consume sus hechos

Con ratificación, un ajuste rechazado moría ahí. Sin ella, los tres hechos que lo causaron **siguen en la base de datos** después de revertirlo, tan concordantes como el primer día: el sistema volvería a aplicar, la ventana volvería a desmentirlo, y el plan del aloe cambiaría cada dos semanas mientras el diario se llena de ruido.

El ADR 0007 tenía la intuición (*«la revocación queda como hecho fechado... evidencia contra volver a proponer lo mismo»*) pero la dejó como intención, porque el humano era quien decía que no. Se convierte en regla:

> Para volver a mover el parámetro **en la misma dirección** hacen falta 3 hechos nuevos, **todos posteriores a la fecha de la reversión** — y uno de ellos del usuario.

La dirección contraria no se bloquea: si la evidencia se da la vuelta, que se mueva.

## El separador pasa del tope a la deriva

El ADR 0007 le daba al tope por paso una segunda función: *«un ajuste dentro del paso es calibrar; querer más que eso es afirmar que WUCOLS clasificó mal a esta planta»*. Eso funcionaba **porque había un humano queriendo más**. Sin ratificación no hay nadie empujando contra el tope: el código no «quiere» pasarse, simplemente no lo hace, y el separador se queda sin mecanismo.

El tope no desaparece — recupera la función que tenía en [#6](https://github.com/Joosle/Plantas/issues/6), proteger de un salto salvaje automático, que es justo lo que vuelve a haber. Lo que se repone es el disparador de la señal buena, la que el ADR 0007 llama información sobre el mundo frente a `KL × 3`, que no lo es:

> El disparador es la **deriva acumulada**: cuando el desplazamiento acumulado respecto del valor citado supera lo que un solo paso permite —el segundo ajuste consecutivo en la misma dirección que sobrevive a su ventana—, deja de ser el rincón.

Es mecánico y no pide criterio, así que un no experto no tiene que levantarlo.

**Y produce una anotación, no una impugnación.** El ADR 0007 cuelga el `ParameterAdjustment` de la `Plant` y no del `CareProfile` porque el ajuste absorbe la realidad del rincón donde vive el ejemplar. Con **4 plantas y 4 taxones distintos** ([#2](https://github.com/Joosle/Plantas/issues/2)), `n = 1` por taxón: la deriva sostenida del olivo es indistinguible de «ese rincón es raro». Queda registrado que este ejemplar lleva N pasos alejándose del valor citado, visible en la procedencia del hecho, y se convierte en impugnación el día que un **segundo ejemplar del mismo taxón** derive igual — el escenario que el ADR 0007 ya dejó anticipado.

## El ajuste es una entrada de diario

Retirada la puerta, el riesgo se da la vuelta: ya no es la parálisis, es que **el plan cambie solo y nadie sepa por qué**. Eso además rompería la revocación libre, que sobrevive: no se puede retirar lo que no se sabe que ha pasado.

Del ADR 0007 sobrevive intacta la mitad buena —*«el desajuste es visible desde el primer hecho, en la línea por planta del plan: dije 20 % y anotaste húmedo»*—, sólo que ya no es el preámbulo de una propuesta: es el canal entero.

No se inventa superficie nueva. **El ajuste y su reversión son entradas de diario escritas por el sistema**, con la maquinaria que [#32](https://github.com/Joosle/Plantas/issues/32) fijó para el análisis de foto: misma línea de tiempo por `occurredAt`, autoría marcada, **voz propia** en tercera persona citando la evidencia de la que sale, y redacción llana del [ADR 0011](0011-plan-agrupado-por-accion-con-las-decisiones-delante.md):

> *«El aloe pasa a pedir agua cada 12 días en vez de cada 19. Tres veces desde julio se han visto hojas arrugadas con el depósito por encima de la mitad.»*

Con eso la revocación manual queda **a un toque desde esa entrada** —el mismo patrón de «el falsador a un toque» de #32— y el rastro *«ajustado en septiembre, revertido en noviembre»* que el ADR 0007 quería conservar existe sin registro aparte, porque la reversión escribe la suya.

## Considered Options

- **Mantener la ratificación sobre el contrafactual y no sobre el número.** El ADR 0007 ya diseñó el objeto correcto —*«el aloe pasaría a pedir agua cada 12 días en vez de cada 19»*—, y es una pregunta sobre el jardín y el tiempo del usuario, no sobre fisiología. Rechazada por el propio usuario: no reclama autoridad para decidir si el aloe debe regarse el doble. Aprobar el efecto sigue siendo aprobar.
- **Conservar la ratificación sólo para `KL`**, cuyo contrafactual sí trae marcador legible (*«habría acertado 4 de 5 veces en vez de 1»*). Rechazada: ese marcador se mide contra las **mismas fechas de riego contaminadas por el hábito** que el ADR 0007 declara no autorizantes al separar el detector de la corrección. Un marcador construido sobre la señal no autorizante no da autoridad, da su apariencia.
- **Que compare el LLM del lunes** leyendo sus propias observaciones y la predicción. Rechazada: es literalmente el modo de fallo que #22 nombró, un paso más allá y sin que se note.
- **Cegar el análisis de foto** (sin descripciones previas en contexto) para romper la correlación entre hechos, en vez de exigir un hecho humano. Rechazada: cuesta la detección de cambio que compró el ADR 0015 — sin pasado no hay «va a más», y el ordinal de #32 se queda sin contra qué compararse.
- **Revertir si el síntoma no desaparece.** Rechazada: el amarilleo es irreversible en la hoja existente, así que revertiría siempre los ajustes correctos.
- **Bloquear para siempre un ajuste revertido.** Rechazada: un error de un agosto raro congelaría el parámetro de esa planta el resto de su vida.
- **Enfriamiento por tiempo** tras una reversión. Rechazada: con ~20 riegos al año el reloj no dice nada; los hechos sí.
- **Que el sistema pregunte «¿esta planta te preocupa?» como acto de ratificación.** Es una pregunta que el usuario sabe contestar, pero contestarla *y* aprobar con el mismo gesto cierra el lazo con su propia mano. Se conserva la pregunta, en el lado de la evidencia.

## Consequences

- **Enmienda al [ADR 0007](0007-lo-que-se-mide-no-se-ajusta.md)**: cae la ratificación en los dos mandos; `ParameterAdjustment` pierde el estado «propuesto, esperando ratificación»; el tope por paso pierde su función de separador y recupera la de #6. Sobreviven sin cambios los dominios (`u ∈ [0,2 , 0,85]`, `KL ∈ [0,05 , 1,2]`), «lo que se mide no se ajusta», el detector que no autoriza, el ajuste colgando de la `Plant` y la revocación manual libre.
- **`ParameterAdjustment` gana estado y fechas de seguimiento**: aplicado, ventana en curso, revertido o consolidado — y la fecha de reversión, que es la que gobierna la regla de vaivén.
- **El comentario en llano del usuario pasa de desahogo a requisito**: es el único hecho no producido por la cadena automática, y sin él `u` no se mueve. Refuerza la decisión del ADR 0006 de dar sitio al cajón de comentarios en el plan vacío.
- **`inspect` gana peso**: es el generador del hecho humano que la regla exige, no sólo un consejo amable para un no experto.
- **[#34](https://github.com/Joosle/Plantas/issues/34) hereda una obligación**: la cadencia de fotos determina cuánto dura en tiempo real la ventana de seguimiento, y por tanto cuánto tarda un ajuste equivocado en revertirse.
- **La procedencia del `CareProfile` gana una anotación de deriva**, que no es un hecho citado ni lo contradice: registra que un ejemplar se aleja del valor de la fuente. Habrá que ver dónde vive dentro de la vista resuelta de [#18](https://github.com/Joosle/Plantas/issues/18).
- **El diario admite entradas de sistema de un segundo tipo** además del análisis de foto del ADR 0017: el ajuste y su reversión. Misma voz, misma autoría marcada, mismo falsador a un toque.
