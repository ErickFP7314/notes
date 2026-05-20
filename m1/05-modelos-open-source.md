# [L05] Modelos Open Source

> **Módulo:** M1 — Fundamentos de IA y Ecosistema LLM  
> **Lección:** L05  
> **Tags:** `open-source` `deepseek` `llama` `mistral` `ollama` `openrouter` `privacidad` `pesos` `kimi`

---

## 🧠 Core Insights

"Open source" en IA no significa lo mismo que en software tradicional. En software, open source = código fuente público. En IA, lo que se publica son los **pesos** — los parámetros numéricos que definen el comportamiento del modelo, resultado de millones de horas de cómputo comprimidas en un archivo descargable. Quien tiene los pesos puede correr el modelo, modificarlo, afinarlo para tareas específicas o redistribuirlo, sin pedir permiso a nadie.

La consecuencia práctica más importante: **los modelos open source pueden correr localmente**, en tu máquina, sin conexión, sin enviar datos a ningún servidor externo. Para datos médicos, documentos legales, proyectos confidenciales o simplemente para no depender de la política de precios de una corporación — esto importa.

**DeepSeek** es el caso más disruptivo de 2025. Su modelo R1 igualó en benchmarks a GPT-4o y Claude Opus, con pesos completamente públicos, construido con una fracción del presupuesto de OpenAI o Google. Lo que lo hizo posible fue la arquitectura **Mixture of Experts (MoE)**: en lugar de activar todo el modelo para cada consulta, activa solo la parte relevante. Esto reduce drásticamente el cómputo necesario por inferencia. El impacto no fue solo técnico — presionó hacia abajo los precios de inferencia de toda la industria.

**Kimi (Moonshot AI)** resuelve un problema diferente: la longitud del contexto. Con más de un millón de tokens de ventana, puede procesar la novela más larga en español (~700k palabras) más tus instrucciones y responder con coherencia. Casos de uso que no existen con ventanas estándar: analizar repositorios de código completos, procesar expedientes legales extensos, trabajar con bases de documentación técnica sin fragmentar.

La filosofía detrás del open source en IA no es solo técnica: es una posición sobre quién controla la infraestructura intelectual. Si solo los modelos cerrados de tres empresas funcionan bien, el acceso a IA de calidad depende de sus políticas, precios y condiciones. Los modelos abiertos rompen esa dependencia. Meta (Llama), Mistral (serie Mistral/Mixtral) y la comunidad de Hugging Face construyeron miles de variantes especializadas encima de esos modelos base.

**Cuándo elegir open source sobre modelos propietarios:**
- Privacidad de datos (médicos, legales, confidenciales)
- Costo predecible sin dependencia de APIs de terceros
- Necesidad de un modelo especializado que no existe en plataformas comerciales
- Requieres modificar el comportamiento del modelo a un nivel que los modelos cerrados no permiten (fine-tuning)

---

## ⚙️ Implementación Práctica

**Ollama — correr modelos localmente:**
```bash
# Instalar Ollama (macOS/Linux)
curl -fsSL https://ollama.com/install.sh | sh

# Descargar y correr DeepSeek
ollama run deepseek-r1:7b

# Descargar y correr Llama 3
ollama run llama3

# Descargar y correr Mistral
ollama run mistral

# Listar modelos descargados
ollama list

# Ver tamaño aproximado por modelo (para elegir según GPU disponible)
# 7B params  → ~4-6 GB VRAM
# 13B params → ~8-10 GB VRAM
# 70B params → ~40+ GB VRAM (requiere múltiples GPUs o CPU+RAM)
```

**OpenRouter — API unificada para decenas de modelos:**
```python
import openai

client = openai.OpenAI(
    base_url="https://openrouter.ai/api/v1",
    api_key="tu-api-key-de-openrouter"
)

response = client.chat.completions.create(
    model="deepseek/deepseek-r1",  # o "meta-llama/llama-3-70b-instruct"
    messages=[{"role": "user", "content": "Tu prompt aquí"}]
)
```

**Hugging Face — explorar y descargar modelos:**
```
huggingface.co/models
→ Filtrar por: Task (text-generation) + Language (Spanish) + Size
→ Los modelos con licencia "apache-2.0" o "mit" son los más libres para uso comercial
→ Los "GGUF" son para correr localmente con llama.cpp u Ollama
```

**Criterios para elegir qué modelo correr localmente:**
| Hardware disponible | Modelo recomendado |
|--------------------|--------------------|
| GPU 4-8 GB VRAM | DeepSeek 7B, Llama 3 8B, Mistral 7B |
| GPU 16-24 GB VRAM | DeepSeek 14B, Llama 3 70B (cuantizado) |
| Solo CPU + 16 GB RAM | Cualquier modelo 7B en formato GGUF Q4 |
| Solo CPU + 32 GB RAM | Modelos 13B en GGUF Q4 |

---

## 📌 Notas y Alertas

> 🔴 **Importante:** La arquitectura Mixture of Experts de DeepSeek cambió el supuesto base de la industria: que escalar parámetros = mejores modelos = más costo. MoE activa solo una fracción del modelo por consulta, lo que da capacidad de razonamiento alta con costo de inferencia bajo. Esto es relevante porque define la dirección en que va la arquitectura de los próximos modelos — propietarios y abiertos.

> ⚠️ **Advertencia:** "Open source" no siempre implica uso comercial libre. Algunos modelos (incluyendo versiones de Llama) tienen licencias que restringen el uso comercial a empresas con menos de cierto número de usuarios activos mensuales. Siempre revisar la licencia específica antes de integrar un modelo open source en un producto.

> 📌 **Nota:** Correr modelos localmente con Ollama tiene latencia más alta que las APIs comerciales en hardware consumer. Un modelo 7B en una GPU GTX 3090 genera ~30-50 tokens/segundo. Para trabajo interactivo es suficiente. Para sistemas de alto volumen en producción, OpenRouter o las APIs directas siguen siendo más eficientes.

> 💡 **Tip:** OpenRouter permite cambiar de modelo en tiempo real manteniendo la misma interfaz de API. Esto es valioso para experimentar: probar el mismo prompt en DeepSeek R1, Llama 3 70B y Mistral Large con una sola línea de cambio, sin tocar el resto del código.

---

## 🔗 Ver también

- [Panorama de Herramientas LLM](./02-panorama-herramientas-llm.md) — dónde encajan DeepSeek, Kimi y los modelos abiertos en el mapa completo del ecosistema
- [04-ollama-modelos-locales.md](../wiki/04-ollama-modelos-locales.md) — M2: configuración práctica de Ollama para agentes y automatizaciones locales

---

[⬅️ Volver al Índice Principal](../README.md)
