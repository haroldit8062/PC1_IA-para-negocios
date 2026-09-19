# Plantilla 3 — AI Product Canvas
## Framework PROMPT | Fase O — Diseño del Producto
### AD5018 Inteligencia Artificial para Negocios | UTEC

---

**Equipo:**
- Integrante 1: Harold Inca
- Integrante 2: Diego Requena
- Integrante 3: Jennifer Patiño

**Fecha de entrega:** 18/09/2026
**Versión:** v2 — alcance reducido a 2 clases, notificación por Telegram/WhatsApp (ajuste tras feedback docente)

---

# SECCIÓN 1 — AI Product Canvas

## 1.1 Nombre del MVP

**Alerta Sonora Accesible**

## 1.2 Problema

Las personas con discapacidad auditiva que permanecen solas en el hogar tienen dificultad para identificar oportunamente una alarma de incendio o detector de humo activado —debido a su acceso limitado o nulo al canal auditivo—, lo que incrementa la posibilidad de no reaccionar a tiempo ante una situación de riesgo real y reduce su seguridad y autonomía.

## 1.3 Usuario objetivo

Personas con discapacidad auditiva total o parcial que pasan periodos de tiempo solas en casa, cuentan con un dispositivo con micrófono, y tienen una cuenta de Telegram o WhatsApp donde puedan recibir notificaciones.

## 1.4 Propuesta de valor

Detectar si suena una alarma de incendio en el entorno del usuario y enviarle de inmediato una notificación clara por Telegram/WhatsApp, indicando el nivel de certeza. Cuando no se detecta ninguna alarma, el sistema también confirma activamente que todo está tranquilo, para que el usuario sepa que sigue protegido sin necesidad de revisar nada.

---

# SECCIÓN 2 — Componentes

## Analítico

**A3 — Clasificación de audio (binaria)**

Clases:
- alarma_incendio (incluye detector de humo)
- ruido_de_fondo

> **Nota de alcance:** se redujo de 5 a 2 clases tras el feedback del docente, priorizando profundidad en el escenario de mayor riesgo de vida en lugar de amplitud en varias categorías.

## Generativo

**G1 — Prompt con contexto fijo**

Entrada:
- clase (alarma_incendio / ruido_de_fondo);
- confianza;
- criticidad;
- umbral.

Salida:
- notificación breve enviada por Telegram/WhatsApp;
- nivel de certeza;
- mensaje tranquilizador cuando no hay alarma detectada.

---

# SECCIÓN 3 — Flujo

```text
Micrófono
↓
Audio
↓
Modelo A3 (2 clases)
↓
Clase + confianza
↓
Criticidad + umbral
↓
G1
↓
Notificación por Telegram/WhatsApp
↓
Usuario
```

---

# SECCIÓN 4 — System Prompt

```text
Eres la capa de comunicación de un sistema de accesibilidad para personas
con discapacidad auditiva. Tu salida se envía como notificación de
Telegram/WhatsApp.

Recibirás una clase de sonido (alarma_incendio o ruido_de_fondo), un score
de confianza, un nivel de criticidad y un umbral.

Genera una notificación breve y clara.

Reglas:
- No inventes información.
- No afirmes que hay una alarma si la confianza está bajo el umbral.
- Si la clase es ruido_de_fondo (o la confianza de alarma_incendio no
  supera el umbral), redacta un mensaje tranquilizador confirmando que
  no se detecta ninguna alarma y que el sistema sigue activo.
- Si la clase es alarma_incendio y supera el umbral, prioriza claridad
  y urgencia sin generar pánico innecesario.
- Máximo dos oraciones.
```

---

# SECCIÓN 5 — Model Design Canvas

## Dataset inicial

| Clase | Muestras |
|---|---:|
| Alarma de incendio | 60 |
| Ruido de fondo | 70 |
| **Total** | **130 audios** |

> **130 corresponde a audios de entrenamiento, no a personas.** El volumen por clase es mayor al de la versión con 5 clases, ya que el esfuerzo de recolección ahora se concentra en solo 2 categorías.

## Baseline técnico

Clase mayoritaria (ruido_de_fondo):

**70 / 130 = 53.8 % de accuracy estimada.**

> Este baseline es más alto que el de la versión de 5 clases (23.1 %), lo cual es esperable en un problema binario. Por eso la métrica que realmente importa no es el accuracy, sino el **recall de alarma_incendio**: un modelo que siempre dijera "ruido_de_fondo" tendría 53.8 % de accuracy pero 0 % de recall en la clase que realmente importa.

## Baseline de negocio provisional

- identificación correcta sin MVP: **40 %**;
- tiempo promedio de reacción: **6 segundos**.

Estos valores son estimaciones académicas provisionales para estructurar la PC1.

## Métricas

- recall de alarma_incendio (métrica prioritaria);
- precision de alarma_incendio;
- accuracy global;
- matriz de confusión (2×2).

## Metas técnicas

- recall de alarma_incendio ≥ 85 %;
- precision de alarma_incendio ≥ 80 % (para evitar saturar al usuario con falsas alarmas);
- accuracy global ≥ 80 %.

---

# SECCIÓN 6 — Umbrales

| Clase | Criticidad | Umbral inicial |
|---|---|---:|
| Alarma de incendio | Alta | 0.80 |
| Ruido de fondo | Baja | 0.70 |

> Umbral más bajo en alarma_incendio (0.80 vs. el que tendría una clase de baja criticidad) para minimizar falsos negativos: el costo de no avisar una alarma real es mucho mayor que el de una falsa alarma ocasional.

---

# SECCIÓN 7 — OKRs

## Objetivo

Mejorar la capacidad del usuario para identificar oportunamente una alarma de incendio en su hogar mediante un MVP accesible por notificaciones.

### KR1
Pasar de un baseline provisional de **40 %** de identificación correcta a **≥ 80 %**.

### KR2
Reducir el tiempo promedio de reacción de **6 segundos** a **≤ 3 segundos**, medido desde que ocurre el sonido hasta que llega la notificación.

### KR3
Superar el baseline técnico de **53.8 %** (accuracy) y alcanzar:
- accuracy ≥ 80 %;
- recall de alarma_incendio ≥ 85 %.

### KR4
Tras reentrenar, mejorar al menos **5 puntos porcentuales de recall** en la clase alarma_incendio.

### KR5
Lograr que **≥ 90 % de 20 casos de prueba G1** generen una notificación correcta y comprensible (tanto de alerta como de mensaje tranquilizador).

---

# SECCIÓN 8 — Validación con usuarios

La validación propuesta es:

- **5 usuarios**;
- **10 eventos por usuario**;
- **50 observaciones en total**.

Cada usuario probará una secuencia de eventos que combine sonidos de alarma_incendio y ruido de fondo, recibiendo las notificaciones en su cuenta de Telegram/WhatsApp vinculada.

Se registrará:

- si el evento fue identificado;
- tiempo de reacción (desde el sonido hasta la notificación);
- tipo de error (falso negativo / falso positivo);
- comprensión del mensaje recibido;
- observaciones del usuario.

> Este tamaño es apropiado para un MVP universitario y además supera el mínimo de 5 usuarios requerido por la rúbrica para aspirar al nivel excelente en validación.

---

# SECCIÓN 9 — Estrategia A3

1. Entrenar V1 con las 2 clases.
2. Evaluar errores (matriz de confusión 2×2).
3. Identificar los tipos de sonido que más confunden al modelo (ej. ruidos domésticos agudos vs. alarma).
4. Recolectar 15–20 audios adicionales en esas condiciones problemáticas.
5. Reentrenar.
6. Comparar V1 vs. V2.
7. Buscar mejora ≥ 5 pp de recall en alarma_incendio.

---

# SECCIÓN 10 — Stack

| Capa | Tecnología |
|---|---|
| Modelo | Teachable Machine |
| Inferencia | TensorFlow.js |
| Audio | Web Audio API |
| Captura de audio | Página web mínima (solo activa el micrófono, sin interfaz visual compleja) |
| G1 | API de LLM |
| Notificaciones | Telegram Bot API (o WhatsApp Business API) |
| Backend | Función serverless |
| Despliegue | Vercel |
| Repositorio | GitHub |

> **Cambio respecto a la versión anterior:** se elimina la idea de una interfaz visual propia o app móvil nativa. El "producto" que ve el usuario final es simplemente su chat de Telegram/WhatsApp recibiendo notificaciones — la página web solo corre en segundo plano para capturar y clasificar el audio.

---

# SECCIÓN 11 — Alcance

## Incluye
- captura de audio;
- dos clases (alarma_incendio, ruido_de_fondo);
- confianza;
- umbrales diferenciados por clase;
- notificación por Telegram/WhatsApp (alerta + mensaje tranquilizador);
- G1;
- expresión de incertidumbre;
- reentrenamiento A3;
- validación con 5 usuarios.

## No incluye
- reconocimiento de más de 2 categorías;
- llamadas automáticas de emergencia;
- geolocalización;
- almacenamiento permanente de audio;
- aplicación móvil nativa;
- interfaz visual propia (dashboard, historial, etc.);
- wearables;
- RAG;
- agentes;
- automatizaciones adicionales.

---

# SECCIÓN 12 — Criterios de éxito

1. ≥ 80 % de alarmas de incendio identificadas correctamente.
2. ≤ 3 segundos de tiempo promedio hasta la notificación.
3. accuracy ≥ 80 %.
4. recall de alarma_incendio ≥ 85 %.
5. mejora ≥ 5 pp tras reentrenamiento.
6. ≥ 90 % de casos G1 correctos (alerta y mensaje tranquilizador).
7. Bot de Telegram/WhatsApp funcional y accesible sin instalación adicional.
8. 5 usuarios completan la prueba.
9. 50 observaciones documentadas.

---

# SECCIÓN 13 — Resumen de cifras

| Elemento | Valor |
|---|---:|
| Audios de entrenamiento | 130 |
| Clases | 2 |
| Personas para validación | 5 |
| Eventos por persona | 10 |
| Observaciones humanas | 50 |
| Baseline técnico (accuracy) | 53.8 % |
| Baseline negocio provisional | 40 % |
| Tiempo base provisional | 6 s |
| Meta identificación | ≥ 80 % |
| Meta reacción | ≤ 3 s |
| Meta accuracy | ≥ 80 % |
| Meta recall crítico (alarma_incendio) | ≥ 85 % |
| Mejora A3 | ≥ 5 pp |
| Casos G1 | 20 |
| Meta G1 | ≥ 90 % |
