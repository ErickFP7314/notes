# [L05] Construcción del Arnés de IA (Bucle y Memoria)

> **Módulo:** M4 — Creación de Productos, SDD y Monetización  
> **Lección:** L05  
> **Tags:** `agente` `rpl-loop` `herramientas` `polimorfismo` `memoria`

---

## 🧠 Core Insights

El arnés es el cuerpo que envuelve a un Modelo de Lenguaje (LLM) para convertirlo de un simple oráculo pasivo a un agente autónomo. Sin un arnés, la IA no puede leer archivos, ejecutar comandos ni delegar tareas. El corazón de este arnés es el bucle RPL (Read, Eval, Print, Loop), idéntico al motor de un videojuego: lee el input, evalúa qué hacer enviándolo al modelo, imprime el resultado y vuelve a empezar.

### El Bucle Interno y Herramientas

La magia ocurre dentro de la fase de evaluación. Cuando el LLM necesita interactuar con el sistema, no lo hace directamente, sino que pide al arnés ejecutar una herramienta. El arnés procesa esta solicitud en el mundo real y devuelve el resultado crudo al modelo. Este ciclo interno se repite hasta que la IA recolecta suficiente información para dar una respuesta definitiva al usuario.

### Polimorfismo, Delegación y Memoria

Atarse a un único proveedor (como OpenAI) es un error arquitectónico. Usando interfaces polimórficas, el arnés puede intercambiar el "cerebro" en milisegundos sin reescribir código, facilitando el uso de modelos locales o subagentes con roles específicos (ej. investigador, programador). Para que estos agentes mantengan coherencia sin saturar la ventana de contexto, el arnés implementa memoria a largo plazo (escribiendo en archivos locales) y técnicas de compactación, donde la IA resume el historial pasado y descarta el original.

## ⚙️ Implementación Práctica

Ejemplo conceptual del bucle interno de evaluación y compactación en un arnés, y cómo delegar en herramientas:

```go
// Ejemplo conceptual del Bucle RPL en Go
func (a *Agent) Eval(input string) Response {
    for {
        // 1. Enviar input al LLM
        decision := a.Provider.Chat(input)
        
        // 2. Si decide usar una herramienta, el arnés la ejecuta
        if decision.NeedsTool {
            result := a.executeTool(decision.ToolCall)
            input = result // Retroalimenta el bucle con el resultado
            continue
        }
        
        // 3. Compactación si el contexto es demasiado grande
        if a.TokenCount > MaxTokens {
            a.History = a.Provider.Summarize(a.History)
        }
        
        return decision.FinalAnswer
    }
}
```

## 📌 Notas y Alertas

> 🔴 **Importante:** Las salvaguardas son obligatorias. Nunca permitas que un agente ejecute herramientas destructivas (como borrar archivos o hacer commits) sin un sistema de permisos que pause la ejecución y requiera confirmación humana.

> ⚠️ **Advertencia:** Delegar tareas a múltiples subagentes en paralelo puede disparar rápidamente el consumo de tokens y el costo de la API si no se monitorea el bucle interno adecuadamente. Utiliza calculadores de tokens integrados para medir esto.

> 💡 **Tip:** Conectar Protocolos de Contexto de Modelos (MCPs) permite ejecutar herramientas en servidores externos en paralelo, evitando que el programa principal se bloquee durante tareas de red largas.

## 🔗 Ver también

- [04-sdd-harness-engineering.md](./04-sdd-harness-engineering.md) — para entender cómo el arnés encaja en la filosofía de Spec-Driven Development.
- [05-gentleman-ai-engram.md](./05-gentleman-ai-engram.md) — detalla la implementación técnica de la persistencia de memoria mencionada aquí.
- [03-mcp-conceptos.md](../m3/03-mcp-conceptos.md) — profundiza en cómo funcionan los MCPs para expandir las herramientas del arnés.

---

[⬅️ Volver al Índice Principal](../README.md)
