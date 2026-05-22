# [L04] MCP Avanzado: FastMCP, Casos Reales y Monetización

> **Módulo:** M3 — Prácticas Avanzadas, Automatización y MCPs  
> **Lección:** L04  
> **Tags:** `mcp` `fastmcp` `python` `oauth` `jwt` `monetización` `xpack` `stripe` `workspace-isolation`

---

## 🧠 Core Insights

Usar MCPs existentes da acceso a herramientas construidas por otros. Construir MCPs propios da algo más: la capacidad de convertir cualquier sistema que puedas programar en una herramienta que el modelo puede usar. Y si ese sistema resuelve un problema real de un nicho, ya tienes un producto vendible.

**FastMCP reduce la creación de un servidor MCP a decorar funciones Python.** El concepto central es el decorador `@mcp.tool()`: cualquier función Python decorada con él queda expuesta automáticamente como una Tool del servidor MCP. No hay esquemas complejos que definir, no hay serialización manual — el decorador lo maneja.

**El aislamiento por workspace es lo que separa un setup profesional de uno de aficionado.** Cuando configuras MCPs globalmente, el agente carga herramientas de todos los proyectos en cualquier contexto. Cuando trabajas en un proyecto de Python, el agente también ve las herramientas del servidor de Notion que configuraste para otro proyecto — el contexto se contamina, los tokens se desperdician. La solución es el archivo `.vscode/mcp.json` dentro del directorio del proyecto: los MCPs configurados ahí están disponibles solo en ese workspace. Cuando abres otro proyecto, tienes exactamente las herramientas de ese otro, sin contaminación cruzada.

**Casos reales que ilustran el patrón:**
- *Notion + finanzas:* el MCP de Notion conectado a una tabla de finanzas permite pedirle al modelo que calcule el patrimonio neto actual, registre una nueva entrada transformando divisas, o genere un resumen mensual de gastos — leyendo y escribiendo datos directamente en Notion, sin exportar CSV.
- *PokeAPI:* el caso más simple posible — el servidor hace una llamada a una API pública sin autenticación y devuelve JSON crudo. El modelo estructura y presenta los datos. No hay estado, no hay autenticación. Solo la demostración de que el protocolo funciona.
- *Beehiiv + dashboard:* un MCP remoto extrae el rendimiento real de newsletters del mes; un agente en VS Code usa esos datos para generar código HTML de un dashboard. El modelo no inventa los datos — los recibe del MCP y produce algo real.

**Autenticación en producción: OAuth 2.0 + JWT.** Cuando el MCP accede a datos sensibles o actúa en nombre del usuario, los cinco requisitos para funcionar con Claude Web son: soporte para OAuth 2.0 con Dynamic Client Registration, PKCE para proteger el flujo de autorización, generación y validación de tokens JWT, gestión de refresh tokens para sesiones largas, y soporte para HTTP stateless. Para MCPs locales o entornos controlados, un token fijo de API es suficiente.

**Monetización con Xpack + Stripe.** Si el MCP resuelve un problema real de un nicho, ya es un producto SaaS. Xpack es una plataforma de hosting para MCPs propios con monetización integrada: subes el servidor, conectas Stripe, configuras precios por uso o suscripción mensual, y compartes la URL. Otros usuarios la configuran en sus clientes MCP y pagan automáticamente. El modelo es especialmente potente para MCPs muy especializados que nadie más ha construido: una integración con una herramienta de nicho, lógica de negocio específica de una industria, o agregación de datos que no existe en ningún otro lugar.

## ⚙️ Implementación Práctica

**Setup inicial:**
```bash
python -m venv .venv
source .venv/bin/activate
pip install mcp uvicorn
```

**Estructura básica de un servidor FastMCP — `server.py`:**
```python
from mcp import FastMCP

mcp = FastMCP("mi-servidor")

@mcp.tool()
def buscar_pokemon(nombre: str) -> dict:
    """Busca información de un Pokémon por su nombre."""
    import requests
    response = requests.get(f"https://pokeapi.co/api/v2/pokemon/{nombre.lower()}")
    data = response.json()
    return {
        "nombre": data["name"],
        "tipos": [t["type"]["name"] for t in data["types"]],
        "altura": data["height"],
        "peso": data["weight"]
    }

@mcp.tool()
def sumar_numeros(a: float, b: float) -> float:
    """Suma dos números y devuelve el resultado."""
    return a + b

if __name__ == "__main__":
    mcp.run()
```

**Probar con MCP Inspector antes de conectar:**
```bash
npx @modelcontextprotocol/inspector python server.py
# Abre interfaz en localhost — invocar herramientas con parámetros de prueba
```

**Deploy en producción con uvicorn:**
```bash
uvicorn server:mcp --host 0.0.0.0 --port 8000
```

**Crear `.vscode/mcp.json` para aislamiento por workspace:**
```json
{
  "servers": {
    "mi-servidor-local": {
      "type": "stdio",
      "command": "python",
      "args": ["server.py"],
      "env": {
        "API_KEY": "${env:MI_API_KEY}"
      }
    }
  }
}
```
Desde VS Code: `Ctrl+Shift+P` → `Add MCP Server` → VS Code genera este archivo en `.vscode/` automáticamente.

**Flujo de monetización con Xpack:**
1. Construir el servidor MCP y desplegarlo.
2. Subir a [xpack.ai](https://xpack.ai) (o equivalente).
3. Conectar cuenta de Stripe.
4. Configurar precios: por uso (tokens consumidos) o suscripción mensual.
5. Compartir la URL del servidor MCP remoto con los usuarios.

## 📌 Notas y Alertas

> 🔴 **Importante:** El decorador `@mcp.tool()` usa el docstring de la función como descripción de la Tool en el servidor MCP. Si el docstring es malo o está vacío, el modelo no sabrá cuándo ni cómo invocar la herramienta. El docstring no es documentación para humanos — es el prompt de invocación para el modelo.

> ⚠️ **Advertencia:** Nunca saltes el paso de MCP Inspector. Un servidor que parece funcionar puede tener errores de serialización que solo aparecen cuando el modelo intenta invocar la herramienta con parámetros reales. Inspector te muestra el esquema exacto que el modelo verá y te permite probarlo antes de conectarlo a un cliente real.

> 💡 **Tip:** Para MCPs que deben funcionar sin autenticación compleja (demos, prototipos, uso personal), un token fijo en variable de entorno es completamente válido. No sobrediseñes la autenticación hasta que el MCP tenga usuarios reales que necesitan cuentas separadas.

> 📌 **Nota:** La contaminación de contexto por MCPs globales no es solo un problema de rendimiento — afecta directamente la calidad de las decisiones del agente. Un agente que ve 30 herramientas disponibles cuando solo necesita 3 tiene mayor probabilidad de invocar la herramienta equivocada. El aislamiento por workspace es una decisión de calidad, no solo de eficiencia.

## 🔗 Ver también

- [MCP: El Protocolo que Conecta la IA con el Mundo](./03-mcp-conceptos.md) — conceptos fundacionales: arquitectura, primitivos y transporte
- [n8n: Flujos Visuales con IA](./05-n8n-flujos.md) — n8n puede consumir MCPs remotos como nodos dentro de flujos visuales
- [06-startup-n8n-graphify.md](../m4/06-startup-n8n-graphify.md) — caso de startup que combina MCP y n8n en un producto real

---

[⬅️ Volver al Índice Principal](../README.md)
