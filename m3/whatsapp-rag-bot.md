# [L20] WhatsApp RAG Bot: Base de Conocimiento con IA

> **Módulo:** M3 — Prácticas Avanzadas, Automatización y MCPs  
> **Lección:** L20  
> **Tags:** `rag` `llm-wiki` `obsidian` `whatsapp` `embeddings` `vectordb` `karpathy` `base-de-conocimiento`

---

## 🧠 Core Insights

El mercado de 2024 estaba lleno de bases de datos vectoriales y pipelines de embeddings. Entonces Andrej Karpathy — ex-director de IA de Tesla — publicó un argumento provocador: la mayoría de los sistemas RAG que las empresas construyen son innecesariamente complejos, y en la mayoría de los casos tres archivos Markdown bien organizados hacen mejor trabajo. Entender por qué esa afirmación es correcta te ahorra semanas de ingeniería innecesaria.

**Cómo funciona RAG tradicional.** Tres partes: tienes documentos que divides en fragmentos (chunking) → esos fragmentos se convierten en vectores numéricos mediante un modelo de embeddings y se guardan en una base de datos vectorial (Pinecone, Weaviate, Qdrant) → cuando el usuario hace una pregunta, esa pregunta también se vectoriza, el sistema busca los fragmentos más similares matemáticamente, y los inyecta como contexto en el prompt.

La ventaja de este enfoque es la escala: millones de documentos, búsqueda en milisegundos. El problema es la fragmentación: al dividir documentos en trozos, el modelo recibe fragmentos que no saben qué hay antes ni después. El resultado son respuestas que son precisas en el detalle pero incorrectas en el contexto más amplio.

**El argumento de Karpathy: el LLM Wiki.** Los LLMs modernos tienen ventanas de contexto enormes. Si puedes organizar tu conocimiento de forma que quepa en esa ventana — o al menos que el índice quepa y los detalles relevantes se puedan recuperar por enlace — el modelo puede razonar sobre relaciones entre conceptos de forma mucho más natural que con fragmentos ciegos.

Un LLM Wiki es una base de conocimiento personal estructurada como una Wikipedia privada, construida en Obsidian (notas Markdown con enlaces bidireccionales). El sistema se basa en **tres archivos fundamentales:**

- **`claude.md`:** define cómo el modelo interactúa con la bóveda — qué reglas seguir, cómo estructurar notas nuevas. Es el contrato de comportamiento del agente sobre este corpus.
- **`index.md`:** el mapa maestro del sistema. Un catálogo por categorías que le dice al modelo dónde está cada tipo de información (herramientas, personas, conceptos, proyectos). Sin un buen índice, el modelo navega a ciegas.
- **`log.md`:** registro cronológico de cada operación, cada ingesta de información, cada actualización relevante.

El proceso de mantenimiento se llama **lint**: periódicamente le pides al modelo que revise el sistema buscando páginas huérfanas (que nadie enlaza), inconsistencias entre notas, o conexiones que deberían existir pero no están.

**La tabla de decisión entre RAG vectorial y LLM Wiki:**
- RAG vectorial → casos enterprise: millones de documentos pesados, equipos grandes, búsquedas de alta frecuencia donde cada milisegundo cuenta, presupuesto para mantener infraestructura de embeddings.
- LLM Wiki → el 90% de los casos reales: emprendedores, freelancers, equipos pequeños, creadores. Si tienes cientos de páginas bien indexadas, el modelo navega el índice y los enlaces mejor que fragmentos vectorizados. El costo son solo los tokens de las consultas — sin infraestructura que mantener.

**Gemini Embeddings 2 para casos multimodales.** Si necesitas RAG real porque tienes datos multimodales (videos, imágenes, audios, PDFs que deben consultarse en el mismo espacio), Google lanzó el primer modelo de embeddings nativo multimodal: convierte texto, imágenes, videos y audios en vectores comparables dentro del mismo espacio vectorial, eliminando la necesidad de pipelines separados por tipo de dato.

**WhatsApp como interfaz del bot.** El flujo en n8n: webhook que recibe mensajes de WhatsApp → nodo que extrae el texto de la pregunta → nodo de IA con acceso a la base de conocimiento en Markdown y las instrucciones del `claude.md` → nodo WhatsApp que envía la respuesta. La clave de que funcione bien no es el modelo: es la calidad del índice.

## ⚙️ Implementación Práctica

**Estructura de la bóveda LLM Wiki:**
```
knowledge-base/
├── claude.md          # reglas de comportamiento del agente
├── index.md           # mapa maestro por categorías
├── log.md             # registro cronológico
├── herramientas/
│   ├── n8n.md
│   ├── claude-code.md
│   └── obsidian.md
├── personas/
│   └── ...
└── proyectos/
    └── ...
```

**Ejemplo de `index.md`:**
```markdown
# Índice Principal

## Herramientas
- [[herramientas/n8n]] — automatización visual, flujos, casos de uso
- [[herramientas/claude-code]] — agente de código, skills, hooks

## Personas y Fuentes
- [[personas/karpathy]] — investigador AI, argumentos sobre RAG

## Proyectos Activos
- [[proyectos/bot-whatsapp]] — bot de soporte con base de conocimiento
```

**Ejemplo de `claude.md`:**
```markdown
# Reglas de Comportamiento

1. Cuando se te haga una pregunta, consulta primero index.md para encontrar la nota relevante.
2. Sigue los enlaces internos de las notas para obtener contexto completo.
3. Si creas una nota nueva, agrégala al índice en la categoría correcta.
4. Registra cada operación en log.md con fecha y descripción breve.
5. Nunca respondas desde memoria interna si existe una nota sobre el tema.
```

**Proceso de lint:**
```
Prompt: "Revisa el sistema de notas completo. Encuentra:
1. Páginas que no están enlazadas desde ningún otro lugar
2. Notas que se contradicen entre sí  
3. Conexiones que deberían existir pero no están (usa [[enlace]] para sugerirlas)
Devuelve un informe con los hallazgos y las correcciones propuestas."
```

**Flujo WhatsApp Bot en n8n:**
1. Trigger: Webhook (URL configurada en WhatsApp Business API o Twilio)
2. Nodo Set: extraer `{{ $json.Body }}` del mensaje
3. Nodo AI (Claude): system prompt = contenido de `claude.md`; user message = pregunta del usuario; archivos adjuntos = `index.md` + notas relevantes
4. Nodo WhatsApp/Twilio: enviar respuesta a `{{ $json.From }}`

**Comparación de costos (aproximado, Mayo 2025):**
| Enfoque | Setup | Mantenimiento | Costo por consulta |
|---------|-------|---------------|-------------------|
| RAG vectorial (Pinecone) | Alto | Alto | Embeddings + tokens |
| LLM Wiki | Bajo | Bajo | Solo tokens de consulta |
| Gemini Multimodal | Medio | Medio | Embeddings multimodal + tokens |

## 📌 Notas y Alertas

> [!IMPORTANT]
> La calidad del `index.md` determina la calidad de las respuestas del bot, independientemente del modelo que uses. Un índice con categorías vagas y enlaces incorrectos produce respuestas confusas incluso con Claude Opus. Invertir tiempo en el índice es invertir en la precisión del sistema completo.

> [!WARNING]
> RAG vectorial tiene un costo operativo continuo que muchos subestiman: generar embeddings cuando los documentos cambian, mantener la base de datos vectorial actualizada, pagar por almacenamiento y consultas. Para la mayoría de los casos de uso reales, ese costo no está justificado por los beneficios. El LLM Wiki no tiene ese costo.

> [!TIP]
> El proceso de lint no es opcional — es lo que mantiene el sistema sano a medida que crece. Sin lint periódico, las páginas huérfanas se acumulan, las inconsistencias se multiplican, y el índice pierde precisión. Programa el lint mensualmente desde el principio como parte del mantenimiento del sistema.

> [!NOTE]
> WhatsApp Business API requiere aprobación de Meta y tiene costos por mensaje. Para prototipado y uso personal, Telegram es más rápido de configurar (API pública, gratuita) y tiene mejor soporte en n8n. WhatsApp es la interfaz final para usuarios — Telegram es la interfaz correcta para prototipar.

## 🔗 Ver también

- [n8n: Flujos Visuales con IA](./n8n-flujos.md) — n8n como orchestrador del flujo completo del bot
- [MCP: El Protocolo que Conecta la IA con el Mundo](./mcp-conceptos.md) — alternativa a cargar archivos Markdown: MCP de filesystem para que el agente navegue la bóveda directamente
- [cerebro-digital-notebooklm.md](../m5/cerebro-digital-notebooklm.md) — construir un cerebro digital completo sobre los mismos principios del LLM Wiki

---

[⬅️ Volver al Índice Principal](../README.md)
