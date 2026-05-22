# [L01] Scripts, APIs y Claude Routines

> **Módulo:** M3 — Prácticas Avanzadas, Automatización y MCPs  
> **Lección:** L01  
> **Tags:** `api` `python` `automatización` `context-management` `skill-creator` `routines`

---

## 🧠 Core Insights

La distinción central de este módulo no es técnica sino conceptual: hay una diferencia entre *usar* IA como herramienta y *construir* herramientas de IA. Llamar a Claude desde el chat es lo primero. Escribir un script Python que procesa cien documentos con la API de Claude y devuelve resultados estructurados es lo segundo. El salto entre ambos modos es menor de lo que parece.

**Skill Creator como meta-skill.** La versión 2.0 del sistema de skills añade una herramienta que genera otras herramientas: describes en lenguaje natural qué debe hacer tu nueva skill, y el sistema construye la estructura completa (descripción, trigger, scope, instrucciones). El modo de evaluaciones permite refinar el resultado con ejemplos de referencia: la IA detecta las diferencias, propone ajustes y los aplica hasta que el output coincide con lo que mostraste. El trigger tuning resuelve el problema de ambigüedad cuando tienes quince o veinte skills activas y el agente duda sobre cuál activar.

**La API como patrón.** El patrón básico para llamar a Claude desde Python tiene exactamente tres partes: instalar `anthropic`, instanciar el cliente con tu API key, y hacer la llamada especificando modelo, rol y contenido. Desde ese punto, controlas qué entra, qué sale y qué ocurre con los resultados — algo que el chat nunca da.

**Claude Routines vs n8n.** Routines ejecuta flujos agénticos desatendidos en los servidores de Anthropic, sin que necesites servidor propio ni computadora encendida. Es la elección correcta para flujos que requieren razonamiento en lenguaje natural, bajo volumen de ejecuciones, y setup rápido. n8n gana cuando la lógica es determinista (if/then), el volumen es alto (pagar tokens por cada ejecución se vuelve caro), o cuando necesitas respuesta en tiempo real como un bot de WhatsApp.

**Las 7 reglas de gestión de contexto.** El problema que pocos conocen: Claude relee todo el historial en cada interacción. En una sesión larga, el modelo puede gastar el 98.5% de los tokens simplemente recordando lo que ya dijiste. Las siete reglas atacan ese problema sistemáticamente:

1. Edita el mensaje original si el modelo se equivoca — no acumules correcciones en el historial.
2. Cada 15-20 mensajes, pide un resumen de 300 palabras y úsalo como primer mensaje en un chat nuevo.
3. Agrupa preguntas: tres en un mensaje gastan menos tokens que tres mensajes separados.
4. Usa Proyectos para archivos recurrentes — los documentos cacheados consumen menos tokens.
5. Configura la memoria de usuario en los ajustes del perfil.
6. Desactiva búsqueda web o razonamiento extendido si no los necesitas.
7. Usa el modelo adecuado: Opus consume el doble que Sonnet, Sonnet el doble que Haiku. Para clasificar un email, Haiku es suficiente.

## ⚙️ Implementación Práctica

```bash
# Instalar el cliente oficial de Anthropic
pip install anthropic
```

```python
import anthropic

client = anthropic.Anthropic(api_key="TU_API_KEY")  # usar variable de entorno en producción

message = client.messages.create(
    model="claude-sonnet-4-5",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": "Analiza este documento: ..."}
    ]
)

print(message.content[0].text)
```

**Obtener API key:** console.anthropic.com → Settings → API Keys.

**Configurar Routines:**
1. Dale nombre a la rutina y descríbela en lenguaje natural.
2. Conecta las aplicaciones con los conectores nativos de Anthropic.
3. Define el trigger: cron, evento específico, o webhook.
4. El resultado llega a Telegram, Slack o email.

**Regla de límites de uso:** los límites se restauran en una ventana móvil de 5 horas. Distribuir el trabajo intensivo en bloques a lo largo del día y evitar horas pico reduce la probabilidad de alcanzarlos.

## 📌 Notas y Alertas

> ⚠️ **Advertencia:** El 98.5% de tokens gastados en historial no es un caso extremo — es lo que ocurre en cualquier sesión larga de trabajo real. Sin gestión activa del contexto, el costo real por sesión es mucho mayor de lo que aparece en la factura de tokens útiles.

> 🔴 **Importante:** Skill Creator es una meta-skill: genera skills, no produce resultados directos. El modo de evaluaciones es lo que separa una skill genérica de una que se activa con precisión quirúrgica. Sin ejemplos de referencia, el trigger puede ser ambiguo y el agente dudará entre skills.

> 💡 **Tip:** La decisión entre Routines y n8n no es de preferencia sino de caso de uso. Para un resumen diario que requiere síntesis y razonamiento, Routines. Para un bot que debe responder en tiempo real o flujos de alto volumen donde cada token tiene costo, n8n.

> 📌 **Nota:** La API key debe vivir como variable de entorno, nunca hardcodeada en el código. El archivo `.env` con `ANTHROPIC_API_KEY=...` y el módulo `python-dotenv` es el patrón estándar para esto.

## 🔗 Ver también

- [Integración con Servicios Externos](./02-integracion-terceros.md) — el siguiente paso: conectar scripts Python con GitHub, Trello, Linear y n8n
- [MCP: El Protocolo que Conecta la IA con el Mundo](./03-mcp-conceptos.md) — la capa de estandarización que unifica todas las integraciones
- [06-claude-code.md](../m2/06-claude-code.md) — fundamentos del agente y sistema de skills sobre el que se construye Skill Creator

---

[⬅️ Volver al Índice Principal](../README.md)
