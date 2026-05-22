# [L02] Ecosistema de Agentes: Mapa, Tipos y Criterios de Elección

> **Módulo:** M2 — Agentes de IA y Entornos de Ejecución  
> **Lección:** L02  
> **Tags:** `ecosistema` `claude-code` `opencode` `multi-agente` `cursor` `agentes-cloud`

---

## 🧠 Core Insights

El ecosistema de agentes de código se divide en cuatro categorías según **dónde viven** y **cómo se usan**. No son intercambiables: cada una resuelve un conjunto de problemas distinto.

### Agentes de terminal privativos

Claude Code (Anthropic) y Codex (OpenAI) son los más populares como punto de entrada. Funcionan bien desde el primer día sin configuración adicional porque están directamente integrados con sus modelos. La desventaja es el lock-in: estás limitado al modelo que el proveedor decide en cada momento.

### Agentes de terminal open source

OpenCode es el más completo: soporta cualquier modelo —Claude, GPT, modelos locales, modelos chinos— y permite cambiar de modelo en tiempo real durante la misma sesión. Goose es una alternativa escrita en Rust, notablemente más rápida para tareas que implican ejecutar múltiples acciones en paralelo.

### Editores AI-first

Herramientas donde el código sigue siendo visible pero el agente es el protagonista. **Cursor** fue el referente durante 2024: Plan Mode integrado, checkpoints para restaurar estados anteriores del proyecto, y acceso a múltiples modelos. **Windsurf** es la alternativa más reciente con interfaz más limpia y modelo propio (Codeium). Aquí la diferencia con los agentes de terminal es la visibilidad del código: en estos editores, el desarrollador sigue viendo y aprobando cambios de forma más granular.

### Agentes cloud

OpenClaw y Hermes corren en servidores remotos (VPS o Raspberry Pi), funcionan las 24 horas sin que tu computadora esté encendida, y se comunican por Telegram o Discord. Son para tareas verdaderamente autónomas en segundo plano: vigilar precios, recopilar datos, enviar resúmenes diarios. La computadora del desarrollador es irrelevante para la ejecución.

### Multi-agente: el nivel siguiente

Más allá de un solo agente haciendo todo, está la arquitectura de **múltiples agentes trabajando en paralelo con roles distintos**. Abacus Swarm es el ejemplo más claro en producción: le das un prompt de alto nivel y el sistema lanza agentes especializados simultáneamente —uno para la API, otro para el frontend, otro para la base de datos— que coordinan sus outputs.

Lo que cambia con multi-agente no es solo la velocidad. Es la **calidad**: cuando cada agente tiene un contexto mínimo y una responsabilidad clara, produce resultados más coherentes que un agente solo intentando hacer todo con un contexto saturado. El límite del 20-40% de degradación de contexto (ver L08) se vuelve menos relevante porque cada agente lleva mucho menos contexto.

### La filosofía del agente mínimo

Pi es una herramienta que no destaca por capacidades sino por su ausencia de ellas. Sin integraciones preinstaladas, sin modelo fijo, sin características que no pediste. Su ventaja: no asume nada. El costo de las herramientas que no usas es cero, y el contexto que no cargas es contexto que no satura al agente.

Esta es la filosofía correcta: **el mejor agente para tu caso es el que tiene exactamente las herramientas que necesita y ninguna más**.

---

## ⚙️ Implementación Práctica

### Árbol de decisión para elegir agente

```
¿Necesitas que trabaje mientras duermes sin tu PC encendida?
├── SÍ → OpenClaw / Hermes en VPS
└── NO ↓

¿Quieres controlar el modelo o cambiar de modelo según la tarea?
├── SÍ → OpenCode (open source, agnóstico de proveedor)
└── NO ↓

¿Prefieres interfaz visual o línea de comandos?
├── Visual → Cursor o Windsurf
└── Terminal → Claude Code (integración máxima con Anthropic)
```

### Mapa del ecosistema por categoría

| Categoría | Herramienta | Modelo | Cuándo usarla |
|-----------|-------------|--------|---------------|
| Terminal privativo | Claude Code | Claude (fijo) | Integración profunda con Anthropic |
| Terminal privativo | Codex | GPT (fijo) | Ecosistema OpenAI/Microsoft |
| Terminal open source | OpenCode | Cualquiera | Libertad de modelo, multi-sesión |
| Terminal open source | Goose | Cualquiera | Velocidad en tareas paralelas |
| Editor AI-first | Cursor | Múltiples | Visibilidad del código, checkpoints |
| Editor AI-first | Windsurf | Codeium + otros | UI limpia, modelo propio |
| Cloud autónomo | OpenClaw | Configurable | Agente 24/7 en VPS |
| Exploración / minimalismo | Pi | Sin default | Proyectos donde el contexto limpio importa |

### Configurar un flujo multi-agente básico con Claude Code

```bash
# Sesión 1: documentación (contexto: solo docs/)
claude --context docs/

# Sesión 2: fix de bug en API (contexto: solo src/api/)
claude --context src/api/

# Sesión 3: revisión de UI (contexto: solo src/frontend/)
claude --context src/frontend/
```

Cada sesión trabaja en paralelo, en el mismo repositorio, con contexto mínimo y responsabilidad clara.

---

## 📌 Notas y Alertas

> 🔴 **Importante:** La transición del ecosistema ya está ocurriendo: para un número creciente de desarrolladores, el editor solo se abre para revisar lo que ya hizo la IA. La interfaz principal ya no es el editor — es el agente. Diseñar tu flujo de trabajo asumiendo que el agente es el protagonista (no la asistencia) cambia qué herramientas eliges y cómo las configuras.

> ⚠️ **Advertencia:** Antigravity (el agente de entrada del curso) es una interfaz gráfica que integra Claude Code en VS Code. Mismo poder, curva de entrada más amable. Si después decides escalar a terminal pura con Claude Code o migrar a OpenCode, la configuración de Skills y claude.md es transferible — no empiezas desde cero.

> 💡 **Tip:** La arquitectura multi-agente no requiere herramientas especiales para empezar. Claude Code con Agent View ya permite múltiples sesiones paralelas en el mismo repositorio. La clave es diseñar tareas con contexto mínimo y responsabilidad clara por sesión, no abrir una sola sesión que intente hacer todo.

> 📌 **Nota:** LM Studio y Ollama no aparecen en este mapa porque no son agentes: son infraestructura para ejecutar modelos locales que luego se conectan a los agentes. Son la capa por debajo del ecosistema de agentes, no parte de él.

---

## 🔗 Ver también

- [Antigravity: Instalación y Skills](./03-antigravity-skills.md) — el agente de entrada del curso, cómo instalarlo y configurarlo
- [Ollama y Modelos Locales](./04-ollama-modelos-locales.md) — capa de infraestructura para modelos locales que se conecta a cualquier agente
- [Claude Code](./06-claude-code.md) — el agente privativo de Anthropic en profundidad
- [OpenCode y Opciones Abiertas](./07-opencode-opciones.md) — el agente open source agnóstico de modelo

---

[⬅️ Volver al Índice Principal](../README.md)
