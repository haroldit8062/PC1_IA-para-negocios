# Plantilla 1 — Problem Statement Canvas
## Framework PROMPT | Fase P — Problema de Negocio
### AD5018 Inteligencia Artificial para Negocios | UTEC

---

**Equipo:**
- Integrante 1: Harold Inca
- Integrante 2: Diego Requena
- Integrante 3: Jennifer Patiño

**Fecha de entrega:** 18/09/2026
**Versión del canvas:** v2 (alcance ajustado según feedback docente)

---

## SECCIÓN 1 — Definición del problema

### 1.1 Usuario afectado

Personas con discapacidad auditiva (sordera total o parcial) que pasan tiempo solas en su hogar, especialmente en situaciones donde no hay otra persona oyente cerca que pueda advertirles sobre sonidos críticos de seguridad en su entorno.

### 1.2 Problema específico

Estas personas pueden no percibir oportunamente una **alarma de incendio o detector de humo activado** en su vivienda. Al no contar con el canal auditivo, pueden no enterarse de una situación de riesgo real hasta que sea demasiado tarde para reaccionar con seguridad.


### 1.3 Causa raíz

La causa raíz es que una alarma de incendio o detector de humo se comunica exclusivamente mediante una señal acústica, mientras que el usuario tiene acceso limitado o nulo a ese canal sensorial. Como consecuencia, una alerta de seguridad crítica puede no ser percibida en el momento en que ocurre.

### 1.4 Consecuencia medible

Una persona con discapacidad auditiva puede no identificar oportunamente una alarma de incendio real, lo que incrementa el riesgo de no reaccionar a tiempo ante una situación de peligro y reduce su autonomía y seguridad dentro del hogar.

Para esta propuesta académica se trabajará con un **baseline provisional** que permitirá estructurar la PC1:

- **Identificación correcta sin apoyo del MVP: 40 %.**
- **Tiempo promedio de reacción: 6 segundos.**

Estos valores se consideran **estimaciones académicas provisionales**, no resultados de una prueba real. La validación posterior del MVP se realizará con una muestra pequeña y manejable para el curso:

- **5 usuarios**;
- **10 eventos por usuario**;
- **50 observaciones de prueba en total**.

Los indicadores principales serán:

1. porcentaje de alarmas de incendio identificadas correctamente;
2. tiempo promedio desde que ocurre el sonido hasta que el usuario recibe la notificación.

> **Aclaración:** las muestras de audio mencionadas en el proyecto corresponden a audios de entrenamiento del modelo, no a personas participantes.

### 1.5 Declaración del problema — formato obligatorio

Las personas con discapacidad auditiva que permanecen solas en el hogar tienen dificultad para identificar oportunamente una alarma de incendio o detector de humo activado, debido a su acceso limitado o nulo al canal auditivo, lo que incrementa la posibilidad de no reaccionar a tiempo ante una situación de riesgo real y reduce su seguridad y autonomía.

---

## SECCIÓN 2 — Filtro de validación IA

| Pregunta | SÍ/NO | Justificación |
|---|---|---|
| ¿Una hoja de cálculo o un formulario resuelve esto? | NO | El problema exige reconocer un patrón acústico capturado por un micrófono en tiempo real. |
| ¿El problema escala con el volumen de datos o usuarios? | SÍ | El desempeño del clasificador puede mejorar al incorporar más ejemplos y mayor diversidad de condiciones (distintos modelos de alarma, distancias, ruido ambiental). |
| ¿Hay un patrón repetitivo que un humano reconoce pero tarda en procesar? | SÍ | La alarma de incendio presenta un patrón acústico consistente y diferenciable del ruido de fondo. |
| ¿El problema requiere generar contenido, responder preguntas o razonar en lenguaje natural? | SÍ, de forma acotada | El resultado del clasificador debe convertirse en un mensaje de alerta claro enviado por notificación (Telegram/WhatsApp). |
| ¿Necesitas tanto predecir como explicar, comunicar o actuar? | SÍ | El componente analítico clasifica el sonido y el componente generativo redacta y envía la notificación al usuario. |

---

## SECCIÓN 3 — Los dos componentes del producto

### 3.1 Componente analítico

**Tarea:** Clasificación binaria.

**Clases:**
- alarma_incendio (incluye detector de humo)
- ruido_de_fondo

**Nivel:** **A3**

El modelo se entrenará en una primera versión, se evaluará su desempeño (matriz de confusión, recall), se recolectarán nuevos datos en las condiciones donde falle más y luego se reentrenará para comparar la mejora. Al trabajar con solo 2 clases, el equipo puede invertir ese esfuerzo de reentrenamiento en profundidad real —más variantes de alarmas, más condiciones de ruido— en vez de dispersarlo entre múltiples categorías.

### 3.2 Componente generativo

**Nivel:** **G1**

La capa de lenguaje recibe:
- clase predicha (alarma_incendio / ruido_de_fondo);
- score de confianza;
- nivel de criticidad.

Con esa información genera un mensaje breve y claro, enviado como **notificación por Telegram/WhatsApp**:
- Si detecta alarma_incendio con confianza suficiente → alerta de seguridad.
- Si detecta ruido_de_fondo (o confianza insuficiente) → mensaje tranquilizador confirmando que no hay ninguna alarma activa, para que el usuario no quede con incertidumbre sobre si el sistema sigue funcionando.

### 3.3 Patrón de conexión

**Patrón 1 — Modelo → lenguaje**

```text
Audio → Clasificador (2 clases) → Clase + score → Criticidad/umbral → G1 → Notificación (Telegram/WhatsApp)
```

### 3.4 Dónde va la ambición del equipo

- [X] **Profundidad en A3 y piso en G1**

La prioridad es mejorar la confiabilidad de la detección de la alarma de incendio. Un falso negativo (no detectar una alarma real) es el error más costoso posible en este producto, mucho más grave que una notificación redactada con poca sofisticación. Reducir el alcance a 2 clases permite dedicar el esfuerzo de recolección y reentrenamiento a que esa única detección crítica sea lo más confiable posible.

### 3.5 Métricas principales

- recall de la clase alarma_incendio (métrica prioritaria);
- precision de la clase alarma_incendio;
- accuracy global;
- matriz de confusión (2×2);
- porcentaje de alarmas identificadas correctamente;
- tiempo de reacción / tiempo hasta la notificación.

### 3.6 Solo si el equipo solicita excepción

No aplica. El equipo sí entrenará un modelo de clasificación de audio.

---

## SECCIÓN 4 — Autoevaluación

| Pregunta | Respuesta |
|---|---|
| ¿El problema está descrito sin mencionar tecnología? | Sí |
| ¿La consecuencia tiene indicadores medibles? | Sí |
| ¿Los dos componentes están definidos? | Sí |
| ¿El patrón de conexión está elegido? | Sí |
| ¿Está clara la ambición del equipo? | Sí |
| ¿Está diferenciada la cantidad de audios frente a la cantidad de usuarios? | Sí |
| ¿Todos los integrantes pueden explicar el canvas? | Sí |
