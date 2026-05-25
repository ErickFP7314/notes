# [L09] OpenCode y Opciones Abiertas: Libertad de Modelo y Paralelismo

> **Módulo:** M2 — Agentes de IA y Entornos de Ejecución  
> **Lección:** L09  
> **Tags:** `opencode` `openclaw` `modelos` `multi-sesion` `open-source` `codex`

---

## 🧠 Core Insights

Claude Code es excelente cuando quieres la integración más directa con los modelos de Anthropic. Pero hay situaciones donde no quieres estar atado a un proveedor: cuando cambias de modelo según la tarea, cuando el proyecto tiene restricciones de privacidad que impiden enviar código a servidores externos, o cuando necesitas gestionar múltiples tareas en paralelo con máximo control.

Para esos casos, OpenCode es la respuesta.

### La diferencia central: agnóstico de modelos

La característica que define a OpenCode es que **no está atada a ningún proveedor**. En la misma sesión puedes usar Claude, GPT-4o, DeepSeek, modelos locales de Ollama, o cualquier modelo disponible en OpenRouter. Con `/models` dentro de una sesión activa se abre un selector de cambio de modelo en segundos.

Esto cambia el flujo de trabajo de forma concreta: puedes empezar explorando con un modelo liviano y barato, cambiar a Opus para el momento de diseño arquitectónico, y volver al modelo rápido para la implementación. Sin reiniciar la sesión. Sin perder el contexto acumulado.

### El cerebro del agente: archivos de texto

OpenClaw (la versión orientada a agentes autónomos en servidor) tiene una arquitectura de archivos que ilustra perfectamente la filosofía del agente: **todo vive en archivos de texto que puedes leer, editar y versionar con git**.

- `soul.md` — personalidad del agente: comportamiento, tono, prioridades
- `agents.md` — límites y reglas del agente
- `user.md` — descripción de quién eres y cómo trabajas
- `tools.md` — registro de cómo usar herramientas específicas

No hay magia interna. No hay configuración oculta. Todo está en archivos. Esta arquitectura refuerza el principio de L09: el repositorio es la memoria del agente.

### Heartbeat: proactividad sin supervisión

OpenClaw tiene un concepto llamado **heartbeat**: el agente se despierta cada 30 minutos para revisar una lista de tareas pendientes, aunque no estés activamente usando la sesión. Puede avisarte si algo necesita atención sin que tú se lo preguntes.

Esta funcionalidad existe en la versión cloud de OpenClaw (corriendo en un VPS), no en el cliente de terminal estándar. Es lo que convierte a OpenClaw en un agente genuinamente autónomo — no solo reactivo a tus mensajes, sino proactivo respecto a su lista de tareas.

### Codex: para flujos corporativos, no para código

Codex de OpenAI es un caso distinto. Mientras Claude Code y OpenCode están optimizados para trabajo técnico desde terminal, Codex está diseñado para **automatizaciones sin código**: conectar Gmail con Slack, revisar campañas, navegar páginas web de forma autónoma.

Funciona a través de automatizaciones con plugins — conectas los servicios, le dices en lenguaje natural qué hacer y con qué frecuencia, el agente lo ejecuta solo.

Su funcionalidad más avanzada es **Computer Use**: control del sistema operativo completo. No solo del navegador, sino de cualquier aplicación. Navegar páginas web, abrir aplicaciones, hacer clics, rellenar formularios. Para empresas que necesitan automatizar flujos con interfaces gráficas que no tienen API, esto es genuinamente útil.

Para desarrollo de código, Claude Code u OpenCode son más adecuados. Codex brilla en automatización de flujos de negocio y entornos corporativos donde el ecosistema Microsoft-OpenAI es prioritario.

---

## ⚙️ Implementación Práctica

### Instalación de OpenCode

```bash
# Instalar globalmente:
npm install -g opencode-ai

# Iniciar en cualquier directorio:
opencode

# La primera vez, configurar claves de API:
# → Solo configura las que tienes — añades más proveedores después
# → Puedes funcionar con solo OpenRouter como proveedor universal
```

### Cambio de modelo en tiempo real

```bash
# Dentro de una sesión activa:
/models

# → Se abre un selector interactivo
# → Cambias de modelo en segundos sin reiniciar

# Flujo recomendado por tipo de tarea:
# Exploración inicial    → Modelo liviano (Gemma, Llama, DeepSeek)
# Diseño arquitectónico  → Claude Opus o GPT-4o
# Implementación         → Claude Sonnet (equilibrio velocidad/calidad)
# Tareas privadas        → Modelo local via Ollama
```

### Gestión de sesiones paralelas

```bash
# OpenCode gestiona múltiples tareas en paralelo en el mismo proyecto
# sin abrir terminales adicionales

# Ejemplo de sesiones simultáneas:
# Sesión A: documentación    (contexto: solo docs/)
# Sesión B: fix de bug       (contexto: solo src/api/bug-123/)
# Sesión C: revisión de API  (contexto: solo src/api/)

# Cambiar entre sesiones: atajo de teclado en el TUI de OpenCode
```

### Arquitectura de archivos OpenClaw (para agentes en servidor)

```
workspace/
├── soul.md      # "Eres un agente enfocado en desarrollo backend.
│                #  Prioriza código limpio, tests, y documentación clara."
├── agents.md    # "No modifiques archivos fuera de src/. 
│                #  No ejecutes comandos de eliminación sin confirmación."
├── user.md      # "Trabajo en Python/FastAPI. Prefiero async sobre sync.
│                #  Convenciones: snake_case, type hints en todo."
└── tools.md     # Registro de herramientas disponibles y cómo usarlas
```

### Tabla de decisión: cuándo usar OpenCode sobre Claude Code

| Situación | Herramienta recomendada |
|-----------|------------------------|
| Cambio de modelo según tarea o costo | OpenCode |
| Múltiples sesiones paralelas en un proyecto | OpenCode |
| Privacidad: código no puede ir a servidores externos | OpenCode + Ollama |
| Independencia de Anthropic por estrategia | OpenCode |
| Integración profunda con Claude, Proyectos, Claude Design | Claude Code + Claude Pro |
| Rutinas de Anthropic, ya todo configurado y funciona | Claude Code (no cambiar) |
| Automatización de flujos corporativos sin código | Codex |
| Agente 24/7 en servidor, sin tu PC encendida | OpenClaw en VPS |

---

## 📌 Notas y Alertas

> 🔴 **Importante:** La separación **Plan Mode / Build Mode es más explícita en OpenCode que en Claude Code**. En OpenCode, son dos modos con cambio visual claro. Esto reduce el riesgo de que el agente empiece a implementar antes de que hayas validado el plan. Si en Claude Code te pasa que el agente "se adelanta" a escribir código, considera que en OpenCode ese comportamiento está más controlado estructuralmente.

> ⚠️ **Advertencia:** OpenClaw (versión cloud/servidor) no es lo mismo que OpenCode (cliente de terminal). Son dos productos de la misma familia. OpenCode es para tu máquina. OpenClaw es para servidores autónomos. El heartbeat, la proactividad y la comunicación por Telegram/Discord son características de OpenClaw, no del cliente de terminal. No esperes esas funcionalidades si instalas OpenCode localmente.

> 💡 **Tip:** OpenRouter como proveedor en OpenCode es la combinación más flexible para alguien en fase de aprendizaje: $5 de crédito, acceso a múltiples modelos, y sin estar atado a ningún proveedor. Configura OpenRouter como proveedor principal en OpenCode, usa modelos baratos (DeepSeek, Llama) para exploración, y cambia a Claude Sonnet via OpenRouter solo para las tareas que lo justifican.

> 📌 **Nota:** La arquitectura de archivos de OpenClaw (`soul.md`, `agents.md`, `user.md`) es conceptualmente idéntica al sistema de `claude.md` + Skills de Claude Code/Antigravity. La diferencia es la granularidad: OpenClaw separa en múltiples archivos lo que Claude Code consolida en `claude.md`. El principio subyacente es el mismo: el repositorio de texto plano es la memoria y las instrucciones del agente.

---

## 🔗 Ver también

- [Ecosistema de Agentes](./02-ecosistema-agentes.md) — mapa completo de herramientas donde OpenCode encaja como agente open source
- [Ollama y Modelos Locales](./04-ollama-modelos-locales.md) — OpenCode + Ollama es la combinación para trabajo local sin costos de API
- [Claude Code](./06-claude-code.md) — la alternativa privativa de Anthropic para comparar directamente

---

[⬅️ Volver al Índice Principal](../README.md)
