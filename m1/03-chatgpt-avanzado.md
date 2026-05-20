# [L03] ChatGPT Avanzado

> **Módulo:** M1 — Fundamentos de IA y Ecosistema LLM  
> **Lección:** L03  
> **Tags:** `chatgpt` `prompting` `deep-search` `GPTs` `proyectos` `alucinaciones` `instrucciones-personalizadas`

---

## 🧠 Core Insights

La mayoría de personas usa ChatGPT como un buscador. La diferencia entre ese uso y el uso profesional es la estructura: un prompt con estructura produce resultados consistentemente mejores porque le da al modelo el marco correcto de referencia antes de que empiece a generar.

**Los 5 componentes de un prompt profesional:**

1. **Rol** — Quién debe ser el modelo para esta tarea. No activa una "personalidad": activa el marco de referencia estadístico correcto. "Eres un desarrollador senior especializado en Python y bases de datos relacionales" no es retórica — determina qué vocabulario, qué nivel técnico y qué tipo de respuestas el modelo produce.

2. **Contexto del proyecto** — Qué estás haciendo y por qué. La IA no tiene contexto de tu situación a menos que se lo des explícitamente. "Estoy construyendo una API para e-commerce con FastAPI. La consulta de catálogo tarda 4 segundos con 10k productos" es el tipo de contexto que marca la diferencia entre una respuesta genérica y una útil.

3. **Tarea exacta** — Qué quieres que haga, sin ambigüedad. "Analiza este query SQL y sugiere tres optimizaciones concretas con justificación para cada una."

4. **Restricciones** — Qué no debe hacer y los límites del resultado. "No uses librerías externas. Compatible con Python 3.10. No me expliques fundamentos de SQL."

5. **Formato de salida** — Cómo debe venir la respuesta. "Lista numerada con el query modificado en bloque de código y explicación debajo de cada punto."

Las **instrucciones personalizadas** son una función subestimada. Se configuran una sola vez en los ajustes del perfil y se aplican a todos los chats nuevos. Dos bloques: quién eres (rol, nivel técnico, idioma preferido) y cómo debe comportarse el modelo (respuestas cortas o detalladas, si debe preguntar antes de ejecutar). El efecto: no tienes que presentarte en cada conversación.

Los **Proyectos** son el salto de calidad real de ChatGPT: espacios de trabajo donde subes archivos de referencia (catálogo, guías de estilo, especificaciones técnicas) que persisten entre sesiones. El modelo los tiene disponibles sin que gastes tokens presentándolos cada vez.

Los **jailbreaks** funcionan porque manipulan el contexto, no porque "hackean" el sistema. El modelo no tiene un módulo de seguridad separado — tiene instrucciones de comportamiento dentro de su propio contexto. Una narrativa bien construida que hace parecer que las restricciones no aplican puede llevar al modelo a ignorarlas. Implicación práctica: si recibes una respuesta extraña de un modelo que normalmente se comporta bien, puede ser que el contexto de esa conversación haya acumulado señales contradictorias.

---

## ⚙️ Implementación Práctica

**Plantilla de prompt estructurado:**
```
Rol: Eres [especialidad + dominio específico].

Contexto: [Qué proyecto, qué problema, qué estado actual].

Tarea: [Qué quieres que haga, verbo + objeto + especificidad].

Restricciones:
- [Lo que NO debe hacer o incluir]
- [Limitaciones técnicas o de alcance]

Formato de salida: [Cómo debe estructurarse la respuesta].
```

**Configuración recomendada de instrucciones personalizadas:**
```
[Quién eres — bloque 1]
Soy [tu rol]. Trabajo en [tipo de proyectos]. 
Mi nivel técnico en [dominio] es [básico/intermedio/avanzado].
Prefiero respuestas en español.

[Cómo debe comportarse — bloque 2]
Responde directo sin introducción ni cierre de cortesía.
Asume conocimiento técnico en [áreas].
Si la tarea es ambigua, pregunta antes de ejecutar.
Usa bloques de código con el lenguaje especificado.
```

**Prompts para reducir alucinaciones:**
```
"Si no estás seguro de algún dato, indícalo explícitamente."
"Cita la fuente de cada estadística que menciones."
"¿Hay algo en lo que acabas de decir que debería verificar de forma independiente?"
```

**Capacidades de procesamiento de documentos en ChatGPT:**
- PDFs, Excel (múltiples hojas), CSVs
- Pregunta al modelo que analice, resuma o extraiga patrones
- Para documentos de 20+ páginas, mejor preguntar por secciones específicas que pedir un resumen general

**Deep Search:** actívalo cuando necesites información actual (noticias, precios, lanzamientos recientes). El modelo consulta la web antes de construir la respuesta. Verifica siempre las fuentes que cita — puede parafrasear distorsionando el original.

---

## 📌 Notas y Alertas

> 🔴 **Importante:** El rol en el prompt no es decorativo. El modelo no "actúa" como un experto — accede a los patrones estadísticos que corresponden a ese perfil en sus datos de entrenamiento. Un prompt sin rol activa el promedio de todos los perfiles posibles, que es precisamente lo que produce respuestas genéricas.

> ⚠️ **Advertencia:** Los GPTs personalizados de la tienda de ChatGPT tienen acceso a instrucciones y archivos que el creador configura pero que el usuario no puede ver. Si usas un GPT de terceros para procesar información confidencial, esa información puede estar siendo enviada a sistemas externos vía "acciones" configuradas en el GPT. Para trabajo sensible, usa ChatGPT estándar sin GPTs de terceros.

> 💡 **Tip:** El modo voz de la app de ChatGPT en iOS/Android tiene calidad suficiente para pensar en voz alta sobre un problema técnico mientras caminas o estás en movimiento. No es solo dictado — el modelo responde con la misma calidad que en texto y mantiene el hilo de la conversación.

> 📌 **Nota:** "¿Hay algo en lo que acabas de decir que debería verificar de forma independiente?" es una de las preguntas más útiles que se le pueden hacer a un LLM después de una respuesta larga. No garantiza que encuentre todos los errores, pero activa un tipo de revisión interna que a veces captura afirmaciones que el modelo generó por inercia estadística más que por conocimiento sólido.

---

## 🔗 Ver también

- [Contexto de Chat, Tokens y Tipos de IA](./01-contexto-tokens-tipos-ia.md) — por qué el consumo de tokens determina la calidad de las respuestas en chats largos
- [Panorama de Herramientas LLM](./02-panorama-herramientas-llm.md) — cómo encaja ChatGPT en el ecosistema completo y cuándo elegir otra herramienta

---

[⬅️ Volver al Índice Principal](../README.md)
