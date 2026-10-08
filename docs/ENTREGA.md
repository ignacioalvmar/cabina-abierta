# Entrega, verificación y evaluación

## Qué se entrega

Antes de la fecha y hora límite de tu cohorte (hora de Colombia). Sin prórrogas individuales: las cuatro cohortes se tratan igual.

1. **Pull request** desde tu fork hacia `prompt-drive`, rama `cabina-abierta/<tu-equipo>`, con la plantilla del repositorio rellenada: equipo, cohorte, enlace al video, tabla de features, checklist. El PR contiene únicamente `src/feedback/features/<id>.js` (uno por feature), el bundle regenerado `static/js/feedback.js`, recursos en `static/feedback/<id>/` si los hay, y `docs/feedback/<id>.md` con la nota de diseño de cada feature.
2. **Video, inglés, máximo 3 minutos.** Grabación de pantalla con voz, usando el guion de demostración (`demo/`) para disparar las acciones. Para cada feature: el estado antes, la frase de la persona, la acción y lo que se percibe. Luego la decisión de diseño principal y una limitación que conozcas. Enlace en el PR (YouTube no listado, Drive o la plataforma del curso).
3. **Nota de diseño por feature** (`docs/feedback/<id>.md`, plantilla en `docs/feedback/TEMPLATE_nota_de_diseno.md`): qué viste en el estudio, qué debería percibir la persona, alternativas, decisión, implementación, limitaciones.
4. **`REFLEXION.md`** (300 a 500 palabras, español o inglés) y **`AI_LOG.md`** en la raíz de tu fork, con las plantillas de `templates/`. No van en el PR.

Entrega mínima válida: una feature que funcione, su nota, el video y los dos archivos de reflexión. Entregas incompletas se evalúan con lo que haya; lo que falte puntúa cero.

## Protocolo de verificación (lo que hace el jurado antes de puntuar)

1. Baja la rama del PR y ejecuta `npm run build`. Si falla, se intenta una vez `npm run build:feedback`; si sigue fallando, la funcionalidad puntúa cero y el resto se evalúa igual.
2. Abre `http://localhost:3000/?autostart=1&hideMenu=1`, pulsa **begin**, y corre el guion de demostración completo con **Reproducir todo**, más las acciones manuales que el video muestre.
3. Para cada feature declarada en el PR, anota: se percibe (sí, parcial, no), coincide con el video (sí, no), respeta el estado inicial tras recargar (sí, no), errores en la consola del navegador (sí, no), escribe en `VehicleState` (sí, no).
4. Una feature que no coincide con el video, o que escribe en `VehicleState`, no cuenta como funcional. Si el PR declara más de tres features, se evalúan las tres primeras de la tabla.

## Pesos

| Componente | Peso | Cómo se mide |
| --- | --- | --- |
| Funcionalidad verificada | 30 % | protocolo anterior, por feature; promedio de las features declaradas (máximo 3) |
| Calidad del diseño | 25 % | rúbrica del jurado sobre lo que se percibe |
| Nota de diseño | 10 % | rúbrica: problema, alternativas, decisión justificada, limitaciones honestas |
| Calidad e integrabilidad del código | 15 % | rúbrica sobre el PR |
| Video | 10 % | rúbrica: muestra en vez de afirmar, explica una decisión, inglés comprensible, dentro de 3 minutos |
| Reflexión y registro de IA | 10 % | rúbrica: especificidad, honestidad, una lección transferible |

Una feature excelente puede valer más que tres regulares: la funcionalidad se promedia y el diseño se juzga por lo mejor que entregaste, no por la cantidad.

## Rúbrica del jurado (0 a 4 por fila)

- **Funcionalidad verificada.** 0: no se percibe nada o el build falla. 2: se percibe, pero con fallos (no respeta el estado inicial, errores en consola, difiere del video en algo menor). 4: se percibe exactamente como en el video, desde el inicio y tras recargar, sin errores.
- **Calidad del diseño.** 0: confunde o distrae (tapa la vía, parpadea, suena fuerte). 2: se entiende qué cambió, pero el canal, el tiempo o la estética son mejorables. 4: se entiende qué cambió y cuánto, de un vistazo, sin apartar la atención de la vía; coherente con la cabina; sutil cuando debe serlo y claro cuando importa (por ejemplo, el baúl abierto o la entrega del control).
- **Nota de diseño.** 0: falta o describe solo el código. 2: problema y decisión descritos. 4: alternativas reales descartadas con razones, referencias a principios de HMI o a carros reales, limitaciones honestas y una idea de cómo probarla con usuarios.
- **Código e integrabilidad.** 0: toca bundles a mano, escribe en el estado, no compila. 2: sigue la estructura, compila, funciona, con detalles (nombres poco claros, código muerto, recursos sin licencia). 4: un archivo limpio por feature, bundle regenerado, sin dependencias, defensivo (handles nulos, estado inicial), listo para hacer merge sin cambios.
- **Video.** 0: no hay o no se entiende. 2: muestra las features. 4: muestra, explica una decisión con claridad, dura 3 minutos o menos, inglés comprensible.
- **Reflexión y registro.** 0: faltan. 2: genéricos. 4: incidentes concretos, honestidad sobre lo que la IA hizo mal, una lección que servirá en otro proyecto.

Dos personas del jurado por entrega; las diferencias de más de un punto por fila se discuten. El jurado anota las observaciones de verificación antes de abrir la rúbrica.

## Integración y reconocimiento

Las features con 4 en funcionalidad y al menos 3 en diseño y en código son candidatas a integrarse en `prompt-drive`. La organización puede pedir cambios pequeños en el PR (nombres, un ajuste de volumen, un comentario) antes del merge; se integran los archivos de la feature y su nota de diseño (no `REFLEXION.md` ni `AI_LOG.md`, que se quedan en el fork); los autores quedan en `docs/feedback/CREDITS.md` y en el historial de Git. Si dos equipos resolvieron la misma acción, se integra la mejor y la otra se cita en la nota de la integrada.

Por cohorte: ganador y segundo puesto, anunciados dentro de las 72 horas siguientes, con la retroalimentación de verificación y la rúbrica para todos los participantes. Al terminar la gira: la mejor feature entre las cuatro cohortes. Los mejores equipos quedan invitados a una conversación sobre los programas de maestría y doctorado de THI.
