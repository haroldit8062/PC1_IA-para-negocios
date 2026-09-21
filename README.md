# Alertly
## Proyecto Integrador de IA para Negocios — AD5018
### Universidad de Ingeniería y Tecnología (UTEC)

---

## 1. Información del equipo

**Integrantes**
- Harold Inca
- Diego Requena
- Jennifer Patiño

**Curso:** AD5018 — Inteligencia Artificial para Negocios  
**Framework:** PROMPT v2.0  
**Semana actual:** Semana 5  
**Próximo hito:** PC1 — Semana 6  
**Fin del ciclo académico:** Semana 16 
**Estado del proyecto:** En diseño y preparación para PC1 (v2 — alcance ajustado tras feedback docente)

---

## 2. Nombre del MVP

**Alertly**

---

## 3. Resumen del proyecto

Alertly es un MVP orientado a personas con discapacidad auditiva que pueden tener dificultad para identificar oportunamente una alarma de incendio en su hogar.

El proyecto busca detectar dos categorías de sonido —**alarma de incendio (incluye detector de humo)** y **ruido de fondo**— mediante un modelo de clasificación de audio entrenado por el equipo. El resultado del modelo se conecta con una capa de lenguaje que transforma la clase detectada, el nivel de confianza y la criticidad del evento en una **notificación por Telegram/WhatsApp**, breve y comprensible.

> **Nota de versión:** el alcance original consideraba 5 categorías de sonido (alarma, detector de humo, timbre, bocina, ruido de fondo) y una interfaz web/app móvil. Tras recibir feedback del docente, el equipo redujo el alcance a **2 clases** —priorizando profundidad en el escenario de mayor riesgo de vida— y cambió el canal de entrega de una interfaz visual propia a **notificaciones por Telegram/WhatsApp**, lo cual simplifica el desarrollo sin perder el valor central del producto.

El proyecto combina:

- **Componente analítico A3:** clasificación binaria de audio con reentrenamiento a partir de errores observados.
- **Componente generativo G1:** prompt con contexto fijo para convertir la salida del modelo en una notificación accesible (alerta o mensaje tranquilizador).
- **Patrón de conexión:** Modelo → Lenguaje.

---

## 4. Problema

Las personas con discapacidad auditiva que permanecen solas en el hogar tienen dificultad para identificar oportunamente una alarma de incendio o detector de humo activado, debido a su acceso limitado o nulo al canal auditivo, lo que incrementa la posibilidad de no reaccionar a tiempo ante una situación de riesgo real y reduce su seguridad y autonomía.

---

## 5. Usuario objetivo

Personas con discapacidad auditiva total o parcial que:

- pasan periodos de tiempo solas en casa, sin una persona oyente cerca;
- necesitan reconocer si suena una alarma de incendio en su entorno;
- pueden utilizar un dispositivo con micrófono para la captura de audio;
- cuentan con una cuenta de Telegram o WhatsApp donde recibir notificaciones.

---

## 6. Propuesta de valor

Detectar si suena una alarma de incendio en el entorno del usuario y enviarle de inmediato una notificación clara por Telegram/WhatsApp, indicando el nivel de certeza. Cuando no se detecta ninguna alarma, el sistema también confirma activamente que todo está tranquilo, para que el usuario sepa que sigue protegido sin necesidad de revisar nada.

---

## 7. Alcance del MVP

### Incluye

- captura de audio desde el micrófono;
- clasificación de dos categorías:
  - alarma de incendio (incluye detector de humo);
  - ruido de fondo;
- score de confianza;
- umbrales iniciales por clase;
- clasificación de criticidad;
- notificación por Telegram/WhatsApp (alerta + mensaje tranquilizador);
- capa generativa G1;
- expresión de incertidumbre en casos de baja confianza;
- despliegue de la captura de audio mediante página web mínima;
- reentrenamiento del modelo para cumplir el nivel A3;
- validación con usuarios y registro de resultados.

### No incluye

- reconocimiento de más de 2 categorías de sonido;
- llamadas automáticas a servicios de emergencia;
- geolocalización;
- almacenamiento permanente de audio;
- reconocimiento o transcripción de conversaciones;
- aplicación móvil nativa;
- interfaz visual propia (dashboard, historial de alertas);
- integración con wearables;
- RAG;
- agentes autónomos;
- automatizaciones G4.

---

## 8. Diseño de IA

### Componente analítico — A3

El modelo clasificará fragmentos de audio en dos categorías:

```text
alarma_incendio
ruido_de_fondo
```

La estrategia A3 será:

1. entrenar una primera versión del modelo;
2. medir su desempeño (recall, matriz de confusión 2×2);
3. analizar errores y condiciones problemáticas;
4. recolectar nuevas muestras dirigidas a esos errores;
5. reentrenar;
6. comparar V1 vs. V2.

### Componente generativo — G1

La capa G1 recibe:

```text
clase predicha + score de confianza + criticidad + umbral
```

y genera una notificación breve, clara y proporcional al nivel de confianza, enviada por Telegram/WhatsApp. Si la clase es `ruido_de_fondo` (o la confianza de `alarma_incendio` no supera el umbral), el mensaje generado es tranquilizador, confirmando que no hay alarma activa.

### Flujo

```text
Micrófono
   ↓
Audio
   ↓
Modelo de clasificación A3 (2 clases)
   ↓
Clase + score de confianza
   ↓
Criticidad + umbral
   ↓
Capa G1
   ↓
Notificación por Telegram/WhatsApp
   ↓
Usuario
```

---

## 9. Datos

### Dataset inicial propuesto

| Clase | Muestras de audio objetivo |
|---|---:|
| Alarma de incendio | 60 |
| Ruido de fondo | 70 |
| **Total** | **130** |

> **Importante:** las 130 observaciones corresponden a muestras de audio para entrenar el modelo, no a personas participantes. El volumen por clase es mayor al de la versión anterior (5 clases), ya que el esfuerzo de recolección ahora se concentra en solo 2 categorías.

### Fuentes previstas

- Freesound.org
- Zapsplat
- grabaciones propias

La trazabilidad de fuentes, licencias y disponibilidad se documenta en la Plantilla 2.

---

## 10. Métricas y OKRs propuestos

Para estructurar la PC1 se utilizan valores académicos provisionales que deberán sustituirse por resultados reales cuando se disponga de evidencia.

| Indicador | Baseline / referencia | Meta |
|---|---:|---:|
| Identificación correcta de eventos | 40 % provisional | ≥ 80 % |
| Tiempo promedio hasta la notificación | 6 s provisional | ≤ 3 s |
| Baseline técnico de clase mayoritaria | 53.8 % accuracy (ruido_de_fondo) | Superarlo — foco en recall, no en accuracy |
| Accuracy global del modelo | — | ≥ 80 % |
| Recall en alarma_incendio | — | ≥ 85 % |
| Precision en alarma_incendio | — | ≥ 80 % (para no saturar con falsas alarmas) |
| Mejora después del reentrenamiento | V1 | ≥ +5 pp de recall en alarma_incendio |
| Calidad del componente G1 | — | ≥ 90 % de 20 casos de prueba |

> **Nota:** al pasar de 5 a 2 clases, el baseline técnico de "predecir siempre la clase mayoritaria" sube de 23.1 % a 53.8 % de accuracy. Por eso la métrica prioritaria del proyecto es el **recall de alarma_incendio** y no el accuracy general: un modelo que siempre predijera "ruido de fondo" tendría accuracy alto pero 0 % de recall en la clase que realmente importa.

---

## 11. Validación propuesta

La validación del MVP se realizará con una muestra pequeña y manejable acorde al alcance de un proyecto universitario:

- **5 usuarios**
- **10 eventos por usuario**
- **50 observaciones totales**

Se registrará:

- evento presentado (alarma_incendio o ruido_de_fondo);
- si fue identificado correctamente;
- tiempo hasta recibir la notificación;
- comprensión del mensaje recibido;
- errores observados (falso positivo / falso negativo);
- comentarios del usuario.

---

## 12. Stack tecnológico propuesto

| Capa | Tecnología |
|---|---|
| Entrenamiento del modelo | Teachable Machine — Audio |
| Inferencia | TensorFlow.js |
| Captura de audio | Web Audio API (página mínima, sin interfaz visual compleja) |
| Capa G1 | API de un modelo de lenguaje con prompt fijo |
| Notificaciones | Telegram Bot API (o WhatsApp Business API) |
| Backend | Función serverless |
| Despliegue | Vercel |
| Repositorio | GitHub |

El stack deberá ser verificado mediante una prueba técnica antes de considerarse definitivo.

---

## 13. Estado del proyecto — Semana 5

### Completado

- [x] Definición del usuario.
- [x] Definición del problema.
- [x] Causa raíz.
- [x] Consecuencia medible planteada.
- [x] Elección de A3 + G1.
- [x] Patrón Modelo → Lenguaje.
- [x] Ajuste de alcance a 2 clases (feedback docente).
- [x] Cambio de canal a notificación por Telegram/WhatsApp (feedback docente).
- [x] Alcance preliminar del MVP.
- [x] Inventario inicial de datos.
- [x] Diseño preliminar del flujo.
- [x] Definición inicial de métricas.
- [x] Propuesta de stack.
- [x] Plan preliminar de validación.

### Por cerrar antes de PC1

- [ ] Completar la recolección real de audios (alarma_incendio y ruido_de_fondo).
- [ ] Registrar el número final de muestras por clase.
- [ ] Documentar licencias y fuentes de los audios.
- [ ] Definir e implementar el bot de Telegram/WhatsApp.
- [ ] Verificar técnicamente el stack.
- [ ] Formalizar la tabla de criticidad.
- [ ] Confirmar los umbrales iniciales.
- [ ] Completar el resumen ejecutivo.
- [ ] Completar el cronograma de construcción.
- [ ] Preparar la presentación de PC1.
- [ ] Revisar que todos los integrantes puedan explicar cualquier fase de la propuesta.

---

## 14. Estructura del repositorio

```text
/
├── README.md
├── resumen_ejecutivo.md
├── presentacion_pc1.pdf
├── cronograma.md
│
├── plantillas/
│   ├── plantilla_1_problem_statement.md
│   ├── plantilla_2_data_readiness.md
│   └── plantilla_3_ai_product_canvas.md
│
├── datos/
│   ├── alarma_incendio/
│   └── ruido_de_fondo/
│
└── evidencia/
    ├── fuentes_datos.md
    ├── licencias_audios.md
    └── pruebas_stack.md
```

---

## 15. Próximos pasos

### Semana 5
- cerrar las tres plantillas de PC1;
- organizar y documentar el dataset (2 clases);
- verificar acceso a las herramientas (Teachable Machine, Telegram/WhatsApp API);
- construir el resumen ejecutivo;
- elaborar el cronograma.

### Semana 6
- entregar PC1;
- sustentar problema, datos, producto, stack y plan de construcción;
- dejar congelados los niveles A3 + G1, el patrón de conexión, el alcance de 2 clases y los KRs comprometidos.

### Después de PC1
- construir la primera versión del modelo;
- integrar la capa G1;
- configurar el bot de Telegram/WhatsApp;
- desplegar una primera versión funcional;
- evaluar V1;
- recolectar nuevos datos;
- reentrenar V2;
- validar con usuarios;
- medir resultados y documentar riesgos.

> Aunque el ciclo académico termina en la **Semana 18**, el cronograma operativo del proyecto debe seguir los hitos específicos definidos por la guía del curso para PC1 y PC2.

---

## 16. Documentos principales

- `plantillas/plantilla_1_problem_statement.md` — Fase P
- `plantillas/plantilla_2_data_readiness.md` — Fase R
- `plantillas/plantilla_3_ai_product_canvas.md` — Fase O
- `resumen_ejecutivo.md` — síntesis de P + R + O
- `cronograma.md` — plan de construcción y responsables
- `presentacion_pc1.pdf` — sustentación de la propuesta

---
