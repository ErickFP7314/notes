# [L13] Claude Code: Instalación, Comandos y Manejo de Contexto

> **Módulo:** M2 — Agentes de IA y Entornos de Ejecución  
> **Lección:** L13  
> **Tags:** `claude-code` `plan-mode` `agent-view` `claude-md` `slash-commands` `contexto`

---

## 🧠 Core Insights

Boris Cherney, el creador de Claude Code, empieza el 80% de sus proyectos en **Plan Mode**. No en modo de implementación. No escribiendo código. Planificando.

Eso dice todo sobre cómo está diseñada la herramienta y cómo deberías usarla. Claude Code no es un autocomplete glorificado: es un agente que puede diseñar, implementar, testear y corregir software autónomamente. Pero solo funciona bien cuando le das el contexto correcto desde el principio.

### El sistema nervioso: claude.md

`claude.md` en la raíz del proyecto es la pieza más importante de tu configuración. Todo lo que el agente sabe sobre cómo trabajar contigo vive ahí. El agente lo lee al inicio de cada sesión — es su memoria persistente entre conversaciones.

La regla de oro: **bajo 1000 tokens**. No es documentación para humanos; son instrucciones para la IA. Un `claude.md` de 200 palabras con reglas claras supera a uno de 2000 palabras con todo lo que se te ocurrió documentar. Si creció demasiado, pídele al agente que lo limpie.

El símbolo `#` al inicio de cualquier mensaje en sesión hace que el agente memorice esa instrucción permanentemente en el `claude.md`. Si escribes `# No uses el tipo 'any' en TypeScript`, esa regla queda registrada y se aplica en todas las sesiones siguientes del proyecto.

### Comandos personalizados: la automatización dentro del agente

Dentro del repositorio, puedes crear `.claude/commands/` con archivos Markdown. Cada archivo es un comando personalizable:

```
.claude/commands/review.md    → /review activa esas instrucciones
.claude/commands/deploy.md    → /deploy activa el flujo de deploy
.claude/commands/test.md      → /test activa criterios de testing
```

Es el equivalente de una Skill pero específico para comandos de sesión. Para flujos repetitivos que repites en cada proyecto, esto es la forma de no reinventarlos cada vez.

### Agent View: paralelismo sin complejidad

Claude Code tiene una funcionalidad llamada **Agent View** que permite gestionar múltiples sesiones dentro del mismo proyecto. Presionas la flecha izquierda durante una sesión activa y accedes al gestor de sesiones.

Caso de uso directo: mientras una sesión trabaja en actualizar la documentación, abres otra para revisar la UI, y otra para un fix en la API. Las tres trabajan en paralelo, en el mismo repositorio, sin bloquearse entre sí. Cada una con su propio contexto mínimo.

---

## ⚙️ Implementación Práctica

### Instalación

```bash
# Opción 1: como herramienta de terminal (recomendada para uso avanzado)
npm install -g @anthropic-ai/claude-code

# Esto da el comando `claude` directamente en la terminal,
# independientemente del editor

# Iniciar Claude Code en el directorio actual:
claude

# Salir de la sesión:
Ctrl+C  (dos veces)
```

```
# Opción 2: extensión de VS Code / Cursor
→ Marketplace de extensiones → busca "Claude Code" o "Anthropic"
→ Instala la extensión oficial
→ Autentica con tu cuenta de Claude
```

### Slash commands esenciales

```bash
/init          # Escanea el proyecto y genera claude.md
               # → Ejecuta SIEMPRE al inicio de un proyecto nuevo

/compact       # Comprime el historial cuando el contexto se satura
               # → El agente resume lo esencial y continúa más liviano
               # → Úsalo cuando las respuestas pierden coherencia

/model         # Cambia el modelo en la sesión actual
               # → Empieza con Sonnet para exploración rápida
               # → Cambia a Opus para decisiones arquitectónicas complejas

/cost          # Muestra tokens consumidos y costo estimado de la sesión
/stats         # Estadísticas de uso de la sesión actual
```

### Atajos de Agent View

```bash
←  (flecha izquierda)    # Abre el gestor de sesiones
Ctrl+R                   # Renombra la sesión activa (usa etiquetas: "UI", "API", "Docs")
Ctrl+T                   # Fija la sesión en la parte superior de la lista
```

### El flujo Plan Mode → Implementación

```
1. Activa Plan Mode (botón en el panel o comando del agente)

2. Describe el problema. Luego añade:
   "Antes de proponer ninguna solución, entrevístame sobre el problema.
   Identifica los puntos de decisión y las ambigüedades.
   Genera el plan técnico y espera mi aprobación antes de escribir código."

3. El agente te hará preguntas:
   - Quién es el usuario final
   - Cuáles son las restricciones
   - Qué NO debe hacer la solución

4. Revisa el plan resultante. Corrígelo ahora — una línea en un doc
   de planificación vs. días de refactorización.

5. Aprueba el plan → pasa a modo implementación
   → No interrumpas durante la implementación salvo emergencia real
```

### Gestión del claude.md durante una sesión

```bash
# Añadir una regla permanente al claude.md:
# Escribe en el chat con # al principio:
# No uses el tipo 'any' en TypeScript
# Todos los componentes deben tener un test asociado
# Los commits siguen convención semántica: feat/fix/docs/chore

# Pedir al agente que limpie el claude.md cuando creció:
"Actualiza mi claude.md eliminando lo que ya no sea necesario,
lo que sea redundante o lo que haya quedado obsoleto."
```

### Señales para usar /compact

- Las respuestas ignoran reglas que definiste al inicio de la sesión
- El agente repite errores ya corregidos
- El código generado no sigue el estilo del proyecto
- Las respuestas se vuelven más genéricas y menos específicas al proyecto

---

## 📌 Notas y Alertas

> [!IMPORTANT]
> El 80% del uso profesional de Claude Code empieza en Plan Mode, no en implementación. La herramienta está diseñada así a propósito. Saltarse la planificación para "ir más rápido" produce código que el agente mismo tendrá que refactorizar más adelante — y ese ciclo de refactorización consume más contexto y más tiempo que la planificación inicial.

> [!WARNING]
> `/compact` comprime el historial pero **no es una solución infinita**. Úsalo cuando el contexto empieza a saturarse, pero si el proyecto requiere sesiones extremadamente largas, la solución correcta es rediseñar el flujo hacia múltiples sesiones cortas con Agent View, cada una con responsabilidad clara. La compresión reduce el ruido; no recupera calidad perdida por saturación severa.

> [!TIP]
> El comando `#` para memorizar instrucciones en `claude.md` es más valioso cuando lo usas para capturar **decisiones que surgen durante la implementación**. Si en medio de una sesión descubres que el patrón X no funciona en este proyecto, escríbelo con `#` en ese momento. No esperes al final de la sesión para actualizar el archivo manualmente.

> [!NOTE]
> Antigravity y Claude Code son el mismo motor con distinta interfaz. Para trabajo cotidiano con buena experiencia visual, Antigravity. Para scripts de automatización, integración con CI/CD, o cuando el editor pasa a segundo plano, Claude Code desde terminal tiene ventajas claras. Muchos developers usan ambos según el contexto — no hay que elegir uno definitivamente.

---

## 🔗 Ver también

- [Concepto de Agentes](./concepto-agentes.md) — los fundamentos de bucle autónomo y modo planificación que hacen que Claude Code funcione como funciona
- [Ecosistema de Agentes](./ecosistema-agentes.md) — contexto de Claude Code dentro del mapa completo de herramientas
- [OpenCode y Opciones Abiertas](./opencode-opciones.md) — la alternativa open source agnóstica de modelo
- [M3: Hooks y Arquitectura de Contexto](../m3/hooks-arquitectura-contexto.md) — cómo extender Claude Code con hooks para automatización avanzada
- [M4: SDD y Harness Engineering](../m4/sdd-harness-engineering.md) — metodología de desarrollo guiado por especificaciones sobre Claude Code

---

[⬅️ Volver al Índice Principal](../README.md)
