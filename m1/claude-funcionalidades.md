# [L06] Claude: Funcionalidades Clave

> **Módulo:** M1 — Fundamentos de IA y Ecosistema LLM  
> **Lección:** L06  
> **Tags:** `claude` `anthropic` `proyectos` `artefactos` `extended-thinking` `routines` `claude-design` `MCP` `desktop`

---

## 🧠 Core Insights

Claude no es un modelo de chat con features adicionales: es un ecosistema de trabajo técnico completo. La diferencia entre el chat básico de Claude y su uso profesional es entender cómo encajan sus piezas: Proyectos para persistencia, Artefactos para prototipar, Extended Thinking para decisiones complejas, Routines para automatización, Claude Design para interfaces, y la app Desktop para acceso al sistema operativo.

**Tres modelos con perfiles distintos — elegir mal tiene costo real:**
- **Haiku**: respuesta rápida, bajo consumo de tokens. Para tareas repetitivas, simples o de alto volumen.
- **Sonnet**: el equilibrio. Alta calidad, velocidad razonable. El 80% del trabajo cotidiano va aquí.
- **Opus**: razonamiento profundo, decisiones estratégicas. Más lento, más caro. Usar solo cuando el razonamiento importa más que la velocidad.

Usar Opus para todo cuando Sonnet resuelve igual es un error de eficiencia, no de calidad.

**Proyectos** resuelven el problema fundamental de los LLMs: cada chat empieza desde cero. Un Proyecto es un espacio de trabajo con instrucciones fijas y archivos que persisten entre sesiones. Subes tu documento de arquitectura, criterios del negocio, glosario del equipo y guías de estilo — Claude los tiene disponibles en cada chat que abres dentro del Proyecto, sin que gastes tokens presentándolos. Los archivos subidos a un Proyecto se **cachean**: consumen muchos menos tokens que subirlos manualmente en cada chat. Es doble ganancia: persistencia y eficiencia.

**Artefactos** son el mecanismo de prototipos rápidos: páginas web funcionales, gráficos, calculadoras, dashboards, SVGs — visualizados directamente en la interfaz, compartibles por URL pública. Flujo de prototipado real: pides una landing page → la ves renderizada en segundos → iteras en el mismo chat → compartes la URL para aprobación. Sin servidor, sin código que subir, sin esperar a nadie. Diez minutos de trabajo.

**Extended Thinking** muestra el proceso de razonamiento antes de la respuesta final: qué alternativas consideró el modelo, qué ángulos evaluó, por qué descartó ciertas opciones. El valor no es solo la respuesta sino la trazabilidad del razonamiento — puedes ver si hay un salto lógico o si el proceso es sólido. Costo: tiempo y tokens. No usarlo para todo: reservarlo para decisiones donde el proceso importa tanto como la conclusión.

**Routines** es automatización sin código: describes en lenguaje natural qué debe hacer Claude (revisar calendario, resumir emails, mandar un briefing), conectas las herramientas, defines el trigger, y Claude ejecuta en los servidores de Anthropic sin que tu computadora esté encendida. Para flujos simples y bajo volumen, elimina la necesidad de n8n o Zapier. Para flujos complejos con alto volumen de datos, n8n sigue siendo más eficiente.

**Claude Design** genera código HTML/CSS/JavaScript funcional, no wireframes ni mockups. El método correcto no es entrar a improvisar: primero definir estrategia, estilo visual y recursos gráficos en Claude Chat normal, luego usar Claude Design para el ensamblaje final con todos los materiales listos. Resultado: páginas con animaciones, video de fondo, scroll interactivo — trabajo que antes requería un diseñador y un desarrollador frontend.

**Claude Desktop** (app nativa Mac/Windows) es la forma más potente: acceso al sistema de archivos, ejecución de comandos de terminal, y conexión a herramientas externas vía **MCPs** (Model Context Protocol — se cubre en profundidad en M3). No estás limitado a texto: Claude tiene acceso a tu entorno completo de trabajo.

---

## ⚙️ Implementación Práctica

**Configurar un Proyecto efectivo:**
```
1. Crear Proyecto con nombre descriptivo (ej: "API E-commerce v2")
2. Subir archivos de referencia:
   - Documento de arquitectura del sistema
   - Criterios y restricciones del negocio
   - Glosario de términos del equipo
   - Instrucciones de estilo de código/respuesta
3. Escribir instrucciones del Proyecto (System Prompt fijo):
   "Eres un colaborador técnico de este proyecto. 
   Conoces la arquitectura definida en [archivo]. 
   Siempre propón soluciones compatibles con [stack]. 
   Responde en español, directo, sin introducción."
4. Todos los chats nuevos dentro del Proyecto arrancan con ese contexto
```

**Flujo de prototipo con Artefactos:**
```
"Crea una landing page para [producto] con:
- Hero section con headline y CTA
- Sección de 3 beneficios con íconos
- Formulario de contacto básico
- Paleta: [colores] / Fuente: [tipografía]
Hazlo funcional, que se vea en pantalla."
→ Ver render en la interfaz
→ "Ajusta el hero: agrega un video de fondo loop, 
   cambia el CTA a color [X]"
→ Compartir URL para revisión
```

**Configurar una Routine básica:**
```
Descripción natural:
"Cada lunes a las 8 AM:
1. Revisa mi calendario de la semana
2. Revisa mis emails no leídos de las últimas 12 horas
3. Manda un briefing a mi Telegram con:
   - Reuniones de la semana (hora, título, preparación necesaria)
   - Top 3 emails prioritarios con resumen de una línea cada uno"
→ Conectar: Google Calendar, Gmail, Telegram
→ Trigger: cron lunes 8 AM
```

**Cuándo usar cada modelo:**
| Tarea | Modelo |
|-------|--------|
| Responder preguntas frecuentes, formatear texto, generar boilerplate | Haiku |
| Escribir código, analizar documentos, redactar contenido técnico | Sonnet |
| Arquitectura de sistemas, decisiones estratégicas, análisis legal | Opus |
| Depurar un bug complejo con contexto amplio | Sonnet (primero), Opus si Sonnet falla |

---

## 📌 Notas y Alertas

> 🔴 **Importante:** Los archivos en Proyectos se cachean automáticamente. Esto no es solo comodidad — tiene implicaciones económicas directas si usas la API: el costo de tokens en cache es significativamente menor que el costo de tokens normales. En proyectos largos, estructurar bien los Proyectos puede reducir el costo de API a la mitad o menos.

> ⚠️ **Advertencia:** Claude Design genera código funcional, pero el método de "entrar a improvisar" produce resultados inconsistentes. La calidad del output de Claude Design es directamente proporcional a la calidad de los materiales que le das: si entras sin estrategia visual definida, el resultado se nota. Preparar primero en chat normal, ejecutar después en Design.

> 📌 **Nota:** Extended Thinking consume entre 3x y 10x más tokens que una respuesta normal de Sonnet. En la API esto se factura por separado. En el plan de suscripción tiene límites de uso. La regla práctica: activarlo solo cuando el razonamiento necesita ser auditable (decisiones con consecuencias, análisis con múltiples variables, evaluación de riesgos).

> 💡 **Tip:** MCPs (Model Context Protocol) son la capa que convierte Claude Desktop de un chatbot en un agente que opera tu entorno. Con MCPs puedes conectar Claude a bases de datos, APIs de terceros, herramientas de diseño, sistemas de archivos y cualquier servicio que tenga un conector. Se cubre en detalle en M3, pero vale saber desde ahora que la app Desktop es el punto de entrada a esa capacidad.

---

## 🔗 Ver también

- [Panorama de Herramientas LLM](./panorama-herramientas-llm.md) — por qué Claude es la herramienta principal del curso frente a otras alternativas
- [Contexto de Chat, Tokens y Tipos de IA](./contexto-tokens-tipos-ia.md) — los Proyectos y el caché son la solución práctica al problema de la ventana de contexto
- [claude-code.md](../wiki/claude-code.md) — M2: Claude Code para programación con agentes directamente desde el terminal

---

[⬅️ Volver al Índice Principal](../README.md)
