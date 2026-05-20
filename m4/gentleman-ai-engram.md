# [L26] Orquestación Multi-Agente y Memoria Persistente con Engram

> **Módulo:** M4 — Creación de Productos, SDD y Monetización  
> **Lección:** L26  
> **Tags:** `orquestación` `multi-agente` `engram` `memoria-persistente` `tmux` `multi-modelo` `solopreneur`

---

## 🧠 Core Insights

Hay una regla que los equipos de agentes en producción aplican consistentemente: **el orquestador nunca escribe código.** Coordina, delega, sintetiza — pero no implementa. Esta separación no es estética; es lo que hace posible procesar decenas de miles de líneas de código usando solo el 15-18% de la ventana de contexto.

Cuando el orquestador escribe código, su contexto se contamina con el proceso de implementación y llega rápidamente al límite. Cuando delega, recibe resúmenes concisos y mantiene su ventana limpia para la coordinación. La ventana de contexto es el recurso escaso del sistema — el diseño de quién la usa y para qué determina si el proyecto puede escalar.

**El patrón de delegación:** Cada subagente nace con contexto en blanco y una tarea exacta. Explora lo que necesita, hace el trabajo, devuelve un resumen conciso. Cuando termina, esa ventana se cierra. El orquestador no acumula el proceso interno de los subagentes — solo recibe resultados. Un agente monolítico se satura después de las primeras tareas. Un orquestador con subagentes puede manejar proyectos de cualquier tamaño.

**Multi-modelo — el modelo correcto para cada tarea:**
La estrategia que funciona en producción no usa el mismo modelo para todo. El costo varía con la capacidad, y asignar correctamente multiplica la eficiencia sin multiplicar el costo:
- **Opus** para planificación arquitectónica — decisiones de alto nivel que requieren razonamiento profundo
- **Sonnet** para implementación y escritura de código — calidad buena a menor costo
- **Haiku** para clasificación, formateo, tareas repetitivas — alto volumen, bajo requerimiento cognitivo
- **Modelos open source (Qwen, Llama via Ollama)** para tareas rutinarias de muy alto volumen — costo cero por token

**Engram** resuelve el problema estructural de los agentes: empiezan cada sesión desde cero. Sin memoria persistente, cada sesión nueva es un empleado de primer día — hay que re-explicar el contexto, las convenciones, los errores que no debe cometer. Engram guarda en base de datos local las decisiones de arquitectura, convenciones del equipo, bugs resueltos con su causa raíz, patrones establecidos, y preferencias de trabajo. Al inicio de cada nueva sesión, el agente carga el contexto relevante y continúa desde donde lo dejaste.

**El Solopreneur Harness:** La arquitectura completa da a un solo creador la capacidad operativa de un equipo de cuatro personas. El agente central actúa como Product Manager. Subagentes especializados tienen roles definidos: Frontend Developer, Security Reviewer, QA Specialist. Cada uno con ventana de contexto aislada, cerrando cuando termina. El PM integra resultados. No porque la IA sea perfecta — no lo es — sino porque la arquitectura distribuye el trabajo correctamente.

---

## ⚙️ Implementación Práctica

### Estructura del Solopreneur Harness

```
Orquestador (Product Manager)
├── Lee la documentación SDD como fuente de verdad
├── Delega tareas a subagentes especializados
├── Consolida resultados y resuelve dependencias
└── Nunca escribe código directamente

Subagentes (ventana de contexto aislada):
├── Frontend Developer   → componentes UI, Stitch/Claude Design
├── Backend Developer    → APIs, base de datos, autenticación
├── Security Reviewer    → auditoría de vulnerabilidades
└── QA Specialist        → Playwright, tests, verificación
```

### Instrucción de delegación correcta al orquestador

```
Eres el orquestador de este proyecto. Tienes acceso al plan SDD
en [ruta al documento].

Tu trabajo:
1. Lee el plan y entiende las dependencias entre tareas
2. Asigna cada tarea al subagente correcto
3. Cuando un subagente termina, recibe su resumen y continúa
4. Si hay dependencias entre tareas, espera a que la tarea
   previa esté completa antes de delegar la siguiente
5. NO escribas código directamente

Empieza con la primera tarea del plan.
```

### Protocolo de Engram — guardar durante la sesión

```
El agente debe guardar proactivamente durante la sesión:

mem_save({
  title: "Verbo + qué — descriptivo y buscable",
  type: "decision|bugfix|architecture|discovery|pattern|config",
  content: {
    what: "Una frase — qué se hizo",
    why: "Qué motivó la decisión o acción",
    where: "Archivos o paths afectados",
    learned: "Gotchas o casos edge que sorprendieron (opcional)"
  }
})
```

### Session Summary — protocolo de cierre obligatorio

```
Al finalizar cualquier sesión de trabajo, ejecutar:

mem_session_summary({
  goal: "En qué estábamos trabajando",
  discoveries: ["Hallazgos técnicos, gotchas, aprendizajes no obvios"],
  accomplished: ["Items completados con detalles clave"],
  next_steps: ["Qué queda por hacer en la próxima sesión"],
  relevant_files: ["path/archivo — qué hace o qué cambió"]
})

Sin este paso, la próxima sesión empieza sin contexto.
```

### Tmux — observabilidad visual del equipo de agentes

```bash
# Iniciar sesión Tmux con múltiples paneles
tmux new-session -s proyecto

# Dividir en paneles (uno por subagente)
# Ctrl+b % → dividir verticalmente
# Ctrl+b " → dividir horizontalmente

# Claude Code en modo Tmux: abre paneles por subagente automáticamente
# → ves en tiempo real qué está haciendo cada agente
# → cuántos están activos simultáneamente
# → qué herramientas están ejecutando
```

### Asignación de modelos — regla práctica

```yaml
# En la configuración del orquestador:
model_assignments:
  architecture_decisions: claude-opus-4-5
  planning:              claude-opus-4-5
  implementation:        claude-sonnet-4-5
  code_review:           claude-sonnet-4-5
  classification:        claude-haiku-3-5
  formatting:            claude-haiku-3-5
  bulk_processing:       ollama/qwen2.5  # costo cero
```

---

## 📌 Notas y Alertas

> [!IMPORTANT]
> El orquestador que escribe código contamina su contexto y llega al límite rápidamente. La regla "el orquestador nunca escribe código" no es una preferencia — es lo que hace posible procesar proyectos grandes usando solo el 15-18% de la ventana de contexto. Violarla colapsa el sistema en sesiones largas.

> [!NOTE]
> Engram no es solo conveniencia — es lo que convierte un agente episódico en un colaborador de largo plazo. Sin memoria persistente, cada sesión paga el costo de re-establecer el contexto. Con Engram, ese costo se paga una sola vez cuando se documenta la decisión, y se recupera sin costo en todas las sesiones siguientes.

> [!TIP]
> El routing de modelos no tiene que ser manual. Un orquestador bien configurado asigna el modelo según el tipo de tarea automáticamente. Define las reglas una vez en la configuración del harness y el sistema las aplica sin que intervengas en cada delegación.

> [!WARNING]
> El Session Summary al final de cada sesión es el paso más saltado y el más costoso de saltar. Sin él, la próxima sesión empieza con un agente que no sabe en qué estaba trabajando, qué decisiones se tomaron, o qué no se debe repetir. El costo de re-establecer ese contexto es mayor que el de documentarlo en el momento.

---

## 🔗 Ver también

- [SDD y Harness Engineering](./sdd-harness-engineering.md) — la metodología que el orquestador ejecuta
- [Startup Completa con n8n y Graphify](./startup-n8n-graphify.md) — el orquestador en acción en un producto completo
- [NotebookLM, RAG y Google Labs](../wiki/notebooklm-rag-google-labs.md) — herramienta para validar specs antes de delegar
- [Segundo Cerebro Empresarial](../wiki/segundo-cerebro-empresarial.md) — cómo Engram se integra en el sistema de conocimiento del M5

---

[⬅️ Volver al Índice Principal](../README.md)
