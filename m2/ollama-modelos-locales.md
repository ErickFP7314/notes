# [L11] Ollama y Modelos Locales: IA sin Costo de Tokens

> **Módulo:** M2 — Agentes de IA y Entornos de Ejecución  
> **Lección:** L11  
> **Tags:** `ollama` `modelos-locales` `lm-studio` `privacidad` `ia-local`

---

## 🧠 Core Insights

Hay una categoría entera de tareas que **no necesita el modelo más inteligente del mercado**: generar borradores, resumir notas, reformatear datos, buscar en archivos, hacer consultas simples. Usar Claude Opus para eso es sobredimensionamiento innecesario — y cara.

Ollama te permite correr modelos open source directamente en tu computadora, sin internet, sin costo por token, y conectarlos al mismo agente que ya tienes configurado. Para el 80% de las tareas de bajo requerimiento cognitivo, esto es suficiente y mucho más eficiente.

### Qué es Ollama exactamente

Ollama no es una aplicación de chat. Es un **servidor local** que descarga y ejecuta modelos de lenguaje open source, y los expone a través de una API estándar (compatible con OpenAI). Una vez instalado, puede conectarse con Antigravity, Claude Code, OpenCode, o cualquier aplicación que hable con una API de LLM.

Para el agente, la diferencia entre hablar con Claude en la nube y hablar con un modelo local a través de Ollama es **transparente**: la interfaz es la misma. Lo que cambia es el costo (cero) y la privacidad (los datos nunca salen de tu máquina).

### LM Studio: exploración antes de comprometerte

Ollama es potente pero es una herramienta de terminal. LM Studio es la alternativa con interfaz gráfica para quien prefiere no trabajar desde la línea de comandos, y tiene una ventaja clave: su buscador integrado te dice **antes de descargar** si tu GPU y RAM pueden soportar el modelo. Eso elimina el problema de descargar un modelo que luego no corre.

Limitación importante de LM Studio: **no es recomendable para producción**. Es una herramienta de exploración y comparación. Para conectar modelos locales de forma estable a un agente en producción, Ollama es más confiable.

### La tabla de decisión: local vs cloud

No es una competencia. Es una herramienta por caso de uso:

| Criterio | IA Local (Ollama) | IA en la Nube (Claude) |
|----------|-------------------|------------------------|
| Costo | Cero por token | Pago por uso o suscripción |
| Privacidad | Total — datos no salen de tu máquina | Datos viajan a servidores externos |
| Internet | No requerido | Requerido |
| Capacidad | Limitada por tu hardware | Modelos más potentes del mercado |
| Velocidad | Depende de tu GPU/CPU | Extremadamente rápida |
| Actualización | Manual (descargas la versión nueva) | Siempre actualizada |

**Estrategia que funciona**: usar modelos locales para trabajo de volumen (borradores, reformateo, búsquedas en archivos) y reservar modelos en la nube para decisiones que requieren razonamiento real.

---

## ⚙️ Implementación Práctica

### Instalación de Ollama

```bash
# macOS con Homebrew (una línea):
brew install ollama

# O descarga el instalador desde ollama.com
# (disponible para Windows, macOS, Linux)
# → Una vez instalado, el servicio corre en segundo plano automáticamente
```

### Comandos esenciales de Ollama

```bash
# Descargar un modelo:
ollama pull gemma3:4b

# Iniciar el modelo en modo chat desde terminal:
ollama run gemma3:4b

# Ver los modelos que tienes descargados:
ollama list

# El servidor corre por defecto en:
# http://localhost:11434
```

### Modelos recomendados por hardware

| Modelo | Parámetros | RAM necesaria | Mejor para |
|--------|-----------|---------------|------------|
| Gemma 3 (Google) | 4B | ~4 GB (CPU ok) | Redacción, análisis de texto |
| Qwen 3.5 (Alibaba) | 7B | ~6 GB (GPU recomendada) | Relación calidad/consumo |
| GLM 5.1 | 4B-9B | Variable | Alternativa competitiva |

**Referencia práctica**: un modelo de 7B parámetros necesita ~6 GB de RAM y funciona bien con GPU dedicada. Un modelo de 4B puede correr en CPU, aunque más lento.

### Conectar Ollama con Antigravity

```
1. Abre la configuración de Antigravity
2. Ve a la sección de Modelos
3. Selecciona "Modelo local"
4. Apunta al servidor de Ollama: http://localhost:11434
5. Elige el modelo descargado que quieres usar

→ A partir de aquí, cada sesión puedes elegir:
  - Claude (API cloud) para razonamiento complejo
  - Modelo local (Ollama) para tareas repetitivas o privadas
```

### Flujo de trabajo híbrido recomendado

```
Tarea de volumen (borrador, resumen, reformateo):
  → ollama run gemma3:4b
  → Sin costo, sin internet, sin límites de uso

Tarea compleja (arquitectura, debug difícil, código nuevo):
  → Claude Sonnet o Opus vía API
  → Máxima capacidad de razonamiento

Tarea privada (datos sensibles, código propietario):
  → Cualquier modelo local vía Ollama
  → Los datos no salen de tu máquina
```

---

## 📌 Notas y Alertas

> [!IMPORTANT]
> Ollama expone los modelos en `localhost:11434` con una API compatible con el formato de OpenAI. Esto significa que **cualquier herramienta que acepte una URL de API personalizada** puede conectarse a Ollama — no solo Antigravity. OpenCode, LM Studio, y la mayoría de clientes de LLM lo soportan. Una sola instalación de Ollama sirve a todo tu stack.

> [!WARNING]
> Los modelos locales son menos capaces que los grandes modelos en la nube, y la diferencia de calidad **es notable** para tareas complejas. El error común es intentar usar un modelo local de 4B parámetros para diseño arquitectónico o debugging difícil porque "gratis es mejor". Para esas tareas, la diferencia de calidad cuesta más cara que el token de Claude.

> [!TIP]
> Antes de descargar un modelo con Ollama, ábrelo en LM Studio primero: busca el modelo, ve los requisitos de hardware, y verifica que tu máquina puede correrlo. Solo entonces usa `ollama pull`. Evita descargar modelos de 7B+ si tu máquina tiene menos de 8 GB de RAM disponible.

> [!NOTE]
> El comando `ollama run nombre-modelo` inicia una sesión de chat directa en terminal. Es útil para probar el modelo rápidamente antes de conectarlo al agente. Pero el flujo de trabajo real siempre va a pasar por el agente (Antigravity, OpenCode, etc.) — usar Ollama directamente es para exploración, no para producción.

---

## 🔗 Ver también

- [Antigravity: Instalación y Skills](./antigravity-skills.md) — cómo conectar Ollama a Antigravity para usar modelos locales en el agente
- [OpenCode y Opciones Abiertas](./opencode-opciones.md) — OpenCode también soporta Ollama como proveedor de modelos
- [M5: OpenRouter e IA Local](../m5/openrouter-ia-local.md) — capa alternativa para acceder a modelos open source sin gestionar Ollama localmente

---

[⬅️ Volver al Índice Principal](../README.md)
