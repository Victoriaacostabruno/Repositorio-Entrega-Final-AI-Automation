# 🚀 Entrega Final – Ecosistema de Automatización IA para Gestión Inteligente de Leads

## 📌 Descripción del Proyecto

Este proyecto consiste en el desarrollo de un ecosistema de automatización inteligente para la gestión de leads de la agencia ficticia **Nova PR**.

El objetivo es automatizar el proceso desde que un potencial cliente completa un formulario de contacto hasta el envío de una propuesta comercial, incorporando Inteligencia Artificial para analizar cada solicitud y un proceso **Human-in-the-Loop (HITL)** que garantice la aprobación humana antes de contactar al cliente.

La solución integra herramientas de automatización, bases de datos, IA y comunicación multicanal, siguiendo una arquitectura modular y escalable.

---

# 🎯 Objetivo

Automatizar el proceso comercial de Nova PR para:

- Centralizar la información de los leads.
- Analizar automáticamente cada solicitud mediante IA.
- Clasificar la prioridad del lead.
- Generar un resumen ejecutivo y una propuesta personalizada.
- Registrar toda la información en Airtable.
- Incorporar un proceso de aprobación humana antes del contacto con el cliente.
- Enviar automáticamente la propuesta o la notificación correspondiente según la decisión del responsable.

---

# 🛠 Tecnologías utilizadas

| Tecnología | Función |
|------------|----------|
| Make | Orquestador principal de automatización |
| Google Forms | Recepción de solicitudes de clientes |
| Google Sheets | Trigger de los escenarios |
| Airtable | Base de datos y memoria del sistema |
| Google Gemini AI | Análisis inteligente de leads |
| Gmail | Comunicación con responsables y clientes |

---

# 🏗 Arquitectura del Sistema

El proyecto está compuesto por **dos escenarios independientes**, conectados mediante un proceso Human-in-the-Loop.

## Escenario 1 – Recepción y análisis del lead

Flujo:

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

Este escenario recibe la información enviada por el cliente, registra el lead en Airtable, utiliza Google Gemini AI para analizar la consulta y genera automáticamente:

- Prioridad del lead
- Resumen ejecutivo
- Propuesta comercial personalizada

Finalmente se envía un correo electrónico solicitando la aprobación del responsable de Nova PR.

---

## Human in the Loop (HITL)

Para evitar que la Inteligencia Artificial tome decisiones completamente autónomas sobre el contacto con los clientes, el sistema incorpora un proceso Human-in-the-Loop.

El flujo se detiene luego del análisis realizado por Gemini.

El responsable recibe un correo electrónico y completa un formulario de aprobación donde indica:

- ID del Lead
- Aprobar
- Rechazar

La automatización no continúa hasta recibir esa decisión humana.

---

## Escenario 2 – Continuación tras aprobación humana

Flujo:

Formulario de aprobación

↓

Google Sheets

↓

Airtable (Search Record)

↓

Router

├── Aprobado

│

├── Airtable (Estado = Aprobado por Humano)

├── Gmail (Envío de propuesta al cliente)

│

└── Rechazado

↓

Airtable (Estado = Rechazado)

↓

Gmail (Correo de rechazo)

Este segundo escenario solamente se ejecuta cuando existe una decisión humana explícita.

---

# 🤖 Procesamiento mediante IA

Google Gemini AI recibe dinámicamente la información ingresada por el cliente.

El modelo analiza:

- Tipo de empresa
- Objetivo
- Presupuesto
- Servicio solicitado
- Urgencia

Y devuelve:

- Prioridad IA
- Justificación
- Resumen ejecutivo
- Propuesta comercial personalizada

Toda esta información se almacena automáticamente en Airtable.

---

# 🗄 Base de Datos

La solución utiliza Airtable como memoria del sistema.

## Tabla Leads

Campos principales:

- Nombre
- Email
- Empresa
- Cargo
- Servicio solicitado
- Objetivo
- Presupuesto
- Prioridad IA
- Resumen IA
- Propuesta IA
- Estado
- Error

## Tabla Aprobaciones

Relacionada mediante Linked Record con la tabla Leads.

Contiene:

- Lead relacionado
- Decisión
- Fecha
- Comentarios (opcional)

---

# ⚙ Gestión de Errores

El escenario incorpora Error Handlers de Make para mejorar la resiliencia del sistema.

Si ocurre un error durante el procesamiento con Google Gemini AI:

- el flujo evita detener completamente la automatización;
- se registra el estado correspondiente;
- el escenario continúa mediante Resume.

---

# 🔒 Seguridad

Para evitar ejecuciones repetidas o bucles infinitos:

- ambos escenarios utilizan Google Sheets Watch New Rows configurado en modo **From now on**;
- cada escenario trabaja sobre eventos independientes;
- el segundo escenario solo puede iniciarse mediante una aprobación humana.

---

# 🧪 Pruebas realizadas

| Prueba | Descripción | Resultado |
|---------|-------------|-----------|
| 1 | Lead aprobado | ✅ Correcto |
| 2 | Lead rechazado | ✅ Correcto |
| 3 | Prioridad Alta | ✅ Correcto |
| 4 | Prioridad Media | ✅ Correcto |
| 5 | Manejo de error (Error Handler) | ✅ Correcto |

---

# 📷 Evidencias

El repositorio incluye capturas de:

- Escenario 1
- Escenario 2
- Formularios
- Airtable
- Correos electrónicos
- Error Handling
- Human-in-the-Loop

---

# 📂 Archivos del Proyecto

```
Entrega-Final-AI-Automation

README.md

ArquitecturadeFlujo.pdf

Escenario2-ContinuacióntrasAprobacionHumana.blueprint.json

Escenario1-RecepciónyAnálisisIA(HITL Gate).blueprint.json

/evidencias

FormularioLeadSeccion1.png

FormularioLeadSeccion2.png

FormularioLeadSeccion3.png

FormularioLeadRespuestas.png

FormularioAprobacion.png

FormularioAprobacionRespuestas.png

Escenario1.png

Escenario2.png

PrintAirtableCRM.png

PrintAirtableCRM2.png

PrintAirtableCRM3.png

CorreoAprobacion.png

CorreoCliente.png

ErrorHandler.png
```

---

# 🎥 Video Demo

Video demostrativo (3 minutos):

(https://youtu.be/3w8OR4_OH4E?si=K_fX3aLhjs9JwE4A)

---

# 🔗 Enlaces

## Base de datos (Airtable)

**Modo lectura**

https://airtable.com/app4tYdSsIxLp07Kt/shrUTJzr7WgViVPyG 

---

## Repositorio GitHub

https://github.com/Victoriaacostabruno/Entrega-Final-AI-Automation

---

# 👩‍💻 Autora

**Victoria Acosta Bruno**

Entrega Final

Curso AI Automation

Coderhouse

2026
