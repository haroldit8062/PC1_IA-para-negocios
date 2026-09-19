# Resumen Ejecutivo
## Proyecto Integrador de IA para Negocios — AD5018
### MVP: Alertly

**Equipo:** Harold Inca · Diego Requena · Jennifer Patiño  
**Semana actual:** 5  
**Hito próximo:** PC1 — Semana 6

---

## Problema y usuario

El proyecto aborda una necesidad de accesibilidad de **personas con discapacidad auditiva total o parcial** que pasan tiempo solas en el hogar, sin una persona oyente cerca.

El problema central es que una alarma de incendio o detector de humo se comunica exclusivamente mediante una señal acústica. Como resultado, una persona con acceso limitado o nulo al canal auditivo puede no identificar oportunamente esta alerta de seguridad, lo que puede retrasar su reacción frente a una situación de riesgo real y reducir su autonomía.

Para estructurar la propuesta en PC1 se utilizará un **baseline académico provisional** de:

- **40 % de identificación correcta** de eventos sin apoyo del MVP.
- **6 segundos de tiempo promedio de reacción.**

Estos valores son provisionales y deberán sustituirse por resultados reales cuando se realice la validación.

> **Nota de alcance:** tras recibir feedback del docente, el equipo redujo el proyecto de 5 categorías de sonido a **2 clases**, priorizando profundidad en el escenario de mayor riesgo de vida (alarma de incendio) en lugar de amplitud en varias categorías. Asimismo, se cambió el canal de entrega de una interfaz visual propia a **notificaciones por Telegram/WhatsApp**.

---

## Solución propuesta

El MVP, denominado **Alertly**, busca detectar si suena una alarma de incendio en el entorno del usuario y enviarle de inmediato una notificación clara por Telegram/WhatsApp.

El producto integra dos componentes:

### Componente analítico — A3
Un modelo de **clasificación de audio binaria** entrenado por el equipo que distingue dos clases:

- alarma_incendio (incluye detector de humo);
- ruido_de_fondo.

La estrategia A3 consiste en entrenar una primera versión, analizar errores, recolectar nuevos datos en los casos problemáticos y reentrenar para medir la mejora.

### Componente generativo — G1
Una capa de lenguaje con contexto fijo que recibe:

- clase predicha;
- score de confianza;
- criticidad;
- umbral.

Con esa información genera una **notificación por Telegram/WhatsApp**, breve, accesible y proporcional al nivel de certeza. Si la confianza es insuficiente, o si la clase detectada es ruido_de_fondo, el sistema envía un mensaje tranquilizador confirmando que no hay ninguna alarma activa, en lugar de afirmar que el evento ocurrió.

**Patrón de conexión:** Modelo → Lenguaje.

---

## Datos

El dataset inicial propuesto está compuesto por **130 muestras de audio**:

| Clase | Muestras |
|---|---:|
| Alarma de incendio | 60 |
| Ruido de fondo | 70 |
| **Total** | **130** |

Las fuentes previstas son Freesound.org, Zapsplat y grabaciones propias.

La Fase R aún requiere completar la recolección real, verificar el número final por clase y documentar fuente y licencia de los audios externos.

---

## Métricas y objetivos

El baseline técnico estimado para un clasificador ingenuo que siempre predice la clase mayoritaria (ruido_de_fondo) es **53.8 % de accuracy**. Por ese motivo, la métrica prioritaria del proyecto no es el accuracy general sino el **recall de alarma_incendio**: un modelo que siempre predijera "ruido de fondo" tendría un accuracy alto pero 0 % de recall en la clase que realmente importa.

Las metas propuestas para el MVP son:

- **≥ 80 %** de alarmas de incendio identificadas correctamente;
- **≤ 3 segundos** de tiempo promedio hasta la notificación;
- **≥ 80 %** de accuracy global;
- **≥ 85 %** de recall en alarma_incendio;
- **≥ 80 %** de precision en alarma_incendio (para no saturar al usuario con falsas alarmas);
- mejora de **≥ 5 puntos porcentuales de recall** después del reentrenamiento A3;
- **≥ 90 %** de respuestas correctas y comprensibles en 20 casos de prueba de la capa G1.

---

## Validación

La validación se plantea con un alcance manejable para el curso:

- **5 usuarios**;
- **10 eventos por usuario**;
- **50 observaciones totales**.

Se registrará si cada evento fue identificado correctamente, el tiempo hasta recibir la notificación, la comprensión del mensaje y los errores observados.

Las **130 muestras corresponden a audios de entrenamiento**, no a personas participantes.

---

## Alcance y despliegue

El MVP incluirá captura de audio desde el micrófono, clasificación de dos categorías, score de confianza, umbrales, notificación por Telegram/WhatsApp (alerta + mensaje tranquilizador), capa G1, expresión de incertidumbre, reentrenamiento A3 y despliegue de la captura de audio mediante una página web mínima.

No incluirá reconocimiento de más de 2 categorías de sonido, llamadas automáticas a emergencias, geolocalización, almacenamiento permanente de audio, aplicación móvil nativa, interfaz visual propia (dashboard, historial), wearables, RAG ni agentes autónomos.

El stack propuesto es:

- **Teachable Machine** para el modelo;
- **TensorFlow.js** para inferencia;
- **Web Audio API** para captura de audio;
- **API de un modelo de lenguaje** para G1;
- **Telegram Bot API (o WhatsApp Business API)** para las notificaciones;
- **función serverless** para proteger credenciales;
- **Vercel** para despliegue;
- **GitHub** para repositorio y documentación.

---

## Estado actual

En la Semana 5, el proyecto ya cuenta con:

- problema y usuario definidos;
- niveles A3 + G1;
- patrón Modelo → Lenguaje;
- alcance del MVP ajustado a 2 clases;
- canal de notificación definido (Telegram/WhatsApp);
- diseño inicial de datos;
- métricas y OKRs;
- flujo del producto;
- stack propuesto;
- plan de validación.

Antes de la PC1 se debe cerrar la recolección real de audios, documentar licencias, definir e implementar el bot de notificaciones, verificar técnicamente el stack y consolidar el cronograma y la presentación.

---

*Framework PROMPT v2.0 — AD5018 Inteligencia Artificial para Negocios*
