# El plan semanal se agrupa por acción, y las decisiones van antes que la lista

La pantalla del plan agrupa los ítems **por verbo** («Riega · 2», «Mira y contéstame · 1»), no una ficha por planta. El silencio —las plantas sin nada que hacer— se pliega al pie como una línea **contada** y desplegable, no desaparece.

Encima de la lista, y antes que ella, va un bloque con todo lo que el sistema necesita que decidas: las supresiones propuestas de [#8](https://github.com/Joosle/Plantas/issues/8) y las reconciliaciones por silencio de [#9](https://github.com/Joosle/Plantas/issues/9), con su «aceptar cambios» y un contador de lo que queda. Mientras quede alguna sin contestar, **la lista de abajo se muestra atenuada**, y las tareas que dependen de una decisión salen punteadas y sin casilla, diciendo de qué dependen. Al contestar se resuelven a la vista, y lo que se retira **queda listado al pie** en vez de esfumarse.

La razón de la agrupación es que el plan no es un parte del estado del jardín: es qué hacer en una pasada. Con las 4 plantas de [#2](https://github.com/Joosle/Plantas/issues/2), de las que sólo una se riega, una ficha por planta gasta la pantalla entera en plantas que no piden nada.

La razón del orden es más fuerte: **las decisiones no son otra tarea más, son las que determinan qué tareas existen**. Aceptar quitar el riego del rosal borra una fila de la lista; contestar que el aloe se regó el jueves borra otra. Enseñar la lista primero es enseñar una lista que puede no valer.

## Considered Options

- **Una ficha por planta, las cuatro siempre visibles.** Se lee como el estado del jardín y en la semana vacía tranquiliza. En la semana llena entierra la única tarea real bajo tres fichas que dicen «nada».
- **Una cosa a la vez, en cola a pantalla completa.** La mejor justificación —la razón cabe entera sin desplegar nada— y la peor visión de conjunto: no se ve cuánto queda ni se puede saltar. Además obliga a contestar la alerta de clima para avanzar, cuando la alerta no se contesta.
- **Agrupado por acción con las decisiones al final**, que es como salían del motor. Descartado al reaccionar al prototipo: coloca la causa después del efecto.

## Consequences

- **El gesto de hecho es una casilla de una pasada**, que pierde la distinción *hecho / poco / no* de las otras dos formas. No es una pérdida: [#9](https://github.com/Joosle/Plantas/issues/9) ya decidió que *ausente = a fondo = `S ← W_max`* y que `poco` es el único desvío, así que la casilla sola significa «regué a fondo» —el caso normal— y `poco` cuesta un toque más en el cajón de nota que se abre al marcar. La interfaz hereda el defecto del modelo en vez de inventarse otro.
- **La semana vacía** —la mitad de las semanas, según `docs/adr/0006`— sale como una tarjeta «Nada que hacer» más el pliegue contado. Que el silencio esté **contado y sea abrible** es lo que, junto con la edad visible del plan de [#10](https://github.com/Joosle/Plantas/issues/10), separa *no hay nada* de *el motor lleva un mes muerto*.
- **La alerta de clima se queda como banda arriba y no se contesta.** No es una tarea: no produce entrada de diario, caduca sola y su ámbito es el sitio o la casa, no la planta ([#7](https://github.com/Joosle/Plantas/issues/7)).
- Lo retirado por una decisión se lista al pie en una línea. Un ítem que desaparece sin rastro sería el mismo fallo que [#8](https://github.com/Joosle/Plantas/issues/8) prohibió al exigir que nada se omita en silencio, sólo que cometido por la interfaz en vez de por el LLM.
