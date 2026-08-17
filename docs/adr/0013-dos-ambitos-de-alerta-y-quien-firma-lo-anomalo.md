# Dos ámbitos de alerta, y AEMET firma lo anómalo

[#19](https://github.com/Joosle/Plantas/issues/19) llegó pidiendo «la alerta de helada», en singular, apoyada sobre la rusticidad calculada que [#3](https://github.com/Joosle/Plantas/issues/3) dejó preparada. Al abrirlo resultó que ahí había **tres cosas distintas pegadas**, y que la del título no era ninguna de las otras dos.

## La rusticidad calculada no puede avisar de nada

La zona USDA es, por definición, la **media de las mínimas extremas anuales** sobre una serie larga. Es una estadística de treinta años: no sabe qué va a pasar mañana por la noche y nunca podrá disparar un aviso.

Lo que sí contesta es otra pregunta, la del alta de una planta: **¿puedo tener esto aquí?** Y la contesta con los dos números de #3 y no con uno, porque el mensaje está en el hueco entre ellos — para la serie de Madrid 1995–2024, media **−3,64 °C** (zona 9b) y mínima absoluta **−11,1 °C** (zona 8a), **7,5 °C de cola**. Por eso la app nunca dice «esta planta es rústica aquí»: dice *en un año normal aguanta; en el peor de los últimos treinta, no*. Y como ERA5 suaviza los extremos con sesgo optimista, la cola se declara con 2–3 °C de margen restado.

Vive en la ficha del sitio, no en el canal de avisos. Se recalcula **una vez al año**, enganchada al cierre de temporada que ya creó [#8](https://github.com/Joosle/Plantas/issues/8) — no porque el número se mueva (añadir un invierno a treinta lo mueve décimas) sino porque esa es la ocasión de enseñar **el invierno que acaba de pasar contra el histórico**: «esta vez bajó a −6,2 °C; el récord de treinta años es −11,1». Eso audita al sistema contra la realidad a la única cadencia a la que el dato cambia de verdad.

## Alerta de sitio y alerta de planta disparan por cosas distintas

- La **alerta de sitio** dispara por **anomalía** y no menciona ninguna planta: *esta noche va a hacer aquí un frío que no es normal*.
- La **alerta de planta** dispara por **cruce de umbral** de un ejemplar concreto, sea la noche típica o no. Al aloe le da igual la estadística: a −4 °C se muere aunque sea un invierno del montón.

No son la misma alerta con distinto texto. Tienen disparador distinto, fuente distinta y destinatario distinto, y confundirlas es lo que hacía que el ticket pareciera irresoluble.

## Quién decide qué es anómalo: AEMET, y no nosotros

La tentación era calcular un percentil sobre la serie histórica de Open-Meteo. Se rechaza: sería un **umbral inventado** en el sitio exacto donde [#17](https://github.com/Joosle/Plantas/issues/17) mandó penalizarlos.

Y no hace falta, porque [#12](https://github.com/Joosle/Plantas/issues/12) encontró que AEMET **ya ha hecho ese juicio para esta zona** y lo publica gratis: el aviso de la zona `681903` trae el umbral numérico **calibrado aquí** —37 °C de máximas, cuando el mismo día hay zonas con 34 y con 40—, la ventana `onset`/`expires` con hora, y el fichero de nivel **verde** que firma explícitamente «hoy no hay aviso». Eso último es lo que separa *no hay alerta* de *no me he podido descargar los avisos*, que es la escalera que este mapa lleva legislando desde [#4](https://github.com/Joosle/Plantas/issues/4).

La alerta de sitio **es el aviso oficial retransmitido**. El cálculo propio sobre el histórico queda como respaldo para cuando el paquete no baje, y cuando actúe **lo dice**.

Esto **enmienda a #4**, que dejaba a AEMET como respaldo: sube a disparador de una de las dos señales.

De los 13 fenómenos que emite AEMET se retransmiten **cuatro**: `BT` heladas, `AT` máximas, `VI` vientos y `NE` nevadas — quemar, cocer, tronchar y romper por peso. Es la regla de admisión de [#9](https://github.com/Joosle/Plantas/issues/9) aplicada a una fuente externa: entra si el motor lo lee o el plan puede mandar algo por ello. `PR` lluvias queda fuera del canal de avisos porque ya entra por otro sitio — es una **entrada del balance hídrico**, no una advertencia.

## El umbral de la planta es un hecho citable, leído por su borde malo

`minTemp` no es un campo nuevo: es un hecho más de la tabla de [#18](https://github.com/Joosle/Plantas/issues/18), colgado del taxón o del grupo funcional, con URL, frase literal e intervalo, y por tanto citable desde `citedFacts`.

Como todo en esa tabla, llega como intervalo. **Se dispara contra el borde cálido** — el que daña antes: si la fuente dice «daño entre 0 y −2 °C», el aviso salta a 0. La anchura no se promedia; **se gasta en seguridad**. Es la aplicación directa de la regla del ADR `0010` (rama de [#18](https://github.com/Joosle/Plantas/issues/18)) a un consumidor que, a diferencia del riego, es asimétrico: equivocarse por prudencia cuesta una noche de maceta dentro, equivocarse por optimismo cuesta la planta.

## El calor no puede copiar a la helada, y hay que decirlo

**No existe fuente citable de temperatura máxima tolerada por especie.** #3 barrió veinte fuentes y ninguna la tiene; la única que existe —el mapa de zonas de calor de la AHS— es de EE. UU. y va por días sobre 30 °C, no por planta. No hay `maxTemp` que citar, y fabricarlo sería inventar el número en el hueco.

Así que el calor **invierte el reparto**: el número lo pone **AEMET** (37 °C aquí, ya calibrado) y lo que aporta la planta no es un umbral sino la **vulnerabilidad**, que no cuelga del taxón sino del `placement` de [#16](https://github.com/Joosle/Plantas/issues/16) — maceta expuesta al sol, que en 7,5 L se recalienta como no lo hace el suelo. Es exactamente un consejo **protector** de #17: cuelga del grupo funcional, se puede dar sin identificar nada, y da una acción real y simétrica a la de la helada — mover el aloe a la sombra.

La asimetría queda escrita a propósito: **la helada la dispara un hecho de la planta contra pronóstico propio; el calor lo dispara AEMET y la planta sólo dice a quién le importa.**

## Sin acción no se calla: cambia de forma

Tres de las cuatro plantas de la casa están en el suelo y no se pueden proteger. Un aviso de helada sobre un olivo arraigado es, como dijo el propio ticket, ansiedad sin remedio.

Pero suprimirlo sería peor: es el fallo que [#8](https://github.com/Joosle/Plantas/issues/8) prohibió — nada desaparece en silencio. La salida es que **el aviso sin acción no pide actuar, pide mirar después**: «anoche cayó a −9 °C sobre el olivo; revísalo en unos días». Sólo interrumpe por Telegram el que tiene acción; el otro aparece y genera una `Observation`.

Eso convierte el aviso inútil en la única cosa útil que podía ser: **la señal de resultado que el sistema no tiene**. [#7](https://github.com/Joosle/Plantas/issues/7) ya había admitido que la muerte iba a ser la única señal de si el consejo servía; esto añade la anterior, la del daño observado con su causa fechada al lado.

## El texto lo escribe una plantilla, no el LLM

[#10](https://github.com/Joosle/Plantas/issues/10) separó los dos crons precisamente para que el diario —clima, balance, alertas— fuera barato y no frágil, **sin LLM**. La alerta se emite ahí, así que no puede llamarlo.

No hace falta: [#11](https://github.com/Joosle/Plantas/issues/11) ya decidió que la **redacción llana es un campo** de cada hecho de #18 y no una traducción en caliente, justamente para que el LLM no parafrasee un número que no calculó. La alerta lo consume tal cual — plantilla + número + frase llana + acción — con coste cero, latencia cero y ninguna alucinación en la única ruta que tiene que funcionar la noche de la helada. Es el **segundo cliente** de esa decisión de #11, que es la mejor prueba de que era buena.

## El microsito no corrige el pronóstico; el emplazamiento sí

Open-Meteo da un pronóstico para el punto de la casa, pero [#2](https://github.com/Joosle/Plantas/issues/2) estableció que el `Site` es el **microsito**: la terraza suroeste y el jardín en campo abierto no tienen la misma noche, y en una helada por radiación eso son varios grados.

Aun así **no lleva desfase por `Site`**. [#13](https://github.com/Joosle/Plantas/issues/13) fijó que un parámetro sólo es ajustable si existe un canal de evidencia que lo detecte, y aquí no hay termómetro: un desfase mal puesto no se manifestaría nunca — el modo de fallo silencioso que aquel ticket existe para cazar.

Lo que sí penaliza es la **maceta**, por mecánica conocida: el cepellón se congela entero mientras el suelo tampona, así que un ejemplar en contenedor resiste menos frío que el mismo en tierra. Cuelga del `placement`, misma forma que el calor. **La magnitud no se inventa aquí**: sale como ticket de investigación.

## Considered Options

- **Una sola alerta, con ámbito planta o sitio según el caso.** Es como lo planteaba el ticket. Rechazada porque los dos ámbitos no comparten disparador: uno mira la anomalía y el otro un umbral absoluto, y forzarlos a un mecanismo obliga a elegir cuál de los dos se pierde.
- **La rusticidad calculada calibra el umbral de aviso** (se avisa sólo de lo raro para este sitio). Elegante y es lo que hace AEMET. Rechazada como mecanismo único: vale para las plantas de suelo, que se eligieron porque el clima normal les vale, y falla justo en la única accionable — al aloe en maceta le da igual que la helada sea típica aquí.
- **Percentil propio sobre el histórico de Open-Meteo** para definir lo anómalo. Independiente de la disponibilidad de AEMET y con criterio de plantas, no de riesgo a personas. Rechazada porque el percentil habría que elegirlo a mano y defenderlo, teniendo al lado un umbral calibrado por zona y gratis.
- **Sólo AEMET, sin cálculo propio de respaldo.** Máxima simplicidad. Rechazada porque #12 no pudo comprobar en vivo ni un aviso `BT` **con umbral** —en agosto todos son verdes— así que la forma del aviso de mínimas está inferida por simetría, y apostarlo todo a una inferencia sin red no procede.
- **Retransmitir los 13 fenómenos de AEMET.** Fidelidad total a la fuente y cero criterio propio que defender. Rechazada: decenas de interrupciones al año por cosas sobre las que no se va a hacer nada, que es el ruido que este ticket existe para evitar.
- **Umbral con duración mínima** (N horas bajo cero, no rozar el número un instante). Más fiel a la biología, y el dato horario ya está en Open-Meteo. Rechazada porque «N horas» no lo dice ninguna fuente disponible: sería un umbral inventado encima de uno citado.
- **Umbral a mano por ejemplar**, porque un olivo adulto aguanta lo que uno recién plantado no. Rechazada: rompe el join por clave de [#5](https://github.com/Joosle/Plantas/issues/5) y #18, y convierte cada alta en un ejercicio de invención sin cita.
- **`maxTemp` empírico por planta, declarado como sin fuente.** Daría simetría formal con la helada. Rechazada por el mismo motivo, y agravado: sobre cuatro plantas de las que tres no admiten ninguna acción frente al calor.
- **Sin alerta de calor de planta**, delegando en el balance hídrico, que con ET0 alta manda regar antes solo. Honesta y barata. Rechazada porque deja fuera el único daño por calor que el balance no modela: el cepellón cocido en una maceta al sol, que no es sed sino temperatura de raíz.
- **Suprimir el aviso donde no hay acción posible.** El argumento literal del ticket contra la ansiedad sin remedio. Rechazada porque tira la observación post-evento, que es la única señal de resultado disponible.
- **Llamar al LLM también en el cron diario** para que el aviso hable natural. Rechazada: mete el componente caro y frágil dentro de la ruta crítica, y la noche de la helada es cuando más caro sale que falle.
- **Desfase térmico por `Site` escrito a mano.** Lo más fiel a cómo funciona de verdad una helada. Rechazada por la regla de #13: sin detector, no se ajusta.

## Consequences

`minTemp` entra en la tabla de #18 como un hecho más. **`maxTemp` no existe y su ausencia se declara** — no es una fila pendiente de rellenar, es una que ninguna fuente puede rellenar hoy.

`Alert` gana dos formas —la que pide acción y la que pide mirar después— que se distinguen por si hay acción posible, no por severidad. Sólo la primera interrumpe.

El respaldo de anomalía calculada es código que se ejecutará rara vez y por tanto se pudrirá sin que nadie lo note. Debe ejercitarse en cada ejecución (calcular siempre, usar sólo si falta AEMET) o no estará ahí el día que haga falta.

Queda abierta la magnitud de la penalización de la maceta frente al suelo. Hasta que haya fuente, el `placement` marca la vulnerabilidad pero no mueve el número.
