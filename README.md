# 🚀 Entrega Final – Ecosistema de Automatización IA para Gestión Inteligente de Leads

# 📌 Descripción del Proyecto

Este proyecto implementa un ecosistema de automatización inteligente para la gestión de leads de la agencia ficticia **Nova PR**.

El objetivo es automatizar todo el proceso comercial desde que un potencial cliente completa un formulario de contacto hasta el envío de una propuesta comercial personalizada, incorporando Inteligencia Artificial para analizar las solicitudes y un proceso **Human-in-the-Loop (HITL)** que requiere aprobación humana antes de contactar al cliente.

La solución integra Google Forms, Google Sheets, Airtable, Google Gemini AI, Gmail y Make, siguiendo una arquitectura modular basada en dos escenarios independientes.

---

# 🎯 Objetivo

Automatizar el proceso comercial de Nova PR para:

- Centralizar la información de los leads.
- Analizar automáticamente cada solicitud mediante IA.
- Clasificar la prioridad del lead.
- Generar un resumen ejecutivo.
- Generar una propuesta comercial personalizada.
- Registrar toda la información en Airtable.
- Incorporar una validación humana antes del contacto con el cliente.
- Enviar automáticamente la propuesta o el correo de rechazo según la decisión tomada.

---

# 🛠 Tecnologías utilizadas

- Google Forms
- Google Sheets
- Airtable
- Google Gemini AI (Gemini 3.5 Flash)
- Gmail
- Make

---

# 🏗 Arquitectura del Sistema

La solución está compuesta por **dos escenarios independientes**, conectados mediante un proceso Human-in-the-Loop.

---

## Escenario 1 – Recepción y análisis inteligente del lead

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

Este escenario recibe automáticamente la información enviada por el potencial cliente, crea el registro en Airtable y utiliza Google Gemini AI para analizar la solicitud.

La IA genera automáticamente:

- Prioridad del lead
- Resumen ejecutivo
- Propuesta comercial

Posteriormente actualiza el registro en Airtable y envía un correo solicitando la aprobación humana.

---

# Human in the Loop (HITL)

El proyecto implementa un proceso **Human-in-the-Loop** antes de ejecutar la acción crítica (contactar al cliente).

Una vez finalizado el análisis realizado por Google Gemini AI:

- el escenario se detiene;
- se envía un correo electrónico al responsable comercial;
- el responsable completa un formulario de aprobación indicando:

- ID del Lead
- Decisión (Aprobado / Rechazado)

La automatización únicamente continúa cuando existe una decisión humana explícita.

---

## Escenario 2 – Continuación luego de la aprobación

Flujo:

Formulario de aprobación

↓

Google Sheets (Watch New Rows)

↓

Airtable (Search Record)

↓

Router

├── Ruta Aprobado

│

├── Airtable (Update Estado)

├── Gmail (Envío de propuesta)

│

└── Ruta Rechazado

↓

Airtable (Update Estado)

↓

Gmail (Correo de rechazo)

Este escenario solamente se ejecuta luego de recibir la aprobación o rechazo del responsable.

---

# 🤖 Procesamiento mediante Inteligencia Artificial

Google Gemini AI recibe dinámicamente la información ingresada en el formulario.

El modelo analiza:

- Industria
- Tamaño de empresa
- Servicio solicitado
- Objetivo comercial
- Presupuesto
- Urgencia

Como resultado devuelve:

- Prioridad IA
- Resumen IA
- Propuesta IA

Estos resultados se almacenan automáticamente en Airtable.

---

# 🗄 Base de Datos

Se utiliza una única base de Airtable denominada **Nova PR – CRM IA**.

## Tabla Leads

Contiene toda la información del proceso.

Campos principales:

- Nombre y Apellido
- Correo Electrónico
- Empresa
- Cargo
- Industria
- Tamaño empresa
- Servicios
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

Aunque el proyecto utiliza una única tabla en Airtable, existe una **relación lógica** entre Google Sheets y Airtable mediante el **ID del Lead**.

Funcionamiento:

1. El primer escenario crea el registro del lead en Airtable.
2. En el correo de aprobación se envía el **ID del Lead**.
3. El responsable completa el formulario indicando ese mismo ID y la decisión (Aprobado o Rechazado).
4. Google Forms registra la respuesta en Google Sheets.
5. El segundo escenario utiliza ese **ID del Lead** para ejecutar un **Search Records** en Airtable mediante la fórmula:

```
RECORD_ID() = "ID del Lead"
```

De esta manera se identifica el registro correcto y posteriormente se actualiza su estado.

Esta relación mediante una clave compartida reemplaza la necesidad de utilizar múltiples tablas relacionadas en Airtable.

---

# 📊 Flujo de Estados

Cada lead evoluciona durante el proceso mediante el campo **Estado**.

Estados posibles:

- Pendiente
- Aprobado por Humano
- Rechazado

Esto permite conocer en todo momento la situación de cada solicitud comercial.

---

# ⚙ Gestión de Errores

La solución incorpora manejo de errores mediante **Error Handlers de Make**.

En caso de producirse un error durante el procesamiento:

- el escenario no se detiene completamente;
- se utiliza **Resume** para continuar la ejecución;
- se registra el estado correspondiente en Airtable.

Esto mejora la resiliencia de la automatización.

---

# 🔒 Seguridad

Para evitar ejecuciones repetidas o bucles:

- ambos escenarios utilizan Google Sheets Watch New Rows configurado en modo **From now on**;
- el segundo escenario sólo puede iniciarse mediante una aprobación humana;
- la acción crítica nunca es ejecutada automáticamente por la IA.

---

# 🧪 Pruebas realizadas

| Prueba | Descripción | Resultado |
|---------|-------------|-----------|
| Lead aprobado | Flujo completo con aprobación humana | ✅ Correcto |
| Lead rechazado | Flujo completo con rechazo humano | ✅ Correcto |
| Clasificación IA | Generación de prioridad, resumen y propuesta | ✅ Correcto |
| Actualización Airtable | Registro actualizado correctamente | ✅ Correcto |
| Error Handler | Continuidad del escenario mediante Resume | ✅ Correcto |

---

# 📷 Evidencias

El repositorio incluye capturas de:

- Formulario Lead
- Formulario de aprobación
- Google Sheets
- Airtable
- Escenario 1
- Escenario 2
- Correos electrónicos
- Human-in-the-Loop
- Error Handler

---

# 📂 Archivos del Proyecto

- README.md
- Arquitectura de Flujo.pdf
- Escenario 1 (.blueprint.json)
- Escenario 2 (.blueprint.json)
- Carpeta Evidencias

---

# 🎥 Video Demo

https://youtu.be/3w8OR4_OH4E?si=K_fX3aLhjs9JwE4A

---

# 🔗 Enlaces

## Base de datos Airtable (Modo Lectura)

https://airtable.com/app4tYdSsIxLp07Kt/shrUTJzr7WgViVPyG

---

## Repositorio GitHub

https://github.com/Victoriaacostabruno/Repositorio-Entrega-Final-AI-Automation

---

# 👩‍💻 Autora

**Victoria Acosta Bruno**

Entrega Final – AI Automation

Coderhouse

2026
