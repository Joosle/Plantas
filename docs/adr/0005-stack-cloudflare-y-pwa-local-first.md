# El stack es Cloudflare de extremo a extremo, con una PWA local-first y ejecución diaria

Todo vive en un solo Worker de Cloudflare: sirve los assets estáticos de la PWA, expone la API y ejecuta los dos cron triggers. El dato vive en **D1**, que es SQLite. El front es una **SPA pura** (Vite + React) que guarda el plan de la semana y encola sus escrituras en IndexedDB, de modo que la checklist funciona en el fondo del jardín sin cobertura. Coste de infraestructura: **0 €/mes**, sin tarjeta y sin suscripción.

El criterio que ordenó la decisión no fue la capacidad —con cuatro plantas y un usuario, cualquier opción sobra por dos órdenes de magnitud— sino **cuál sigue siendo gratis dentro de tres años sin intervención**. Cloudflare es el único candidato sin mecanismo de caducidad por inactividad, y el plan Free no factura al excederse: devuelve error y para, así que no existe la factura sorpresa.

## Dos crons, no uno

El ticket que originó esta decisión hablaba de «el job semanal». No lo hay. Hay **dos**, y separarlos es la decisión, no un detalle de despliegue:

- **Diario (05:00 UTC)**: trae el clima de Open-Meteo, persiste el snapshot, recalcula el balance hídrico y evalúa alertas. **Sin LLM.** Es el camino barato y crítico.
- **Semanal (lunes 05:30 UTC)**: lee lo anterior, llama al LLM para redactar y persiste el `WeeklyPlan`. Es el camino caro y frágil.

La cadencia diaria no es una elección: la fijaron [#4](https://github.com/Joosle/Plantas/issues/4) con su llamada diaria a Open-Meteo y [#6](https://github.com/Joosle/Plantas/issues/6) con un balance hídrico que es una recursión día a día. Un pronóstico de helada aparece con dos o tres días de antelación y un job semanal lo vería cuando ya pasó. Además, un job diario que falla pierde un día; uno semanal que falla pierde la semana.

Lo que la separación compra es **aislamiento de fallos**: la llamada al LLM es lo más lento, lo más caro y lo que más formas tiene de romperse. Dentro del mismo job, un LLM caído un lunes te dejaría también sin evaluación de heladas esa mañana.

## El plan se consulta; sólo la alerta interrumpe

El plan de la semana **no avisa de nada**: el lunes está generado y esperando. Un aviso que anuncia una checklist previsible es ceremonia vacía.

Las alertas van por el camino contrario, porque son de naturaleza distinta: impredecibles y perecederas. [#9](https://github.com/Joosle/Plantas/issues/9) fijó que una `Alert` caduca sola y nunca se arrastra, luego una alerta no vista es una alerta **perdida**, no una tarea pendiente. El canal es un **bot de Telegram** —un `fetch` desde el Worker— y no Web Push, precisamente porque se usa poco: una suscripción push que se ejercita tres veces al año es la que se habrá invalidado en silencio el día de la helada, y en iOS además exigiría tener la PWA añadida a la pantalla de inicio para existir siquiera. El token de Telegram no caduca y sus fallos son visibles.

## El silencio del sistema también caduca

Pasar el plan a consulta creó un problema que el aviso semanal tapaba: **«esta semana no hay nada que hacer» y «el motor lleva un mes muerto» se ven igual** — y con 3 de 4 plantas arraigadas y sin riego ([#2](https://github.com/Joosle/Plantas/issues/2)), la primera va a ser la respuesta normal.

Es el mismo problema que [#9](https://github.com/Joosle/Plantas/issues/9) resolvió para el diario al fijar que *el silencio es evidencia con caducidad*, aplicado ahora al sistema en vez de al usuario. La solución es la misma escalera: el `WeeklyPlan` y el snapshot de clima muestran su edad en la app, y el job diario avisa por Telegram si el plan más reciente supera los 8 días. El hueco aceptado conscientemente es que **si muere el job diario no queda nadie dentro del sistema para decirlo**; se descubre al abrir la app.

## Considered Options

- **Vercel + base de datos gestionada (Neon, Supabase).** Mejor DX, pero dos proveedores y el free tier de BD gestionada es el más recortado del sector; Supabase además pausa el proyecto tras 7 días de inactividad, y una app que se toca una vez por semana vive en ese filo.
- **GitHub Pages + Actions con el dato versionado en el repo.** Cero proveedores nuevos y gratis sin caducidad, pero el camino de escritura desde el móvil exige un token de GitHub en el navegador, y los workflows programados se desactivan tras 60 días de inactividad del repo.
- **Framework con SSR (Next.js, SvelteKit).** Descartado por incompatibilidad de propósito: local-first exige que la app funcione entera sin servidor, que es exactamente la mitad que el SSR aporta. Se pagaría su complejidad tras apagar su beneficio.
- **Cliente fino sin IndexedDB.** Menos código y nada que desincronizar, pero la app fallaría justo en el momento para el que fue diseñada: marcar la checklist en el jardín.
- **Cloudflare Access (Zero Trust) para cerrar la API.** Gratis y sin escribir código, pero introduce sesiones que expiran en el único sitio donde duele: la cola offline volvería del jardín con diez escrituras y recibiría una página de login en vez de un 200.

## Consequences

- **La sincronización no necesita resolución de conflictos.** El diario sólo añade y el usuario es uno, así que la cola de salida es «reenvía lo pendiente en orden». Encaja con las dos fechas de [#9](https://github.com/Joosle/Plantas/issues/9): la entrada encolada conserva su `occurredAt` real y el `recordedAt` no miente cuando llega tarde.
- **El sistema no tiene ninguna caché.** Todo dato externo entra como hecho fechado y persistido, no como copia con TTL: el clima es un snapshot con la escalera de degradación de [#4](https://github.com/Joosle/Plantas/issues/4), los cuidados son la tabla curada de [#18](https://github.com/Joosle/Plantas/issues/18) y la identificación se materializa como `Taxon` más un `LifecycleEvent`. Es consecuencia directa de que el diario sea el estado del sistema: un hecho fechado no necesita invalidarse.
- **Ningún secreto llega al navegador.** La PWA sólo habla con el Worker, así que las claves de terceros no existen del lado cliente. Todas van en `wrangler secret put`; en local, `.dev.vars` fuera del control de versiones.
- **La API se cierra con un token compartido**, no con auth. La preferencia del mapa («sin cuentas, sin auth») se lee como «no construyas usuarios», no como «deja el endpoint abierto»: sin cierre, cualquiera con la URL leería el diario y gastaría el presupuesto de LLM. El token vive en `localStorage`, se pega una vez y no caduca — y no caducar es el requisito, para no pelearse con la cola offline.
- **El backup vive fuera de Cloudflare.** D1 trae Time Travel gratis (7 días en Free, siempre activo), que cubre el borrado accidental detectado pronto pero desaparece con el proveedor. El cron del lunes vuelca la base a un repo privado de Git: unos kilobytes por commit, historial versionado y un `.sql` que cualquier SQLite abre. Es la respuesta literal a «¿y si el servicio cierra?».
- **El techo de 10 ms de CPU por invocación** del plan Free existe y conviene recordarlo, aunque la espera de red de `fetch` no cuente y el balance de cuatro plantas sobre diez días sea aritmética trivial. Los crons corren en **UTC**, de modo que la hora local se desplaza una hora entre invierno y verano.
- **Lo único que cuesta dinero se ejecuta 52 veces al año** y nunca en respuesta a un gesto del usuario, porque el camino de alertas es sin LLM por diseño.
