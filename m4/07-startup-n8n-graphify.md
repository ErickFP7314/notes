# [L07] Startup Completa: Workflows Agénticos y Graphify

> **Módulo:** M4 — Creación de Productos, SDD y Monetización  
> **Lección:** L07  
> **Tags:** `startup` `n8n` `graphify` `workflows-agénticos` `e2b` `mcp` `cuarta-era` `codebase`

---

## 🧠 Core Insights

Las eras del desarrollo de software siguen una lógica de abstracciones crecientes:
1. Programación pura — código desde cero
2. Scripts y librerías — código reusable
3. No-code drag and drop (Zapier, Make, n8n) — automatizaciones sin código
4. **La era actual — lenguaje natural como interfaz**

En la cuarta era no arrastras bloques ni llamas funciones. Describes el objetivo. El agente diseña la arquitectura, implementa la solución y la configura de principio a fin.

El ejemplo concreto que marca la diferencia: "Cuando llegue un email con PDF adjunto, extrae el contenido, genera un resumen y regístralo en Notion con prioridad asignada automáticamente". En la era tres: construyes ese flujo en n8n conectando nodos durante una hora. En la era cuatro: el agente usa el MCP de n8n, diseña la arquitectura del flujo, crea los triggers, las transformaciones y las acciones, y te lo entrega funcionando en minutos.

**El cambio de valor que esto implica:** lo que se vuelve valioso no es escribir código — es entender cómo la automatización beneficia al negocio, identificar cuellos de botella, diseñar sistemas que escalen, hacer las preguntas correctas. El criterio y la arquitectura son irreemplazables; la implementación se genera.

**La secuencia de una startup con el stack completo:**
- SDD primero: Constitución + Spec + Clarificación + Plan = el primer activo real del proyecto (vive en Git, no desaparece si el agente pierde contexto)
- Orquestador delega en paralelo: Frontend (Stitch/Claude Design) y Backend (BaaS/Railway) en worktrees aislados
- MCPs conectan a servicios externos: Notion, GitHub, Stripe
- n8n gestiona flujos de automatización que no requieren código personalizado
- La documentación SDD es la fuente de verdad que mantiene la coherencia del sistema

**Graphify** resuelve el problema de orientación en repositorios grandes. Un agente que empieza una sesión nueva en un repo de 100 archivos hace búsquedas lineales (grep) que consumen miles de tokens solo para entender la estructura — tiempo y dinero gastados antes de hacer trabajo real. El benchmark es directo: 52 archivos crudos en contexto = 123,000 tokens. Con Graphify, el agente lee el `graph_report.md` (~500 tokens) y consulta solo los clusters relevantes. **Reducción de 70 veces.**

Graphify opera en tres pasos: (1) parseo determinista gratuito con Tree-sitter en 25+ lenguajes — extrae qué función llama a qué, qué módulo importa cuál; (2) transcripción local de audio/video con Faster Whisper; (3) subagentes paralelos de Claude extraen significado de assets no-código (Markdown, PDFs, imágenes). El resultado es un knowledge graph consultable, no solo una lista de archivos.

---

## ⚙️ Implementación Práctica

### Instalar y correr Graphify

```bash
# Instalar
pip install graphify

# Generar el knowledge graph del proyecto
cd /path/to/proyecto
/graphify .

# Resultado: graph_report.md en la raíz del proyecto
# El agente lee este archivo al inicio de cada sesión
# en lugar de explorar el repo a ciegas
```

### Prompt de inicio de sesión con Graphify

```
Lee el archivo graph_report.md para entender la estructura
del proyecto antes de cualquier acción.

Usa el grafo para navegar el codebase — no hagas grep
ni leas archivos que el grafo ya describe.

Cuando necesites detalles de un componente específico,
consulta el cluster relevante del grafo primero.
```

### Flujo de startup con el stack completo M4

```
1. SDD (documentación primero)
   - Constitución: stack + convenciones + prohibiciones
   - Spec (PRD): problema + funcionalidades + alcance
   - Clarificación: el agente hace preguntas sobre casos edge
   - Plan (RFC): arquitectura técnica revisada y aprobada
   - Todo esto va a Git — es el activo real del proyecto

2. Orquestador configura worktrees paralelos
   git worktree add ../worktrees/frontend feature/frontend
   git worktree add ../worktrees/backend feature/backend

3. Subagentes trabajan en paralelo
   - Subagente Frontend: Stitch/Claude Design → Vercel
   - Subagente Backend: BaaS o Railway deploy

4. MCPs conectan los servicios externos
   - MCP Notion → documentación del producto
   - MCP GitHub → gestión del repositorio
   - MCP Stripe → pagos y suscripciones
   - MCP n8n → flujos de automatización

5. n8n gestiona automaciones de negocio
   - Notificaciones de nuevos usuarios
   - Sincronización de datos entre herramientas
   - Triggers de negocio (email → Notion, etc.)
```

### Crear un workflow en n8n via lenguaje natural (era 4)

```
Usando el MCP de n8n:

"Crea un workflow que:
1. Se active cuando llega un email con adjunto PDF
2. Extraiga el texto del PDF
3. Genere un resumen con Claude
4. Registre en Notion con campos: título, resumen, prioridad (alta/media/baja)
   donde la prioridad se asigna automáticamente según el contenido
5. Envíe notificación Slack al canal #nuevos-docs"

El agente diseña la arquitectura, configura los nodos
y entrega el workflow funcionando.
```

### E2B Sandboxes — para agentes que necesitan libertad total

```python
from e2b import Sandbox

# Entorno cloud efímero con control total
with Sandbox() as sandbox:
    # El agente puede instalar dependencias, ejecutar código arbitrario,
    # modificar el sistema de archivos — todo aislado de la máquina local
    result = sandbox.process.start("pip install dependencia-experimental")
    
    # Cuando el bloque termina, el sandbox desaparece
    # Sin riesgo para el entorno local
```

### Patrón Best of N con E2B

```
Orquestador lanza N sandboxes en paralelo:
- Sandbox 1: implementa la solución con enfoque A
- Sandbox 2: implementa la solución con enfoque B
- Sandbox 3: implementa la solución con enfoque C

Orquestador compara resultados según criterio definido:
- Tests que pasan
- Cobertura de casos edge
- Legibilidad del código
- Performance del benchmark

Selecciona la mejor solución sin revisión manual de cada iteración.
```

---

## 📌 Notas y Alertas

> 🔴 **Importante:** La reducción de 70x en tokens que logra Graphify no es solo un ahorro por sesión — es acumulativa. En sesiones largas con múltiples consultas, el agente que trabaja desde el grafo no repite lecturas de archivos ya procesados. La curva de ahorro crece con la duración del proyecto y el tamaño del codebase.

> 📌 **Nota:** Tree-sitter (el primer paso de Graphify) es completamente gratuito y determinista — no usa tokens de LLM. El costo de los subagentes Claude en el tercer paso es una inversión única: el graph_report.md generado se reutiliza en todas las sesiones siguientes.

> 💡 **Tip:** La startup no es un proyecto monolítico — es un sistema de agentes trabajando en paralelo sobre capas distintas, coordinados por un orquestador que usa la documentación SDD como fuente de verdad. Si algo no está en la documentación SDD, el orquestador no tiene criterio para decidir. Por eso la documentación primero es la regla de oro.

> ⚠️ **Advertencia:** El MCP de n8n requiere que tengas una instancia de n8n corriendo (local o cloud) y la URL de la API configurada. Sin la instancia activa, el agente puede diseñar el workflow correctamente pero no puede crearlo. Verifica la conectividad antes de delegar la tarea.

---

## 🔗 Ver también

- [Arquitectura Web: Los 5 Pilares](./01-arquitectura-web.md) — los fundamentos de lo que la startup implementa
- [SDD y Harness Engineering](./04-sdd-harness-engineering.md) — la metodología que estructura el desarrollo de la startup
- [Orquestación Multi-Agente y Engram](./05-gentleman-ai-engram.md) — el orquestador que coordina los subagentes
- [Pagos, Deploy y el Desarrollador Potenciado](./07-pagos-deploy-reflexion.md) — el cierre económico del ciclo
- [N8n y Flujos de Automatización](../wiki/05-n8n-flujos.md) — profundización en workflows de n8n del M3

---

[⬅️ Volver al Índice Principal](../README.md)
