# Guía técnica

Lo mínimo que necesitas saber de Prompt Drive para construir una feature de feedback. El detalle completo está en `API.md` y `AGENTS.md` del repositorio.

## 1. Cómo llega una acción del asistente al simulador

En el estudio, WorlDrive recibe la llamada a herramienta del modelo (por ejemplo `set_reading_light(seat=PASSENGER, on=true)`), la traduce a un cambio de estado del vehículo y se la envía al simulador embebido por `postMessage`: `{ ns:'promptdrive', op:'vehicle.set', args:[{ reading_light_passenger: true }] }`. El simulador valida el cambio en `VehicleState` (31 campos CAR-bench, valores y rangos estrictos), lo guarda y avisa a los **proyectores** registrados con la lista de campos que cambiaron y el estado completo. `CabinFeedback` es uno de esos proyectores y reparte el cambio a las features que declararon esos campos.

```
WorlDrive / demo  --postMessage-->  PromptDrive.vehicle.set({...})
                                         |
                                   VehicleState (valida, guarda)
                                         |  registerProjector
                                   CabinFeedback._dispatch(keys, snapshot)
                                         |
                                   tu feature.onChange(changed, snapshot, ctx)
```

Los estados del asistente (escuchando, pensando, hablando, confirmación, acción, error) llegan por otro camino: `PromptDrive.feedback.agent(state, detail)`, que `CabinFeedback` reparte a `onAgent`.

## 2. Anatomía de una feature

Un archivo en `src/feedback/features/<id>.js`:

```js
(function () {
  if (typeof CabinFeedback === 'undefined') return;

  CabinFeedback.register({
    id: 'window_glass',                     // único; mismo nombre que el archivo
    title: 'Ventanas',
    fields: ['window_driver_position', 'window_passenger_position'],

    init(ctx) {
      // Se llama una vez. Construye tu DOM dentro de ctx.layer (un <div>
      // a pantalla completa, sin eventos de puntero, sobre el lienzo).
    },

    onChange(changed, snapshot, ctx) {
      // `changed` solo trae los campos tuyos que cambiaron, con el valor nuevo.
      // `snapshot` es el estado completo del vehículo.
      // Se llama también una vez tras init() con el estado actual.
    },

    onAgent(state, detail, ctx) {       // opcional
      // state: 'listening' | 'thinking' | 'speaking' | 'confirm' | 'action' | 'error' | 'idle'
    },

    dispose() {},                        // opcional
  });
})();
```

`ctx` te da:

| Miembro | Qué es |
| --- | --- |
| `ctx.layer` | tu `<div>` propio (`position:absolute; inset:0; pointer-events:none`) dentro de `#cabin-feedback`, que está sobre el lienzo con `z-index: 900` |
| `ctx.audio.play(url, {volume, loop})` | reproduce un archivo; devuelve el `HTMLAudioElement` |
| `ctx.audio.tone({freq, freqEnd, ms, type, volume})` | tono sintetizado con WebAudio, sin archivos |
| `ctx.audio.say(texto, {lang})` | voz del navegador (útil para depurar; el asistente real ya habla) |
| `ctx.snapshot()` | estado actual del vehículo |
| `ctx.handles()` | handles del motor desde `PromptDriveBridge` o `null` antes de arrancar: `THREE`, `camera`, `ego`, `audioManager`, `centerConsole`, `vehicleConfig`, `sceneConfig`, `autodrive`, `speedControl`, `ticker`, `drivingMetrics`, `roadState`, `project`, `firstPerson` |
| `ctx.config` | `{ side: 'left' | 'right', units }` (lado del conductor) |
| `ctx.agentState()` | último estado del asistente |
| `ctx.log(...)` | `console.log` con prefijo de tu feature |

Nombres y valores de los campos: `docs/CATALOGO.md`, o `PromptDrive.vehicle.get()` en la consola del navegador.

## 3. Ciclo de trabajo

```bash
npm run dev                      # sirve en http://localhost:3000
# edita src/feedback/features/<id>.js
npm run build:feedback           # regenera static/js/feedback.js
# recarga http://localhost:3000/?autostart=1 y dispara la acción
```

Tres maneras de disparar acciones:

1. Consola del navegador: `PromptDrive.vehicle.set({ window_driver_position: 50 })`, `PromptDrive.feedback.agent('confirm', { text: '¿Abro el baúl?' })`.
2. `api-test.html` (misma carpeta, pestaña **Vehicle**): formularios para todos los campos.
3. El guion de demostración `demo/` de este repositorio: reproduce la secuencia del estudio por `postMessage`, con subtítulos y voz. Es lo que usarás para el video.

Al terminar, `npm run build` completo (reconstruye todo en orden y falla si algo se rompió) y haz commit **incluyendo** `static/js/feedback.js` regenerado: los bundles están versionados en el repositorio.

## 4. Tres niveles de implementación

**Capa DOM (recomendado para empezar).** HTML y CSS sobre el lienzo. Transiciones, gradientes, SVG, texto. Es independiente del motor, no afecta al rendimiento del juego, funciona antes de que el simulador arranque y es lo más fácil de verificar. Las dos features de referencia son de este tipo. Ten en cuenta que el lienzo cambia de tamaño con la ventana: usa unidades relativas (`vw`, `vh`, `%`).

**Consola central.** La pantalla de la consola es un lienzo 2D de 1280 por 720 que el motor proyecta sobre una malla del tablero y redibuja en cada cuadro. Tiene una **franja de estado** reservada en la parte superior para el feedback: `inst.registerOverlay({ id, height, draw(ctx, rect, now, data) })`, donde `inst` es `ctx.handles().centerConsole` (o `window.CenterConsole.lastInstance`). Tu `draw` recibe el contexto 2D del lienzo, el rectángulo de la franja en coordenadas del lienzo y `now` en milisegundos para animar; las apps bajan automáticamente para dejar espacio y la franja desaparece si nadie la registra. La instancia existe solo después de **begin**, así que regístrate cuando aparezca (la feature de referencia `agent_indicator.js` hace exactamente eso, con un sondeo cada segundo). Usa la paleta de la consola (fondo `#0b0d10`, acento `#40E0D0`, verde `#3ddc84`, ámbar `#f5a623`, rojo `#e5484d`) para que se vea de fábrica, y ten en cuenta que la pantalla se proyecta sobre una malla pequeña del tablero: con la calidad gráfica baja el texto se vuelve ilegible, así que prefiere formas grandes, color y movimiento (la referencia `agent_indicator.js` no usa texto por eso). También puedes añadir un pictograma o una app nueva siguiendo el patrón de `src/console/apps/` (requiere `npm run build:console`).

**Escena 3D.** `ctx.handles().THREE` es el módulo three.js del motor, `camera` la cámara en primera persona, `ego` el controlador del carro. Puedes añadir mallas hijas de la cámara (quedan fijas respecto a la cabina, como hace `src/cluster/InstrumentCluster.js` con su overlay) o luces puntuales en el interior. Guárdate de dos cosas: `handles()` es `null` hasta que la persona pulsa **begin** (crea tus objetos de forma perezosa, en el primer `onChange` con handles disponibles), y el ciclo día-noche del motor cambia la iluminación, así que prueba tu efecto de día y de noche (`PromptDrive.dynamic.set({'scene.weatherIndex': n})` o la tecla de clima).

Evita parchear el motor (`scripts/build-main.js`) salvo que una feature lo necesite de verdad: es frágil y el jurado no lo premia por sí mismo.

## 5. Reglas que el registro no puede comprobar por ti

- **Nunca escribas en `VehicleState`** desde una feature (`vehicle.set`, `reset`). El feedback refleja; no decide. En modo benchmark el simulador tiene que poder afirmar que el estado solo cambia cuando el agente lo pide.
- **Estado inicial.** Al recargar la página, tu feature recibe un `onChange` con los valores actuales: respétalos (una ventana que está en 50 se muestra en 50 desde el primer cuadro, sin animación de "abrir").
- **Benchmark y `hideMenu`.** No añadas controles interactivos sobre el lienzo (tu capa no recibe eventos de puntero por diseño). Si necesitas un ajuste de usuario, ponlo detrás de `localStorage` o en el panel de ajustes del juego, nunca como botón flotante.
- **Rendimiento.** Nada de bucles por cuadro en la capa DOM; usa transiciones CSS y, si necesitas animar por tiempo, `requestAnimationFrame` que se detenga solo cuando termina la animación.
- **Sonido.** Los navegadores bloquean el audio hasta la primera interacción; después del clic en **begin** ya hay permiso. Mantén los volúmenes bajos (0,1 a 0,3) y los sonidos cortos; el simulador ya tiene ruido de motor y viento.
- **Recursos.** Archivos nuevos van en `static/feedback/<id>/`. Licencia compatible con MIT y fuente citada en la nota de diseño.

## 6. Lo que mira el jurado en el código

Que el archivo esté donde debe, que el bundle esté regenerado, que `npm run build` pase, que la feature no toque el estado, que funcione desde el inicio y tras recargar, que los nombres sean claros, que no haya dependencias nuevas, y que lo que hace coincida con la nota de diseño y con el video.
