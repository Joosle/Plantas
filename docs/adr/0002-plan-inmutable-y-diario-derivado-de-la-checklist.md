# El plan semanal es inmutable y el diario nace de responder a su checklist

El `WeeklyPlan` se genera una vez por semana y se congela con los hechos que citó; un `PlanItem` **no guarda estado de completado**. Su estado se deriva de las `JournalEntry` que lo responden. La consecuencia práctica es que la checklist semanal, con su cajón de comentarios, *es* la interfaz principal de la app: responder a un ítem —con un vocabulario cerrado de respuestas, no un booleano— es lo que produce la entrada de diario.

Esto invierte el problema clásico de estas apps. El mapa fijó que el diario es el estado del sistema, y el diario es justo el dato que más cuesta que alguien mantenga con disciplina. Al hacerlo nacer de la checklist, se rellena solo como efecto secundario de la interacción que el usuario ya quiere hacer.

## Considered Options

- **Estado mutable en el `PlanItem`** (`pending | done | skipped`), marcado a mano. Más directo, pero la casilla y el diario se desincronizan el primer día: marcas hecho, no se registra nada, y el motor sigue creyendo que esa planta lleva tres semanas sin agua. El diario quedaría como bitácora decorativa, que es exactamente lo que el mapa descartó.
- **Escribir en los dos sitios** (estado en el ítem y acción en el diario). Evita consultar el diario para pintar la lista, a cambio de un dato duplicado que se queda obsoleto en cuanto se registra un riego desde el diario en vez de desde el plan.
- **Plan efímero, recalculado al abrir la app.** Siempre coherente con el presente y cero persistencia, pero imposibilita auditar qué se aconsejó, comparar semanas ([#8](https://github.com/Joosle/Plantas/issues/8)) y medir predicho contra observado ([#13](https://github.com/Joosle/Plantas/issues/13)). El texto lo redacta un LLM: recalcular marzo en agosto no reproduce lo que dijo en marzo.

## Consequences

- Regar el sábado y anotarlo desde el diario deja la casilla del plan ya marcada el domingo, sin reconciliación.
- «No hace falta, está húmeda» no es un estado muerto del ítem: se registra como `Observation` con `reading`, que es material aprovechable para la calibración de [#13](https://github.com/Joosle/Plantas/issues/13).
- **Restricción heredada por [#9](https://github.com/Joosle/Plantas/issues/9)**: toda tarea que el plan sepa mandar necesita su `CareAction` correspondiente en el vocabulario del diario, o habrá ítems imposibles de cerrar.
- Lo no respondido se arrastra: el ítem de la semana siguiente nace nuevo apuntando al viejo, de modo que la antigüedad de lo pendiente es visible. Las `Alert` quedan fuera de este arrastre por diseño — caducan solas.
