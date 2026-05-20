# [L16] Integración con Servicios Externos

> **Módulo:** M3 — Prácticas Avanzadas, Automatización y MCPs  
> **Lección:** L16  
> **Tags:** `apis` `github` `trello` `linear` `n8n` `integraciones` `webhooks`

---

## 🧠 Core Insights

Toda herramienta profesional que usas — GitHub, Trello, Notion, Linear, Slack — tiene una API. Eso significa que puedes hablarles desde código, desde un agente, o desde una plataforma de automatización, sin abrir la aplicación ni hacer clics. El trabajo manual de copiar datos entre sistemas es una deuda técnica disfrazada de proceso.

**El patrón técnico es siempre el mismo.** Tres componentes que no cambian de una API a otra:
- **Endpoint:** la URL específica a la que apuntas (ej. la ruta de GitHub que devuelve PRs de un repo).
- **Método HTTP:** GET para pedir datos, POST para enviarlos, DELETE para eliminar.
- **Token de autenticación:** la clave que identifica quién hace la petición y verifica que tiene permiso.

El resultado siempre llega en JSON — un objeto estructurado que puedes procesar, transformar, enviar a un modelo, o pasar a otro sistema.

**Casos concretos con lógica real:**
- *GitHub:* leer todos los PRs abiertos, filtrarlos por autor o etiqueta, obtener comentarios, crear issues, cerrar ramas. Una revisión de código diaria puede automatizar su propia fase de recopilación de información.
- *Trello:* crear tarjetas automáticamente cuando se cumple una condición, mover tarjetas entre listas según el estado de un proceso, leer el tablero completo para que el modelo lo resuma.
- *Linear:* crear, actualizar y priorizar tickets desde código. Si los emails de soporte deben convertirse en tickets con prioridad, Linear es el último nodo de esa cadena.

**n8n como capa visual de integración.** Escribir Python para cada integración funciona, pero tiene un costo de mantenimiento: cada nueva herramienta es más código, cada cambio de API es una actualización. n8n resuelve eso desde una interfaz visual donde cada nodo representa una acción: obtener datos de GitHub, procesarlos con un modelo, enviar el resultado a Slack.

Lo que distingue n8n de Zapier no es la interfaz — es el modelo de control: n8n es open source, puedes instalarlo en tu propio VPS, los datos no salen de tu infraestructura, y el costo escala con el hardware que usas, no con el número de ejecuciones. Para equipos pequeños, esa diferencia es decisiva.

**El flujo Daily PR Review** es el caso pedagógico más claro: trigger de Cron a las 9am → nodo GitHub extrae todos los PRs pendientes → nodo de IA con Claude Sonnet genera resumen con recomendaciones de revisión → nodo Slack envía el resumen al canal del equipo. Sin abrir GitHub, sin copiar nada, sin intervención humana.

**La heurística de decisión** entre código y n8n: si la integración requiere lógica específica de tu negocio, razonamiento complejo, o procesamiento que no encaja en nodos visuales → escribe código. Si es una cadena de acciones estándar entre herramientas con soporte nativo en n8n → usa n8n. Las dos formas coexisten: un script Python puede llamar a un webhook de n8n, que a su vez llama de vuelta a Claude.

## ⚙️ Implementación Práctica

```python
import requests
import os

# Autenticación con token de GitHub (desde variable de entorno)
GITHUB_TOKEN = os.getenv("GITHUB_TOKEN")
headers = {"Authorization": f"Bearer {GITHUB_TOKEN}"}

# Obtener PRs abiertos de un repositorio
response = requests.get(
    "https://api.github.com/repos/usuario/repo/pulls",
    headers=headers,
    params={"state": "open"}
)

prs = response.json()
for pr in prs:
    print(f"#{pr['number']} — {pr['title']} por {pr['user']['login']}")
```

```python
# Crear un ticket en Linear
LINEAR_TOKEN = os.getenv("LINEAR_API_KEY")

mutation = """
mutation {
  issueCreate(input: {
    title: "Bug: login falla en móvil",
    description: "Reproducible en iOS 17...",
    priority: 2,
    teamId: "TU_TEAM_ID"
  }) {
    success
    issue { id url }
  }
}
"""

resp = requests.post(
    "https://api.linear.app/graphql",
    headers={"Authorization": LINEAR_TOKEN, "Content-Type": "application/json"},
    json={"query": mutation}
)
```

**Guardar tokens como variables de entorno:**
```bash
# .env (nunca en el repositorio)
GITHUB_TOKEN=ghp_...
LINEAR_API_KEY=lin_api_...
TRELLO_API_KEY=...
TRELLO_TOKEN=...
```

**Flujo Daily PR Review en n8n:**
1. Trigger: Schedule (Cron `0 9 * * 1-5`)
2. Nodo GitHub: Get Pull Requests → autenticar con token
3. Nodo AI (Claude Sonnet): prompt con título + descripción del PR → generar resumen + recomendaciones
4. Nodo If: filtrar PRs con más de 2 días sin revisión (opcional)
5. Nodo Slack: enviar resumen formateado en Markdown al canal `#engineering`

## 📌 Notas y Alertas

> 🔴 **Importante:** El token de autenticación nunca va en el código fuente. Siempre como variable de entorno. Una API key expuesta en un repositorio público puede ser revocada automáticamente por la plataforma en minutos, pero el daño ya está hecho.

> 💡 **Tip:** Para instalar n8n en VPS con Docker: `docker run -d -p 5678:5678 -v ~/.n8n:/home/node/.n8n n8nio/n8n`. El flag `-v` persiste los flujos fuera del contenedor — sin él, pierdes todo si el contenedor cae.

> ⚠️ **Advertencia:** n8n y Zapier no son intercambiables. Zapier no permite self-hosting: tus datos pasan por sus servidores siempre. Si manejas datos sensibles de clientes o información confidencial del negocio, n8n self-hosted es la única opción aceptable.

> 📌 **Nota:** Los flujos de n8n se exportan como JSON y se pueden versionar en Git. Trata tus automatizaciones como código de producción — con commits, pull requests y rollback si algo falla.

## 🔗 Ver también

- [Scripts, APIs y Claude Routines](./scripts-apis-publicas.md) — el patrón Python base que se conecta con estas integraciones
- [MCP: El Protocolo que Conecta la IA con el Mundo](./mcp-conceptos.md) — la capa de estandarización que va un nivel por encima de las integraciones directas
- [n8n: Flujos Visuales con IA](./n8n-flujos.md) — profundización en n8n: más casos, anatomía del flujo, y flujos como código

---

[⬅️ Volver al Índice Principal](../README.md)
