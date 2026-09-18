# Plantilla 2 — Data Readiness Checklist
## Framework PROMPT | Fase R — Recursos de Datos
### AD5018 Inteligencia Artificial para Negocios | UTEC

---

**Equipo:**
- Integrante 1: Diego Requena Falero
- Integrante 2: Harold Inca Tenorio
- Integrante 3: Jeniffer Patiño Landa

**Fecha de entrega:** _______________
**Tipo de IA del proyecto:** Combinación — Clasificación de audio, Nivel A3 (analítico, con reentrenamiento y medición de mejora) + capa de lenguaje G1 que traduce el resultado en alerta (generativo)

---

## SECCIÓN 1 — Inventario de datos

### Parte A — Componente generativo: inventario de conocimiento

| # | Tipo de información | Dónde está actualmente | Formato | ¿Está disponible? |
|---|---|---|---|---|
| 1 | Mapeo sonido detectado → texto de alerta (ej. "Bocina detectada") | Se define por el equipo, no existe aún como documento | Tabla / JSON de configuración | PARCIAL — hay que redactarlo |
| 2 | Mapeo sonido detectado → patrón de vibración/color de alerta | Se define por el equipo | Tabla / JSON de configuración | PARCIAL — hay que redactarlo |
| 3 | Texto del aviso de privacidad (uso de micrófono) | Ya redactado en sesiones previas del proyecto | Texto plano | SÍ |
| 4 | Justificación del umbral de confianza por clase (costo del error) | Definido conceptualmente, falta formalizar por clase | Documento de diseño | PARCIAL |
| 5 | Instrucciones de uso para el usuario final (persona con discapacidad auditiva) | No existe aún | Texto plano / pantalla de ayuda | NO — pendiente de redactar |

**Estrategia de contexto elegida:**
- [X] **G1** Prompt simple *(base de conocimiento pequeña y estable)*
- [ ] **G2** RAG *(base de conocimiento grande o cambiante)*
- [ ] **G3/G4** Agente con herramientas
- [ ] Memoria de sesión

> **Justificación:** el conocimiento que necesita el componente generativo es fijo y pequeño (5 categorías de sonido, cada una con un mensaje y una acción predefinida). No cambia con frecuencia ni requiere buscar en documentos externos, por lo que G1 (prompt con contexto fijo) es suficiente — no se necesita RAG ni agente.

**Si el nivel es G2 (RAG):** No aplica.

**Si el nivel es G3 o G4:** No aplica.

---

### Parte B — Componente analítico: inventario de datos históricos

| # | Dataset | Fuente | Formato | N° de registros aprox. | ¿Tiene etiquetas? |
|---|---|---|---|---|---|
| 1 | Audio de bocina de vehículo | Freesound.org, Pixabay Audio, grabación propia | .mp3 / .wav | ~50 muestras (vía clips troceados por Teachable Machine) | SÍ (por carpeta/clase) |
| 2 | Audio de alarma (sirena, auto, emergencia) | Freesound.org, Zapsplat, grabación propia | .mp3 / .wav | ~50 muestras | SÍ |
| 3 | Audio de detector de humo | Freesound.org | .mp3 / .wav | ~50 muestras | SÍ |
| 4 | Audio de timbre de puerta | Freesound.org, Pixabay Audio, grabación propia | .mp3 / .wav | ~50 muestras | SÍ |
| 5 | Audio de ruido de fondo (clase obligatoria) | Grabación propia (conversación, calle, música, silencio) | .mp3 / .wav o grabación directa en Teachable Machine | ~50+ muestras (la más variada) | SÍ |

**Variable objetivo (target):**
```
La categoría de sonido detectado en un fragmento de audio: bocina,
alarma, detector_humo, timbre, o ruido_de_fondo (ausencia de sonido
relevante).
```

**Tipo de problema confirmado:**
- [X] Clasificación — decide entre categorías
- [ ] Regresión
- [ ] Agrupamiento

**Recuento de casos por categoría:**

| Categoría que se quiere distinguir | N° de casos disponibles |
|---|---|
| Bocina | ~10 (de 50 objetivo)) |
| Alarma | ~10 (de 50 objetivo)) |
| Detector de humo | ~10 (de 50 objetivo) |
| Timbre | ~10 (de 50 objetivo)) |
| Ruido de fondo | ~10 (de 50 objetivo) |

> **Nota (Nivel A3):** la referencia mínima de la plantilla es ~50 audios por categoría en Teachable Machine. El equipo ya descargó 10 audios reales de cada una de las 5 categorías (20% del objetivo por clase), lo cual verifica el acceso real a las fuentes identificadas (Freesound, Pixabay Audio, grabación propia) con un volumen inicial ya considerable. El resto (hasta 50 por clase) se termina de recolectar durante la Fase M (Semanas 7-11), antes de entrenar el modelo definitivo. Como el proyecto se comprometió a reentrenar el modelo con datos nuevos tras la primera prueba, el equipo debe planificar una segunda ronda de recolección después de la Semana 8-9, enfocada en los casos donde el primer modelo se equivoque (por ejemplo, confusión entre timbre y ruido de fondo). 

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
predicha (ej. "alarma") y el score de confianza (ej. 0.92).

Ese resultado viaja al componente generativo (capa de lenguaje G1), que
usa la tabla fija de mapeo (Parte A, ítems 1 y 2) para traducir la
clase y el score en: (a) el texto de alerta que se muestra en pantalla,
y (b) el patrón de vibración correspondiente. Si el score está por
debajo del umbral definido para esa clase, el generativo muestra un
mensaje de "no estoy seguro" en vez de forzar una alerta.
```

---

## SECCIÓN 2 — Evaluación de calidad con semáforo

### Dataset / Fuente principal: Audio de sonidos peligrosos (Freesound, Pixabay Audio, Zapsplat, grabación propia)

| Dimensión | Semáforo | Evidencia que respalda la evaluación | Plan de acción (si es 🟡 o 🔴) |
|---|---|---|---|
| **Disponibilidad** | 🟢 | Se verificó que Freesound y Pixabay Audio tienen clips de bocina, alarma, detector de humo y timbre, de acceso gratuito. | — |
| **Volumen** | 🟡 | 	El equipo ya descargó 10 audios reales por cada una de las 5 categorías (20% del objetivo), verificando que las fuentes son accesibles y el formato funciona. Falta completar hasta las ~50 muestras por clase, lo cual se hará en la Fase M antes de entrenar | Ver Bloqueante 1, Sección 3. |
| **Calidad** | 🟡 | Algunos clips de bancos gratuitos pueden traer música de librería de fondo, lo que podría confundir al modelo. | Revisar cada clip antes de usarlo y descartar los que tengan sonidos superpuestos. |
| **Relevancia** | 🟢 | Los 4 sonidos objetivo corresponden exactamente a los definidos como críticos en la Fase P (Plantilla 1). | — |
| **Legalidad** | 🟡 | Freesound y Zapsplat usan licencias variadas (algunas requieren atribución); no se ha revisado clip por clip. | Ver Bloqueante 2, Sección 3. |
| **Etiquetas (analítico)** | 🟢 | Cada clase se etiqueta automáticamente por el nombre de la carpeta/clase en Teachable Machine al momento de subir el audio. | — |
| **Balance (analítico)** | 🟡 | Aún no se ha verificado que las 5 clases queden con una cantidad similar de muestras; la clase "ruido de fondo" tiende a ser más fácil de sobre-poblar. | Contar muestras finales por clase antes de entrenar; ajustar si alguna clase queda muy por debajo de las demás. |
| **Fuga de datos (analítico)** | 🟢 | Revisado en Sección 1, Parte B — no se detectaron variables con fuga. | — |
| **Vigencia (analítico)** | 🟢 | Los sonidos de bocina, alarma, timbre y detector de humo no cambian con el tiempo; no hay riesgo de que los datos queden obsoletos. | — |
| **Cobertura (generativo)** | 🟢 | El conocimiento fijo (5 mensajes de alerta) cubre exactamente las 5 clases que el modelo puede predecir; no hay preguntas fuera de ese alcance. | — |
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
pero aún no ha completado la recolección de las ~50 muestras mínimas
por clase que exige Teachable Machine.
Acción concreta para resolverlo: Descargar/grabar los clips necesarios
por clase, apoyándose en grabaciones continuas de 20-40 segundos que
Teachable Machine trocea automáticamente en múltiples muestras.
Responsable dentro del equipo: [Nombre integrante]
Fecha límite de resolución: [Fecha, dentro de Semana 5]
¿Qué pasa si no se resuelve? (Plan B): Reducir temporalmente el
alcance a 3 clases (alarma, timbre, ruido de fondo) para asegurar un
modelo funcional dentro del plazo, y agregar bocina/detector de humo
en una iteración posterior (Fase M).
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
Fecha límite de resolución: [Fecha, dentro de Semana 5]
¿Qué pasa si no se resuelve? (Plan B): Sustituir esos clips por
grabaciones propias del equipo, que no tienen restricción de licencia.
```

---

## SECCIÓN 4 — Privacidad y legalidad de los datos

| Pregunta | Respuesta | Detalle |
|---|---|---|
| ¿Los datos contienen información personal de usuarios? | NO | Los audios son sonidos ambientales (bocina, alarma, timbre, humo); no se capturan voces identificables ni datos personales. |
| ¿Se cuenta con consentimiento explícito para usar esos datos? | N/A | No aplica al no tratarse de datos personales; los clips de banco de sonido se usan bajo su licencia de uso correspondiente. |
| ¿Los datos serán anonimizados antes de usarlos en el proyecto? | N/A | No aplica — no hay datos personales que anonimizar. |
| ¿Aplica la Ley N° 29733 de Protección de Datos Personales del Perú? | NO (para los datos de entrenamiento) | No aplica a los audios de entrenamiento. Sí aplicaría en el uso real del producto si el micrófono llegara a captar voces identificables del entorno del usuario final — esto se documentará como riesgo en la Plantilla 4 (Fase T). |
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



---
