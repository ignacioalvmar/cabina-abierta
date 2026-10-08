# Catálogo de acciones del asistente sin retroalimentación

Cada fila es una acción que el asistente ejecutó (o pudo ejecutar) en el estudio y que hoy cambia el estado del carro sin que la cabina lo muestre. Escoge entre una y tres. Los nombres de campo son los de `VehicleState` (`PromptDrive.vehicle.get()`); tu feature los declara en `fields` y recibe los valores nuevos en `onChange`.

La columna "Hoy" dice qué se ve actualmente. "Dificultad" es una estimación para una capa DOM; la escena 3D suma un nivel.

## Aperturas

| Acción del asistente | Campos | Valores | Hoy | Ideas para percibir | Dificultad |
| --- | --- | --- | --- | --- | --- |
| Abrir o cerrar una ventana ("abre mi ventana a la mitad", "súbeme las ventanas") | `window_driver_position`, `window_passenger_position`, `window_driver_rear_position`, `window_passenger_rear_position` | 0 (cerrada) a 100 | nada | marco de ventana en el borde del lienzo cuyo vidrio baja y sube con la posición; sonido de motor de ventana proporcional al recorrido; aumento del ruido de viento según velocidad y apertura | media |
| Abrir el techo corredizo ("abre el techo un poquito") | `sunroof_position` | 0 a 100 | nada | franja superior que se despeja mostrando cielo; cambio de luz en la cabina; ruido de aire | media |
| Abrir la cortinilla del techo | `sunshade_position` | 0 a 100 | nada | la franja superior pasa de opaca a translúcida; va antes del techo (política AUT-POL:005) | baja |
| Abrir o cerrar el baúl (requiere confirmación) | `trunk_door_position` | `"open"`, `"closed"` | nada | aviso persistente mientras está abierto (como un carro real), sonido de cierre, pictograma en la consola | baja |

## Luces

| Acción | Campos | Valores | Hoy | Ideas | Dificultad |
| --- | --- | --- | --- | --- | --- |
| Luz de lectura de un puesto | `reading_light_driver`, `reading_light_passenger`, `reading_light_driver_rear`, `reading_light_passenger_rear` | `true`/`false` | nada (referencia: `reading_light.js`) | resplandor en la esquina del puesto, clic mecánico; versión 3D: luz puntual en el interior | baja |
| Luz ambiental | `ambient_light` | `OFF`, `RED`, `GREEN`, `BLUE`, `YELLOW`, `WHITE`, `PINK`, `ORANGE`, `PURPLE`, `CYAN` | nada | borde inferior y laterales de la cabina teñidos con el color, transición suave; en 3D, tinte de las superficies del tablero | baja a media |
| Luces antiniebla ("exploradoras") | `fog_lights` | `true`/`false` | nada | testigo verde en el cluster o en el borde, haz bajo y ancho sobre la vía (3D o gradiente en la parte baja del lienzo), sonido de relé | media |
| Luces altas (requiere confirmación) | `head_lights_high_beams` | `true`/`false` | nada (las bajas sí las dibuja el motor) | testigo azul, haz más largo y más brillante sobre la vía; el motor expone `ego.setHeadlights` solo para las bajas, así que las altas son un buen reto 3D | media a alta |

## Clima y visibilidad

| Acción | Campos | Valores | Hoy | Ideas | Dificultad |
| --- | --- | --- | --- | --- | --- |
| Desempañar adelante o atrás ("está empañado, hágale") | `window_front_defrost`, `window_rear_defrost` | `true`/`false` | nada | vaho sobre el parabrisas que se va aclarando desde abajo en 10 a 20 s cuando se activa (y aparece si se desactiva en clima frío); sonido de ventilador que sube; relación con `fan_speed` y `fan_airflow_direction` (AUT-POL:010) | media |
| Dirección del aire y ventilador | `fan_airflow_direction`, `fan_speed` | `FEET`, `HEAD`, `WINDSHIELD` y combinaciones; 0 a 5 | solo números en la app Comfort | pictograma de la silueta con flechas; ruido de ventilador continuo proporcional al nivel; pequeña ondulación de aire en el borde del lienzo | baja a media |
| Aire acondicionado y circulación | `air_conditioning`, `air_circulation` | `true`/`false`; `AUTO`, `FRESH_AIR`, `RECIRCULATION` | solo números en Comfort | testigo A/C, sonido de compresor al arrancar, icono de recirculación; recuerda AUT-POL:011 (ventanas cerradas) | baja |
| Temperatura por zona ("yo con calor y mi copiloto con frío") | `climate_temperature_driver`, `climate_temperature_passenger` | 16 a 28 | números en Comfort | retroalimentación por zona: tinte frío o cálido efímero en el lado correspondiente al cambiar, con el valor grande y breve; voz de confirmación no, eso ya lo hace el asistente | baja |
| Calefacción de silla y timón ("ponme la silla calientica") | `seat_heating_driver`, `seat_heating_passenger`, `steering_wheel_heating` | 0 a 3 | número en Comfort | icono de silla con ondas por nivel, resplandor rojizo breve en la parte baja del lado correspondiente; el timón 3D (`src/wheel/`) podría teñirse | baja a media |

## Conducción y navegación

| Acción | Campos o evento | Valores | Hoy | Ideas | Dificultad |
| --- | --- | --- | --- | --- | --- |
| Entregar o retomar el control ("maneja tú") | no es un campo del vehículo: el modo autónomo vive en el motor (`dynamic.set({'controls.autodrive': true})`). Léelo en cada `tick` con `PromptDrive.on('tick', s => s.autodrive)` o desde el observable `ctx.handles().autodrive` (`.value`) | `true`/`false` | el carro se mueve solo, sin aviso | secuencia de transición de 2 a 3 s: borde de la pantalla que cambia de color, sonido ascendente, texto "El carro tiene el control"; y al revés, cuenta regresiva y aviso de tomar el timón | media |
| Fijar o cancelar un destino | `navigation_active` (y la app Nav de la consola) | `true`/`false` | mapa en la consola | confirmación breve del destino con distancia en el borde superior, sonido de ruta iniciada; al cancelar, aviso de que ya no hay guía | baja |
| Control crucero | motor: `PromptDrive.telemetry.state().cruise` en cada `tick` | | número en el cluster | testigo de crucero y velocidad objetivo al fijarlo | baja |

## Estados del asistente (no son campos del vehículo)

Llegan por `CabinFeedback.agent(state, detail)` y a tu feature por `onAgent(state, detail, ctx)`. El guion de demostración los envía en el orden en que ocurren en una interacción real; en el estudio los enviará WorlDrive.

| Estado | Cuándo | Hoy | Ideas | Dificultad |
| --- | --- | --- | --- | --- |
| `listening` | el micrófono está abierto | franja de estado de la consola, barras cian que respiran (referencia: `agent_indicator.js`) | alternativas: tira de luz en el tablero, un orbe en el cluster, sonido corto de apertura | baja |
| `thinking` | el modelo está procesando | barrido ámbar en la franja de la consola | otras metáforas de espera, sin sonido | baja |
| `speaking` | el asistente habla (`detail.text`, `detail.question`) | barras blancas rápidas en la franja de la consola | subtítulo breve del texto (la referencia no muestra texto a propósito), color distinto si es pregunta | baja |
| `confirm` | el asistente espera un "sí" (`detail.text`) | franja ámbar parpadeando | aviso persistente con lo que va a hacer (pictograma de la acción), hasta que llegue la acción o se cancele | media |
| `action` | se ejecutó una herramienta (`detail.tool`, `detail.args`) | destello verde con una marca de verificación | pictograma de la acción ejecutada (ventana, luz, temperatura) en vez de un destello genérico; cola si llegan varias seguidas | media |
| `error` | la herramienta falló o la petición no se pudo cumplir (`detail.text`) | destello rojo con una cruz y tono grave | distinguir "no puedo" de "no debo" (imposible frente a inseguro) | baja |

## Propuestas propias

Si quieres algo que no está aquí (por ejemplo, un resumen visual de todo lo que el asistente cambió en el último minuto, o una vista para el acompañante), vale, siempre que reaccione a campos de `VehicleState` o a estados del asistente, respete las reglas y tenga su nota de diseño. Pregunta en el canal de ayuda si dudas de si cabe en 24 horas.
