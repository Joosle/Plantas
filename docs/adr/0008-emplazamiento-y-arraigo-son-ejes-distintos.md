# El emplazamiento y el arraigo son ejes distintos, y ninguna planta de casa está arraigándose

Una `Plant` lleva dos cosas que se venían confundiendo en una. El **emplazamiento** —`suelo`, `bancal`, `maceta`, `jardinera`— dice dónde vive y decide **qué ecuación de balance corre**; es permanente. El **arraigo** dice que la planta todavía se está acomodando a un medio nuevo y decide **cuál es el depósito**; es transitorio, y ocurre igual en suelo que en maceta.

De las cuatro plantas de la casa, **ninguna está en arraigo**. Que tres de ellas no se rieguen no es un estado que compartan: es el resultado de tener el depósito grande.

## Lo que este ADR corrige

[#2](https://github.com/Joosle/Plantas/issues/2) describió el inventario como «3 de 4 arraigadas, en suelo y sin riego», y [#16](https://github.com/Joosle/Plantas/issues/16) heredó ese vocabulario y preguntó qué hace la app con ellas. La pregunta parecía grande porque el nombre estaba mal: *arraigada* se estaba usando para decir *lleva años aquí y no la riego*, que no es un estado del sistema sino una consecuencia aritmética.

[#13](https://github.com/Joosle/Plantas/issues/13) ya había puesto el número sin darse cuenta de que cerraba esto: con los valores por defecto de v1 el olivo en suelo tiene `RAW` ≈ 99 mm y pide agua a los **~99 días**, mientras el aloe en 7,5 L acotados se vacía en **poco más de un día**. Ese factor 100 no viene de ningún interruptor: viene del emplazamiento y del tamaño del depósito. **No hay nada que declarar y nada que apagar** — que es exactamente lo que el [ADR 0007](0007-lo-que-se-mide-no-se-ajusta.md) defendió al anular la regla de «no-regable por decreto» para las crasas.

Así que #16 no necesitaba un concepto nuevo para las tres plantas. Necesitaba **dejar de llamarlas arraigadas**.

## Emplazamiento: cuatro nombres, dos matemáticas

[#6](https://github.com/Joosle/Plantas/issues/6) ya tenía `maceta | suelo` como campo estático de la ficha de la planta. Se amplía a cuatro, pero lo que de verdad varía son dos cosas: si el depósito está acotado por paredes, y por dónde entra la lluvia.

| Emplazamiento | Depósito | La lluvia entra por | Balance |
| --- | --- | --- | --- |
| `suelo` | abierto | área radicular | mm (`TAW`/`RAW`, `Zr`) |
| `bancal` | abierto por abajo, drena al terreno | área radicular | mm |
| `maceta` | acotado | boca del tiesto | litros |
| `jardinera` | acotado y **compartido** | boca del tiesto | litros |

`bancal` cubre también el alcorque: acotado de lado pero sin fondo, así que se comporta como suelo. `jardinera` es geométricamente una maceta, y lo único que la separa es que tiene varios inquilinos — un depósito compartido que el balance de #6 no contempla, porque asume uno por planta. **Eso queda fuera de este ADR** y se ficha aparte; hoy no hay ninguna jardinera en la casa.

El emplazamiento vive en la `Plant`, no en el `Site`. Es coherente con #6, y con el hallazgo de #2 de que el `Site` es el **micrositio** —el rosal bajo alero frente al abeto a campo abierto, mismo jardín—: dos plantas del mismo sitio pueden estar una en tierra y otra en tiesto.

## Arraigo: el depósito es el cepellón

Mientras las raíces no han salido del cepellón que trajo la planta, **el depósito real es el cepellón**, no el medio nuevo. Ésa es la razón mecánica por la que una planta recién puesta se seca rodeada de tierra húmeda: el agua está, pero no donde llegan las raíces.

Un solo mecanismo sirve para los dos medios, que es lo que confirma que el arraigo es ortogonal al emplazamiento:

```
aloe recién trasplantado a un tiesto de 7,5 L
    depósito = cepellón 1,5 L   ->  ~1 día de autonomía
    (no 7,5 L                   ->  ~3 semanas)

olivo recién plantado en suelo
    depósito = cepellón 5 L     ->  ~2 días
    (no Zr 1 m                  ->  ~99 días)
```

No hay regla especial, no se apaga nada, no se sube ningún umbral: **es el mismo balance con otro número**. Y esto tapa un agujero declarado de `docs/research/modelo-agronomico-riego.md`, que decía que el modelo «no aplica durante ~4–8 semanas tras el trasplante» y pedía «consejo de establecimiento, no balance». El modelo sí aplica; lo que estaba mal era el volumen que se le daba.

### Arranca en el alta, y se rearma solo

El alta de una `Plant` hace una pregunta binaria **obligatoria**: *acabo de plantarla* / *ya llevaba tiempo*. Un toque, sin fecha exacta, sin tercer estado.

Lo obligatorio es lo que importa, y es la misma objeción que este mapa lleva cortando desde [#4](https://github.com/Joosle/Plantas/issues/4): un campo opcional vacío haría que *no lo dije* y *lleva veinte años aquí* se vieran igual, y el fallo caería del lado peor — plantas algo, lo registras dos días tarde sin decirlo, el motor le da régimen de planta establecida y no vuelve a mencionarla en tres meses. Se pide una fecha de plantación exacta **no**: para las heredadas nadie la tiene, y volvería a meter la ausencia interpretada por otra puerta.

Después del alta ya no hay que declarar nada. Cambiar el emplazamiento de una planta que ya existe *es* un trasplante, y [#9](https://github.com/Joosle/Plantas/issues/9) definió que editar la ficha genera un `LifecycleEvent` fechado: **el arraigo se rearma solo**, con el cepellón que corresponda.

### Termina preguntando, no venciendo

El `CareProfile` del taxón da una ventana (del orden de 4–8 semanas, según grupo). Al vencer, el arraigo **no se cierra solo**: el plan emite una pregunta fenológica —*«¿el rosal echa brotes nuevos?»*— y la respuesta la cierra o la extiende otro tanto. Mientras no contestes, sigue en arraigo y el plan lo dice.

Que cierre sola sería el fallo caro: una planta que **no** ha tirado pasaría a régimen de establecida —de regar cada dos días a no tocarla en tres meses— justo cuando más agua necesita, y nadie habría preguntado nada.

**No hay curva de interpolación** entre el cepellón y el medio, y se descarta por el mismo motivo por el que [#20](https://github.com/Joosle/Plantas/issues/20) descartó la del acolchado: sería precisión inventada sobre algo que no se mide. El salto de depósito ocurre cuando tú confirmas un hecho que has visto, no cuando un calendario lo decreta.

Es además el patrón de [#17](https://github.com/Joosle/Plantas/issues/17) exactamente: aquí el sensor no es el clima, **es el diario**, y la tarea se emite como pregunta, no como orden.

## Qué hacen entonces las tres plantas en el plan semanal

Nada nuevo, y ésa es la respuesta. Salen como línea del **plan vacío razonado** del [ADR 0006](0006-el-llm-supervisa-y-el-codigo-propone.md) —*«olivo: 62 mm de lluvia en 30 días, sin acción»*— y sólo generan `PlanItem` cuando #17 tiene algo fenológico que preguntar. Una semana en la que tres de cuatro plantas no tienen nada que hacer no es una lista con huecos ni un caso borde: es la pantalla principal, cerca de la mitad de las semanas del año.

## Considered Options

- **`arraigada` como booleano en la ficha, que el usuario marca y desmarca.** Lo más explícito y lo más simple de implementar. Rechazado porque reintroduce con otro nombre el «no-regable por decreto» que el ADR 0007 acaba de anular: un interruptor que impide al motor pedir agua es un fallo, no una precaución, y si el olivo la necesita un agosto duro el motor tiene que poder decirlo.
- **Derivar el arraigo de una fecha de plantación más la ventana del taxón.** Fue la propuesta inicial de esta sesión. Rechazada al ver que el arraigo no es cuestión de calendario sino de si las raíces han colonizado el medio, y que la fecha exacta no existe para las plantas heredadas.
- **No modelar el arraigo en absoluto**, dejando que emerja de un `Zr` pequeño declarado por el usuario. Da la frecuencia aproximadamente correcta y no añade ningún concepto. Rechazada porque no captura el fallo real —el agua está fuera del alcance de la raíz, no es que haya poca— y porque `Zr` es un parámetro *que se mide* ([ADR 0007](0007-lo-que-se-mide-no-se-ajusta.md)): usarlo como mando de arraigo lo convierte en un ajuste encubierto.
- **Que el motor calle durante el arraigo** y emita un ítem fijo sin números («riega cada 2–3 días»), como recomienda literalmente la investigación de #6. Honesto, pero el consejo no responde al clima: llueven 40 mm y sigue diciendo lo mismo. Con el cepellón como depósito se consigue lo mismo *y* reaccionando al tiempo real.
- **Subir sólo el umbral de agotamiento durante el arraigo** (regar al 20 % en vez de al 50 %). Un solo parámetro tocado. Rechazada por insuficiente: con `Zr` = 1 m el olivo recién plantado seguiría avisando a los ~40 días, cuando lleva un mes muerto.
- **Sólo `suelo | contenedor`**, dejando jardinera y bancal como parámetros de esos dos. Cero código nuevo. Rechazada porque la jardinera con tres plantas quedaría sin forma de decir que comparten agua, y el motor las contaría tres veces.
- **Subir el emplazamiento al `Site`**, de modo que la jardinera *sea* un `Site` con volumen. Resuelve el depósito compartido con elegancia, pero contradice a #6 y al hallazgo de #2 de que el `Site` es el micrositio, no el recipiente.

## Consequences

- **Enmienda a [#7](https://github.com/Joosle/Plantas/issues/7)**: la `Plant` gana `placement` (enum de cuatro) y `rootBallVolume`; el arraigo es **derivado**, no un campo — hay `LifecycleEvent` de plantación sin pregunta de cierre respondida. El `CareProfile` gana la ventana de arraigo.
- **Enmienda a [#2](https://github.com/Joosle/Plantas/issues/2)**: `docs/inventario-plantas.md` deja de llamar «arraigadas» a las tres plantas de suelo. No lo están: están **establecidas**, que es distinto y no es un estado del sistema.
- **Se paga una deuda de `modelo-agronomico-riego.md`**: la excepción «el modelo no aplica durante 4–8 semanas» desaparece. El modelo aplica siempre; lo que cambia es el volumen del depósito.
- **La pregunta de cierre del arraigo es una tarea fenológica más**, así que entra por el carril de #17 y por la regla de admisión del vocabulario del [ADR 0003](0003-regla-de-admision-del-vocabulario-del-diario.md) — no hace falta verbo nuevo, porque lo que se registra es una `Observation`.
- **Aparece un ticket nuevo**: el depósito compartido de la `jardinera`. El balance de #6 asume un depósito por planta, y N plantas en una jardinera comparten uno solo mientras cada una transpira con su `KL` y su dosel. No urge — hoy no hay ninguna.
- **Desbloquea [#11](https://github.com/Joosle/Plantas/issues/11)**: el prototipo de la pantalla del plan semanal ya tiene fijado el caso que domina esta casa.
- El alta gana un paso obligatorio. Es el único punto de fricción que este ADR añade, y se acepta porque es un toque una vez por planta, con cuatro plantas.
