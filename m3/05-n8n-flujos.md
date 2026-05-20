# [L19] n8n: Flujos Visuales con IA

> **Módulo:** M3 — Prácticas Avanzadas, Automatización y MCPs  
> **Lección:** L19  
> **Tags:** `n8n` `automatización` `flujos` `webhook` `cron` `haiku` `sonnet` `self-hosting`

---

## 🧠 Core Insights

No toda automatización necesita código. Hay una categoría entera de integraciones — conectar GitHub con Slack, convertir emails en tickets, notificar cuando se cumplen condiciones específicas — donde escribir Python añade más costo de mantenimiento que valor. n8n cubre ese espacio. Y cuando le añades nodos de IA dentro del flujo, el resultado es una automatización que puede *razonar*, no solo ejecutar.

**n8n vs Zapier: la diferencia real no es la interfaz.** Es el modelo de control. Zapier es un SaaS cerrado donde tus datos pasan por sus servidores, pagas por ejecuciones, y dependes de su infraestructura. n8n puedes instalarlo en tu VPS con Docker — tus datos no salen de tu infraestructura, y el costo es el del servidor, no el de las ejecuciones. Para un equipo pequeño o creador que automatiza procesos propios, eso cambia completamente el análisis económico.

**Anatomía de un flujo:** todo flujo tiene tres capas.
1. **Trigger:** el evento que inicia la ejecución. Un Schedule (Cron) ejecuta a una hora específica. Un Webhook inicia cuando una URL recibe una llamada HTTP. Los triggers de apps reaccionan a eventos en servicios externos (email nuevo, cambio en Asana, alerta de AWS).
2. **Nodos de acción:** llaman APIs, aplican lógica condicional (If), iteran listas (Loop), esperan aprobación humana, transforman datos con JavaScript, o delegan a un modelo de IA.
3. **Nodos de IA:** el diferenciador genuino de n8n. Puedes insertar un LLM directamente en el flujo como si fuera cualquier otro nodo. Recibe datos del nodo anterior, los procesa con lenguaje natural, y devuelve respuesta estructurada que el siguiente nodo puede usar.

**Caso 1 — Daily PR Review:** a las 9am el trigger Schedule activa el flujo → nodo GitHub extrae todos los PRs pendientes → nodo de IA con Claude Sonnet genera un resumen con recomendaciones (qué revisar, qué patrones detecta, qué preguntas haría un revisor experimentado) → nodo Slack envía el resumen al canal `#engineering`. El equipo empieza cada día con una vista consolidada sin que nadie haya abierto GitHub manualmente.

**Caso 2 — Soporte técnico automatizado:** el trigger es un email entrante a soporte → nodo de IA con Claude Haiku recibe el cuerpo del email y devuelve JSON estructurado con cuatro campos (si es soporte válido o spam, tipo de problema, nivel de urgencia, resumen breve) → nodo JavaScript limpia la respuesta → nodo If filtra: si no es soporte válido, el flujo termina; si es válido, nodo Linear crea el ticket automáticamente con título, descripción y prioridad generados por el modelo.

**Por qué Haiku para clasificación y Sonnet para razonamiento.** Clasificar un email es cognitivamente simple — categorización estructurada que no requiere razonamiento profundo. Haiku lo hace igual de bien que Sonnet a un costo significativamente menor. Para flujos de alto volumen donde el modelo procesa cientos de emails al día, esa diferencia de costo es relevante y real.

**Flujos como código.** Cada flujo de n8n puede exportarse como JSON, guardarse en Git, versionarse y restaurarse. Los flujos se tratan como activos del proyecto, no como configuración opaca dentro de una plataforma SaaS. La comunidad publica flujos en GitHub y el foro oficial — si alguien ya automatizó lo que quieres automatizar, descargas el JSON y lo adaptas.

## ⚙️ Implementación Práctica

**Instalar n8n en VPS con Docker:**
```bash
docker run -d \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  --restart unless-stopped \
  n8nio/n8n
```
Acceso en `http://tu-vps-ip:5678`. El flag `-v` persiste los flujos fuera del contenedor.

**Flujo Daily PR Review — configuración del nodo de IA:**
- **Modelo:** Claude Sonnet 3.5 (via Anthropic o LangChain node)
- **Prompt del sistema:** "Eres un revisor de código senior. Analiza los siguientes Pull Requests pendientes y genera un resumen con: (1) qué partes revisar, (2) patrones de código que detectas, (3) preguntas que haría un revisor experimentado."
- **Input:** `{{ $json.title }} — {{ $json.user.login }}: {{ $json.body }}`

**Nodo JavaScript para limpiar JSON de respuesta del modelo:**
```javascript
const rawText = $input.first().json.text;
// Extraer JSON del bloque de código si el modelo lo envolvió
const match = rawText.match(/```json\n([\s\S]*?)\n```/);
const jsonStr = match ? match[1] : rawText;
return [{ json: JSON.parse(jsonStr) }];
```

**Estructura del JSON que el modelo debe devolver (Caso soporte):**
```json
{
  "es_soporte_valido": true,
  "tipo_problema": "autenticación",
  "urgencia": "alta",
  "resumen": "Usuario no puede iniciar sesión desde iOS 17 tras la última actualización"
}
```

**Exportar e importar flujos:**
```bash
# Exportar todos los flujos
n8n export:workflow --all --output=./backups/workflows.json

# Importar flujos desde archivo
n8n import:workflow --input=./backups/workflows.json
```

**Cron expressions útiles:**
- `0 9 * * 1-5` → Lunes a viernes a las 9am
- `0 */2 * * *` → Cada 2 horas
- `0 8 * * 1` → Lunes a las 8am (resumen semanal)

## 📌 Notas y Alertas

> 🔴 **Importante:** La elección de modelo dentro del flujo de n8n tiene impacto económico directo. En un flujo de soporte que procesa 200 emails/día, la diferencia entre Haiku y Sonnet es aproximadamente 10x en costo por token. Usa el modelo más capaz solo donde el razonamiento complejo es necesario — síntesis, decisiones ambiguas. Para clasificación estructurada, Haiku es la respuesta correcta.

> ⚠️ **Advertencia:** n8n sin `-v` en Docker pierde todos los flujos si el contenedor se reinicia o actualiza. El volumen no es opcional en producción. También: exponer el puerto 5678 sin autenticación en un VPS público es un riesgo de seguridad real — configura Basic Auth o un reverse proxy con HTTPS desde el primer día.

> 💡 **Tip:** El repositorio oficial de la comunidad n8n en GitHub tiene cientos de flujos listos para importar. Busca por caso de uso antes de construir desde cero. El tiempo de construcción pasa de horas a minutos cuando partes de algo que ya funciona.

> 📌 **Nota:** Un script Python puede llamar a un webhook de n8n, que dispara un flujo que a su vez llama de vuelta a Claude. La integración bidireccional entre código y flujos visuales no es un antipatrón — es la arquitectura correcta cuando cada parte hace lo que mejor hace.

## 🔗 Ver también

- [Integración con Servicios Externos](./02-integracion-terceros.md) — el contexto de las integraciones manuales que n8n viene a simplificar
- [MCP Avanzado: FastMCP, Casos Reales y Monetización](./04-mcp-avanzado.md) — MCPs que n8n puede consumir como herramientas dentro de sus flujos
- [WhatsApp RAG Bot](./06-whatsapp-rag-bot.md) — n8n como orchestrador del bot de WhatsApp con base de conocimiento
- [06-startup-n8n-graphify.md](../m4/06-startup-n8n-graphify.md) — caso real de producto construido sobre n8n como infraestructura de automatización

---

[⬅️ Volver al Índice Principal](../README.md)
