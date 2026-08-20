# Pl@ntNet: proyectos (floras) disponibles y cuota real

Datos obtenidos de primera mano contra la API el **20 de agosto de 2026**, con la key del
plan gratuito de este proyecto. Resuelve el ticket
[Alta de cuenta y API key de Pl@ntNet](https://github.com/Joosle/Plantas/issues/14), que
existía precisamente porque `my.plantnet.org` era inalcanzable durante la investigación de
[#5](https://github.com/Joosle/Plantas/issues/5) y estas cifras quedaron sin verificar.

## Cuota

`GET /v2/quota` → `{"quota":{"identify":500}}`

**500 identificaciones/día**, confirmado contra la propia key y no contra la página de
precios. Coincide con lo que anuncia el plan gratuito (0 €).

Dos endpoints útiles descubiertos al probar, ambos **gratuitos y sin consumir
identificaciones**:

- `GET /v2/quota` — la cuota de la key. La app puede comprobar su propio límite sin
  gastar una identificación.
- `GET /v2/_status` — `{"status":"ok"}`. Comprobación de servicio.

(`GET /v2/subscription` existe pero devuelve 403 en el plan gratuito.)

## El dato que se venía a verificar: `useful`

| campo | valor |
|---|---|
| id | `useful` |
| título | Plantas útiles |
| descripción | **Plantas cultivadas y ornamentales** |
| especies | **5.549** |

La investigación de #5 citaba **~5.443** y advertía de que era «el dato más decisivo y el
peor verificado» del informe. El real es **5.549**: la estimación era buena (+106
especies, +1,9 %). La elección de `useful` para ornamentales de casa se sostiene, y su
descripción oficial —«plantas cultivadas y ornamentales»— es literalmente el caso de uso
de este proyecto.

## Hallazgo no buscado: `useful` no es el único recorte que nos sirve

`GET /v2/projects` devuelve **77 proyectos**, y entre ellos hay dos que compiten con
`useful` para esta casa por razones opuestas — uno por ser mucho más grande, otro por ser
mucho más pequeño:

| id | título | especies | recorte |
|---|---|---|---|
| `k-world-flora` | Flora mundial | 84.571 | ninguno — red de seguridad |
| `k-southwestern-europe` | Suroeste de Europa | **7.686** | geográfico: todo lo que crece aquí, silvestre incluido |
| `useful` | Plantas útiles | 5.549 | por uso: cultivadas y ornamentales, mundial |
| `monver` | Los árboles ornamentales mediterráneos | **231** | uso + geografía: árboles y arbustos para ciudad y jardines del Mediterráneo |

No contradice a #5; es un eje distinto. `useful` recorta por **uso** y `k-southwestern-europe`
por **geografía**, y para las plantas de esta casa —tres arraigadas en el jardín y un aloe
en maceta, todas plantadas a propósito— el recorte por uso es el que corresponde. Pero un
ornamental poco común podría estar en la flora regional y no en `useful`.

`monver` merece atención aparte: con **231 especies** es el cruce exacto de los dos ejes
—ornamental *y* mediterráneo—, y sobre el papel el olivo, el rosal y las coníferas del
inventario caen justo dentro de su alcance. Una lista corta es un arma de doble filo: si la
planta está dentro, la confianza sube mucho porque hay menos candidatos con los que
confundirse; si está fuera, la respuesta no es «no sé» sino **el vecino más parecido que sí
está en la lista**, que es el modo de fallo peligroso porque llega con aspecto de acierto.

**Esto es materia de [#15](https://github.com/Joosle/Plantas/issues/15)**, que es quien
prueba el camino de identificación contra las plantas reales. La pregunta que abre —si
merece la pena una segunda llamada a otro proyecto cuando la primera devuelve baja
confianza, y en qué orden— sólo se responde con las fotos delante. Aquí sólo se deja el
dato.

## Los 77 proyectos

| id | título | descripción | especies |
|---|---|---|---|
| `k-world-flora` | Flora mundial | Plantas de la flora mundial | 84.571 |
| `k-central-america` | América Central | Plantas de América Central | 15.920 |
| `k-western-south-america` | Oeste de Sudamérica | Plantas del Oeste de Sudamérica | 13.786 |
| `k-west-central-tropical-africa` | Centro-oeste de África Tropical | Plantas del Centro-Oeste de África Tropical | 11.943 |
| `k-mexico` | México | Plantas de México | 11.587 |
| `k-brazil` | Brasil | Plantas de Brasil | 10.627 |
| `k-western-indian-ocean` | Oeste del Océano Índico | Plantas del Océano Índico Occidental | 10.442 |
| `k-northern-south-america` | Norte de Sudamérica | Plantas del norte de Sudamérica | 8.792 |
| `k-china` | China | Plantas de China | 7.727 |
| `k-southwestern-europe` | Suroeste de Europa | Plantas del Suroeste de Europa | 7.686 |
| `k-indian-subcontinent` | Subcontinente indio | Plantas del subcontinente indio | 7.585 |
| `k-southeastern-europe` | Sureste de Europa | Plantas del Sureste de Europa | 7.450 |
| `k-south-tropical-africa` | Sur de África Tropical | Plantas del Sur de África Tropical | 6.550 |
| `k-east-tropical-africa` | África ecuatorial oriental | Plantas de África ecuatorial oriental | 6.492 |
| `k-southern-africa` | África del Sur | Plantas del África del Sur | 6.446 |
| `k-caribbean` | Caribe | Plantas del Caribe | 6.242 |
| `k-southern-south-america` | Sur de Sudamérica | Plantas del sur de Sudamérica | 6.120 |
| `k-western-asia` | Asia Occidental | Plantas de Asia Occidental | 5.927 |
| `k-southwestern-u-s-a` | Suroeste de Estados Unidos | Plantas del Suroeste de Estados Unidos | 5.633 |
| `k-west-tropical-africa` | Oeste de África Tropical | Plantas del Oeste de África Tropical | 5.626 |
| `k-indo-china` | Indochina | Plantas de Indochina | 5.605 |
| `k-southeastern-u-s-a` | Sureste de Estados Unidos | Plantas del Sureste de Estados Unidos | 5.598 |
| `useful` | Plantas útiles | Plantas cultivadas y ornamentales | 5.549 |
| `k-middle-europe` | Europa Central | Plantas de Europa Central | 5.389 |
| `k-australia` | Australia | Plantas de Australia | 5.158 |
| `k-northeast-tropical-africa` | Noreste del África Tropical | Plantas del noreste tropical de África | 5.032 |
| `k-eastern-asia` | Asia oriental | Plantas de Asia oriental | 4.935 |
| `k-malesia` | Malesia | Plantas de Malesia | 4.841 |
| `k-northern-africa` | África del Norte | Plantas de África del Norte | 4.516 |
| `k-south-central-u-s-a` | Centro-Sur de Estados Unidos | Plantas del Centro-Sur de Estados Unidos | 4.453 |
| `xprize-final` | Brazilian Amazon | Brazilian Amazon flora | 4.392 |
| `k-northern-europe` | Europa del Norte | Plantas del Norte de Europa | 4.228 |
| `k-northeastern-u-s-a` | Noreste de Estados Unidos | Plantas del Noreste de Estados Unidos | 4.052 |
| `k-northwestern-u-s-a` | Noroeste de Estados Unidos | Plantas del Noroeste de Estados Unidos | 3.964 |
| `k-north-central-u-s-a` | Centro-norte de Estados Unidos | Plantas del Centro-norte de Estados Unidos | 3.929 |
| `k-eastern-europe` | Europa del Este | Plantas de Europa del Este | 3.885 |
| `k-southwestern-pacific` | Pacífico Suroccidental | Plantas del Pacífico Suroccidental | 3.866 |
| `acg` | Área de Conservación Guanacaste, Costa Rica | Especies vegetales del Área de Conservación Guanacaste, Costa Rica | 3.712 |
| `k-caucasus` | Cáucaso | Plantas del Cáucaso | 3.341 |
| `k-macaronesia` | Macaronesia | Plantas de Macaronesia | 3.025 |
| `k-middle-asia` | Asia Central | Plantas de Asia Central | 2.828 |
| `k-eastern-canada` | Este de Canadá | Plantas del Este de Canadá | 2.713 |
| `k-western-canada` | Oeste de Canadá | Plantas del Oeste de Canadá | 2.553 |
| `xprize-final-trees` | Trees of the Brazilian Amazon | Tree flora of the Brazilian Amazon | 2.468 |
| `cevennes` | Flora del parque nacional de Cévennes | Flora del parque nacional de Cévennes | 2.399 |
| `k-papuasia` | Papuasia | Plantas de Papuasia | 2.215 |
| `prota` | Plantas útiles de África tropical | Recursos vegetales de África tropical | 2.212 |
| `provence` | Provenza, Francia | Flora provenzal dep. Bouches-du-Rhône | 2.212 |
| `prosea` | Plantas útiles de Asia | Recursos Vegetales de Asia | 2.190 |
| `k-arabian-peninsula` | Arabia | Plantas de Arabia | 2.181 |
| `k-russian-far-east` | Extremo oriental de Rusia | Plantas del extremo oriente de Rusia | 2.040 |
| `k-siberia` | Siberia | Plantas de Siberia | 2.035 |
| `k-new-zealand` | Nueva Zelandia | Plantas de Nueva Zelandia | 1.791 |
| `k-north-central-pacific` | Centro Norte del Pacífico | Plantas del Centro-norte del Pacífico | 1.501 |
| `weeds` | Arvenses | Arvenses/Malezas en campos de agricultura de Europa | 1.431 |
| `k-northwestern-pacific` | Noroeste del Pacífico | Plantas del Noroeste del Pacífico | 1.259 |
| `k-subarctic-america` | Subártico Americano | Plantas del Subártico Americano | 1.259 |
| `k-mongolia` | Mongolia | Plantas de Mongolia | 1.189 |
| `couloubrier` | Domaine Saint Jacques du Couloubrier | Flora de la finca Saint Jacques du Couloubrier | 1.168 |
| `k-south-central-pacific` | Centro-Sur del Pacífico | Plantas del Centro-Sur del Pacífico | 1.163 |
| `invasion` | Plantas invasivas | Especies invasivas que amenazan los medios de vida y el ambiente en el mundo | 1.095 |
| `lewa` | LEWA in KENYA | Lewa Wildlife Conservancy | 848 |
| `eu-trees` | Trees of Europe | Trees and scrubs species observed in Europe | 612 |
| `esalq` | ESALQ and Piracicaba trees | Trees and shrubs of ESALQ park and surrounding areas | 584 |
| `k-middle-atlantic-ocean` | Océano Atlántico Central | Plantas del Atlántico Central | 457 |
| `k-subantarctic-islands` | Islas Subantárticas | Plantas de las islas Subantárticas | 412 |
| `sugarcane` | Weeds of sugarcane in Australia | Weeds found in sugarcane fields of Queensland and Northern New South Wales | 328 |
| `salad` | Les Ecologistes de l'Euzière | - | 245 |
| `clcpro` | Biotopos de la langosta del desierto en África occidental | Flora de los biotopos de la langosta del desierto en África occidental | 237 |
| `monver` | Los árboles ornamentales mediterráneos | Árboles y arbustos para la ciudad y jardines del mar Mediterráneo | 231 |
| `eu-crops` | Cultivos Europeos | Plantas y cultivos europeos | 221 |
| `museum-albert-kahn` | Jardin du musée départemental Albert-Kahn | Jardin du musée départemental Albert-Kahn | 180 |
| `ordesa` | Ordesa | Plantas del parque nacional de Ordesa | 142 |
| `gbb-cf` | Gardens by the Bay - Cloud Forest | Singapore | 102 |
| `alpes-maritimes` | Flore remarquable des Alpes-Maritimes | Flore patrimoniale des parcs naturels départementaux des Alpes-Maritimes | 98 |
| `gbb-fd` | Gardens by the Bay - Flower Dome | Singapore | 80 |
| `k-antarctic-continent` | Continente antártico | Plantas del continente antártico | 2 |

## Dónde vive la key

**Fuera del repositorio**, en `C:\Users\Jose\.secrets\plantas.env`, como
`PLANTNET_API_KEY`. Hoy este repositorio es sólo documentación: no tiene `.gitignore` ni
código, y un secreto en un árbol sin protección es exactamente cómo se acaban commiteando
las keys.

Cuando aterrice el código del ADR 0005 la convención pasa a ser la de Cloudflare, y la key
se mueve allí: `.dev.vars` (gitignoreado) en local, y `wrangler secret put PLANTNET_API_KEY`
en producción. Lo mismo aplicará a la key de AEMET de
[#12](https://github.com/Joosle/Plantas/issues/12).

La cuenta es del plan gratuito y **es una sola por persona**, según los términos de
Pl@ntNet: no se dan de alta más cuentas para multiplicar la cuota.
