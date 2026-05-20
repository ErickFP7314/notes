# [L17] MCP: El Protocolo que Conecta la IA con el Mundo

> **Módulo:** M3 — Prácticas Avanzadas, Automatización y MCPs  
> **Lección:** L17  
> **Tags:** `mcp` `protocolo` `herramientas` `tools` `resources` `stdio` `http` `arquitectura`

---

## 🧠 Core Insights

Los LLMs tienen un problema estructural por defecto: solo saben lo que aprendieron durante el entrenamiento. No pueden leer tus archivos, consultar tu base de datos, navegar una página en tiempo real, ni ejecutar una acción en GitHub. Para hacer cualquiera de esas cosas, alguien tenía que construir una integración a medida — con su propio formato, su propia forma de comunicarse con el modelo, sin ningún estándar compartido.

**MCP (Model Context Protocol)** es la respuesta de Anthropic a ese problema: un estándar open source que define cómo los modelos de lenguaje se comunican con herramientas y fuentes de datos externas.

**La analogía del USB-C es precisa.** Antes del USB-C, cada fabricante usaba su conector. Después, un solo formato para todo. MCP hace lo mismo: un servidor MCP construido con el estándar funciona con Claude, con VS Code en modo agente, con Cursor, con Windsurf, con cualquier cliente que soporte el protocolo. Se construye una vez, funciona en todos lados.

**Arquitectura: tres componentes.**
- **Host:** el entorno donde corre todo (tu computadora, un servidor, el sistema donde vive la aplicación).
- **Client:** la interfaz que usa el usuario — Claude Desktop, VS Code en modo Agente, Cursor. Es el componente con el que interactúas.
- **Server MCP:** el programa que conecta el cliente con las herramientas externas (filesystem, base de datos, API de GitHub, lo que quieras).

El ciclo de vida es predecible: cliente inicializa sesión → servidor expone las herramientas disponibles → cliente las detecta → el usuario hace una petición en lenguaje natural → el modelo infiere qué herramienta usar → el cliente la invoca → el servidor la ejecuta y devuelve JSON → el modelo procesa y presenta el resultado de forma inteligible.

Lo más importante: **no necesitas decirle al modelo qué herramienta usar. Él lo infiere desde tu pregunta.**

**Los 3 primitivos — Tools, Resources y Prompts.**
- **Resources:** datos de solo lectura. Los *sustantivos* del sistema. El contenido de un archivo, los resultados de una query, el HTML de una página. El modelo consulta pero no modifica.
- **Prompts:** plantillas de instrucciones para operaciones repetitivas. Estandarizan flujos que siempre empiezan igual.
- **Tools:** el primitivo más poderoso. Las *acciones* — los *verbos*. Toman parámetros, ejecutan lógica real, pueden modificar estado, llamar APIs, crear archivos, abrir pull requests. Son lo que hace que el modelo pase de leer a *hacer*.

**Dos modos de transporte.**
- **STDIO:** para desarrollo y servidores locales. El cliente ejecuta el servidor como proceso en la misma máquina; la comunicación ocurre por stdin/stdout.
- **HTTP stateless:** para producción. El cliente hace peticiones POST/GET sin mantener conexión permanente. Es lo que hace que MCP funcione en arquitecturas serverless y entornos cloud donde una conexión abierta no es posible.

## ⚙️ Implementación Práctica

**Directorio de servidores listos para usar:** [mcp.so](https://mcp.so) — filesystem, GitHub, PostgreSQL, Playwright, JetBrains y cientos más.

**Configurar MCP en Claude Desktop** — editar `claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/ruta/al/directorio"],
      "env": {}
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_..."
      }
    }
  }
}
```
Después de editar: reiniciar Claude Desktop. Las herramientas quedan disponibles automáticamente.

**Configurar MCP en VS Code:**
1. Abrir paleta de comandos (`Ctrl+Shift+P`)
2. Buscar `Add MCP Server`
3. Elegir si es un comando local o URL remota
4. VS Code genera la configuración automáticamente

**Probar antes de conectar con MCP Inspector:**
```bash
npx @modelcontextprotocol/inspector
```
Lanza una interfaz local en `localhost:5173` donde puedes explorar las herramientas que expone el servidor, invocarlas con parámetros de prueba, y verificar que responden correctamente. **Hacer este paso antes de conectar ahorra horas de debugging.**

## 📌 Notas y Alertas

> 🔴 **Importante:** El modelo infiere qué herramienta usar desde lenguaje natural — no la seleccionas manualmente. Esto es poderoso pero también significa que herramientas mal nombradas o con descripciones ambiguas pueden invocarse en el momento equivocado. La descripción de cada Tool en el servidor MCP es tan importante como su implementación.

> ⚠️ **Advertencia:** Configurar MCPs de forma global hace que el agente cargue herramientas de todos los proyectos en cualquier contexto. Un servidor MCP activo puede consumir hasta 10,000 tokens de contexto aunque no se use. Usa `.vscode/mcp.json` por workspace (ver L18) para aislar qué herramientas están disponibles en cada proyecto.

> 💡 **Tip:** Para explorar servidores MCP disponibles sin instalar nada: el directorio mcp.so tiene categorías, estrellas de popularidad, y comandos de instalación listos. Es el punto de partida correcto antes de considerar construir uno propio.

> 📌 **Nota:** HTTP stateless en MCP no es lo mismo que HTTP tradicional. El "stateless" aquí es intencional y estructural — permite que el protocolo funcione con funciones serverless (AWS Lambda, Vercel Functions) donde no hay proceso persistente. Esto es la actualización que habilita MCPs en producción real.

## 🔗 Ver también

- [MCP Avanzado: FastMCP, Casos Reales y Monetización](./mcp-avanzado.md) — construir servidores MCP propios con Python y el patrón FastMCP
- [Integración con Servicios Externos](./integracion-terceros.md) — el nivel anterior: integraciones directas que MCP viene a reemplazar y estandarizar
- [Hooks y Arquitectura de Contexto](./hooks-arquitectura-contexto.md) — cómo el aislamiento de MCPs por workspace encaja en la gestión de contexto del agente

---

[⬅️ Volver al Índice Principal](../README.md)
