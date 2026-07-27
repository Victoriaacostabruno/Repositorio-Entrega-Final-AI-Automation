# 🚀 Entrega Final – Ecosistema de Automatización IA para Gestión Inteligente de Leads

## 📌 Descripción del proyecto

Este proyecto implementa un ecosistema de automatización para la gestión inteligente de leads de la agencia ficticia **Nova PR**.

El flujo automatiza el proceso comercial desde que un potencial cliente completa un formulario de contacto hasta que se envía una respuesta al cliente, incorporando Inteligencia Artificial para analizar la solicitud y un proceso **Human-in-the-Loop (HITL)** que exige una aprobación humana antes de ejecutar la acción final.

La solución fue desarrollada en **Make** e integra Google Forms, Google Sheets, Airtable, Google Gemini AI y Gmail.

---

# 🎯 Objetivo

Automatizar el proceso de recepción y análisis de solicitudes comerciales para:

- Centralizar toda la información de los leads.
- Analizar automáticamente cada solicitud mediante Inteligencia Artificial.
- Clasificar la prioridad del lead.
- Generar un resumen ejecutivo.
- Generar una propuesta comercial personalizada.
- Registrar toda la información en Airtable.
- Incorporar una aprobación humana antes del contacto con el cliente.
- Enviar automáticamente la propuesta comercial o el correo de rechazo según la decisión del responsable.

---

# 🛠 Tecnologías utilizadas

- Make
- Google Forms
- Google Sheets
- Airtable
- Google Gemini AI (Gemini 3.5 Flash)
- Gmail

---

# 🏗 Arquitectura de la solución

La automatización está dividida en **dos escenarios independientes** conectados mediante un proceso Human-in-the-Loop.

---

# Escenario 1 – Recepción y análisis del lead

Flujo general:

Google Forms

↓

Google Sheets (Watch New Rows)

↓

Airtable (Create Record)

↓

Google Gemini AI

↓

Airtable (Update Record)

↓

Gmail (Solicitud de aprobación)

↓

Fin del escenario

### Funciones del escenario

Cuando un usuario completa el formulario:

- se detecta automáticamente una nueva fila en Google Sheets;
- se crea un registro en Airtable;
- Google Gemini analiza la información enviada;
- la IA genera:
  - Prioridad IA
  - Resumen IA
  - Propuesta IA;
- Airtable actualiza el registro con los resultados;
- se envía un correo electrónico al responsable solicitando una aprobación humana.

---

# Human-in-the-Loop (HITL)

Antes de contactar al cliente existe una validación humana obligatoria.

El proceso funciona de la siguiente manera:

1. Finaliza el análisis realizado por Google Gemini.
2. Se envía un correo al responsable comercial.
3. El responsable completa un formulario indicando:
   - ID del Lead.
   - Decisión (Aprobado o Rechazado).
4. Recién después de esa acción humana comienza el segundo escenario.

La Inteligencia Artificial nunca envía automáticamente una propuesta al cliente sin intervención humana.

---

# Escenario 2 – Continuación tras la aprobación humana

Flujo general:

Formulario de aprobación

↓

Google Sheets (Watch New Rows)

↓

Airtable (Search Records)

↓

Router

├── Ruta Aprobado
│   ↓
│   Airtable (Update Record)
│   ↓
│   Gmail (Enviar propuesta)
│   ↓
│   Fin

└── Ruta Rechazado
    ↓
    Airtable (Update Record)
    ↓
    Gmail (Enviar correo de rechazo)
    ↓
    Fin
    
Este escenario solamente puede ejecutarse cuando existe una respuesta del responsable comercial.

---

# 🤖 Uso de Inteligencia Artificial

Se utiliza **Google Gemini 3.5 Flash** para analizar automáticamente la información enviada por cada potencial cliente.

La IA recibe dinámicamente los siguientes datos:

- Industria.
- Tamaño de empresa.
- Servicios de interés.
- Objetivo comercial.
- Presupuesto.
- Urgencia.

Como resultado devuelve:

- Prioridad IA.
- Resumen IA.
- Propuesta IA.

Estos resultados se almacenan automáticamente en Airtable.

---

# 🗄 Base de datos

El proyecto utiliza una única base de Airtable denominada:

**Nova PR – CRM IA**

La información del proceso se almacena en una sola tabla.

## Campos principales

- Nombre y Apellido
- Correo Electrónico
- Empresa / Razón Social
- Cargo
- Industria
- Tamaño empresa
- Servicios de interés
- Objetivo
- Presupuesto
- Urgencia
- Prioridad IA
- Resumen IA
- Propuesta IA
- Estado
- Error

---

# 🔗 Relación entre Google Sheets y Airtable

Aunque el proyecto utiliza una única tabla en Airtable, existe una relación lógica entre Google Sheets y Airtable mediante el **ID del Lead (Record ID de Airtable)**.

El funcionamiento es el siguiente:

1. El Escenario 1 crea el registro del lead en Airtable.
2. Airtable genera automáticamente un **Record ID** para ese registro.
3. Ese ID se utiliza posteriormente en el formulario de aprobación.
4. Cuando el responsable responde el formulario, Google Forms registra una nueva fila en Google Sheets.
5. El Escenario 2 busca el registro correspondiente utilizando la fórmula:

```
RECORD_ID() = "ID del Lead"
```

6. Una vez localizado el registro, el escenario actualiza el campo Estado y continúa por la ruta Aprobado o Rechazado.

De esta manera, la relación entre ambos escenarios se realiza mediante una clave única compartida, sin necesidad de utilizar múltiples tablas relacionadas en Airtable.

---

# 📊 Flujo de estados

Durante la automatización cada lead cambia su estado dentro de Airtable.

Estados utilizados:

- Pendiente
- Aprobado por Humano
- Rechazado

Esto permite conocer en todo momento el estado actual de cada solicitud.

---

# ⚙ Gestión de errores

La solución incorpora manejo de errores mediante **Error Handlers de Make**.

Cuando ocurre un error en un módulo que no impide continuar el proceso:

- el escenario no finaliza inmediatamente;
- se utiliza un Error Handler con la acción **Resume**;
- la ejecución continúa siempre que sea posible.

Este mecanismo mejora la resiliencia del flujo frente a errores puntuales.

En las pruebas no se registraron fallas, por lo que se incluyó evidencia de la configuración del handler.

---

# 🔒 Seguridad y prevención de bucles

El proyecto incorpora distintas medidas para evitar ejecuciones repetidas y proteger las credenciales utilizadas.

## Gestión de credenciales

Las conexiones de Google Gemini, Airtable, Gmail y Google Sheets fueron configuradas mediante las conexiones nativas de Make.

No se incluyen claves API ni credenciales dentro de los blueprints ni en este repositorio.

## Prevención de bucles

Los dos escenarios utilizan módulos **Google Sheets – Watch New Rows**, por lo que únicamente reaccionan ante nuevas respuestas registradas en los formularios.

Además:

- el Escenario 2 sólo puede iniciarse cuando existe una aprobación humana;
- la acción final nunca es ejecutada directamente por la Inteligencia Artificial;
- el cambio de estado en Airtable permite identificar el avance del proceso sin volver a ejecutar acciones ya completadas.

---

# 🧪 Casos de prueba

| Caso de prueba                                    | Resultado  |
| ------------------------------------------------- | ---------- |
| Lead aprobado (flujo completo)                    | ✅ Correcto |
| Lead rechazado (flujo completo)                   | ✅ Correcto |
| Clasificación de prioridad **Alta**               | ✅ Correcto |
| Clasificación de prioridad **Media**              | ✅ Correcto |
| Manejo de errores mediante Error Handler (Resume) | ✅ Correcto |

---

# 📷 Evidencias incluidas

El repositorio contiene capturas de:

- Formulario de Lead.
- Formulario de aprobación.
- Google Sheets.
- Airtable.
- Escenario 1.
- Escenario 2.
- Correos electrónicos.
- Human-in-the-Loop.
- Error Handler.

También se incluyen ambos blueprints y el diagrama de arquitectura.

---

# 📂 Archivos del proyecto

- README.md
- Arquitectura de Flujo.pdf
- Ecosistema de Automatización IA para la Gestión Inteligente de Leads en Nova PR.mp4
- Escenario 1 - Recepción y Análisis IA (HITL Gate).blueprint.json
- Escenario 2 - Continuación tras aprobación humana.blueprint.json
- Carpeta Evidencias

---

# 🎥 Video demostrativo

En el siguiente video se muestra el funcionamiento completo de la automatización, incluyendo:

- Recepción del lead mediante Google Forms.
- Procesamiento en Make.
- Análisis con Google Gemini AI.
- Registro y actualización en Airtable.
- Proceso Human-in-the-Loop (HITL).
- Continuación del flujo tras la aprobación humana.
- Envío automático del correo correspondiente.

https://youtu.be/1HpR31bS8B8

---

# 🔗 Enlaces

## Base de Airtable (Modo lectura)

https://airtable.com/app4tYdSsIxLp07Kt/shrUTJzr7WgViVPyG
La base fue compartida en modo lectura (Viewer) para permitir la revisión del modelo de datos sin posibilidad de edición.

## Repositorio

https://github.com/Victoriaacostabruno/Repositorio-Entrega-Final-AI-Automation

---

# 👩‍💻 Autora

**Victoria Acosta Bruno**

Entrega Final – AI Automation

Coderhouse

2026
