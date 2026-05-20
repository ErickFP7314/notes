# [L02] Panorama de Herramientas LLM

> **Módulo:** M1 — Fundamentos de IA y Ecosistema LLM  
> **Lección:** L02  
> **Tags:** `ecosistema` `modelos` `chatgpt` `gemini` `claude` `deepseek` `open-source` `benchmarks`

---

## 🧠 Core Insights

El ecosistema de IA se puede ordenar en cinco plataformas principales de uso conversacional, cada una con un nicho diferenciado:

**ChatGPT (OpenAI)** — La más conocida. Fortaleza en integraciones creativas: DALL-E para imágenes, Sora para video, y una tienda de GPTs personalizados que cualquiera puede crear y compartir. Ecosistema cerrado pero el más completo para producción multimedia.

**Gemini (Google)** — La ventaja competitiva es el ecosistema, no el modelo. Acceso a búsquedas en tiempo real, integración nativa con Drive, Docs, Gmail y el calendario. Una de las ventanas de contexto más grandes del mercado. La mejor opción para quienes ya trabajan dentro del stack de Google.

**Claude (Anthropic)** — El más usado para trabajo técnico intenso: código, documentos largos, flujos con agentes. Proyectos con memoria persistente y Claude Design para generar interfaces web desde el chat. El modelo que se usa principalmente en este curso.

**DeepSeek** — Llegó en enero de 2025 y cambió la industria. Igualó benchmarks de GPT-4o y Claude Opus con una fracción del presupuesto de entrenamiento. Open source completo. La demostración de que la ventaja de las grandes privadas no es insuperable.

**Kimi (Moonshot AI)** — Diferencial en longitud de contexto: más de un millón de tokens. Permite procesar libros enteros, repositorios de código completos o expedientes legales extensos en una sola sesión.

Las empresas de IA operan bajo filosofías distintas que afectan qué puedes hacer con sus modelos:
- **OpenAI y Anthropic**: privadas, acceso vía API/suscripción, invierten en seguridad antes de publicar
- **Meta y Mistral**: publican pesos abiertos, cualquiera puede descargar y modificar. La responsabilidad de uso se transfiere al operador
- **Google**: juega en los dos tableros — Gemini (propietario) y Gemma (abierto)

Los **benchmarks** son referencia, no criterio definitivo. Un modelo que puntúa alto en matemáticas puede ser mediocre redactando emails comerciales. El benchmark mide capacidades en condiciones controladas. Tu caso de uso puede no ser ninguna de esas condiciones.

**La lógica de elección correcta**: no "¿cuál modelo es mejor?" sino "¿qué necesito que haga, qué contexto voy a darle, y cuánto quiero pagar?". Con esas tres variables la elección se vuelve obvia.

---

## ⚙️ Implementación Práctica

**Mapa de herramientas por propósito (estado a 2025):**

| Propósito | Primera opción | Alternativa |
|-----------|---------------|-------------|
| Programar | Claude Sonnet, GPT-4o | DeepSeek Coder (gratuito) |
| Redacción en español | Claude | Gemini (si necesitas web en tiempo real) |
| Imágenes | DALL-E 3, Midjourney | Ideogram (texto en imagen), Flux (fotorrealismo) |
| Video | Sora, Kling, Runway | Hailuo, Pika |
| Voz sintética | ElevenLabs | — |
| Música generativa | Suno, Udio | — |
| Contexto muy largo | Kimi (+1M tokens) | Gemini |
| Uso sin API ni costos | DeepSeek + Ollama | Llama + Ollama |

**Cómo evaluar una herramienta nueva cuando aparece:**
1. ¿Qué tipo es? (fundacional / aplicación sobre fundacional / herramienta especializada)
2. ¿Qué ventana de contexto tiene?
3. ¿Propietario u open source? ¿Qué implica para privacidad y costo?
4. ¿En qué benchmarks fue evaluado y son relevantes para mi caso de uso?

---

## 📌 Notas y Alertas

> 🔴 **Importante:** DeepSeek demostró en enero 2025 que el costo de entrenamiento de modelos de frontera puede reducirse drásticamente con arquitecturas eficientes (Mixture of Experts). Esto presionó hacia abajo los precios de inferencia de toda la industria. Seguir asumiendo que los modelos privados de OpenAI y Google son insuperables por su escala ya no es una premisa válida.

> ⚠️ **Advertencia:** El "mejor modelo del mundo" cambia cada dos o tres meses. Elegir herramientas por benchmarks actuales es una estrategia que caduca rápido. Más sostenible: entender el nicho de cada proveedor (ecosistema, open source, especialización técnica) y adaptar la elección según el proyecto.

> 📌 **Nota:** La ventana de contexto de Kimi (+1M tokens) no es un detalle académico. La novela más larga en español tiene alrededor de 700k palabras. Poder procesar esa cantidad en un solo chat abre casos de uso que son imposibles con ventanas estándar de 200k tokens.

> 💡 **Tip:** Para trabajo en este curso: Claude para todo lo técnico y de largo plazo (agentes, proyectos, código), Gemini para investigación con web en tiempo real, NotebookLM para sintetizar documentos propios. Esa trinidad cubre el 90% de los escenarios.

---

## 🔗 Ver también

- [ChatGPT Avanzado](./chatgpt-avanzado.md) — uso profesional de la plataforma más conocida del ecosistema
- [Google Gemini](./google-gemini.md) — la apuesta de Google por integrar IA en su ecosistema
- [Modelos Open Source](./modelos-open-source.md) — DeepSeek, Llama, Mistral y el caso para correr modelos localmente
- [Claude: Funcionalidades Clave](./claude-funcionalidades.md) — por qué Claude es la herramienta principal del curso

---

[⬅️ Volver al Índice Principal](../README.md)
