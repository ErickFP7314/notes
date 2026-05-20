# [L25] Harness Engineering y Spec-Driven Development

> **Módulo:** M4 — Creación de Productos, SDD y Monetización  
> **Lección:** L25  
> **Tags:** `sdd` `harness` `metodología` `specs` `prd` `rfc` `agentes` `vibe-coding`

---

## 🧠 Core Insights

El dato que lo cambia todo: cambiando solo el sistema que envuelve al modelo — sin tocar el modelo, el prompt, ni el LLM — los benchmarks de performance saltaron **del 42% al 95%**. El modelo no es el límite. El límite es el harness que lo rodea.

**Harness Engineering** es el diseño de la infraestructura completa, el entorno y los guardarraíles que rodean a un modelo para que ejecute tareas complejas de forma fiable en producción. La analogía del Fórmula 1: el modelo es el piloto; el harness es el chasis, la telemetría y el equipo de pit. El mejor piloto del mundo no gana sin el sistema que lo rodea.

El harness tiene dos tipos de componentes que operan en momentos distintos:

- **Guides (feed-forward):** Le dicen al modelo qué hacer *antes* de que actúe. `claude.md`, `agents.md`, Skills, system prompts, archivos de arquitectura machine-readable. Configuran el comportamiento correcto de antemano.
- **Sensors (feedback loops):** Detectan errores *después* de que el agente actúa y devuelven correcciones. Linters, tests automáticos, verificadores de tipos, hooks de validación. Generan el closed loop que permite autocorrección sin intervención humana.

Un harness maduro tiene ambos. Un harness que solo tiene Guides pero no Sensors requiere supervisión humana constante. Un harness que solo tiene Sensors pero no Guides produce trabajo inconsistente que los Sensors no pueden siempre detectar.

**El Principio de Sustracción** es contraintuitivo: a medida que los modelos mejoran, el harness correcto se *poda*, no crece. Más instrucciones no es mejor — es ruido en el contexto. Si el modelo ya hace algo bien de forma inherente, la regla que lo forzaba antes degrada el rendimiento al añadir procesamiento innecesario. Si tu `claude.md` crece constantemente sin que nada salga, estás añadiendo complejidad sin añadir valor.

**SDD (Spec-Driven Development)** convierte el Harness Engineering en proceso estructurado. Resuelve el problema del vibe coding a escala: sin documentación, el agente toma las decisiones arquitectónicas por ti. Algunas son correctas; otras generan deuda técnica que tarda meses en resolverse. En SDD, **la documentación es el entregable real. El código es simplemente el resultado de esa documentación.**

---

## ⚙️ Implementación Práctica

### Las 5 fases de SDD

```
Fase 1 — CONSTITUCIÓN
  Documento con las reglas no negociables:
  - Stack tecnológico elegido + razón
  - Convenciones del equipo (naming, estructura de archivos)
  - Prohibiciones explícitas (qué NO usar)
  
  Sin esto: el agente puede implementar la misma funcionalidad
  de tres formas distintas en tres partes del código.

Fase 2 — ESPECIFICACIÓN (PRD)
  El producto en términos de negocio:
  - Qué problema resuelve
  - Qué funcionalidades tiene
  - Alcance y no-alcance
  Sin detalles técnicos todavía.

Fase 3 — CLARIFICACIÓN
  El agente lee Constitución + Spec y hace preguntas
  sobre los vacíos lógicos que encuentra.
  
  Aquí la IA te fuerza a pensar en lo que omitiste.
  Las preguntas del agente son el QA de tu spec.

Fase 4 — PLAN (RFC)
  El agente genera el plan técnico:
  - Estructura de archivos
  - Dependencias entre componentes
  - Secuencia de implementación
  
  El humano revisa y aprueba.
  Cambiar el plan es trivial. Cambiar la arquitectura
  después de escribir código es caro.

Fase 5 — EJECUCIÓN
  El agente toma el plan aprobado y lo ejecuta
  paso a paso, marcando cada tarea completada.
```

### PRD vs RFC — mantener el control correcto

```
PRD (Product Requirements Document)
  → Define el QUÉ y el POR QUÉ
  → Reglas de negocio, problema, alcance
  → Lo escribe el HUMANO con ayuda de la IA
  → Aquí vive tu criterio irreemplazable

RFC (Request for Comments)
  → Define el CÓMO técnico
  → Arquitectura, decisiones de base de datos, trade-offs
  → Lo genera la IA a partir del PRD
  → El humano revisa, no inventa desde cero

La distinción fuerza a mantener control sobre decisiones
de negocio mientras delegas las técnicas.
```

### Las tres capas de desarrollo con IA

```
Prompt Engineering    → ¿Qué preguntar?
Context Engineering   → ¿Qué enviar al modelo?
Harness Engineering   → ¿Cómo opera el sistema completo?
                         (sandboxes, permisos, loops de
                          verificación, docs machine-readable)
```

### Validar el Spec con NotebookLM

```
1. Escribir el Spec completo (PRD + RFC)
2. Subir a NotebookLM como fuente
3. Generar audio "Podcast" — dos voces debaten el documento
4. Escuchar mientras haces otra cosa

Por qué funciona: leer tu propio documento te hace ver lo que
QUISISTE escribir. Escucharlo debatido te hace ver lo que
REALMENTE escribiste. Las ambigüedades y contradicciones
aparecen en audio de una forma que la lectura no detecta.
```

### Prompt para la fase de Clarificación

```
Lee la Constitución y la Especificación adjuntas.
Identifica todos los vacíos lógicos, ambigüedades y casos edge
que no están cubiertos.

Para cada uno, formula una pregunta específica que me ayude
a definir el comportamiento correcto antes de planificar.

No implementes nada todavía. Solo pregunta.
```

---

## 📌 Notas y Alertas

> 🔴 **Importante:** El vibe coding funciona para demos. Para proyectos reales que deben crecer y mantenerse, el harness es lo que hace la diferencia entre un agente que entrega y uno que produce desastres de nueve dólares por sesión. Construir sin harness es posible — hasta el punto donde ya no lo es.

> ⚠️ **Advertencia:** Más instrucciones en el `claude.md` no es siempre mejor. El Principio de Sustracción dice que la sobre-ingeniería del harness degrada el rendimiento porque añade ruido al contexto. Revisa periódicamente qué reglas siguen siendo necesarias y elimina las que el modelo ya respeta de forma inherente.

> 📌 **Nota:** La fase de Clarificación en SDD es donde más valor oculto hay. Cuando el agente pregunta "¿qué pasa cuando el usuario cancela su suscripción antes del próximo ciclo de facturación?", te está haciendo pensar en un caso edge que hubieras descubierto en producción — con usuarios reales y dinero real sobre la mesa.

> 💡 **Tip:** NotebookLM como herramienta de validación de specs no es obvia. El cambio de medio (de leer a escuchar) activa una percepción diferente del documento. Los creadores que usan este paso reportan encontrar entre 3 y 7 ambigüedades importantes por documento que no habían detectado leyendo.

---

## 🔗 Ver también

- [Backend Automático y Git Worktrees](./03-backend-git-worktrees.md) — donde SDD se aplica en la construcción del backend
- [Orquestación Multi-Agente y Engram](./05-gentleman-ai-engram.md) — el sistema que ejecuta el plan SDD con múltiples agentes
- [Claude Code: Fundamentos](../wiki/06-claude-code.md) — el agente que ejecuta las fases de SDD
- [Hooks y Arquitectura de Contexto](../wiki/07-hooks-arquitectura-contexto.md) — los Sensors del harness (hooks de validación)

---

[⬅️ Volver al Índice Principal](../README.md)
