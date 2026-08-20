# El depósito acotado es una entidad con inquilinos, y la maceta es el caso de un solo inquilino

El balance de [#6](https://github.com/Joosle/Plantas/issues/6) asume **un depósito por planta**. La jardinera lo rompe: es un depósito acotado que varias plantas comparten. La corrección no es añadir un caso especial para la jardinera, sino **darle identidad al depósito**: nace `Container` (recipiente), con litros y área de boca, al que apuntan de 1 a n `Plant`.

La maceta es ese mismo `Container` con **un solo inquilino**. Y en cuanto el depósito tiene identidad propia, la pregunta de «¿y varias plantas en una maceta grande?» —que [#29](https://github.com/Joosle/Plantas/issues/29) fichaba como caso aparte— **deja de existir**: es el mismo `Container` con n=3. El nombre del recipiente no decidía nada; el número de inquilinos, tampoco.

## El emplazamiento baja de cuatro valores a dos

El [ADR 0008](0008-emplazamiento-y-arraigo-son-ejes-distintos.md) fijó cuatro emplazamientos y, en la misma tabla, admitió que sólo eran **dos matemáticas**. `bancal` comparte con `suelo` las tres columnas —depósito abierto, lluvia por el área radicular, balance en mm— y `jardinera` comparte con `maceta` las tres suyas. Cuatro nombres para dos ecuaciones son dos ramas muertas.

| Emplazamiento | Depósito | La lluvia entra por | Balance |
| --- | --- | --- | --- |
| `suelo` | abierto | área radicular | mm (`TAW`/`RAW`, `Zr`) |
| `container` | acotado, 1..n inquilinos | boca del recipiente | litros |

`bancal`, `alcorque`, `maceta` y `jardinera` no desaparecen del mundo: **bajan de rango**. Siguen siendo lo que el usuario escribe y lo que la interfaz muestra, pero dejan de ser ramas del motor porque no cambian ninguna ecuación. Es la misma regla que este mapa lleva aplicando desde [#13](https://github.com/Joosle/Plantas/issues/13): un valor existe si algo lo lee.

Que `bancal` sobreviviera «por si el drenaje lateral se diferencia más adelante» se rechaza por el motivo del [ADR 0007](0007-lo-que-se-mide-no-se-ajusta.md): sería una rama que hoy no hace nada, esperando un número que nadie mide.

## Un depósito, varios inquilinos: la aritmética

El esqueleto en litros de #6 no cambia. Lo que cambia es de quién es cada término:

```
por Container, cada día:

  salida  = Σ ET_i      cada inquilino con SU KL y SU área de dosel
  entrada = P · A_boca  la lluvia entra UNA vez, por la boca

  depósito(t+1) = min(V, depósito(t) + entrada − salida)
```

La lluvia entra una sola vez porque hay una sola boca. La transpiración se suma porque hay varias plantas tirando del mismo agua. **No se reparte el balance**: hay un depósito y cada planta lee de él.

Se descarta la aproximación barata de dar `V/n` litros a cada planta y dejar n balances independientes. Da una frecuencia aproximadamente correcta —riegas cuando la más sedienta agota su parte— pero miente en la física: dentro de un tiesto **no hay pared** entre los inquilinos, y las raíces de la sedienta tiran del volumen entero. Además obliga al usuario a dividir litros a mano y hace que el plan emita n ítems para un solo gesto.

### Manda el más sediento

Un depósito tiene un umbral, pero cada inquilino trae su propia fracción de agotamiento tolerable de su especie. **Dispara el primero que llega al suyo** — el mínimo de los `p`.

```
jardinera 30 L: helecho (p=0,3) + aloe (p=0,7)

  el helecho pide a los  4 días
  el aloe    pide a los 19 días

  dispara: día 4
```

El fallo se elige del lado húmedo a propósito: **secar es irreversible y encharcar es lento**. Un aloe algo más húmedo de lo que le gusta sigue vivo el mes que viene; un helecho seco, no.

### La incompatibilidad se dice, no se bloquea

Cuando la brecha entre inquilinos es grande, el supervisor LLM del [ADR 0006](0006-el-llm-supervisa-y-el-codigo-propone.md) lo menciona en prosa, junto al ítem: *«la cadencia la manda el helecho; el aloe va a estar más húmedo de lo que le conviene»*.

No lleva índice numérico de compatibilidad: sería precisión inventada sobre algo que no se mide (ADR 0007). Y **no bloquea el alta**, porque cuando das de alta la planta ya está plantada en ese tiesto — un portero que te niega registrar un hecho consumado no previene nada, sólo impide describir la casa como es. Es el patrón de [#17](https://github.com/Joosle/Plantas/issues/17) y [#26](https://github.com/Joosle/Plantas/issues/26): se informa, no se ordena.

## El PlanItem apunta al recipiente; el diario, a la planta

Aquí es donde #29 encontró la costura: **el `PlanItem` es por planta y el riego es por recipiente**. [#9](https://github.com/Joosle/Plantas/issues/9) ya había resuelto la mitad —el lote es de interfaz: N entradas con `batchId`, y explícitamente **no** guardado contra el `Site`, porque mentiría al cambiar los inquilinos—. Queda la otra mitad, y la respuesta es **un ítem, no N**.

El target del `PlanItem` se generaliza a `Plant | Container`, **según el verbo**:

| Verbo | Target | Por qué |
| --- | --- | --- |
| riego | `Container` | el gesto es llenar el depósito |
| poda, observación, abono sólido | `Plant` | el gesto es sobre un ejemplar concreto |

No es un concepto nuevo en el modelo: la `Alert` ya se define «sobre una `Plant` o sobre un `Site` entero», así que un aviso con target que no es una planta ya existía.

Tres ítems que **sólo pueden completarse juntos son un ítem disfrazado**. El [ADR 0002](0002-plan-inmutable-y-diario-derivado-de-la-checklist.md) deriva el estado del `PlanItem` del diario que lo responde; como regar la jardinera genera las tres `CareAction` a la vez, los tres ítems estarían siempre en el mismo estado. Y rompería el contador por verbo que fijó [#11](https://github.com/Joosle/Plantas/issues/11): diría «Riega · 4» cuando son dos gestos.

```
WeeklyPlan
  Riega · 2
    ├─ jardinera del porche   ← 1 ítem, 3 plantas
    └─ aloe (maceta 7,5 L)    ← 1 ítem, 1 planta
  Poda · 1
    └─ helecho                ← planta, no recipiente

al responder "regada la jardinera":
  CareAction(helecho, riego, batchId=b7)
  CareAction(hosta,   riego, batchId=b7)
  CareAction(sedum,   riego, batchId=b7)
```

**El diario no sube al recipiente.** Se descarta colgar la `CareAction` del `Container` aunque sea más corto de escribir: es el mismo fallo que #9 rechazó para el `Site`. Saca el helecho a una maceta propia y su historial de riego se habría quedado en la jardinera — el motor no sabría cuándo se regó por última vez justo cuando más lo necesita.

## El arraigo saca a su planta del depósito común

El ADR 0008 fijó que **durante el arraigo el depósito es el cepellón**, no el medio nuevo. Dentro de un recipiente compartido eso produce dos depósitos anidados: el cepellón se seca en un día mientras los 30 L compartidos aguantan una semana.

Aplicar «manda el más sediento» a ciegas aquí sería el fallo caro: el cepellón es siempre el umbral más bajo, así que la jardinera entera se regaría a diario durante las seis semanas del arraigo, **encharcando a los veteranos**.

Así que el inquilino en arraigo **no entra en la `Σ ET` del recipiente y no lo dispara**. Corre su propio balance sobre el cepellón y emite su propio `PlanItem`, porque el gesto también es distinto: mojar al pie de la recién plantada no es llenar la jardinera. Se reincorpora al depósito común cuando la pregunta fenológica del ADR 0008 cierra el arraigo — y entonces el plan baja de dos ítems a uno.

Esto no es una excepción a la regla: es la regla del ADR 0008 leída literalmente. El arraigo dice *cuál es el depósito*, y mientras dure, el depósito de esa planta no es el recipiente.

## El Site cuelga del Container

La lluvia entra por la boca, y cuánta llueve lo dice el `Site`. Si cada inquilino declarase el suyo, el balance del recipiente sería ambiguo: no habría de cuál leer.

Un recipiente está en un sitio, y sus inquilinos están donde el recipiente. **El `Site` es del `Container`** cuando lo hay; una `Plant` en suelo lo sigue llevando directamente, sin cambio respecto a [#7](https://github.com/Joosle/Plantas/issues/7).

No contradice el hallazgo de [#2](https://github.com/Joosle/Plantas/issues/2) de que el `Site` es el **micrositio**: tres plantas a 40 cm no están en micrositios distintos. Y lo que sí varía dentro de un tiesto —la exposición solar— ya era de la `Plant` y no del `Site`, exactamente por este motivo.

```
Site(porche este)
  └─ Container(jardinera, 30 L, boca 0,25 m²)
       ├─ hosta    exposición: sombra
       ├─ helecho  exposición: sombra
       └─ sedum    exposición: sol
```

## Entra en v1

Hoy no hay ninguna jardinera en la casa: el inventario de #2 son cuatro plantas, tres en suelo y el aloe en maceta individual. Aun así `Container` se construye en v1, y **no es alcance añadido**: los litros y el área de boca ya tenían que vivir en algún sitio, y `Container` sólo los mueve de la `Plant` a una tabla propia. Con n=1 el comportamiento observable de la casa de hoy es idéntico al de no tenerlo.

```
v1, casa real:
  olivo, abeto, rosal  →  suelo
  aloe                 →  Container(7,5 L, n=1)
```

Lo que se ahorra es la migración: el día que compres una jardinera es un `INSERT`, no mover datos y reescribir el bucle del balance y el target del `PlanItem`. Y ese día no lo eliges tú.

## Considered Options

- **`V/n` litros por planta, sin modelar nada.** Cero entidades y cero código. Rechazada porque miente en la física —no hay pared entre los inquilinos—, obliga al usuario a dividir litros a mano, y emite n ítems para un gesto.
- **La jardinera *es* un `Site` con volumen.** Resuelve el depósito compartido con elegancia. Rechazada ya en el ADR 0008 por chocar con el hallazgo de #2 de que el `Site` es el micrositio, no el recipiente. `Container` la sustituye sin ese choque: el recipiente vive *en* un sitio en vez de ser uno.
- **`Container` sólo para la jardinera**, dejando la maceta como campo de la `Plant`. Menos cambio sobre el ADR 0008. Rechazada porque deja dos caminos distintos para la misma ecuación en litros, y el motor tendría que saber cuál mirar.
- **Umbral propio del recipiente, declarado a mano.** Control explícito sobre cuándo riega. Rechazada porque reintroduce un parámetro que el usuario ajusta a ojo, que es lo que el ADR 0007 anuló.
- **Callar sobre la incompatibilidad**: juntar un aloe y un helecho es problema del jardinero. Más simple. Rechazada porque el daño ocurre en silencio y la app tiene el dato para decirlo.
- **Bloquear el alta de un inquilino incompatible.** Previene el daño antes. Rechazada porque la planta ya está en el tiesto cuando la registras: el portero no previene, sólo impide describir la casa como es.
- **N `PlanItem` agrupados en la interfaz.** No toca `CONTEXT.md`. Rechazada porque son n ítems que nunca pueden estar en estados distintos, y rompen el contador por verbo de #11.
- **Subir también la `CareAction` al `Container`.** Más limpio de escribir. Rechazada por lo que #9 ya rechazó para el `Site`: al cambiar los inquilinos, la planta que sale pierde su historial de riego.
- **Ignorar el arraigo dentro de un recipiente compartido.** Simplifica el motor. Rechazada porque reintroduce el fallo que el ADR 0008 identificó: el agua está en la jardinera, no donde llegan las raíces.

## Consequences

- Nace `Container` (recipiente) en el modelo de dominio, con `Site`, litros y área de boca. La `Plant` deja de llevar litros.
- El emplazamiento pasa de cuatro valores a dos: `suelo` | `container`. La tabla del ADR 0008 queda enmendada; `bancal`, `alcorque`, `maceta` y `jardinera` sobreviven como vocabulario de interfaz.
- El bucle del balance itera **por `Container`**, no por `Plant`, en el modo litros. El modo mm sigue iterando por `Plant`.
- El `PlanItem` cambia de forma: su target es `Plant | Container`. `CONTEXT.md` deja de decir «una tarea sobre una `Plant`».
- El motor necesita saber qué inquilinos están en arraigo para excluirlos de la `Σ ET`, lo que acopla este balance al mecanismo del ADR 0008.
- La casa de hoy no cambia de comportamiento: cuatro plantas, un `Container` de n=1.
