# [L12] Suscripciones y Precios: Qué Compras con Cada Plan

> **Módulo:** M2 — Agentes de IA y Entornos de Ejecución  
> **Lección:** L12  
> **Tags:** `precios` `claude-pro` `openrouter` `gemini` `chatgpt` `suscripciones`

---

## 🧠 Core Insights

Los planes de IA no son todos iguales. Antes de elegir qué pagar, necesitas entender qué estás comprando: algunos te dan **acceso al modelo**, otros te dan el modelo más **infraestructura del agente**, y otros te dan un **ecosistema completo** con memoria, herramientas integradas y flujos no-código.

Pagar el plan equivocado es pagar por cosas que no usas, o quedarte sin las que necesitas.

### Claude Pro — $20/mes

El plan de referencia para trabajo técnico intenso con agentes en este curso. Incluye:
- Acceso prioritario a Claude Sonnet y Opus con límites de uso significativamente más altos que el plan gratuito
- Proyectos con memoria persistente entre sesiones
- Acceso a Claude Design (construcción de interfaces web desde Claude)
- Posibilidad de usar Claude Code conectado a tu cuenta de API

Si vas a usar Claude Code como herramienta principal de desarrollo y quieres el mejor modelo de Anthropic con límites altos, este es el plan directo.

### Google One AI Premium — $19/mes

La propuesta de valor es la **integración con el ecosistema Google**. Incluye Gemini Advanced (el modelo más potente de Google) más integración nativa con Drive, Docs, Gmail y Calendar — Gemini funciona directamente dentro de los documentos. Para quienes trabajan principalmente dentro del ecosistema de Google, tiene sentido. Para trabajo técnico orientado a código, Claude Pro es más relevante.

### ChatGPT Plus — $20/mes

El plan más **generalista** de los tres. Acceso a GPT-4o con límites más altos, GPTs personalizados, Deep Search con resultados en tiempo real, y Sora para generación de video. Útil si tu trabajo requiere múltiples modalidades (texto, imagen, video, búsqueda en tiempo real). Para desarrollo de código como actividad principal, no es la opción más enfocada.

### OpenRouter — ~$5 de crédito

La opción que muy poca gente conoce. OpenRouter es un **agregador de modelos**: en lugar de pagar una suscripción mensual fija, pagas por lo que consumes, con acceso a versiones de los modelos más potentes del mercado (DeepSeek, Llama, Mistral, y otros modelos abiertos) a un costo por token menor que los planes directos.

Con $5 de crédito, un usuario en fase de aprendizaje puede tener acceso durante **semanas**. El flujo: conectas OpenRouter como proveedor en OpenCode u otro cliente compatible, y en lugar de estar atado a un proveedor, eliges el modelo más conveniente para cada tarea.

**Lo que no tienes con OpenRouter**: sin Proyectos de Claude, sin historial persistente dentro de la plataforma de Anthropic, sin Claude Design. Tienes el modelo y la API. Para trabajo con agentes donde el contexto lo manejas tú con archivos locales (`claude.md`, Skills), eso es suficiente.

---

## ⚙️ Implementación Práctica

### Árbol de decisión

```
¿Tu flujo principal es desarrollo técnico con agentes?
├── SÍ + quiero lo mejor de Anthropic → Claude Pro ($20/mes)
├── SÍ + quiero flexibilidad de modelo → OpenRouter ($5 crédito)
└── NO ↓

¿Trabajas mayormente en el ecosistema Google (Drive, Docs, Gmail)?
├── SÍ → Google One AI Premium ($19/mes)
└── NO ↓

¿Necesitas múltiples modalidades (video, imagen, búsqueda en tiempo real)?
└── ChatGPT Plus ($20/mes)
```

### Tabla comparativa

| Plan | Precio | Modelo principal | Integración agente | Mejor para |
|------|--------|-----------------|-------------------|------------|
| Claude Pro | $20/mes | Sonnet + Opus | Claude Code nativo | Desarrollo técnico con agentes |
| Google One AI Premium | $19/mes | Gemini Advanced | Workspace (Docs/Drive) | Flujo de trabajo en Google |
| ChatGPT Plus | $20/mes | GPT-4o | GPTs personalizados | Multimodalidad generalista |
| OpenRouter | ~$5 crédito | Múltiples | Cualquier cliente API | Aprendizaje, presupuesto limitado |
| Gratuito (cualquier plataforma) | $0 | Modelo limitado | Sin agente completo | Exploración inicial del curso |

### Configurar OpenRouter en OpenCode

```bash
# Al iniciar OpenCode por primera vez:
opencode

# En la configuración de proveedores:
# → Proveedor: OpenRouter
# → API Key: tu clave de openrouter.ai
# → Modelo por defecto: elige según la tarea

# Para cambiar de modelo en tiempo real durante la sesión:
/models
```

### Maximizar el plan gratuito durante el aprendizaje

Los planes gratuitos de Claude, Gemini y ChatGPT son suficientes para seguir la mayoría de lecciones de este curso. Los límites son más ajustados y el acceso a los modelos más potentes es restringido, pero:
- Para entender conceptos y seguir ejemplos: funciona
- Para las lecciones prácticas con código: funciona con límites
- Para producción con agentes ejecutando tareas complejas en largas sesiones: necesitas plan de pago

---

## 📌 Notas y Alertas

> 🔴 **Importante:** La distinción clave que no es obvia: **Claude Pro** te da acceso al ecosistema completo de Anthropic (Proyectos, Claude Design, memoria persistente en la plataforma). **OpenRouter** te da solo el modelo vía API. Si tu gestión de contexto depende de Proyectos de Claude o del historial de claude.ai, necesitas Claude Pro. Si gestionas todo el contexto localmente (claude.md, Skills, archivos), OpenRouter es suficiente y más barato.

> ⚠️ **Advertencia:** Los créditos de OpenRouter no se renuevan automáticamente. Son pay-as-you-go: cuando los gastas, recargas. Para alguien en fase intensiva de desarrollo esto puede ser más caro que una suscripción mensual. Calcula tu consumo estimado antes de optar por créditos vs suscripción fija.

> 💡 **Tip:** OpenRouter soporta la API de OpenRouter directamente en OpenCode con `/models`. Desde ahí puedes cambiar entre DeepSeek (muy económico, competitivo en código), Llama 3 (open source, gratuito en muchas plataformas), o Mistral según el costo y la tarea. Un mismo $5 de crédito puede durar más si usas modelos más baratos para trabajo de volumen y reservas los más capaces para razonamiento.

> 📌 **Nota:** Google One AI Premium incluye 2 TB de almacenamiento en Google Drive además del acceso a Gemini Advanced. Si ya ibas a pagar por ese almacenamiento, el plan se vuelve prácticamente gratuito como herramienta de IA. Es un dato que cambia el cálculo si eres usuario intensivo del ecosistema Google.

---

## 🔗 Ver también

- [Ecosistema de Agentes](./ecosistema-agentes.md) — qué agentes necesitan qué tipo de plan para funcionar
- [Claude Code](./claude-code.md) — cómo conectar Claude Code con Claude Pro para trabajo técnico completo
- [OpenCode y Opciones Abiertas](./opencode-opciones.md) — OpenCode es el cliente que mejor aprovecha OpenRouter como proveedor

---

[⬅️ Volver al Índice Principal](../README.md)
