# El silencio del diario es evidencia, pero caduca

No anotar nada de una `Plant` significa **no haber hecho nada** — y el balance hídrico corre con esa premisa. Pero sólo hasta cierto punto: cuando el silencio supera con holgura la cadencia que el motor esperaba de *esa* planta (regla de partida: `días_silencio > 2 × intervalo_predicho`), deja de ser creíble. Entonces el motor **lo declara**, degrada el consejo de cuantitativo a cualitativo y pide una reconciliación de un solo toque.

El mapa fijó que el diario es el estado del sistema, y el informe agronómico ([#6](https://github.com/Joosle/Plantas/issues/6)) ya avisó de la consecuencia: *"si el usuario no registra, el modelo deriva y hay que decírselo"*. Esto es el "hay que decírselo", con un umbral concreto.

La forma es deliberadamente la misma que [#4](https://github.com/Joosle/Plantas/issues/4) le dio al clima rancio, y por la misma razón: **un dato viejo no se descarta ni se cree, se degrada por escalones declarados**.

| | señal | al caducar |
| --- | --- | --- |
| Clima ([#4](https://github.com/Joosle/Plantas/issues/4)) | snapshot > 72 h | deja de emitir alertas, cae a plan por calendario |
| Diario (aquí) | silencio > 2 × intervalo predicho | deja de dar litros, pide reconciliación |

## El umbral es relativo, no un plazo fijo

Es la parte que no se puede simplificar. En el inventario real, doce días sin anotar nada del olivo es exactamente lo normal —está arraigado, no se toca— y doce días del aloe en agosto es sospechoso. Un plazo fijo global preguntaría por el olivo (absurdo), interrumpiría al aloe en enero sin motivo y llegaría tarde en agosto.

Al ser relativo, una planta de la que el motor **no espera nada** nunca genera sospecha por callar: no había nada que anotar. Sobre el inventario de hoy, **sólo el aloe puede disparar una reconciliación**.

## La reconciliación

Una pregunta, dos respuestas: *"llevo N días sin saber de ti — comprueba con el dedo: [seco] [húmedo]"*. La respuesta se guarda como `Observation` con `reading` de humedad del sustrato, y resetea el depósito.

Eso la hace triplemente útil sin coste extra: cierra el hueco del modelo, es la señal negativa que [#13](https://github.com/Joosle/Plantas/issues/13) pedía para calibrar (el diario registra cuándo *regaste*, no cuándo hacía *falta*), y es el "botón de reset" que ya proponía el informe agronómico.

## Considered Options

- **El silencio es evidencia sin caducidad.** Lo más coherente con "el diario es el estado del sistema" y no interrumpe nunca. Rechazado porque una racha de olvidos produce consejo seguro de sí mismo y equivocado: *"URGENTE, el aloe lleva 12 días sin agua"* cuando lo regaste el martes y se te olvidó anotarlo. Ese es el fallo que hace que la gente deje de creerse la app.
- **El silencio no significa nada.** Nunca miente, pero se calla justo cuando hace falta y convierte cada domingo en un interrogatorio previo: "¿regaste esta semana? ¿y la anterior?".
- **Silencio global, no por planta.** Un solo momento de fricción al volver tras una ausencia larga. Rechazado porque no distingue "no he anotado" de "no había nada que anotar", que es precisamente la distinción que el inventario de esta casa hace todo el rato.

## Consequences

- **Sólo cubre el riego.** El mecanismo necesita un intervalo predicho, y hoy sólo lo tiene el agua. La poda, el abonado o la protección no tienen cadencia hasta que [#3](https://github.com/Joosle/Plantas/issues/3) diga cuál es, así que su silencio se queda sin reconciliación. Queda en la niebla del mapa.
- El factor `2 ×` es un punto de partida, no un resultado: es material de [#13](https://github.com/Joosle/Plantas/issues/13), que es quien tiene los datos para juzgarlo.
- La reconciliación es una interrupción, y es la **única** que este diseño se permite. Cualquier otra pregunta proactiva de la app tiene que justificarse contra este listón.
