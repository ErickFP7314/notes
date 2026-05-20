# [L31] Obsidian + Claude Code: El Cerebro que Aprende

> **Módulo:** M5 — Obsidian y Gestión Empresarial  
> **Lección:** L31  
> **Tags:** `obsidian` `claude-code` `cloud-md` `brain-dump` `memoria-persistente` `notebooklm`

---

## 🧠 Core Insights

El problema raíz de los asistentes de IA sin memoria no es la calidad del modelo — es que cada sesión empieza desde cero. El modelo no sabe quién eres, no conoce tu negocio, no recuerda las decisiones de la semana pasada. El resultado: contexto re-explicado en cada sesión y respuestas genéricas por falta de contexto específico.

La solución no es hacer que el modelo recuerde — es externalizar la memoria a archivos. Cuando Claude Code opera dentro de la carpeta de tu vault, la memoria no vive en el historial de conversación (efímero) sino en los archivos Markdown (persistentes). La amnesia desaparece no porque el modelo cambie, sino porque los archivos siguen ahí mañana.

El sistema tiene tres roles sin solapamiento: el **usuario** aporta ideas, contexto del negocio y decisiones estratégicas. **Claude Code** hace el trabajo pesado — redactar, estructurar, investigar, conectar, organizar. **Obsidian** almacena todo en Markdown local y visualiza las conexiones. Ninguno de los tres puede substituir al otro.

La pieza más importante del sistema no es la vault ni Claude Code: es el **`cloud.md`**. Este archivo en la raíz de la vault funciona como system prompt permanente del agente para esa bóveda específica. Define qué carpetas existen y para qué, cómo formatear notas nuevas, qué convenciones de nomenclatura seguir, cómo enlazar información nueva con la existente. Sin `cloud.md`, Claude Code trabaja con suposiciones; con él, trabaja con reglas de tu sistema.

El `cloud.md` no es estático — se actualiza solo. Periódicamente le pides a Claude Code que analice las notas recientes para detectar patrones de trabajo, intereses recurrentes, cuellos de botella y conexiones no detectadas, y que actualice el archivo. El sistema aprende de tu uso sin requerir configuración manual.

El **efecto compuesto** es la promesa real del sistema: marginalmente útil el primer día, claramente útil a la semana, genuinamente potente al mes. A diferencia de cualquier asistente sin memoria, este empieza cada sesión donde lo dejaste ayer.

**NotebookLM** opera como capa de síntesis para conocimiento externo que no está en tu vault — libros, papers, videos de YouTube, documentación técnica. Subes las fuentes, solicitas un podcast de audio, escuchas mientras haces otra cosa. Lo que capturas de ese análisis va al brain dump del día siguiente; Claude Code lo integra a la vault. El conocimiento externo se convierte en parte del sistema interno.

## ⚙️ Implementación Práctica

**Los 6 pasos de configuración:**

```
1. Crear la vault en Obsidian (carpeta local en disco)
2. Abrir esa misma carpeta en VS Code con extensión Claude Code
3. Instalar las Skills de Kepano (CEO de Obsidian):
   → Buscar repositorio en GitHub: kepano/obsidian-llm-skills
   → Pedirle a Claude Code que instale las Skills automáticamente
4. Crear cloud.md en la raíz de la vault
5. Pedirle a Claude Code que genere la estructura de carpetas base
6. Iniciar el flujo diario de brain dump
```

**Contenido base del `cloud.md`:**
```markdown
# Sistema: [Tu Nombre] Vault

## Rol del agente
Eres el organizador de conocimiento de esta vault. Tu trabajo es 
estructurar, conectar y hacer crecer este repositorio de conocimiento.

## Estructura de carpetas
- /Limbo — captura sin procesar, entrada sin fricción
- /Daily Notes — notas diarias con fecha YYYY-MM-DD
- /Proyectos — una nota por iniciativa activa
- /Research — información recopilada y sintetizada
- /Personas — contexto de relaciones profesionales
- /Ideas — conceptos sin proyecto asignado

## Convenciones
- Formato de fecha: YYYY-MM-DD
- Backlinks siempre entre [[dobles corchetes]]
- Una idea por nota (notas atómicas)
- Nuevas notas sin clasificar → /Limbo primero
- Etiquetas: #proyecto #persona #decisión #aprendizaje

## Al procesar información nueva
1. Crear nota atómica en la carpeta correcta
2. Añadir backlinks a notas relacionadas existentes
3. Actualizar el Índice si es un concepto nuevo importante
4. Si la nota tiene muchos backlinks posibles → revisar si merece entrada en Índice
```

**El flujo de brain dump diario:**
```
Cada mañana → volcás todo en un mensaje a Claude Code:
"Brain dump: [tareas, ideas, pendientes, preocupaciones, decisiones]"

Claude Code:
→ Crea archivos Markdown en la carpeta correcta
→ Genera backlinks hacia proyectos y personas mencionados
→ Organiza las tareas con checkboxes
→ Mueve todo a la carpeta correspondiente

Resultado: de caos mental a sistema organizado en ~10 minutos
```

**Actualizar el `cloud.md` con aprendizajes del sistema:**
```
Prompt: "Analiza las notas de las últimas dos semanas en /Daily Notes 
y /Proyectos. Identifica: patrones de trabajo recurrentes, temas que 
aparecen frecuentemente, cuellos de botella mencionados más de una vez, 
conexiones entre proyectos que no estaban documentadas. 
Actualiza cloud.md con ese contexto nuevo."
```

**Flujo de investigación automatizada:**
```
Prompt: "Investiga [tema] y guarda el resultado en /Research/[tema].md.
Incluye: resumen del estado del arte, mejores prácticas actuales, 
errores comunes, recursos clave. Añade backlinks hacia [[Proyecto X]] 
y [[Proyecto Y]] donde sea relevante."
```

## 📌 Notas y Alertas

> 🔴 **Importante:** Claude Code no opera en la interfaz de Obsidian — opera en VS Code sobre la misma carpeta. La vault es simplemente una carpeta de archivos Markdown. Obsidian solo la visualiza. Puedes tener ambas aplicaciones abiertas simultáneamente: Obsidian para navegar el grafo visual, VS Code + Claude Code para trabajar y editar.

> ⚠️ **Advertencia:** Las Skills de Kepano no son un plugin de Obsidian — son archivos de instrucciones que le dicen a Claude Code cómo interactuar con el formato de Obsidian correctamente: cómo crear backlinks, cómo respetar plantillas, cómo no romper la estructura existente. Sin ellas, Claude Code puede crear notas que no siguen las convenciones de Obsidian y quedan desconectadas del sistema.

> 💡 **Tip:** Para incorporar conocimiento de fuentes externas (libros, papers, videos) sin romper el flujo de trabajo: usa NotebookLM para la síntesis inicial en formato audio, luego volcás los insights al brain dump diario. El costo cognitivo es mínimo — escuchas mientras te movés o haces otra cosa, y Claude Code hace el trabajo de estructurar lo que capturas.

> 📌 **Nota:** El efecto compuesto no es una promesa de marketing — tiene una mecánica concreta. Cada sesión con Claude Code añade notas, backlinks y contexto al `cloud.md`. La próxima sesión comienza con más contexto. Con el tiempo, el agente puede predecir qué proyectos están relacionados, qué decisiones anteriores son relevantes para el problema actual, y qué información de Research aplica. No porque el modelo mejore, sino porque la memoria crece.

## 🔗 Ver también

- [Obsidian: Tu Segundo Cerebro](./01-obsidian-intro.md) — fundamentos de vault, backlinks y notas atómicas
- [Ecosistema Técnico de Obsidian](./03-obsidian-ecosistema.md) — LLM Wiki, arquitectura de tres capas, VS Code nativo
- [El Segundo Cerebro Empresarial](./05-segundo-cerebro-empresarial.md) — cómo escalar este sistema al conocimiento operativo de una empresa
- [07-notebooklm-rag-google-labs.md](../m1/07-notebooklm-rag-google-labs.md) — NotebookLM como capa de síntesis de fuentes externas
- [05-gentleman-ai-engram.md](../m4/05-gentleman-ai-engram.md) — Engram como sistema de memoria persistente equivalente para agentes

---

[⬅️ Volver al Índice Principal](../README.md)
