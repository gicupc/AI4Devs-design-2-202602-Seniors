# User Stories — LTI ATS

**Autor:** GFS  
**Fecha:** Marzo 2026  
**Basado en:** LTI-GFS.md (Práctica 1)

---

## Plantilla utilizada

| Campo                       | Contenido                                     |
| --------------------------- | --------------------------------------------- |
| **ID**                      | US-XXX                                        |
| **Título**                  | Nombre descriptivo                            |
| **Historia**                | Como [rol], quiero [acción], para [beneficio] |
| **Criterios de aceptación** | Formato BDD: Dado/Cuando/Entonces             |
| **Prioridad**               | Alta / Media / Baja                           |
| **Estimación**              | Puntos de historia (Fibonacci)                |
| **Evaluación INVEST**       | ✅/⚠️ por cada criterio                       |
| **Notas adicionales**       | Contexto técnico relevante                    |

---

## User Stories

---

### US-001 — Publicación de oferta en múltiples canales

**Historia:**  
Como reclutador de LTI, quiero publicar una oferta de empleo en múltiples portales (LinkedIn, Indeed, InfoJobs) desde un único panel, para ahorrar tiempo y evitar acceder a cada portal por separado.

**Criterios de aceptación (BDD):**

_Escenario 1 — Publicación exitosa en todos los canales_

- **Dado que** soy un reclutador autenticado con una oferta en estado "aprobada"
- **Cuando** selecciono los canales de publicación y pulso "Publicar"
- **Entonces** la oferta se publica en todos los canales seleccionados y recibo confirmación con enlace a cada publicación

_Escenario 2 — Canal externo no disponible_

- **Dado que** intento publicar en LinkedIn e Indeed
- **Cuando** LinkedIn devuelve un error de conexión
- **Entonces** la oferta se publica en Indeed correctamente, recibo aviso de que LinkedIn falló y se me ofrece la opción de reintentarlo

_Escenario 3 — Campos obligatorios incompletos_

- **Dado que** estoy creando una oferta nueva
- **Cuando** intento publicar sin haber rellenado título, descripción o ubicación
- **Entonces** el sistema bloquea la publicación y resalta visualmente los campos que faltan

**Prioridad:** Alta  
**Estimación:** 8 puntos  
**Evaluación INVEST:**

- ✅ Independent: No depende de otras US
- ✅ Negotiable: Los canales integrados son negociables
- ✅ Valuable: Reduce horas de trabajo manual del recruiter
- ✅ Estimable: Integración con APIs externas conocida, alcance claro
- ✅ Small: Completable en un sprint
- ✅ Testable: Criterios de aceptación claros y medibles

**Notas adicionales:** Integración con APIs de LinkedIn Jobs, Indeed e InfoJobs vía Jobs Service. Usar sistema de colas (Kafka) para reintentos automáticos en fallos de publicación.

---

### US-002 — Asistencia de IA para redactar la Job Description

**Historia:**  
Como reclutador de LTI, quiero que la IA genere un borrador de Job Description a partir del título y requisitos básicos que introduzco, para redactar ofertas más completas e inclusivas en menos tiempo.

**Criterios de aceptación (BDD):**

_Escenario 1 — Generación del borrador_

- **Dado que** he introducido el título del puesto y al menos 3 requisitos
- **Cuando** pulso "Generar con IA"
- **Entonces** en menos de 10 segundos aparece un borrador completo con descripción del puesto, responsabilidades, requisitos y beneficios

_Escenario 2 — Detección de lenguaje sesgado_

- **Dado que** la IA ha generado o yo he escrito una JD
- **Cuando** el texto contiene expresiones con sesgo de género o edad (p.ej. "joven dinámico", "hombre proactivo")
- **Entonces** el sistema resalta esos fragmentos en amarillo y sugiere alternativas más inclusivas

_Escenario 3 — Edición posterior al borrador_

- **Dado que** la IA me ha generado un borrador
- **Cuando** modifico cualquier sección manualmente
- **Entonces** los cambios se guardan y la IA no sobreescribe mis ediciones sin que yo lo solicite explícitamente

**Prioridad:** Alta  
**Estimación:** 8 puntos  
**Evaluación INVEST:**

- ✅ Independent: Funciona sin depender de US-001
- ✅ Negotiable: El modelo LLM utilizado es negociable (GPT-4o, Claude, etc.)
- ✅ Valuable: Mejora la calidad y velocidad de creación de ofertas
- ✅ Estimable: Alcance claro, llamada a LLM externo bien definida
- ✅ Small: Completable en un sprint
- ✅ Testable: Tiempo de respuesta (<10s) y detección de sesgo son verificables

**Notas adicionales:** Utiliza el AI Service (componente Score & Explain Engine adaptado). Llamada a OpenAI GPT-4o con prompt template especializado en redacción de JDs inclusivas.

---

### US-003 — Cribado automático de CVs con IA y ranking explicado

**Historia:**  
Como reclutador de LTI, quiero que el sistema analice automáticamente los CVs recibidos y me muestre un ranking con puntuación y resumen explicado de cada candidato, para centrarme en los más relevantes sin revisar cientos de CVs manualmente.

**Criterios de aceptación (BDD):**

_Escenario 1 — Análisis automático al recibir candidatura_

- **Dado que** un candidato ha enviado su CV a una oferta activa
- **Cuando** el CV es procesado por el AI Service
- **Entonces** en menos de 30 segundos el candidato aparece en el pipeline con puntuación (0-100), fortalezas detectadas y gaps respecto al perfil

_Escenario 2 — Filtrado por puntuación mínima_

- **Dado que** estoy revisando candidatos de una oferta con 50+ candidaturas
- **Cuando** aplico un filtro de puntuación mínima de 70
- **Entonces** solo se muestran los candidatos con score ≥ 70 y el resto quedan ocultos (no eliminados)

_Escenario 3 — CV en formato no soportado_

- **Dado que** un candidato sube un archivo en formato no compatible (.pages, .txt)
- **Cuando** el sistema intenta procesarlo
- **Entonces** se notifica al candidato indicando los formatos válidos (PDF, DOC, DOCX) y la candidatura queda en estado "pendiente" hasta recibir CV válido

**Prioridad:** Alta  
**Estimación:** 13 puntos  
**Evaluación INVEST:**

- ✅ Independent: Funciona sin depender de US-001 ni US-002
- ✅ Negotiable: El algoritmo de puntuación y los pesos son negociables con el equipo
- ✅ Valuable: Es el diferenciador principal de LTI frente a competidores
- ✅ Estimable: CV Parser + Embeddings + Score Engine están definidos en la arquitectura
- ⚠️ Small: Es la más compleja; si el equipo lo considera, dividir en "CV Parser" (5 pts) y "Score & Rank" (8 pts)
- ✅ Testable: Score numérico, tiempo de procesamiento y filtrado son verificables

**Notas adicionales:** Implementa el flujo completo del AI Service: CV Parser → Embeddings Generator → JD Matcher → Score & Explain Engine. Ver diagrama C4 en LTI-GFS.md. Fórmula: score = (similarity×0.4 + hard_match×0.4 + soft_match×0.2) × 100.

---

### US-004 — Gestión del pipeline Kanban

**Historia:**  
Como reclutador de LTI, quiero gestionar los candidatos en un tablero Kanban visual con etapas configurables, para tener visibilidad completa del estado de cada proceso y mover candidatos entre fases fácilmente.

**Criterios de aceptación (BDD):**

_Escenario 1 — Mover candidato entre etapas_

- **Dado que** estoy viendo el pipeline Kanban de una oferta
- **Cuando** arrastro (drag & drop) la tarjeta de un candidato de "Screening" a "Entrevista técnica"
- **Entonces** el candidato aparece en la nueva etapa, se actualiza en tiempo real para todos los usuarios de la empresa y el candidato recibe notificación de avance

_Escenario 2 — Colaboración en tiempo real_

- **Dado que** dos reclutadores están viendo el mismo pipeline simultáneamente
- **Cuando** uno de ellos mueve un candidato
- **Entonces** el otro ve el cambio reflejado en menos de 2 segundos sin necesidad de recargar la página

_Escenario 3 — Descartar candidato con notificación_

- **Dado que** muevo un candidato a la etapa "Descartado"
- **Cuando** confirmo el descarte en el modal de confirmación
- **Entonces** el candidato recibe un email automático de agradecimiento y feedback genérico en menos de 5 minutos

**Prioridad:** Alta  
**Estimación:** 8 puntos  
**Evaluación INVEST:**

- ✅ Independent: El pipeline funciona aunque el scoring de IA no esté activo
- ✅ Negotiable: El número de etapas y sus nombres son configurables por proceso
- ✅ Valuable: Es la interfaz central de trabajo diario del reclutador
- ✅ Estimable: Drag & drop + WebSockets son tecnología conocida
- ✅ Small: Completable en un sprint (sin incluir notificaciones avanzadas)
- ✅ Testable: Tiempo de sincronización (<2s) y recepción de email son verificables

**Notas adicionales:** Usa WebSockets para tiempo real (ya contemplado en la arquitectura frontend). El Notification Service gestiona el email de descarte vía Kafka event. Las etapas se configuran en la entidad PIPELINE_STAGE del modelo de datos.

---

### US-005 — Coordinación automática de entrevistas

**Historia:**  
Como reclutador de LTI, quiero que el sistema proponga automáticamente slots de entrevista disponibles al candidato consultando los calendarios del equipo, para eliminar los intercambios de emails para coordinar fechas.

**Criterios de aceptación (BDD):**

_Escenario 1 — Propuesta automática de slots_

- **Dado que** he marcado un candidato como "Listo para entrevistar" e indicado los entrevistadores
- **Cuando** el sistema consulta Google Calendar / Outlook de los participantes
- **Entonces** el candidato recibe un email con 3 slots disponibles en las próximas 5 días laborables

_Escenario 2 — Confirmación y creación de evento_

- **Dado que** el candidato ha recibido los slots propuestos
- **Cuando** el candidato hace clic en su slot preferido
- **Entonces** el sistema crea el evento en los calendarios de todos los participantes, genera el enlace de videollamada (Zoom/Meet) y envía confirmación a todos

_Escenario 3 — Recordatorios automáticos_

- **Dado que** hay una entrevista programada
- **Cuando** faltan 24 horas y 1 hora para la entrevista
- **Entonces** todos los participantes (reclutador, hiring manager y candidato) reciben recordatorio con enlace a la videollamada

**Prioridad:** Media  
**Estimación:** 8 puntos  
**Evaluación INVEST:**

- ✅ Independent: Funciona sin depender del scoring de IA
- ✅ Negotiable: Los proveedores de calendario y videollamada son negociables
- ✅ Valuable: Elimina uno de los procesos más tediosos del recruiting
- ✅ Estimable: Integración con Google Calendar API y Zoom API bien documentadas
- ✅ Small: Completable en un sprint
- ✅ Testable: Creación de eventos y envío de recordatorios son verificables

**Notas adicionales:** Interview Service gestiona la coordinación. Integración con Google Calendar API y Microsoft Graph API (Outlook). Zoom/Meet para generación de enlace. Los recordatorios se gestionan vía Notification Service con jobs programados.

---

### US-006 — Evaluación colaborativa con scorecards

**Historia:**  
Como hiring manager de LTI, quiero completar una scorecard de evaluación tras cada entrevista y ver las puntuaciones del resto del equipo, para tomar decisiones de contratación basadas en criterios objetivos y compartidos.

**Criterios de aceptación (BDD):**

_Escenario 1 — Completar scorecard_

- **Dado que** he participado en una entrevista marcada como "completada"
- **Cuando** accedo a la scorecard del candidato y puntúo las dimensiones (técnica, comunicación, cultural fit, overall)
- **Entonces** mi evaluación queda guardada y el reclutador recibe notificación de que he completado la scorecard

_Escenario 2 — Visibilidad de evaluaciones del equipo_

- **Dado que** todos los entrevistadores han completado sus scorecards
- **Cuando** el reclutador accede al resumen de evaluaciones del candidato
- **Entonces** ve las puntuaciones individuales, la media global y las recomendaciones (strong yes / yes / no / strong no) de cada evaluador

_Escenario 3 — Scorecard incompleta antes de decisión_

- **Dado que** el reclutador intenta tomar una decisión final sobre un candidato
- **Cuando** hay evaluadores que aún no han completado su scorecard
- **Entonces** el sistema muestra una advertencia indicando qué evaluadores están pendientes, aunque permite continuar si el reclutador lo confirma

**Prioridad:** Media  
**Estimación:** 5 puntos  
**Evaluación INVEST:**

- ✅ Independent: Funciona sin el módulo de IA
- ✅ Negotiable: Las dimensiones de evaluación son configurables por empresa
- ✅ Valuable: Elimina el silo de feedback en emails y WhatsApp (pain point identificado)
- ✅ Estimable: CRUD de scorecards con cálculo de media, alcance bien definido
- ✅ Small: Completable en un sprint
- ✅ Testable: Guardado, visibilidad y cálculo de media son verificables

**Notas adicionales:** Entidad SCORECARD ya definida en el modelo de datos con campos: overall_score, technical, communication, cultural_fit, recommendation. Notificaciones vía Notification Service.

---

### US-007 — Dashboard de analytics y KPIs de selección

**Historia:**  
Como responsable de RRHH, quiero ver un dashboard con los KPIs clave de mis procesos de selección (time-to-hire, fuentes más efectivas, tasa de conversión por etapa), para identificar cuellos de botella y mejorar la eficiencia del equipo.

**Criterios de aceptación (BDD):**

_Escenario 1 — Visualización de KPIs principales_

- **Dado que** accedo al módulo de Analytics
- **Cuando** cargo el dashboard principal
- **Entonces** veo en menos de 3 segundos: time-to-hire promedio, coste por contratación, tasa de conversión por etapa del pipeline y top 3 fuentes de candidatos del último mes

_Escenario 2 — Filtrado por oferta o período_

- **Dado que** estoy en el dashboard de analytics
- **Cuando** aplico un filtro por oferta específica o rango de fechas
- **Entonces** todos los KPIs se actualizan para reflejar únicamente el período o vacante seleccionados

_Escenario 3 — Alerta de proceso estancado_

- **Dado que** hay una candidatura sin movimiento durante más de 7 días
- **Cuando** el sistema detecta esta situación
- **Entonces** el reclutador responsable recibe una notificación proactiva indicando qué candidato y en qué etapa está estancado

**Prioridad:** Media  
**Estimación:** 8 puntos  
**Evaluación INVEST:**

- ✅ Independent: El dashboard puede existir aunque otras funciones estén en desarrollo
- ✅ Negotiable: Los KPIs mostrados y los umbrales de alerta son configurables
- ✅ Valuable: Permite mejorar el proceso de selección con datos reales
- ✅ Estimable: Analytics Service ya contemplado en la arquitectura
- ✅ Small: Completable en un sprint (versión inicial con KPIs básicos)
- ✅ Testable: Tiempo de carga (<3s) y precisión de los datos son verificables

**Notas adicionales:** Analytics Service consume datos de las demás entidades vía Kafka events o consultas read-only a PostgreSQL. Elasticsearch para búsquedas y agregaciones complejas. Las alertas de estancamiento se generan con un job periódico (cron).

---

## Backlog de Producto Priorizado

### Metodología de priorización: WSJF (Weighted Shortest Job First)

Se utiliza WSJF (metodología SAFe) que combina valor de negocio, urgencia temporal, reducción de riesgo y tamaño del trabajo. Las US con mayor ratio WSJF se implementan primero.

| ID     | Título                           | Valor negocio | Urgencia | Riesgo/Oportunidad | Esfuerzo | WSJF | Prioridad |
| ------ | -------------------------------- | ------------- | -------- | ------------------ | -------- | ---- | --------- |
| US-003 | Cribado automático con IA        | 10            | 8        | 10                 | 13       | 2.15 | **1**     |
| US-001 | Publicación en múltiples canales | 9             | 9        | 7                  | 8        | 3.12 | **2**     |
| US-004 | Pipeline Kanban                  | 9             | 9        | 8                  | 8        | 3.25 | **3**     |
| US-002 | IA para redactar JD              | 8             | 7        | 6                  | 8        | 2.62 | **4**     |
| US-005 | Coordinación de entrevistas      | 8             | 7        | 6                  | 8        | 2.62 | **5**     |
| US-006 | Scorecards colaborativas         | 7             | 6        | 7                  | 5        | 4.00 | **6**     |
| US-007 | Dashboard analytics              | 7             | 5        | 6                  | 8        | 2.25 | **7**     |

> **Nota WSJF:** Fórmula = (Valor + Urgencia + Riesgo) / Esfuerzo. Las US-004 y US-006 suben posiciones por su menor esfuerzo relativo. US-003 baja en ratio por ser la más compleja (13 pts) pero es la prioridad estratégica #1 por ser el diferenciador clave del producto.

### Sprints sugeridos (asumiendo velocidad de 20 pts/sprint)

| Sprint   | US incluidas                            | Puntos | Objetivo                                  |
| -------- | --------------------------------------- | ------ | ----------------------------------------- |
| Sprint 1 | US-004 + US-001                         | 16     | Base del producto: pipeline + publicación |
| Sprint 2 | US-003 (parte 1: CV Parser)             | 5      | Infraestructura IA                        |
| Sprint 3 | US-003 (parte 2: Score & Rank) + US-002 | 16     | IA completa + JD asistida                 |
| Sprint 4 | US-005 + US-006                         | 13     | Entrevistas y evaluación                  |
| Sprint 5 | US-007                                  | 8      | Analytics y cierre MVP                    |

---

## Tickets de Trabajo — US-003: Cribado automático con IA

Se desarrollan los tickets técnicos para la User Story más estratégica del producto.

---

### TICKET-001 — Endpoint de recepción de candidatura

**Tipo:** Feature  
**US asociada:** US-003  
**Épica:** Motor de IA — Cribado automático

**Descripción:**  
Crear el endpoint REST en el Applications Service que recibe la candidatura del candidato (datos personales + CV adjunto), valida el formato del archivo, lo almacena en S3 y publica un evento en Kafka para desencadenar el procesamiento por el AI Service.

**Criterios de aceptación:**

- El endpoint `POST /api/v1/applications` acepta multipart/form-data con campos: job_id, candidate_email, candidate_name, cv_file
- Valida que cv_file sea PDF, DOC o DOCX (máx. 10MB). Devuelve 422 si el formato no es válido con mensaje descriptivo
- Almacena el CV en S3 con ruta: `cvs/{company_id}/{job_id}/{application_id}.{ext}`
- Crea el registro en la tabla APPLICATION con status="active" y ai_score=null
- Publica evento `application.created` en Kafka con payload: application_id, job_id, cv_s3_url
- Devuelve 201 con application_id al candidato en menos de 2 segundos
- El candidato recibe email de confirmación (gestionado por Notification Service al consumir el evento)

**Prioridad:** Alta  
**Estimación:** 3 puntos  
**Asignado a:** Equipo Backend  
**Etiquetas:** backend, applications-service, kafka, s3  
**Sprint:** Sprint 2

---

### TICKET-002 — CV Parser: extracción y normalización de texto

**Tipo:** Feature  
**US asociada:** US-003  
**Épica:** Motor de IA — Cribado automático

**Descripción:**  
Implementar el componente CV Parser dentro del AI Service. Descarga el CV desde S3, extrae el texto (PDF/DOCX), normaliza la estructura (secciones: experiencia, educación, skills, idiomas) y persiste el resultado estructurado en la base de datos del AI Service.

**Criterios de aceptación:**

- El worker consume eventos `application.created` desde Kafka
- Descarga el CV desde la URL S3 del evento
- Extrae texto de PDF usando PyMuPDF y de DOCX usando python-docx
- Aplica NLP básico (spaCy) para identificar secciones: experiencia laboral, educación, habilidades técnicas, idiomas
- Persiste el CV estructurado como JSONB en la tabla `cv_parsed` con campos: raw_text, sections, skills[], years_experience
- Publica evento `cv.parsed` en Kafka con application_id y cv_parsed_id
- Maneja errores: si el PDF está protegido o corrupto, publica evento `cv.parse.failed` y notifica al candidato
- Tiempo de procesamiento < 10 segundos para CVs de hasta 5 páginas

**Prioridad:** Alta  
**Estimación:** 5 puntos  
**Asignado a:** Equipo AI/ML  
**Etiquetas:** ai-service, nlp, kafka, python  
**Sprint:** Sprint 2  
**Dependencias:** TICKET-001 (necesita el evento `application.created`)

---

### TICKET-003 — Generación de embeddings y cálculo de score

**Tipo:** Feature  
**US asociada:** US-003  
**Épica:** Motor de IA — Cribado automático

**Descripción:**  
Implementar el Embeddings Generator y el JD Matcher dentro del AI Service. Genera embeddings semánticos del CV parseado y de la Job Description, calcula el score de similitud y lo combina con el hard/soft match para obtener la puntuación final (0-100).

**Criterios de aceptación:**

- Consume eventos `cv.parsed` desde Kafka
- Llama a OpenAI Embeddings API (text-embedding-3-small) para generar vector del CV y de la JD
- Implementa la fórmula de scoring: `score = (cosine_similarity×0.4 + hard_match×0.4 + soft_match×0.2) × 100`
- Hard match: verifica presencia de requisitos obligatorios de la JD en el CV estructurado
- Soft match: verifica presencia de requisitos deseables
- Persiste los embeddings en tabla `cv_embeddings` y el score parcial en `cv_scores`
- Publica evento `cv.scored` en Kafka con application_id y score_id
- Gestiona rate limits de OpenAI con retry exponencial (max 3 reintentos)
- Coste por candidatura estimado < $0.01 en llamadas a API

**Prioridad:** Alta  
**Estimación:** 5 puntos  
**Asignado a:** Equipo AI/ML  
**Etiquetas:** ai-service, openai, embeddings, python  
**Sprint:** Sprint 3  
**Dependencias:** TICKET-002 (necesita el CV parseado y estructurado)

---

### TICKET-004 — Score & Explain Engine: resumen en lenguaje natural

**Tipo:** Feature  
**US asociada:** US-003  
**Épica:** Motor de IA — Cribado automático

**Descripción:**  
Implementar el Score & Explain Engine que llama al LLM para generar un resumen explicado en lenguaje natural con fortalezas y gaps del candidato, y actualiza el registro de APPLICATION con el score final y el resumen.

**Criterios de aceptación:**

- Consume eventos `cv.scored` desde Kafka
- Construye un prompt con: datos del CV estructurado, requisitos de la JD y scores calculados
- Llama a GPT-4o para generar: strengths[] (máx. 3 puntos), gaps[] (máx. 3 puntos), explanation_md (resumen ≤200 palabras)
- La explicación debe ser en español y usar lenguaje no técnico comprensible para reclutadores sin perfil tech
- Actualiza la tabla APPLICATION con: ai_score, ai_summary (Markdown)
- Actualiza el campo stage del candidato a "Nuevos" en el pipeline
- Publica evento `application.scored` en Kafka para que el frontend actualice en tiempo real
- Tiempo total desde recepción del CV hasta score visible en UI < 30 segundos

**Prioridad:** Alta  
**Estimación:** 5 puntos  
**Asignado a:** Equipo AI/ML  
**Etiquetas:** ai-service, openai, gpt4, python  
**Sprint:** Sprint 3  
**Dependencias:** TICKET-003 (necesita el score calculado)

---

### TICKET-005 — Visualización del ranking en el frontend

**Tipo:** Feature  
**US asociada:** US-003  
**Épica:** Motor de IA — Cribado automático

**Descripción:**  
Implementar la vista de ranking de candidatos en el frontend de React, mostrando la puntuación IA, el resumen explicado y el filtro por score mínimo. Incluye actualización en tiempo real vía WebSocket cuando llegan nuevos candidatos analizados.

**Criterios de aceptación:**

- La vista de candidatos de una oferta muestra: nombre, puntuación (badge con color: verde ≥70, naranja 40-69, rojo <40), y snippet del ai_summary
- Al hacer clic en un candidato se abre un panel lateral con el resumen completo (strengths, gaps, explanation_md renderizado en Markdown)
- El slider "Puntuación mínima" (0-100) filtra los candidatos en tiempo real en el cliente (sin llamada al servidor)
- Cuando llega un nuevo candidato analizado (evento WebSocket `application.scored`), aparece automáticamente en el ranking sin recargar la página
- Los candidatos sin score aún (procesándose) muestran un spinner y texto "Analizando..."
- Funciona correctamente con hasta 500 candidaturas cargadas (paginación de 50 en 50)

**Prioridad:** Alta  
**Estimación:** 5 puntos  
**Asignado a:** Equipo Frontend  
**Etiquetas:** frontend, react, websocket, typescript  
**Sprint:** Sprint 3  
**Dependencias:** TICKET-004 (necesita el score y summary en la API)

---

## Estimación de esfuerzo — Método Fibonacci + T-shirt sizes

### Resumen de estimaciones

| Ticket           | Título                         | Story Points  | T-shirt | Complejidad                          |
| ---------------- | ------------------------------ | ------------- | ------- | ------------------------------------ |
| TICKET-001       | Endpoint recepción candidatura | 3             | S       | Baja — CRUD estándar + S3 + Kafka    |
| TICKET-002       | CV Parser                      | 5             | M       | Media — NLP + gestión de formatos    |
| TICKET-003       | Embeddings + Score             | 5             | M       | Media — integración OpenAI + fórmula |
| TICKET-004       | Score & Explain Engine         | 5             | M       | Media — prompt engineering + LLM     |
| TICKET-005       | Frontend ranking               | 5             | M       | Media — WebSocket + UX compleja      |
| **TOTAL US-003** |                                | **23 puntos** |         |                                      |

### Velocidad de referencia

Asumiendo un equipo de 4 desarrolladores con velocidad media de 20 pts/sprint (2 semanas), la US-003 completa requiere aproximadamente **2 sprints** (Sprint 2 para infra + Sprint 3 para IA completa y frontend).

---
