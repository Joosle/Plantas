# La unidad de cuidado es el hecho citable, no la ficha

El issue [#3](https://github.com/Joosle/Plantas/issues/3) concluyó que **ninguna fuente estructurada de cuidados es suficiente** y recomendó una tabla local curada con la disciplina «un campo = una fuente registrada». Al concretar esa forma aparece que la tabla no puede tener una fila por taxón, porque **dos campos de la misma planta resuelven a rangos distintos**: el `ks` del aloe sólo existe a género (WUCOLS no tiene ficha de *Aloe vera*, sólo `Aloe spp. & cvs.`) mientras que su rusticidad de PFAF sí está a especie. Una fila-por-taxón tendría que mentir sobre uno de los dos.

Hay además una exigencia aguas abajo: [#8](https://github.com/Joosle/Plantas/issues/8) decidió que el LLM cita `citedFacts` con ids **de un catálogo que el código valida**. Un hecho que se cita necesita id propio. Eso fuerza que el hecho sea la fila.

Por tanto la tabla es `CareFact`: **una fila = un hecho citable**, con su sujeto, su campo, su valor en escala nativa, su fuente, su frase literal y el rango del que salió. `CareProfile` (issue [#7](https://github.com/Joosle/Plantas/issues/7), ADR [0001](./0001-taxon-separado-de-careprofile.md)) deja de ser una tabla y pasa a ser una **vista resuelta** sobre estos hechos.

Cinco reglas la completan:

**El sujeto es polimórfico: un taxón o un grupo funcional.** [#17](https://github.com/Joosle/Plantas/issues/17) encontró que los consejos que *protegen* cuelgan del grupo funcional y no del taxón —se pueden dar sin identificar nada—, mientras que los que *optimizan* exigen el género. Eso no es una excepción a colocar aparte: es la forma real del dominio. La resolución es **por familia de campo**: los campos hídricos resuelven por taxón subiendo a género; los protectores resuelven por grupo funcional, directo y sin ascenso.

**La precedencia es una lista fija de fuentes escrita en el repo**, no una subasta de confianza. Primero gana la especificidad de rango; empatado el rango, gana la fuente que esté más arriba en la lista. Los perdedores no se borran, quedan visibles debajo del ganador. Esto es lo que impide que reimportar WUCOLS voltee en silencio un hecho respaldado por IFAPA — fuente oficial, española y vigente, frente a un WUCOLS supuesto para *Central Valley*, California. Un hecho escrito a mano **no es un tipo aparte** ni un override del usuario: es una fuente más, y está arriba en la lista cuando es oficial, no por haberla escrito tú. Eso preserva la frontera que fijó [#13](https://github.com/Joosle/Plantas/issues/13) entre *calibrar* e *impugnar un hecho citado*.

**La ausencia se guarda con su motivo.** Las categorías `U` (sin evaluar) y `NA` (región no apta) de WUCOLS **no crean hecho**: sin fila no hay valor y el motor se apaga para ese campo, que es exactamente lo que #3 exigió frente a caer a un defecto. Pero el import deja constancia de por qué falta, porque «nadie lo evaluó» y «el comité dice que aquí no» son cosas distintas que enseñarle a un humano. Se lee **sólo la región 2 (Central Valley)** como supuesto declarado; verificado que ninguna de las 4 plantas del inventario sale `NA` en esa región.

**La verificación de nombre es un invariante de la tabla, no un paso del importador.** PFAF devuelve **HTTP 200 con ficha equivocada** para `LatinName=Rosa` (*Rosa acicularis*, la rosa ártica, rústica hasta zona 2). Es el tercer caso del mismo modo de fallo en este mapa: `limonero` devolvió una araña en [#5](https://github.com/Joosle/Plantas/issues/5) y `El Casar` devolvió Badajoz en [#12](https://github.com/Joosle/Plantas/issues/12) — resolver por cadena falla en silencio devolviendo algo plausible. Y el join por clave **no lo elimina**: la cadena `GBIF → Wikidata P846 → P4301 → PFAF` termina en un `?LatinName=`, así que el último salto sigue siendo por cadena. Por eso `CareFact` exige `verifiedName` y `requestedName` no nulos y coincidentes: **saltarse la verificación no produce un dato malo, produce cero filas**, y el fallo cae en la misma maquinaria de ausencia con el motivo `name-mismatch`.

**El snapshot de la fuente nunca entra en git ni en D1.** WUCOLS es *All rights reserved*; copia local personal es defendible, redistribuir la base no. El repo es privado hoy, pero **la historia de git es permanente**: commitear los 4.103 taxones ahora los republicaría hacia atrás si el repo se abriera algún día. Lo que se versiona es el importador, el `sha256` del snapshot y las filas citadas —hoy ~6, no 4.103—, que es citar y no redistribuir. El snapshot se descarga a un directorio ignorado y muere ahí.

## Considered Options

- **Ficha por taxón con tabla de procedencia paralela.** La forma obvia, y la que sugiere leer «un campo = una fuente registrada» como columnas. Rechazada porque no representa que el `ks` del aloe viva a género y su rusticidad a especie sin partir la planta en dos filas que hay que volver a mezclar al leer, y porque el hecho no tiene id que el LLM pueda citar.
- **Ficha por taxón con JSON por celda.** Procedencia y valor viajan juntos sin tabla aparte. Rechazada porque D1 es SQLite: el JSON no se indexa ni se une por clave, que es justo lo que #5 se esforzó en conseguir, y sigue sin dar id citable.
- **Un solo hecho activo por `(taxón, campo)`, con `UPSERT` en el import y una tabla de excepciones para lo escrito a mano.** Más simple. Rechazada porque coloca el hecho del olivo respaldado por IFAPA en «excepciones» —degradando la mejor fuente del inventario a parche— y porque cada import destruye el dato anterior sin dejar rastro.
- **Gana la confianza más alta.** No hace falta mantener ninguna lista. Rechazada porque la confianza es un número que pones tú, y reimportar con otro criterio puede voltear el ganador sin que nadie lo pida — que es la clase de cambio silencioso que #8 prohibió.
- **Snapshot versionado en el repo privado.** Import reproducible sin red, y a prueba de que el endpoint interno de UC Davis desaparezca. Rechazada por la permanencia de la historia de git; el `sha256` da la reproducibilidad sin el pasivo.
- **Dos tablas, una escalar e importable y otra de guía en prosa.** Ninguna columna nula ni polimorfismo. Rechazada porque obliga al LLM de #8 a citar de dos catálogos y al código a validar dos espacios de id.

## Consequences

`CareProfile` desaparece como tabla y sobrevive como concepto: es el resultado de resolver hechos, no una fila que se lee. Toda lectura de cuidados pasa por un resolutor, nunca por un `SELECT` directo — enmienda a #7 y al ADR [0001](./0001-taxon-separado-de-careprofile.md), que lo describía como entidad.

La confianza de dos ejes que fijó #17 (`confianza_fuente` × `granularidad`) **queda acotada a los hechos no-hídricos**. En los hídricos no le queda función: no desempata (lo hace la lista de fuentes), no expresa precisión (lo hace la anchura del intervalo, ADR [0010](./0010-los-cuidados-se-leen-como-intervalo.md)) y no dispara la interfaz (lo hacen marcadores estructurales).

El vocabulario de grupos funcionales pasa a ser carga estructural —`subjectId = 'conifera'` tiene que significar algo— y **no está definido en ninguna parte**. #17 lo nombró sin enumerarlo; queda como ticket abierto.
