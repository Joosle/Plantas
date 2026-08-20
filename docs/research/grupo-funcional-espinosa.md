# ¿Existe una cita que funde el grupo funcional `espinosa`?

Resuelve el ticket [Espinosa: existe una cita que funde el primer eje transversal del catálogo](https://github.com/Joosle/Plantas/issues/37),
hijo del mapa [Plantas: de idea a spec construible](https://github.com/Joosle/Plantas/issues/1).

Investigación realizada el 2026-08-20. La regla de admisión que se aplica aquí es la del
ADR 0018:
tres requisitos **simultáneos** (hecho citado con `subjectId` = grupo; criterio observable
redactado como pregunta en llano; hecho **protector**, no de optimización), más la **regla del
peor miembro**.

---

## Veredicto, primero

> **`espinosa` no se admite hoy — y el motivo no es el que se esperaba.**

El ticket anticipaba el modo de fallo de `crasa`: que todo el material protector estuviera
citado a nivel de especie y no de rasgo. **No es lo que ha pasado.** La cita de rasgo
**existe**, es de una fuente que el proyecto ya acepta, es protectora y es una restricción.
Los requisitos 1 y 3 tienen material que los satisface *en forma*.

Lo que bloquea la admisión es una pregunta previa que el ADR 0018 no contempla:

> **Todo el material protector que cuelga del rasgo «pincha» protege al humano, no a la planta.**
> No se ha encontrado ni un solo hecho citado en el que la planta espinosa sea el sujeto
> *protegido*.

El ADR 0018 escribió la regla de admisión —y en particular la regla del peor miembro—
presuponiendo que el sujeto protegido es la planta. Con un hecho de seguridad de la persona
esa regla **no se aplica tal cual: se invierte** (§4). Hasta que no se decida si un `CareFact`
admite hechos de sujeto protegido humano, admitir `espinosa` sería meter en el catálogo un
hecho de una clase que el modelo no tiene definida.

**Esto desprende un ticket** (§6). No se resuelve aquí.

---

## 1. Las citas

### 1.1 Nivel RASGO — el material que sí fundaría el grupo

**Fuente (B):** Royal Horticultural Society, *How to garden safely*.
[rhs.org.uk/garden-jobs/how-to-garden-safely](https://www.rhs.org.uk/garden-jobs/how-to-garden-safely)

La cita más fuerte encontrada, y la única puramente de rasgo — no nombra ni una especie:

> *"When undertaking major work on plants with sharp protrusions, gloves with long cuffs
> (gauntlets) provide additional protection to your wrists and lower arms."*

El sujeto gramatical es **`plants with sharp protrusions`**. La recomendación **discrimina**:
se prescribe para ese subconjunto de plantas y no para el resto de la jardinería. Eso es
exactamente lo que un `CareFact` de grupo necesita y lo que, como se ve en §1.3, las fuentes
de prevención laboral **no** dan.

Segunda cita de la misma página, de rasgo con especies como ejemplo:

> *"It is wise to protect your eyes during gardening tasks that involve being inside the canopy
> of plants or working on those like Mahonia, Phormium and Yucca with sharp or pointed leaves."*

Y tercera, de la guía de cultivo de *Berberis* — página de especie, pero donde **el rasgo es la
causa declarada**, no la especie:

**Fuente (B):** RHS, *How to grow berberis*.
[rhs.org.uk/plants/berberis/growing-guide](https://www.rhs.org.uk/plants/berberis/growing-guide)

> *"**As berberis are spiny shrubs**, make sure to protect your eyes and wear thorn-proof gloves
> and clothing when pruning, as thorns can irritate skin."*

El *«as berberis **are spiny shrubs**»* es el giro decisivo: la fuente no dice «al *Berberis* se
le poda con guantes», dice «al *Berberis* se le poda con guantes **porque es un arbusto
espinoso**». El consejo cuelga del rasgo y la especie sólo lo hereda. Es la estructura
justamente contraria a la del aloe en #30, donde todo lo citado era de *Aloe vera* y nada del
hecho de ser crasa.

**Licencia:** © The Royal Horticultural Society 2026 (Registered Charity no. 222879 / SC038262).
Todos los derechos reservados: **es citable en extracto con atribución, no redistribuible**.
Mismo trato que ya recibió el RHS en [#17](https://github.com/Joosle/Plantas/issues/17), donde
se usó como fuente de contraste (B), no como fuente primaria (A).

### 1.2 Nivel RASGO — el mecanismo, en fuente pública española

**Fuente (A):** María de la O Culver González, *NTP 1020: Riesgos biológicos en silvicultura,
explotación forestal y jardinería: prevención*, INSHT/INSST, 2014. NIPO 272-14-024-5.
[PDF](https://insst.es/documents/94886/329170/NTP-1020w.pdf/1cb53850-5f6b-4947-8964-a027952d5fea)

Describe el daño a nivel de rasgo, tres veces:

> *"También puede suponer un riesgo el contacto con determinadas partes de las plantas, como
> las acículas, las espinas, la corteza de los árboles, etc., que pueden provocar lesiones en
> la piel."*

> *"La transmisión se produce normalmente por inoculación en la piel a través de pequeños
> cortes, rozaduras o pinchazos ocasionados por espinas de plantas, acículas de pino, astillas
> de madera"* (esporotricosis, *Sporothrix schenckii*)

> *"Otra vía frecuente de entrada es a través de cortes o pinchazos causados por espinas,
> astillas, herramientas u objetos contaminados"* (tétanos, *Clostridium tetani*)

**Licencia:** publicación oficial del INSST. Su
[aviso legal](https://www.insst.es/informacion-general/aviso-legal) autoriza la reproducción
citando la fuente y sin alterar los contenidos. **Es la fuente de mejor licencia de las
encontradas.**

**Pero no funda el grupo**, y conviene ser explícito sobre por qué — es el punto fino de toda
esta investigación. Ver §1.3.

### 1.3 Por qué la prevención laboral española **no** funda el grupo

Preguntado directamente al documento si alguna medida preventiva se prescribe **sólo** para
plantas espinosas, la respuesta es que **no existe ninguna**. La única recomendación de EPI del
NTP 1020 es:

> *"Empleo de EPI apropiados, según la tarea a realizar: mascarilla de protección respiratoria y
> gafas de protección en caso de exposición a polvo o bioaerosoles, guantes y calzado de
> protección que sean impermeables."*

…y se aplica a **todo trabajo de silvicultura, explotación forestal y jardinería**, no al manejo
de plantas espinosas.

Lo mismo en la ficha autonómica revisada:

**Fuente (A):** Generalitat Valenciana, Servicio de Prevención de Riesgos Laborales del Personal
Propio, *FIR-15: Fichas informativas de riesgos y medidas preventivas — Jardinería*, versión
27/10/2022.
[PDF](https://prevencio.gva.es/documents/161660390/369210876/FI+-+15+Jardiner%C3%ADa.pdf/bbbf61e0-f6ea-dc66-63cc-9d0d0a221e0f?t=1674217710878)

Lista como factor de riesgo *"Golpes, cortes, pinchazos atrapamientos en el uso de herramientas
manuales o **por ramas**, etc."* y prescribe *"guantes de protección frente riesgos mecánicos y
riesgos químicos"* y *"gafas de protección"* — **para toda la jardinería**. La palabra «espina»
no aparece; el riesgo se atribuye a las **ramas**, es decir a la actividad, no a un subconjunto
de plantas.

> **Regla que sale de aquí, y que vale más allá de este ticket:** un consejo prescrito para
> *toda* la actividad **no puede fundar un grupo**, por muy citado y muy protector que sea. Un
> grupo funcional tiene que **particionar** el inventario; un hecho universal no particiona
> nada. Su `subjectId` correcto es la tarea, no el grupo.

Ésa es la razón exacta por la que el INSST aporta el **mecanismo** —el daño es real, está
documentado por un organismo público y llega hasta el tétanos y la esporotricosis— pero es el
RHS, y no el INSST, quien aporta el **hecho que discrimina**.

### 1.4 Nivel ESPECIE, y la confirmación del aviso del ticket

El ticket avisaba de que el sitio probable **no es el evidente**, y se cumple:

- **La Hoja Divulgadora del rosal no dice nada de espinas.** *Poda del rosal y de otros arbustos
  ornamentales*, Hoja Divulgadora 5-6/80 HD (Verdeguer Monge, Servicio de Extensión Agraria,
  Ministerio de Agricultura, 1980, ISBN 84-341-0229-3) —
  [PDF](https://www.mapa.gob.es/ministerio/pags/biblioteca/hojas/hd_1980_05-06.pdf) — fue leída
  íntegra en [#17](https://github.com/Joosle/Plantas/issues/17) y no contiene **ni un pasaje**
  sobre espinas, guantes ni protección personal. La extensión agraria española de la época
  escribe para el cultivo, no para la seguridad de quien poda. Confirmado: buscar aquí habría
  sido el modo de fallo inverso al de #17.
- **La literatura clínica confirma el mecanismo pero no aporta consejo de jardinería citable.**
  La esporotricosis es popularmente *«rose gardener's disease»*, pero el propio NTP 1020 la
  describe con el hongo viviendo en *"el suelo, plantas (principalmente rosales) y madera u
  otros materiales vegetales en descomposición"* — es decir, **la especie aparece como hábitat
  del hongo, no como sujeto del consejo**. El consejo preventivo sigue siendo el genérico.
- **Todo lo demás que aparece buscando «guantes» + «plantas espinosas» es comercial.**
  Fabricantes de guantes, viveros y blogs de SEO. **No vale como cita** bajo el estándar de
  prueba de #17, y se descarta en bloque.

---

## 2. Veredicto de admisión, requisito por requisito

| # | Requisito (ADR 0018) | ¿Pasa? | Por qué |
| --- | --- | --- | --- |
| 1 | Existe un `CareFact` citado con `subjectId` = grupo | ⚠️ **En forma sí** | RHS *«plants with sharp protrusions»* cuelga del rasgo y discrimina. Fuente B, no A — suficiente para *poner* una restricción (el ADR sólo exige `confianza_fuente: alta` para **levantarla**). |
| 2 | El grupo guarda su criterio observable como pregunta en llano | ⚠️ **Redactable, con coste** | Ver §3. Se puede redactar, pero la pregunta que las fuentes respaldan **no es la del borrador**. |
| 3 | El hecho es protector, no de optimización | ❌ **Indeterminable hoy** | Es protector y es una restricción — pero **protege al humano**. El ADR 0018 no define si eso es un `CareFact`. |

**No se admite `espinosa`.** Falla el requisito 3, y no por lo que dice sino por lo que el
modelo aún no dice. **La ausencia no es de fuente: es de decisión.** Por eso el resultado
correcto no es «descartado como `crasa`» sino **«bloqueado a la espera de una decisión de
modelo»**.

Diferencia con `crasa`, que conviene dejar escrita para que nadie las confunda:

| | `crasa` (#30) | `espinosa` (este ticket) |
| --- | --- | --- |
| ¿Hay cita de rasgo? | **No.** Todo era de *Aloe vera* | **Sí.** RHS enuncia sobre el rasgo |
| ¿Qué falta? | La fuente | La decisión de si el humano puede ser sujeto protegido |
| Estado | **Descartado** hasta que aparezca una fuente | **Bloqueado** hasta que se resuelva §6 |

---

## 3. El criterio observable: la pregunta no distingue espina de aguja, **y las fuentes tampoco**

El borrador del ticket es *«¿pincha al tocarla?»*, con la duda de si debe distinguir **espina**
de **aguja** — porque un enebro pincha y una conífera de acículas blandas no.

**El hallazgo es que ninguna fuente hace esa distinción, y las tres la contradicen activamente:**

- El NTP 1020 mete en la misma frase *"las acículas, las espinas, la corteza de los árboles"*, y
  en la vía de inoculación *"espinas de plantas, **acículas de pino**, astillas de madera"*.
- El RHS habla de *"plants with **sharp protrusions**"* y de *"prickles, spines and thorns"* —
  tres palabras distintas, ninguna botánicamente estricta.
- El RHS mete la protección ocular bajo *"sharp or **pointed leaves**"*, nombrando *Mahonia*,
  *Phormium* y *Yucca* — que **no tienen espinas**: tienen hojas duras y punzantes.

> **La clase de peligro que las fuentes reconocen es «pincha», no «tiene espinas».** La distinción
> espina / aguijón / acícula / hoja punzante es botánica, y el daño no la respeta: la piel se
> perfora igual.

Eso resuelve la duda del ticket **a favor del borrador**, y por la mejor de las razones: la
pregunta en llano no es una simplificación que se le concede a un usuario no experto, es la que
**describe correctamente el hecho citado**. Pedirle que distinga espina de aguja sería pedirle
una precisión que la propia fuente no tiene.

**Redacción final propuesta:**

> **«¿Pincha o araña al tocarla o al podarla — espinas, pinchos o hojas duras y puntiagudas?»**

Tres decisiones dentro de esa frase:

1. **«al tocarla o al podarla»**, no sólo «al tocarla». El consejo del RHS es sobre *major work*
   y muñecas y antebrazos: el daño aparece metiendo el brazo dentro de la planta, no rozándola de
   paso. Un rosal se puede acariciar sin pincharse y desgarrarte podándolo.
2. **«o araña»** recoge los aguijones curvos del rosal, que enganchan y rasgan más que pinchan.
3. **«hojas duras y puntiagudas»** es lo que mete a *Yucca*, *Phormium* y *Mahonia*, que el RHS
   cubre explícitamente y que el borrador dejaba fuera.

**Falsos positivos que produce** (planta dentro que quizá no debería):

- **Coníferas de acícula rígida** — *Picea*, enebros, cedros. Entrarían en `espinosa` **además**
  de en `conifera`. **No es un fallo**: es exactamente el caso de dos grupos que el ADR 0018
  previó al fijar cardinalidad 0..N, y el NTP 1020 respalda que la acícula de pino es vía de
  inoculación real. La unión de dos restricciones se cumple sin contradicción.
- **Gramíneas de borde cortante** y plantas con pelos urticantes (ortiga). Pinchan o irritan sin
  ser espinosas. Entrarían por la redacción, y el consejo —guantes y manga larga— les es válido,
  así que el falso positivo **no hace daño**.

**Falsos negativos que produce** (planta fuera que quizá debería estar):

- **El aloe** — ver §5. Sus dientes marginales son de ~2 mm y *"no muy lignificados"*: un usuario
  no experto puede legítimamente contestar «no». Es el falso negativo más probable del inventario
  real.
- **Espinas ocultas bajo el follaje** en plantas densas, que no se notan hasta que se poda.

**Nota importante sobre la asimetría de estos errores.** El ADR 0018 razonó los falsos positivos
suponiendo que el consejo protege a la planta: *«los consejos protectores de conífera aplicados a
un rosal le suprimirían la poda fuerte de enero»*. Aquí **no hay ese coste**: un falso positivo en
`espinosa` hace que el usuario se ponga guantes sin necesitarlos, y eso no le hace nada a nadie.
El error caro es el **falso negativo**. La calibración de la pregunta debería ser generosa —y esa
inversión es, otra vez, síntoma de que el sujeto protegido no es la planta (§6).

---

## 4. La regla del peor miembro: se invierte

El ADR 0018 la fija así:

> *«Un consejo de grupo funcional tiene que ser válido para el peor miembro del grupo. El grupo se
> define por lo que se ve; el consejo se redacta para el miembro más frágil.»*

Comprobación sobre el material de §1:

- **¿Es una restricción (negativo)?** **Sí.** *«No metas el brazo desnudo en una planta que
  pincha»* es la forma canónica; no tiene versión de optimización. Cumple el criterio del ADR
  0018 de que un consejo negativo siempre admite una versión conservadora más estrecha.
- **¿Es válido para el peor miembro?** **Sí, trivialmente** — y ahí está el problema. Ponerse
  guantes es inocuo para **cualquier** planta, incluida la que no pincha. El consejo es válido
  para el miembro más frágil porque **es válido para todas las plantas del mundo**.

> **Y ése es el síntoma.** La regla del peor miembro es un test que muerde cuando el consejo
> puede dañar a un miembro del grupo. Con un consejo que protege al humano, **ningún miembro
> puede resultar dañado por él**, así que la regla pasa vacía: no filtra nada.

Lo que el test *debería* comprobar aquí es lo contrario: no si el consejo es seguro para la planta
más frágil, sino si es **suficiente para la planta más agresiva** del grupo. Guantes finos que
bastan para un aloe no bastan para una pyracantha, y el RHS lo dice — *gauntlets* para *major
work*. **El «peor miembro» de un grupo de seguridad de la persona es el más peligroso, no el más
frágil.**

La regla no se rompe: **se invierte**, porque cambia quién es el sujeto protegido. Es la
demostración más limpia de que §6 no es una duda de forma sino un agujero real en el modelo — y
es también la razón por la que este ticket no se puede cerrar «admitiendo con una nota al pie».

---

## 5. Impacto en el inventario

Contrastado contra [`docs/inventario-plantas.md`](../inventario-plantas.md) (4 plantas, actualizado
2026-08-10), suponiendo que §6 se resolviera a favor y `espinosa` se admitiera con la pregunta de
§3:

| Planta | Taxon | Hoy | Con `espinosa` | Confianza |
| --- | --- | --- | --- | --- |
| El rosal de la fachada | *Rosa* L. (género) | `{}` | **`{espinosa}`** | **Alta** — el RHS nombra `roses` en la lista de *prickles, spines and thorns* |
| El aloe de la terraza | *Aloe vera* | `{}` | **incierto** | **Baja** — ver abajo |
| El abeto | *Unidentified* | `{conifera}` | **`{conifera}`** o **`{conifera, espinosa}`** | Resoluble **hoy, tocándolo** — ver abajo |
| El olivo del jardín este | *Olea europaea* | `{}` | `{}` | Alta — el olivo no pincha |

**El rosal es el caso limpio**, y sería la primera planta del inventario en recibir un consejo de
grupo sin ser el abeto. Hoy sale con el conjunto vacío.

**El aloe no es el caso limpio que el ticket suponía.** La descripción botánica es de *"bordes con
dientes espinados"*, dientes de *~2 mm, gruesos, duros, retrorsos*, pero *"no muy lignificados"*
([Atlas Digital Botánico, FCA-UNC](https://agro.unc.edu.ar/atlasdigital/aloe-spp/) y
[descripción botánica de *Aloe vera*, Dialnet](https://dialnet.unirioja.es/descarga/articulo/4956300.pdf)).
Un aloe **araña**, no perfora. Preguntado *«¿pincha al tocarla?»*, un usuario no experto puede
contestar razonablemente «no» — y por el ADR 0018 esa respuesta la declara el usuario y manda. Así
que **el aloe entra o no entra según quién lo toque**, que es justo la clase de dato inestable que
el ADR 0018 quiere evitar. El ticket esperaba «rosal y aloe entran seguro»; el material dice
**rosal seguro, aloe dudoso**.

**El abeto es el hallazgo más útil.** Su pertenencia a `espinosa` **se puede resolver hoy, sin
identificarlo** — *Picea* y los enebros tienen acícula rígida que pincha, *Abies* la tiene roma y
blanda, y *Thuja* es escamosa. El usuario toca la planta y contesta, sin esperar a
[#15](https://github.com/Joosle/Plantas/issues/15). Es la demostración en vivo de la tesis central
del ADR 0018 —*«el grupo funcional existe para la planta que no sabes qué es»*— sobre la única
planta sin identificar de la casa, y sería además la **primera planta del inventario con dos
grupos**.

**Recuento.** Hoy: 1 de 4 plantas con grupo. Con `espinosa` admitida: **2 de 4 seguras** (rosal,
abeto), 3 de 4 si el aloe entra. El ADR 0018 predijo «tres de cuatro **con** grupo»; el material
sostiene dos con seguridad y la tercera con una respuesta de usuario poco fiable.

---

## 6. La pregunta previa que hay que fichar

**Se documenta la tensión; no se resuelve aquí.**

Todo consejo protector que se ha encontrado colgando del rasgo «pincha» —RHS, INSST, ficha GVA—
tiene el **mismo sujeto protegido: la persona que maneja la planta**. Ninguno protege a la planta.
Y no es un sesgo de búsqueda: es que **el rasgo no tiene consecuencias de cuidado**. Que una planta
pinche no cambia cuándo se riega, cuándo se poda, ni a qué temperatura sufre. Sólo cambia **cómo se
la toca**.

Eso choca con el modelo tal y como está escrito hoy:

- El ADR 0009 define el `CareFact` como
  la unidad de **cuidado**, y su precedencia por especificidad de rango presupone que el sujeto es
  la planta.
- El ADR 0018 hereda ese supuesto en la **regla del peor miembro**, que como se ve en §4 **se
  invierte** cuando el protegido es el humano.
- Ninguno de los dos dice si un hecho de seguridad de la persona **es** un `CareFact`, es otra
  entidad, o simplemente no entra en el sistema.

**La pregunta a fichar:**

> ¿Admite un `CareFact` hechos cuyo sujeto **protegido** es el humano y no la planta — y si los
> admite, cómo se ordenan frente a los de cuidado, y cómo se reescribe la regla del peor miembro
> para ellos?

Consecuencias que la decisión arrastra, y que conviene tener delante al ficharla:

1. **Puede que el sistema deba emitir avisos que no son de cuidado.** *«Ponte guantes de manga
   larga antes de podar el rosal»* no es una tarea de jardinería: es una advertencia de seguridad
   que acompaña a una tarea. Si el modelo lo admite, el plan semanal del
   ADR 0011 gana una clase
   de línea que hoy no tiene.
2. **La regla del peor miembro necesita una segunda forma** (§4): «suficiente para el miembro más
   peligroso», no «seguro para el más frágil».
3. **La calibración de los falsos positivos se invierte** (§3): en cuidado el falso positivo es
   caro, en seguridad es gratis.
4. **Si la respuesta es que no**, `espinosa` queda **descartada como `crasa`** — no por falta de
   fuente, sino porque su único material citado cae fuera del modelo. Y conviene registrarlo así,
   porque es un motivo de descarte distinto y no debe confundirse.
5. **Hay precedente parcial de sujeto no-planta**: el NTP 1020 documenta tétanos y esporotricosis,
   que son riesgos de salud con vacuna y tratamiento. Si el modelo abre la puerta al sujeto humano,
   conviene decidir de una vez hasta dónde llega — o la app acaba dando consejo sanitario.

---

## 7. Resumen para el ticket

- **¿Existe la cita de rasgo?** **Sí.** RHS, *«When undertaking major work on plants with sharp
  protrusions, gloves with long cuffs (gauntlets)…»*, más el *«as berberis **are spiny shrubs**»*
  que cuelga el consejo del rasgo y no de la especie. El INSST (NTP 1020) documenta el mecanismo
  con la mejor licencia, pero **no discrimina** entre plantas espinosas y no espinosas, así que no
  funda el grupo (§1.3).
- **¿Se admite `espinosa`?** **No hoy.** Falla el requisito 3 por una razón que el ADR 0018 no
  previó: el hecho protege al humano, no a la planta.
- **¿Distingue la pregunta espina de aguja?** **No, y no debe.** Ninguna fuente lo hace; la clase
  de peligro real es «pincha». Redacción propuesta: *«¿Pincha o araña al tocarla o al podarla —
  espinas, pinchos o hojas duras y puntiagudas?»*
- **¿Pasa la regla del peor miembro?** Pasa **vacía**, y esa es la señal de alarma: con sujeto
  protegido humano la regla **se invierte** (§4).
- **Inventario:** rosal entra seguro; el abeto entra o no **resoluble hoy sin identificarlo**, y
  sería la primera planta con dos grupos; el aloe es dudoso, contra lo que el ticket suponía; el
  olivo fuera.
- **Desprende ticket:** ¿admite `CareFact` hechos de sujeto protegido humano? (§6)

## 8. Lo que NO se ha podido respaldar

- **Ningún consejo protector de la planta que cuelgue del rasgo espinoso.** Buscado; no aparece, y
  §6 argumenta que probablemente no existe porque el rasgo no tiene consecuencias de cuidado.
- **Ninguna fuente española de tipo A que discrimine plantas espinosas.** Ni MAPA, ni INSST, ni la
  ficha autonómica revisada. La cita que discrimina es del RHS, fuente B y de licencia cerrada.
  **Si el proyecto exigiera fuente A para fundar un grupo, `espinosa` caería aquí mismo** — el ADR
  0018 no lo exige (sólo pide `confianza_fuente: alta` para *levantar* una restricción), pero
  conviene que quien lea esto sepa que el grupo se sostendría sobre una fuente B.
- **Umbrales.** Nada permite decir *cuánto* pincha una planta para entrar en el grupo. El criterio
  es la respuesta del usuario y no hay forma citada de calibrarlo.
