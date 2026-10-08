# Cabina Abierta

Reto para llevar a casa del taller **AI and Autonomous Driving Research at THI** (Technische Hochschule Ingolstadt, HCIS Lab). 24 horas. Haces un fork de [prompt-drive](https://github.com/ignacioalvmar/prompt-drive) y le das al carro una forma de mostrar lo que el asistente hace.

> **English summary.** In the workshop the voice assistant executed your requests inside a simulated car, but most of its actions were invisible: the window "opened", the reading light "came on", the fog lights "switched on", and the cabin did not change. Prompt Drive stores all 31 vehicle-state fields the agent can touch, yet only a handful are rendered. Your task over the next 24 hours: fork the public prompt-drive repository, pick **1 to 3** agent actions from the catalog, and implement the visual and/or auditory feedback a driver should perceive, with a short design note explaining the problem, the alternatives you considered and the solution you chose. A ready-made feedback layer (`src/feedback/`) gives you a registry, a DOM layer over the cabin, audio helpers and the agent's listening, thinking and speaking states, plus two reference features. Deliverables: a pull request from your fork, a 3-minute English video showing each feature working with the provided demo script, one design note per feature, a reflection and an AI-use log. Evaluation is by a jury (see `docs/ENTREGA.md`); the organizers reproduce every demo on the submitted code, and the best features are merged into prompt-drive with credit to their authors. The rest of this document is in Spanish.

---

## Datos de tu cohorte

| | |
| --- | --- |
| Repositorio base | `https://github.com/ignacioalvmar/prompt-drive`, etiqueta `cabina-abierta-v1.0` |
| Fecha y hora límite de tu cohorte | `FECHA_LIMITE` (hora de Colombia) |
| Canal de ayuda | `CANAL` |

---

## De dónde viene esto

En el taller hablaste con un asistente de voz dentro de un carro simulado. Pediste subir la temperatura, prender la luz de lectura, desempañar el parabrisas, prender las exploradoras. El asistente respondía "listo" y el estado del carro cambiaba de verdad (puedes verlo en la pestaña Vehicle de `api-test.html`), pero en la cabina no pasaba casi nada: Prompt Drive guarda los 31 campos del vehículo que el agente puede tocar y solo proyecta unos pocos sobre algo visible (las luces bajas y los números de la app de clima en la consola central). Las ventanas, el techo, las luces de lectura, la luz ambiental, las antiniebla, las altas, el desempañador, el baúl, la circulación del aire y el cambio a modo autónomo cambian en silencio. Y de los estados del propio asistente (está escuchando, está pensando, está pidiendo confirmación, falló) la cabina no muestra nada.

Esa falta de retroalimentación es un problema real de diseño en los carros con agentes de IA: si la persona no percibe que el carro hizo lo que pidió, repite la orden, duda del sistema o deja de usarlo. Tu trabajo es cerrar ese hueco para una, dos o tres acciones.

## Tu misión

1. Haz un fork de prompt-drive (etiqueta `cabina-abierta-v1.0`) y ponlo a correr en tu computador (`npm run dev`, no hay nada que instalar).
2. Escoge **entre 1 y 3 acciones del asistente** del catálogo en `docs/CATALOGO.md` (o una propia, si la documentas igual). Mínimo una. Más de tres no se evalúan: preferimos una feature bien resuelta que cinco a medias.
3. Para cada acción, diseña e implementa lo que la persona al volante debería **ver u oír**: una animación de la ventana bajando, el vidrio que se desempaña, un resplandor cálido donde se prendió la luz, un sonido de confirmación, una barra que respira mientras el asistente escucha, lo que tu criterio de diseño te diga. Puede ser una capa visual sobre la cabina, un cambio en la escena 3D, algo en la consola central, sonido, o una combinación.
4. Escribe una **nota de diseño** por feature (plantilla en `docs/feedback/TEMPLATE_nota_de_diseno.md` dentro del repositorio): qué viste en el estudio, qué debería percibir la persona, qué alternativas consideraste, qué decidiste y por qué, qué limitaciones quedan.
5. Graba un **video de máximo 3 minutos en inglés** mostrando cada feature con el guion de demostración (`demo/`) y explicando la decisión de diseño principal.
6. Abre un **pull request** desde tu fork hacia prompt-drive con la plantilla que aparece sola. Ese PR es tu entrega.

## Flujo de trabajo: dos carpetas, un fork, un pull request

Vas a tener dos repositorios en tu computador, uno al lado del otro, nunca uno dentro del otro:

```
tu-carpeta/
├── cabina-abierta/     este repositorio: el reto, el catálogo, el guion de demostración y las plantillas. Solo se lee.
└── prompt-drive/       TU FORK del simulador: aquí va todo tu código y desde aquí sale el pull request.
```

1. **Fork.** En GitHub, abre `https://github.com/ignacioalvmar/prompt-drive` y pulsa **Fork**. Eso crea `github.com/<tu-usuario>/prompt-drive`, tu copia, la única a la que puedes hacer push.
2. **Clona los dos y crea tu rama desde la etiqueta.**

   ```bash
   git clone https://github.com/ignacioalvmar/cabina-abierta.git
   git clone https://github.com/<tu-usuario>/prompt-drive.git
   cd prompt-drive
   git checkout cabina-abierta-v1.0 -b cabina-abierta/<tu-equipo>
   npm run dev                                   # http://localhost:3000
   ```

   La rama nace de la etiqueta para que todos los equipos partan del mismo estado del simulador.
3. **Trabaja en el fork.** Tus features van en `prompt-drive/src/feedback/features/<id>.js`; cada cambio se prueba con `npm run build:feedback` y recarga. Para disparar las acciones usa la consola del navegador, `api-test.html` o el guion: sirve `cabina-abierta/demo/` en otro puerto y conéctalo a tu simulador (instrucciones en `demo/README.md`).
4. **Documenta en el fork.** Una nota de diseño por feature en `prompt-drive/docs/feedback/<id>.md` (copia `docs/feedback/TEMPLATE_nota_de_diseno.md`, que ya está en prompt-drive), y `REFLEXION.md` y `AI_LOG.md` en la raíz de tu fork, copiados de `cabina-abierta/templates/`. Sube el video a donde quieras (YouTube sin listar, Drive) y guarda el enlace.
5. **Entrega: push y pull request.**

   ```bash
   npm run build                                 # tiene que pasar
   git add -A && git commit -m "Add window feedback feature"
   git push -u origin cabina-abierta/<tu-equipo>
   ```

   GitHub te muestra en tu fork el aviso **Compare & pull request**. Ábrelo con base `ignacioalvmar/prompt-drive`, rama `main`, y como head tu fork y tu rama. La descripción viene prellenada con la plantilla del reto: equipo, features, enlace al video y lista de verificación. Ese pull request abierto es tu entrega; cuenta la hora en que lo abres. Si corriges algo después, haz push a la misma rama: el PR se actualiza solo.

Lo que se integra en prompt-drive de las features ganadoras son los archivos de la feature y su nota de diseño; `REFLEXION.md` y `AI_LOG.md` se quedan en tu fork y solo los lee el jurado.

## Qué te damos

- **La capa de feedback** `src/feedback/` dentro del repositorio: un registro (`CabinFeedback`) al que cada feature se suscribe declarando los campos del vehículo que le interesan; una capa DOM propia sobre el lienzo del simulador (`ctx.layer`), sin tocar el motor; ayudas de audio (`ctx.audio.play`, `ctx.audio.tone`); los estados del asistente (`listening`, `thinking`, `speaking`, `confirm`, `action`, `error`) a través de `onAgent`; y acceso a los handles del motor (`ctx.handles()`: THREE, cámara, carro, audio, consola) si quieres meterte en la escena 3D. Guía completa en `docs/GUIA_TECNICA.md`.
- **Dos features de referencia** que puedes copiar: `reading_light.js` (resplandor cálido y clic al prender una luz de lectura, en la capa DOM sobre la cabina) y `agent_indicator.js` (franja de estado a todo lo ancho en la parte superior de la consola central, sin texto: color y movimiento distintos para escuchando, pensando, hablando, esperando confirmación, acción ejecutada y error, legibles incluso con la calidad gráfica al mínimo).
- **El guion de demostración** `demo/`: una página que se conecta a tu simulador local y reproduce, paso a paso, las acciones del asistente que viviste en el estudio (y algunas más del catálogo), con la frase de la persona y la respuesta del asistente en pantalla y por voz. Sirve para probar y para grabar el video: así todos los videos muestran las mismas acciones y el jurado puede comparar.
- **`api-test.html`** (pestaña Vehicle) para disparar cualquier campo a mano.

## Reglas

- Trabajo individual o en parejas, según lo anunciado en tu cohorte.
- Todo tu código vive en `src/feedback/features/` (un archivo por feature) y, si lo necesitas, en `src/feedback/` o en recursos nuevos bajo `static/feedback/`. No edites a mano los bundles de `static/js/*.js`: se regeneran con `npm run build:feedback`. Si de verdad necesitas tocar el motor (`scripts/build-main.js`), explícalo en la nota de diseño; es la vía difícil y no da puntos extra por sí misma.
- Una feature **muestra** el estado del carro; **nunca lo cambia**. Nada de escribir en `VehicleState` desde el feedback: el modo benchmark del simulador exige que el sistema no cambie nada por su cuenta.
- Sin dependencias nuevas, sin frameworks, sin cambiar el orden de carga de `index.html`. El estilo del repositorio es JavaScript plano; léelo en `AGENTS.md`.
- Puedes usar asistentes de IA (Claude Code, Codex, Copilot, ChatGPT) para todo, y debes declararlo en `AI_LOG.md`. El repositorio trae `AGENTS.md` para que tu asistente entienda el proyecto; úsalo.
- Los recursos (sonidos, imágenes) deben ser propios o con licencia compatible con MIT; cita la fuente.

## Cómo se evalúa

Es una evaluación de diseño, hecha por un jurado, con un criterio de verificación objetivo: el jurado baja tu PR, corre el guion de demostración y comprueba que **lo que se ve en tu video pasa en el código entregado**. Si no coincide, la feature no cuenta. Pesos y rúbrica en `docs/ENTREGA.md`: funcionalidad verificada 30 %, calidad del diseño 25 %, nota de diseño 10 %, calidad e integrabilidad del código 15 %, video 10 %, reflexión y registro de IA 10 %.

Las mejores features se integran al repositorio público de prompt-drive, con los nombres de sus autores en `docs/feedback/CREDITS.md`. Esa es la recompensa principal: código tuyo corriendo en una plataforma de investigación que usan otros grupos.

## Empieza en 15 minutos

```bash
git clone https://github.com/ignacioalvmar/cabina-abierta.git   # este repositorio
git clone https://github.com/<tu-usuario>/prompt-drive.git      # tu fork (haz el fork en GitHub primero)
cd prompt-drive && git checkout cabina-abierta-v1.0 -b cabina-abierta/<tu-equipo>
npm run dev                                                      # http://localhost:3000
```

Abre `http://localhost:3000/?autostart=1`, pulsa **begin** y en la consola del navegador:

```js
PromptDrive.vehicle.set({ reading_light_passenger: true })   // resplandor arriba a la derecha y un clic
PromptDrive.feedback.agent('listening')                       // consola central: barras cian respirando a todo lo ancho
CabinFeedback.list()                                          // features registradas
```

Luego sirve la carpeta `demo/` de este repositorio con cualquier servidor estático (`npx serve demo` o `python -m http.server 3100 -d demo`), ábrela, pon la URL de tu simulador y pulsa **Siguiente paso**. Ya tienes el ciclo completo: editar `src/feedback/features/<tu-feature>.js`, `npm run build:feedback`, recargar, disparar la acción desde el demo.

Para crear una feature: copia `reading_light.js`, cambia el `id`, declara los `fields` que te interesan (nombres en `docs/CATALOGO.md`), construye tu DOM en `init(ctx)` y renderiza el estado en `onChange(changed, snapshot, ctx)`. Para los estados del asistente, implementa `onAgent(state, detail, ctx)` como en `agent_indicator.js`.

## Consejos

- Empieza por el "qué debería percibir la persona" antes de abrir el editor. La nota de diseño vale más si muestra que pensaste en el canal (periférico, focal, sonoro), el tiempo (cuánto dura, cuándo desaparece) y el contexto (de noche, lloviendo, en autónomo).
- Lo bueno en un carro suele ser sutil: una transición de 300 ms, un sonido corto, un color coherente con lo demás. El jurado penaliza lo que distrae o tapa la vía.
- Prueba con el estado inicial: al recargar la página la feature debe mostrar el estado actual (una ventana que ya estaba abierta sigue abierta). El registro llama a `onChange` una vez al iniciar justo para eso.
- Si vas a la escena 3D, mira cómo `src/cluster/InstrumentCluster.js` coloca su malla en el interior y usa `ctx.handles()` (puede ser `null` antes de que el simulador arranque).
- No des por hecho que el guion es el único disparador: en el estudio real las acciones vienen de WorlDrive por `postMessage`, exactamente igual que en el demo.

## Entrega

Antes de la fecha límite de tu cohorte: el pull request abierto desde tu fork (con el video enlazado y las notas de diseño en `docs/feedback/<id>.md`), más `REFLEXION.md` y `AI_LOG.md` en la raíz de tu fork (plantillas en `templates/`). Detalles, rúbrica y protocolo de verificación en `docs/ENTREGA.md`.

## Créditos y licencia

Materiales del HCIS Lab (Human-Centered Intelligent Systems), Technische Hochschule Ingolstadt, para la gira académica THI en Colombia, 2026. Prompt Drive y este material están bajo licencia MIT.
