# Plantilla 1 — Problem Statement Canvas
## Framework PROMPT | Fase P — Problema de Negocio
### AD5018 Inteligencia Artificial para Negocios | UTEC

---

**Equipo:**
- Integrante 1: _______________________________________________
- Integrante 2: _______________________________________________
- Integrante 3: _______________________________________________

**Fecha de entrega:** _______________
**Versión del canvas:** v2

---

## SECCIÓN 1 — Definición del problema

### 1.1 Usuario afectado
```
Personas con discapacidad auditiva (sordera total o parcial) que pasan
tiempo solas en su hogar o en espacios cotidianos, sin otra persona
oyente cerca que pueda alertarles de sonidos importantes de su entorno.
```

### 1.2 Problema específico
```
Estas personas no pueden percibir sonidos críticos de su entorno, como
una alarma de incendio, el timbre de la puerta o la bocina de un
vehículo acercándose. Esto les impide reaccionar a tiempo ante
situaciones de riesgo o enterarse de eventos cotidianos relevantes.
```

### 1.3 Causa raíz
```
La causa raíz es la ausencia de un canal auditivo funcional, combinada
con la falta de sistemas accesibles que traduzcan sonidos ambientales
específicos en señales visuales o de texto de forma automática y en
tiempo real.
```

### 1.4 Consecuencia medible
```
Sin una solución, la persona queda expuesta a riesgos de seguridad
reales (no percibir una alarma de incendio o un vehículo acercándose)
y pierde autonomía, dependiendo de terceros o de dispositivos genéricos
que no distinguen sonidos relevantes de irrelevantes.
```

### 1.5 Declaración del problema — formato obligatorio
```
Las personas con discapacidad auditiva tienen dificultad para percibir
sonidos críticos de su entorno (alarma, detector de humo, timbre,
bocina) porque no cuentan con un sistema que traduzca estos sonidos
específicos en alertas de texto o visuales en tiempo real, lo que
genera riesgos de seguridad y pérdida de autonomía en su vida diaria.
```

---

## SECCIÓN 2 — Filtro de validación IA

| Pregunta | SÍ/NO | Justificación |
|---|---|---|
| ¿Una hoja de cálculo o un formulario resuelve esto? | NO | Se necesita reconocer audio en tiempo real, no datos estructurados manuales. |
| ¿El problema escala con el volumen de datos o usuarios? | SÍ | Más muestras de sonido mejoran la precisión, y el problema aplica a cualquier persona con discapacidad auditiva. |
| ¿Hay un patrón repetitivo que un humano reconoce pero tarda en procesar? | SÍ | Los sonidos tienen patrones acústicos específicos, pero el usuario no puede "reconocerlos" al no poder oírlos. |
| ¿El problema requiere generar contenido, responder preguntas o razonar en lenguaje natural? | SÍ | Se necesita traducir la clase detectada en un mensaje de texto claro y entendible. |
| ¿Necesitas tanto predecir como explicar, comunicar o actuar? | SÍ | Se predice la categoría de sonido y luego se comunica como alerta al usuario. |

---

## SECCIÓN 3 — Los dos componentes del producto

> Todo producto de este curso tiene dos componentes. Uno analítico, que aprende de datos y predice o agrupa. Uno generativo, que trabaja con lenguaje y hace el producto usable.

### 3.1 Componente analítico — qué va a aprender el modelo

**¿Qué va a predecir, clasificar o agrupar?**
```
La categoría de sonido detectado en un fragmento de audio captado por
el micrófono: alarma, detector_humo, timbre, bocina, o ruido_de_fondo
(ausencia de sonido relevante).
```

**Tipo de tarea:**
- [X] Clasificación — decidir entre categorías
- [ ] Regresión
- [ ] Agrupamiento

**Nivel de profundidad elegido:**
- [ ] A1 — Un modelo entrenado, con baseline y métrica interpretada en negocio *(piso mínimo)*
- [ ] A2 — Además, compara modelos y ajusta el umbral según el costo del error
- [X] **A3** — Además, combina agrupamiento y predicción, **o reentrena con datos nuevos y mide la mejora**

> *Cómo se cumple A3:* tras la primera versión entrenada y probada (Semana 8-9), el equipo recolecta audio adicional en las condiciones donde el modelo falle (por ejemplo, confusión entre timbre y ruido de fondo), reentrena el modelo, y mide la mejora comparando recall/precisión de la versión 1 contra la versión 2, especialmente en las clases de seguridad crítica (alarma, detector_humo).

---

### 3.2 Componente generativo — qué va a hacer la capa de lenguaje

**¿Qué comunica, decide o ejecuta?**
```
Traduce la clase de sonido detectada y su nivel de confianza en un
mensaje de alerta claro para el usuario (ej. "🔥 Alarma de incendio
detectada" o "No se detectó ningún sonido relevante"). No decide
acciones ni consulta información externa — solo redacta el mensaje
según una tabla fija de 5 categorías ya definida por el equipo.
```

**Nivel de profundidad elegido:**
- [X] **G1** — Prompt con contexto fijo: la IA responde con la información que el equipo le escribió
- [ ] G2 — RAG
- [ ] G3 — Agente con herramientas
- [ ] G4 — Agente con automatizaciones

---

### 3.3 Cómo se conectan — patrón elegido

- [X] **Patrón 1 — Modelo → lenguaje.** El modelo predice, la capa generativa explica o redacta
- [ ] Patrón 2 — Lenguaje → modelo
- [ ] Patrón 3 — Modelo como herramienta del agente

**¿Qué dato exactamente viaja de un componente al otro?**
```
El modelo de audio (Teachable Machine) devuelve la clase predicha (ej.
"alarma") y su score de confianza (ej. 0.91). Ambos valores se insertan
en el prompt de la capa generativa, que los usa para elegir el mensaje
de alerta correspondiente o, si el score está bajo el umbral definido
para esa clase, responder con un mensaje de "no estoy seguro".
```

---

### 3.4 Dónde va la ambición del equipo

> *Regla de alcance: profundidad en un eje, piso en el otro. Ir a fondo en los dos no da más nota — da un proyecto sin terminar.*

- [ ] Vamos a fondo en el componente generativo (G3 o G4) y mantenemos el analítico en A1
- [X] **Vamos a fondo en el componente analítico (A3) y mantenemos el generativo en G1**
- [ ] Nos quedamos en un punto intermedio en ambos (A2 + G2)

**¿Por qué esa elección?**
```
El problema es de seguridad: un falso negativo en "alarma" o
"detector_humo" puede tener consecuencias graves para el usuario. Por
eso conviene invertir el esfuerzo del equipo en mejorar la precisión
del modelo (iterar con datos reales, ajustar y reentrenar) más que en
construir una capa de lenguaje compleja. El mensaje de alerta que debe
comunicarse es simple y fijo (5 categorías, 5 mensajes), por lo que un
prompt con contexto fijo (G1) ya cubre la necesidad sin agregar
complejidad innecesaria.
```

**3.5 Justificación general**
```
Estos dos niveles son la respuesta correcta para el problema porque la
prioridad del usuario (una persona con discapacidad auditiva) es que el
sistema detecte con la mayor confiabilidad posible los sonidos de
seguridad crítica — un error ahí cuesta mucho más que un mensaje de
alerta poco elaborado. Por eso el componente analítico necesita
profundidad (A3: iterar y reentrenar con datos reales tras la primera
prueba), mientras que el componente generativo puede resolverse con un
diseño simple y fijo (G1), ya que no hay ambigüedad ni necesidad de
consultar información externa para redactar la alerta. Esta
combinación es también la más realista dado el tiempo disponible del
equipo para el semestre.
```

---

### 3.6 Solo si el equipo solicita la excepción

- [ ] Solicitamos excepción al componente analítico

```
No aplica — el equipo sí entrena un modelo (clasificador de audio en
Teachable Machine), por lo que no se solicita la excepción de IA de
relleno.
```

---

## SECCIÓN 4 — Autoevaluación del equipo

| Pregunta de control | Respuesta |
|---|---|
| ¿El problema está descrito sin mencionar tecnología? | SÍ |
| ¿La declaración del problema sigue el formato exacto? | SÍ |
| ¿Los dos componentes están definidos con su nivel (A_ y G_)? | SÍ |
| ¿El patrón de conexión está elegido y justificado? | SÍ |
| ¿Está declarado con claridad en cuál de los dos ejes va la ambición? | SÍ |
| ¿Todos los integrantes pueden explicar este canvas sin leerlo? | *(completar en equipo)* |

> **Si alguna respuesta es NO → el canvas no está listo para entregar.**

---

*Framework PROMPT v2.0 — AD5018 UTEC | Plantilla 1 de 4*
