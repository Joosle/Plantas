# El grupo funcional se ve, y su consejo se escribe para el peor miembro

Un `FunctionalGroup` es un conjunto de plantas definido por un **rasgo observable sin identificar la planta**, y su razón de existir es sostener consejos protectores que no se pueden escribir a nivel de taxón. El [ADR 0009](0009-la-unidad-de-cuidado-es-el-hecho-citable.md) hizo del grupo funcional el sujeto alternativo de un `CareFact` y dejó escrito que su vocabulario **no estaba definido en ninguna parte**. Este ADR lo define.

El catálogo nace con **un** grupo, `conifera`, y de las cuatro plantas de la casa **sólo el abeto sin identificar tiene grupo**. Eso no es un mecanismo infrautilizado: es el mecanismo funcionando. El grupo funcional existe para la planta que no sabes qué es, y hoy hay exactamente una.

## La regla de admisión: el hecho crea el grupo

Se hereda la disciplina del [ADR 0003](0003-regla-de-admision-del-vocabulario-del-diario.md) —no se fija una lista, se fija la condición para entrar en ella— y se hace verificable con tres requisitos **simultáneos**:

1. **Existe al menos un `CareFact` citado cuyo `subjectId` es el grupo.** El grupo no se crea vacío y se puebla después: **lo crea el hecho**. Es integridad referencial, no convención — un grupo huérfano no puede llegar a existir.
2. **El grupo guarda su criterio observable redactado como pregunta en llano.** Sin la pregunta el grupo no es asignable, y un grupo no asignable no sirve para nada.
3. **El hecho es protector, no de optimización.** Un consejo positivo —«pinza las velas»— pertenece al género, no al grupo ([#17](https://github.com/Joosle/Plantas/issues/17)).

Aplicada al material citado de #17, la regla admite hoy **un solo grupo**. Los umbrales del aloe (`−2 ºC`, despunte del escapo floral, desahijado a 20 cm), los del olivo (`−10/−12 ºC` en reposo) y los del rosal (heladas tardías sobre brotes tiernos) están **todos citados a nivel de taxón**: son hechos con sujeto taxón y no fundan ningún grupo. Los cinco consejos de la conífera son los únicos que cuelgan de algo que no es un taxón.

**`crasa`/CAM queda expresamente descartada.** Es el grupo que todo el mundo esperaría ver, y no tiene ni un hecho citado detrás: lo que se sabe del aloe se sabe de *Aloe vera*. El único intento de tratar a las crasas como clase —desactivar el balance hídrico para ellas— ya lo anuló el [ADR 0007](0007-lo-que-se-mide-no-se-ajusta.md).

## El criterio es lo que se ve, y por eso el consejo se escribe para el peor miembro

El consejo de #17 dice *«no cortar más allá de donde hay acículas verdes — **salvo el tejo**»*, y ese *salvo* obliga a elegir qué define la pertenencia. Hay tres candidatos y sólo uno sobrevive:

| Criterio | Qué pasa con el tejo | Por qué se descarta |
| --- | --- | --- |
| **El clado** (Pinophyta) | dentro, y el consejo es falso para él | dice algo que no es cierto de un miembro |
| **El rasgo** (`no-rebrota-de-madera-vieja`) | fuera, y el consejo es exacto | **el rasgo no se ve**, así que el grupo deja de ser asignable sin identificar — que era su única razón de ser |
| **Lo observable** (aguja o escama, y piñas) | dentro | ✅ |

Gana lo observable, y con ello una regla que ordena todo lo demás:

> **Un consejo de grupo funcional tiene que ser válido para el peor miembro del grupo.** El grupo se define por lo que se ve; el consejo se redacta para el miembro más frágil.

Con esa regla el tejo deja de ser un problema: no es el miembro para quien el consejo es *peligroso*, es el miembro para quien es *innecesario*. Podar conservador un tejo no le hace nada.

Y explica **por qué cuatro de los cinco consejos de #17 son negativos**, que hasta ahora era una observación sin causa: un consejo negativo se puede hacer seguro para todo el grupo simplemente estrechándolo, mientras que uno positivo no tiene versión conservadora — o es el método correcto para tu género o es dañino. La línea `seguridad` / `optimización` que #17 encontró mirando el material queda **deducida** en vez de observada.

El precio, aceptado a conciencia: **el grupo funcional nunca da el mejor consejo, sólo el consejo seguro.** La app renuncia por diseño a afinar mientras no identifiques.

## La pertenencia se declara, no se deriva

**Derivar el grupo del taxón queda descartado**, y no por incómodo: una tabla `familia → grupo` **se apaga exactamente donde hacía falta**. Sin taxón no hay grupo, y la planta sin identificar es la razón de ser del mecanismo. Un mecanismo que sólo funciona cuando ya no lo necesitas no es un mecanismo.

Tampoco existe **como comprobación**: si una tabla derivada discrepara de lo declarado, no habría dueño del conflicto, y el ADR 0009 ya rechazó el desempate automático en favor de una lista escrita.

Lo que hay es lo que ya usa el resto del mapa: **el LLM propone desde la foto, el usuario aplica** ([ADR 0006](0006-el-llm-supervisa-y-el-codigo-propone.md)). No incumple la prohibición de [#5](https://github.com/Joosle/Plantas/issues/5) de que el LLM identifique por foto, y la diferencia es exactamente la que fija el criterio observable: identificar un taxón exige una **clave verificable** (`gbif.id`) contra la que un LLM alucinado produce un dato falso e incontrastable; un grupo funcional **no tiene clave**, su criterio es visual y grueso, y su respuesta la confirma un humano antes de aplicarse. Es la diferencia entre *«esto es un abeto»* y *«esto tiene pinta de conífera, ¿sí?»*.

**El usuario declara pertenencia; nunca vocabulario.** El catálogo es dato del repo, como la lista de precedencia de fuentes del ADR 0009, y por el mismo motivo: que ampliarlo deje rastro en la historia de git y exija una cita. Si aparece una planta que evidentemente necesita un grupo que no existe, se queda `sin grupo` hasta que alguien haga la investigación — la misma disciplina que hace que el ADR 0009 prefiera **cero filas** a una fila inventada.

## Tres estados, porque el usuario no es experto

El [ADR 0008](0008-emplazamiento-y-arraigo-son-ejes-distintos.md) hizo binaria y obligatoria la pregunta de arraigo, y funcionó porque *acabo de plantarla* / *ya llevaba tiempo* es algo que el dueño siempre sabe. Esto no lo es: ante una planta heredada, un no-experto puede legítimamente no saberlo, y forzar un sí/no fabrica un dato inventado en la entrada que el motor más se cree.

| Estado | Qué hace el motor |
| --- | --- |
| **en el grupo** | aplica sus consejos protectores |
| **`sin grupo`** | no aplica ninguno, **y lo dice**: *«no sé de qué tipo es esta planta, así que no puedo darte ni los consejos básicos»*, con la acción para resolverlo delante |
| **`no es`** (explícito) | no aplica ninguno, y no vuelve a preguntar |

`sin grupo` y `no es` son estados distintos por el mismo motivo por el que el ADR 0009 guarda la ausencia **con su motivo**. Y el silencio no vale aquí aunque #17 lo consagrara como éxito para la conífera arraigada: **aquel silencio estaba razonado y éste no**. «No hay nada que hacer esta semana» y «no sé qué es esto» se ven igual en pantalla y son opuestos.

**El LLM se calla cuando duda.** Si no está seguro no precarga su mejor apuesta: deja la línea en blanco marcada como *no lo sé*. Un no-experto ante una casilla ya marcada la confirma —es la respuesta cómoda— y una precarga insegura se convierte en dato duro que nadie ha mirado. Y aquí **el falso positivo no es gratis**: los consejos protectores de conífera aplicados a un rosal le suprimirían la poda fuerte de enero, que el rosal sí necesita. La regla del peor miembro protege **dentro** del grupo, no contra meter la planta en el grupo equivocado.

## Cardinalidad 0..N, y la contradicción es un detector

Una `Plant` lleva un **conjunto** de grupos. No hace falta regla de precedencia entre ellos, y hay demostración en vez de promesa: los consejos de grupo son protectores y, por la regla del peor miembro, se redactan como **restricciones**. Dos restricciones no se contradicen — **se componen por unión**. Cumplir las dos siempre es posible, y hacer menos es siempre la salida segura.

> **Si dos grupos producen consejos contradictorios, el fallo no está en la precedencia: está en la admisión.** Significa que se coló un consejo de optimización, y ésos exigen el género. La contradicción es el detector de que la regla de admisión se saltó, no un caso a resolver.

El conjunto no está por si una planta es dos cosas a la vez con las plantas de hoy —no lo es ninguna—, sino porque **el eje transversal es el tipo de grupo más probable que queda por llegar**: un rasgo que cruza taxones es precisamente el que menos se puede escribir a nivel de taxón. El candidato claro es `espinosa`: observable (pincha, se ve), con consejo protector que valdría igual para rosal, aloe, enebro y buganvilla, y sin estar contenido en nada —hay espinosas perennes y caducas, coníferas y angiospermas—. Un acebo es perenne y espinoso; un rosal trepador es espinoso y trepador; un enebro sería `conifera` **y** `espinosa`.

Y hay una segunda razón, propia del ADR 0009: los hechos protectores resuelven *«directo y sin ascenso»*, así que **no hay herencia de grupo a grupo**. Si algún día se admite un grupo más ancho, la conífera no hereda su consejo — tiene que estar en los dos conjuntos, o el consejo no le llega.

## El grupo persiste a la identificación; lo que cambia es quién gana

Identificar no borra el grupo. Un tejo sigue teniendo acículas y piñas, así que sigue siendo `conifera`: el criterio observable no ha cambiado. Lo que cambia es la precedencia.

> **Un `CareFact` protector citado a nivel de taxón vence al del grupo.** Los dos quedan visibles, el perdedor debajo.

No es regla nueva: es la precedencia del ADR 0009 —*primero gana la especificidad de rango*— extendida al sujeto polimórfico, con el grupo como el rango menos específico de todos, por debajo del género. Y encaja con *«directo y sin ascenso»*: el grupo no asciende a nada, simplemente pierde cuando hay algo más específico citado.

**Para levantar un consejo protector se exige `confianza_fuente: alta`.** La asimetría es deliberada: poner una restricción es barato y quitarla no.

Tres consecuencias:

1. **El *«salvo el tejo»* se arregla con una cita, no con una excepción en el código.** Deja de ser nota al pie del consejo y pasa a ser un `CareFact` de `Taxus` que vence al de `conifera`, mientras la regla del peor miembro sigue protegiendo a los demás.
2. **Identificar tiene premio medible.** El grupo da el consejo seguro; el taxón lo afina o lo levanta.
3. **Nada se pierde por identificar mal.** El grupo lo declaró el usuario mirando la planta, así que sigue de suelo si Pl@ntNet se equivoca. El consejo seguro nunca desaparece por un fallo de identificación.

## El alta pregunta una vez, no N veces

Cada grupo trae su pregunta observable, y el catálogo crecerá. El alta enseña **un bloque** con lo que el LLM ve —*«hojas de aguja o escama y piñas: sí»*, *«pincha al tocarla: no»*— y el usuario lo acepta entero de un toque o corrige la línea que esté mal. Lo que el LLM no tenga claro sale en blanco y aceptar el bloque lo deja en `sin grupo`, no en «no».

Así **el coste del alta no crece con N**: crece el bloque que se lee, no las decisiones que se toman. Es la forma que ya ganó en el [ADR 0011](0011-plan-agrupado-por-accion-con-las-decisiones-delante.md) —decisiones delante, lista detrás— y respeta el ADR 0006: la aceptación es explícita del usuario, aunque sea un toque.

**La pregunta nunca está en botánico.** `conifera` es el `subjectId` interno; al usuario se le enseña el criterio literal, *«¿tiene las hojas en forma de aguja o de escama, y da piñas?»*, con la foto de su planta al lado. Pedirle el nombre del clado sería pedirle justo lo que no sabe.

## Consequences

- **Se paga la deuda que el ADR 0009 dejó abierta**: `subjectId = 'conifera'` ya significa algo.
- **El estado «sin identificar» del [ADR 0001](0001-taxon-separado-de-careprofile.md) gana contenido propio.** El abeto recibe hoy los cinco consejos protectores de #17 sin que nadie sepa su género — deja de ser un hueco a la espera.
- **Tres de las cuatro plantas salen con el conjunto vacío**, y es correcto. El día que se admita `espinosa` serían tres de cuatro **con** grupo.
- **`crasa` queda descartada** hasta que aparezca un hecho citado que cuelgue del rasgo y no de *Aloe vera*.
- **Queda fuera de este ADR** la investigación de `espinosa`: qué consejo protector citado la sostiene y con qué fuente. Se ficha aparte.
- **La `Plant` gana un campo de conjunto** (`functionalGroups`, 0..N) y el `FunctionalGroup` gana su pregunta observable como dato. Ninguno de los dos se deriva del `Taxon`.
