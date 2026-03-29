# Prompts utilizados — Práctica 2 LTI

**Autor:** GFS  
**Fecha:** Marzo 2026

---

## Prompts para generación de User Stories

### Prompt 1 — Generación inicial de User Stories (resultado: regular)

```
Genera user stories para un ATS llamado LTI con IA integrada.
```

**Resultado:** Demasiado genérico. Las historias generadas no estaban ancladas al diseño específico de LTI y no reflejaban las funcionalidades reales del sistema. No incluían criterios de aceptación ni evaluación INVEST.

---

### Prompt 2 — Con contexto del PRD pero sin estructura (resultado: mejorado)

```
Actúa como Product Owner. Basándote en este sistema LTI ATS con estas
funcionalidades: [lista de funciones], genera 5 user stories en formato
"Como [rol], quiero [acción], para [beneficio]".
```

**Resultado:** Mejor estructura de las historias, pero los criterios de aceptación eran vagos y no seguían formato BDD. La evaluación INVEST no era rigurosa.

---

### Prompt 3 — Completo con contexto, plantilla y criterios de calidad ⭐ MEJOR RESULTADO

```
Actúa como un Product Owner senior con experiencia en productos SaaS B2B
de RRHH y metodologías ágiles.

Contexto del producto:
- LTI es un ATS (Applicant Tracking System) SaaS multi-tenant
- Diferenciador clave: motor de IA explicable para cribado de CVs
- Arquitectura: microservicios con AI Service, Jobs Service, Applications
  Service, Interview Service, Notification Service
- Usuarios: reclutadores, hiring managers, candidatos
- Modelo de datos clave: Company, User, Job, Application, Interview,
  Scorecard, PipelineStage

Para cada User Story, utiliza esta plantilla obligatoria:
- ID: US-XXX
- Título descriptivo
- Historia: "Como [rol], quiero [acción], para [beneficio]"
- 3 criterios de aceptación en formato BDD (Dado/Cuando/Entonces)
  cubriendo: happy path, error/edge case, y escenario de colaboración
- Prioridad: Alta/Media/Baja
- Estimación en puntos Fibonacci (1,2,3,5,8,13)
- Evaluación INVEST con ✅ o ⚠️ para cada criterio
- Notas técnicas referenciando los microservicios implicados

Genera user stories para estas funcionalidades prioritarias:
1. Publicación de ofertas en múltiples canales
2. Asistencia de IA para redactar Job Descriptions
3. Cribado automático de CVs con ranking explicado
4. Gestión del pipeline Kanban en tiempo real
5. Coordinación automática de entrevistas
6. Evaluación colaborativa con scorecards
7. Dashboard de KPIs y analytics
```

**Por qué fue el mejor resultado:**

- Al proporcionar el contexto completo del PRD (arquitectura, modelo de datos, roles), las historias generadas estaban perfectamente ancladas a la realidad técnica del sistema.
- La plantilla obligatoria garantizó consistencia entre todas las US y que ningún campo quedara vacío.
- Pedir explícitamente 3 escenarios BDD (happy path + error + colaboración) produjo criterios de aceptación mucho más completos y testables.
- La referencia a microservicios específicos en las notas técnicas permitió que los tickets de trabajo posteriores fueran directamente utilizables por el equipo de desarrollo.

---

## Prompts para generación del Backlog priorizado

### Prompt 4 — Priorización con WSJF

```
Considera estas 7 user stories del backlog de LTI ATS con sus estimaciones
en puntos Fibonacci: [lista de US con estimaciones].

Aplica la metodología WSJF (Weighted Shortest Job First) de SAFe para
priorizarlas. Para cada US asigna puntuación del 1 al 10 en:
- Valor de negocio (impacto en activación y retención de clientes)
- Urgencia temporal (¿pierde valor si se retrasa?)
- Reducción de riesgo / oportunidad

Calcula el ratio WSJF = (Valor + Urgencia + Riesgo) / Esfuerzo y ordena
el backlog de mayor a menor ratio. Presenta el resultado en tabla Markdown.

Justifica brevemente por qué las 3 primeras historias deben ir al Sprint 1.
```

**Conclusión:** Este prompt produjo una priorización bien razonada. El uso de WSJF en lugar de métodos más simples (MoSCoW) dio un resultado más objetivo y defendible ante stakeholders.

---

## Prompts para tickets de trabajo

### Prompt 5 — Generación de tickets técnicos

```
Actúa como un Tech Lead con experiencia en arquitecturas de microservicios
y equipos ágiles.

Tenemos esta User Story: [US-003 completa con criterios de aceptación]

La arquitectura del sistema es:
- AI Service: FastAPI + Celery + Redis + PostgreSQL + Kafka
- Modelo de datos: APPLICATION (id, job_id, candidate_id, ai_score,
  ai_summary, cv_url), componentes: CV Parser, Embeddings Generator,
  JD Matcher, Score & Explain Engine

Descompón la User Story en tickets de trabajo técnicos listos para
una sesión de sprint planning. Para cada ticket incluye:
- Título claro y conciso
- Descripción con contexto técnico
- Criterios de aceptación técnicos y medibles
- Dependencias entre tickets
- Estimación en puntos Fibonacci
- Equipo responsable (Backend / AI/ML / Frontend)
- Etiquetas para categorización

Asegúrate de que cada ticket sea completable en 1-3 días de trabajo.
```

**Conclusión:** Al incluir la arquitectura técnica específica y el modelo de datos en el contexto, los tickets generados fueron directamente utilizables sin necesidad de grandes ajustes. El nivel de detalle técnico (nombres de tablas, eventos Kafka, APIs externas) ahorró tiempo en la sesión de planning.

---
