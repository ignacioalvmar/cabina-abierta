# Guion de demostración

Página estática que se conecta a tu simulador local por `postMessage` y reproduce, paso a paso, las acciones del asistente del estudio (tareas t01 a t13 más algunas extra del catálogo). En cada paso muestra la frase de la persona y la respuesta del asistente (con voz del navegador si eliges una), envía los estados del asistente (`listening`, `thinking`, `action`, `speaking`, `confirm`, `error`) y ejecuta los `vehicle.set` correspondientes.

```bash
# en tu fork de prompt-drive:
npm run dev                                   # http://localhost:3000
# en esta carpeta:
python -m http.server 3100                    # o: npx serve -p 3100
# abre http://localhost:3100/?sim=http://localhost:3000/%3Fautostart%3D1%26hideMenu%3D1
```

Pulsa **begin** en el simulador si no arrancó solo, luego **Siguiente paso** o **Reproducir todo**. El cuadro de texto de abajo envía cualquier `vehicle.set` a mano.

`guion.json` es editable: añade pasos para tus propias acciones. Cada paso tiene `persona`, `agente`, y una lista de `acciones` con `tool` (nombre de la herramienta, solo informativo, llega en `detail.tool` del estado `action`) y `set` (el cambio de `VehicleState`). Un paso con `reset` reinicia el estado; `pregunta`, `confirmacion` y `error` cambian el estado del asistente que se envía al final.

Para el video: graba la pantalla con el simulador en grande y el panel del guion visible, con el audio del sistema activado si tu feature suena.
