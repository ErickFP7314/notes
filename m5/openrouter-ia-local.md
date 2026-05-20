# [L34] OpenRouter e IA Local: Sin Límites de Presupuesto

> **Módulo:** M5 — Obsidian y Gestión Empresarial (Lección Bonus)  
> **Lección:** L34  
> **Tags:** `openrouter` `ollama` `ia-local` `lm-studio` `privacidad` `modelos-locales` `free-tier`

---

## 🧠 Core Insights

La barrera de precio no es un impedimento real para trabajar con IA — es una variable que se puede optimizar según el caso de uso. Existen tres opciones con trade-offs distintos, y las tres pueden coexistir en el mismo flujo de trabajo.

**OpenRouter** es una plataforma que agrega cientos de modelos bajo una sola clave de API. Con una cuenta accedes a Gemini, Llama, Qwen, Mistral, DeepSeek y otros, incluyendo versiones gratuitas. El truco no obvio: en lugar de seleccionar un modelo gratuito específico (como Qwen 3 o Llama), se configura el **free models router** de OpenRouter. Este router redirige automáticamente las peticiones al modelo gratuito con menor demanda en ese momento. El beneficio es doble: acceso a modelos gratuitos de mayor calidad, y eliminación de errores de límite de peticiones que ocurren cuando un modelo específico recibe demasiado tráfico global.

El requisito sorpresivo de OpenRouter: aunque uses modelos de costo cero, **la cuenta debe tener al menos $10 de saldo** para que el sistema funcione. Ese saldo no se consume al usar modelos gratuitos — es un umbral de actividad, no un cobro. Pero debe estar ahí.

La consideración de privacidad de OpenRouter hay que nombrarla directamente: los modelos gratuitos son gratuitos porque **los datos que envías pueden usarse para entrenar modelos en el futuro**. Para tareas de aprendizaje, experimentación y trabajo con información pública, aceptable. Para información confidencial de empresa o datos personales sensibles, no lo es.

**Ollama** resuelve el problema de privacidad de forma definitiva: los datos nunca salen de la máquina. La ejecución ocurre en hardware local con pesos abiertos descargados una vez. El hardware mínimo es menos de lo que la mayoría imagina: con **8 GB de RAM** es suficiente para correr modelos de 3-7B parámetros con cuantización Q4. La variante `Q4_K_M` reduce drásticamente el tamaño del modelo en memoria sin pérdida significativa de calidad — es el punto de equilibrio entre eficiencia y rendimiento.

Los modelos más accesibles para hardware estándar son **Gemma** (Google) y **Qwen** (Alibaba) en sus variantes de 3-7B parámetros.

**LM Studio vs Ollama**: LM Studio tiene interfaz gráfica, verifica compatibilidad de hardware antes de descargar, e incluye chat para probar modelos — es la entrada para quien no quiere terminal. Ollama es para producción: más ligera, más confiable para uso continuo, mejor integración con Claude Code y otros agentes. Para un flujo de trabajo serio, Ollama es la elección.

La integración **Claude Code + Ollama** elimina el costo de API completamente para las tareas que no requieren el modelo más potente. Para tareas de bajo requerimiento cognitivo — generar borradores, reformatear datos, organizar notas, escribir código repetitivo — un modelo local de 7B parámetros es suficiente. El crédito de API de Claude se reserva para las decisiones que realmente requieren el razonamiento de Opus o Sonnet.

## ⚙️ Implementación Práctica

**Configuración de OpenRouter en Claude Code:**

```json
// ~/.claude/settings.json
{
  "apiKeyHelper": "",
  "openRouterApiKey": "sk-or-v1-tu-clave-aqui",
  "model": "openrouter/auto"
}
```

Pasos:
```
1. Crear cuenta en openrouter.ai
2. Ir a Panel → API Keys → Generate New Key
3. Depositar mínimo $10 (umbral de actividad, no costo)
4. En Claude Code: crear .claude/settings.json con config arriba
5. En "model": usar "openrouter/auto" (free models router)
   — NO seleccionar un modelo gratuito específico
```

**Ollama — instalación y comandos esenciales:**
```bash
# Instalación (Linux/Mac)
curl -fsSL https://ollama.ai/install.sh | sh

# Descargar modelo (primera vez — se descarga una vez)
ollama pull gemma:7b
ollama pull qwen2.5:7b
ollama pull llama3.2:3b

# Iniciar en modo chat (para probar)
ollama run qwen2.5:7b

# Ver modelos disponibles localmente
ollama list

# Verificar que el servidor está corriendo
ollama ps

# Apagar modelo de la memoria
ollama stop qwen2.5:7b
```

**Selección de modelo por hardware disponible:**

| RAM disponible | Modelo recomendado | Cuantización | Calidad relativa |
|---------------|-------------------|--------------|-----------------|
| 4 GB | Gemma 2B / Qwen 1.5B | Q4_K_M | Básica |
| 8 GB | Qwen 7B / Gemma 7B | Q4_K_M | Buena para tareas rutinarias |
| 16 GB | Llama 13B / Qwen 14B | Q4_K_M | Muy buena |
| 32 GB+ | Llama 34B / Qwen 32B | Q4_K_M | Comparable a modelos en nube |

**Claude Code con modelo local de Ollama:**
```bash
# Arrancar Claude Code usando Ollama en lugar de API de Anthropic
ollama launch clou --model qwen2.5:7b

# O configurar en settings.json para que use Ollama por defecto
# (verificar documentación actualizada de Claude Code para la sintaxis exacta)
```

**Tabla de decisión — cuándo usar cada opción:**

| Situación | Opción | Costo aproximado |
|-----------|--------|-----------------|
| Uso intensivo diario, razonamiento complejo | Claude Pro | $20/mes |
| Aprendizaje, uso intermitente, múltiples modelos | OpenRouter | $5-10 saldo inicial |
| Datos confidenciales, volumen alto, sin costo de API | Ollama local | $0 (hardware propio) |

**Flujo combinado (las tres opciones coexistiendo):**
```
Tareas rutinarias (organizar notas, formatear, borradores):
→ Ollama local con Qwen/Gemma 7B — costo: $0

Experimentación con modelos variados, prototipado:
→ OpenRouter free router — costo: ~$0 (dentro del free tier)

Trabajo exigente (arquitectura, razonamiento complejo, decisiones críticas):
→ Claude Pro / API de Anthropic — costo justificado por calidad
```

**Verificar privacidad del modelo antes de usarlo:**
```bash
# En Ollama, verificar que el modelo es local (nunca sale de la máquina)
ollama show qwen2.5:7b --verbose
# Debe mostrar ruta local del modelo, no URL externa

# En OpenRouter, verificar política de privacidad del modelo específico:
# https://openrouter.ai/models → buscar modelo → sección "Privacy"
# Modelos marcados como "training: opt-out" no usan tus datos para training
```

## 📌 Notas y Alertas

> ⚠️ **Advertencia:** El free models router de OpenRouter no es un modelo — es un enrutador que selecciona dinámicamente entre modelos gratuitos disponibles. Si configuras un modelo gratuito específico (ej. `meta-llama/llama-3.1-8b-instruct:free`) en lugar del router, experimentarás errores de rate limit frecuentes cuando ese modelo tenga alta demanda global. Siempre usar `openrouter/auto` o el free router equivalente.

> 🔴 **Importante:** Los modelos gratuitos de OpenRouter son gratuitos porque los datos se pueden usar para entrenamiento futuro. Esta no es letra pequeña — es el modelo de negocio explícito. Para código de proyectos personales y experimentación no hay problema. Para secrets de empresa, datos de clientes, o información sensible, usar Ollama local obligatoriamente. La distinción no es sobre calidad de modelo sino sobre a quién le pertenecen los datos que envías.

> 💡 **Tip:** La cuantización `Q4_K_M` es el sweet spot para hardware estándar. `Q4` (sin el `_K_M`) es más agresiva y pierde más calidad. `Q8` es casi sin pérdida pero requiere casi el doble de RAM. Para la mayoría de los casos de uso con 8 GB de RAM, `Q4_K_M` con un modelo de 7B parámetros ofrece una relación calidad/recurso óptima. Puedes verificar compatibilidad antes de descargar en LM Studio sin necesidad de hacer el download primero.

> 📌 **Nota:** El requisito de $10 de saldo en OpenRouter para acceder a modelos gratuitos no es un bug ni una práctica oscura — es una medida anti-abuso para evitar que cuentas sin saldo saturen el sistema con peticiones gratuitas ilimitadas. El saldo permanece intacto mientras solo uses modelos gratuitos. Si en algún momento el saldo cae por debajo del umbral (porque usaste modelos de pago), el acceso a gratuitos se suspende temporalmente hasta que recargues.

## 🔗 Ver también

- [ollama-modelos-locales.md](../m2/ollama-modelos-locales.md) — guía detallada de instalación, gestión y optimización de modelos locales con Ollama
- [ecosistema-agentes.md](../m2/ecosistema-agentes.md) — cómo integrar modelos de OpenRouter y Ollama en flujos de trabajo agénticos
- [suscripciones-precios.md](../m2/suscripciones-precios.md) — comparativa completa de costos entre Claude Pro, API de Anthropic, y alternativas

---

[⬅️ Volver al Índice Principal](../README.md)
