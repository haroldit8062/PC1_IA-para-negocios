# Plantilla 2 — Data Readiness Checklist
## Framework PROMPT | Fase R — Recursos de Datos
### AD5018 Inteligencia Artificial para Negocios | UTEC

---

**Equipo:**
- Integrante 1: Harold Inca
- Integrante 2: Diego Requena
- Integrante 3: Jennifer Patiño

**Fecha de entrega:** _______________
**Tipo de IA del proyecto:** Combinación — Clasificación de audio binaria, Nivel A3 (analítico, con reentrenamiento y medición de mejora) + capa de lenguaje G1 que traduce el resultado en una notificación por Telegram/WhatsApp (generativo)

---

## SECCIÓN 1 — Inventario de datos

### Parte A — Componente generativo: inventario de conocimiento

| # | Tipo de información | Dónde está actualmente | Formato | ¿Está disponible? |
|---|---|---|---|---|
| 1 | Mapeo sonido detectado → texto de alerta ("Alarma de incendio detectada") | Se define por el equipo, no existe aún como documento | Tabla / JSON de configuración | Parcial |
| 2 | Mapeo ausencia de alarma → mensaje tranquilizador ("No se detecta ninguna alarma, todo tranquilo") | Se define por el equipo | Tabla / JSON de configuración | Parcial |
| 3 | Texto del aviso de privacidad (uso de micrófono) | Ya redactado en sesiones previas del proyecto | Texto plano | SÍ |
| 4 | Justificación del umbral de confianza por clase (costo del error) | Definido conceptualmente, falta formalizar | Documento de diseño | PARCIAL |
| 5 | Instrucciones de uso para el usuario final (cómo vincular su cuenta de Telegram/WhatsApp al bot) | No existe aún | Texto plano / mensaje de bienvenida del bot | NO — pendiente de redactar |

**Estrategia de contexto elegida:**
- [X] **G1** Prompt simple *(base de conocimiento pequeña y estable)*
- [ ] **G2** RAG *(base de conocimiento grande o cambiante)*
- [ ] **G3/G4** Agente con herramientas
- [ ] Memoria de sesión

> **Justificación:** el conocimiento que necesita el componente generativo consta de **2 categorías** (alarma_incendio / ruido_de_fondo), cada una con un mensaje predefinido. No cambia con frecuencia ni requiere buscar en documentos externos, por lo que G1 (prompt con contexto fijo) sigue siendo la opción correcta.

**Si el nivel es G2 (RAG):** No aplica.

**Si el nivel es G3 o G4:** No aplica.

---

### Parte B — Componente analítico: inventario de datos históricos

| # | Dataset | Fuente | Formato | N° de registros aprox. | ¿Tiene etiquetas? |
|---|---|---|---|---|---|
| 1 | Audio de alarma de incendio / detector de humo | Freesound.org, Zapsplat, grabación propia | .mp3 / .wav | ~50-70 muestras (vía clips troceados por Teachable Machine) | SÍ (por carpeta/clase) |
| 2 | Audio de ruido de fondo (clase obligatoria) | Grabación propia (conversación, calle, música, silencio, televisión) | .mp3 / .wav o grabación directa en Teachable Machine | ~60-80 muestras (la más variada, para evitar falsos positivos) | SÍ |


**Variable objetivo (target):**
```
La categoría de sonido detectado en un fragmento de audio: alarma_incendio
(incluye detector de humo) o ruido_de_fondo (ausencia de alarma).
```

**Tipo de problema confirmado:**
- [X] Clasificación (binaria) — decide entre 2 categorías
- [ ] Regresión
- [ ] Agrupamiento

**Recuento de casos por categoría:**

| Categoría que se quiere distinguir | N° de casos disponibles |
|---|---|
| Alarma de incendio | 50-70 (10 audios recolectados) |
| Ruido de fondo | 60-80 (10 audios recolectados) |

> **Nota:** al trabajar con solo 2 clases, el equipo puede apuntar a un volumen por clase **mayor** al mínimo de referencia (~50), ya que no hay que repartir el esfuerzo entre 5 categorías distintas. Esto debería traducirse en un modelo más robusto para el mismo tiempo de trabajo.
>
> **Nota adicional (Nivel A3):** como el proyecto se comprometió a reentrenar el modelo con datos nuevos tras la primera prueba (Plantilla 1, Sección 3.4), el equipo debe planificar una **segunda ronda de recolección** después de la Semana 8-9, enfocada en los casos donde el primer modelo se equivoque (por ejemplo, ruidos domésticos agudos que se confundan con la alarma). Esta segunda ronda no necesita ser tan grande como la primera — basta con ~15-20 muestras adicionales.

**Revisión de fuga de datos (obligatoria):**

| Variable sospechosa | ¿Existe antes del hecho a predecir? | Decisión |
|---|---|---|
| Volumen/ruido de fondo del clip de entrenamiento | SÍ — el micrófono del dispositivo capta esto en el momento real, igual que en entrenamiento | Se mantiene |
| Duración exacta del clip (1 seg, definido por Teachable Machine) | SÍ — la herramienta trocea igual en entrenamiento y en uso real | Se mantiene |

> No se identifican variables con fuga de datos: el modelo solo usa el audio capturado en el instante, sin información futura ni metadatos externos al sonido mismo.

---

### Parte C — Cómo se conectan los datos de ambos componentes

```
El componente analítico (modelo de audio en Teachable Machine) recibe
el sonido captado por el micrófono y devuelve dos datos: la clase
predicha (alarma_incendio o ruido_de_fondo) y el score de confianza
(ej. 0.92).

Ese resultado viaja al componente generativo (capa de lenguaje G1), que
usa la tabla fija de mapeo (Parte A, ítems 1 y 2) para redactar el
mensaje correspondiente:

- Si la clase es alarma_incendio y el score supera el umbral (0.80):
  se envía una notificación de alerta por Telegram/WhatsApp.
- Si la clase es ruido_de_fondo, o el score de alarma_incendio no
  supera el umbral: se envía un mensaje tranquilizador confirmando que
  no se detecta ninguna alarma, para que el usuario sepa que el
  sistema sigue activo y no hay de qué preocuparse.
```

---

## SECCIÓN 2 — Evaluación de calidad con semáforo

### Dataset / Fuente principal: Audio de alarma de incendio y ruido de fondo (Freesound, Zapsplat, grabación propia)

| Dimensión | Semáforo | Evidencia que respalda la evaluación | Plan de acción (si es 🟡 o 🔴) |
|---|---|---|---|
| **Disponibilidad** | 🟢 | Se verificó que Freesound y Zapsplat tienen múltiples clips de alarmas de incendio/detectores de humo de acceso gratuito. | — |
| **Volumen** | 🟡 | Las fuentes existen, pero el equipo aún no ha descargado/grabado las muestras suficientes por clase. | Ver Bloqueante 1, Sección 3. |
| **Calidad** | 🟡 | Algunos clips de bancos gratuitos pueden traer música de librería de fondo, lo que podría confundir al modelo. | Revisar cada clip antes de usarlo y descartar los que tengan sonidos superpuestos. |
| **Relevancia** | 🟢 | La clase objetivo corresponde exactamente al escenario de mayor riesgo definido en la Fase P (Plantilla 1). | — |
| **Legalidad** | 🟡 | Freesound y Zapsplat usan licencias variadas (algunas requieren atribución); no se ha revisado clip por clip. | Ver Bloqueante 2, Sección 3. |
| **Etiquetas (analítico)** | 🟢 | Cada clase se etiqueta automáticamente por el nombre de la carpeta/clase en Teachable Machine al momento de subir el audio. | — |
| **Balance (analítico)** | 🟡 | Aún no se ha verificado que ambas clases queden con una cantidad similar de muestras; al ser solo 2 clases, este balance es más fácil de controlar que antes. | Contar muestras finales por clase antes de entrenar; ajustar si alguna clase queda muy por debajo de la otra. |
| **Fuga de datos (analítico)** | 🟢 | Revisado en Sección 1, Parte B — no se detectaron variables con fuga. | — |
| **Vigencia (analítico)** | 🟢 | El sonido de una alarma de incendio no cambia con el tiempo; no hay riesgo de que los datos queden obsoletos. | — |
| **Cobertura (generativo)** | 🟢 | El conocimiento fijo (2 mensajes) cubre exactamente las 2 clases que el modelo puede predecir; no hay casos fuera de ese alcance. | — |
| **Actualidad (generativo)** | 🟢 | Al ser G1 con conocimiento fijo y estable, no requiere mantenimiento periódico. | — |
| **Permisos (solo agentes)** | — | No aplica — el producto no tiene agente con herramientas. | — |

---

### Dataset / Fuente secundaria: No aplica — no se usa una segunda fuente de datos independiente en este proyecto.

---

## SECCIÓN 3 — Plan de resolución de bloqueantes

### Bloqueante 1
```
Dimensión afectada: Volumen
Descripción del problema: El equipo identificó las fuentes de audio
pero aún no ha completado la recolección de muestras suficientes para
las 2 clases definidas (alarma_incendio, ruido_de_fondo).
Acción concreta para resolverlo: Descargar/grabar los clips necesarios
por clase, apoyándose en grabaciones continuas de 20-40 segundos que
Teachable Machine trocea automáticamente en múltiples muestras. Al ser
solo 2 clases, priorizar variedad dentro de cada una (distintos
modelos de alarma; ruido de fondo con conversación, calle, TV, música,
silencio).
Responsable dentro del equipo: [Nombre integrante]
Fecha límite de resolución: [Fecha, dentro de Semana 7]
¿Qué pasa si no se resuelve? (Plan B): Entrenar una primera versión
con el volumen disponible aunque sea menor al ideal, documentar el
recall obtenido, y usar esos resultados para priorizar qué grabar en
la segunda ronda (Nivel A3).
```

### Bloqueante 2
```
Dimensión afectada: Legalidad
Descripción del problema: No se ha revisado la licencia de cada clip
descargado de bancos de sonido; algunos requieren atribución.
Acción concreta para resolverlo: Priorizar clips marcados como
"Creative Commons 0" (sin atribución) y, si se usa un clip con
atribución requerida, documentar la fuente en el README del proyecto.
Responsable dentro del equipo: [Nombre integrante]
Fecha límite de resolución: [Fecha, dentro de Semana 7]
¿Qué pasa si no se resuelve? (Plan B): Sustituir esos clips por
grabaciones propias del equipo, que no tienen restricción de licencia.
```

---

## SECCIÓN 4 — Privacidad y legalidad de los datos

| Pregunta | Respuesta | Detalle |
|---|---|---|
| ¿Los datos contienen información personal de usuarios? | NO | Los audios son sonidos ambientales (alarma de incendio, ruido de fondo); no se capturan voces identificables ni datos personales. |
| ¿Se cuenta con consentimiento explícito para usar esos datos? | N/A | No aplica al no tratarse de datos personales; los clips de banco de sonido se usan bajo su licencia de uso correspondiente. |
| ¿Los datos serán anonimizados antes de usarlos en el proyecto? | N/A | No aplica — no hay datos personales que anonimizar. |
| ¿Aplica la Ley N° 29733 de Protección de Datos Personales del Perú? | NO (para los datos de entrenamiento) | No aplica a los audios de entrenamiento. Sí aplicaría al número de Telegram/WhatsApp del usuario final al vincular el bot — esto se documentará como riesgo en la Plantilla 4 (Fase T). |
| ¿Hay alguna restricción contractual o de confidencialidad? | NO | Los datos provienen de fuentes públicas/gratuitas o de grabación propia del equipo. |

---

## SECCIÓN 5 — Autoevaluación del equipo

| Pregunta de control | Respuesta |
|---|---|
| ¿Cada semáforo tiene evidencia concreta que lo respalda? | SÍ |
| ¿Todos los 🔴 tienen un plan de acción con fecha y responsable? | SÍ *(no hay 🔴, solo 🟡 con plan)* |
| ¿El equipo verificó el acceso real a los datos antes de completar este checklist? | PARCIAL — falta completar la descarga/grabación real (ver Bloqueante 1) |
| ¿La estrategia de contexto es coherente con los datos disponibles? | SÍ |
| ¿Hay casos suficientes de **cada** categoría, no solo filas en total? | PARCIAL — pendiente de verificar tras la recolección |
| ¿Se revisó variable por variable que no haya fuga de datos? | SÍ |
| ¿Están inventariadas las herramientas del agente, si el producto tiene una? | N/A — el producto no usa agente |

> **Nota:** este checklist queda en estado "casi listo" — el diseño y las fuentes están verificados, y el alcance reducido a 2 clases facilita completar la recolección real antes de lo previsto. Falta ejecutar la descarga/grabación final de las muestras.

---
