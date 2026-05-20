# [L01] Contexto de Chat, Tokens y Tipos de IA

> **Módulo:** M1 — Fundamentos de IA y Ecosistema LLM  
> **Lección:** L01  
> **Tags:** `tokens` `contexto` `ventana-de-contexto` `alucinaciones` `LLM` `modelos-fundacionales`

---

## 🧠 Core Insights

La ventana de contexto es el único espacio de "memoria" que tiene un LLM durante una conversación. No existe una memoria real ni un archivo de historial: cuando envías un mensaje, el modelo re-lee toda la conversación desde el principio, cada vez. Esto significa que una conversación no es solo costosa al final — es progresivamente más costosa con cada vuelta.

La cifra más reveladora del guion: en conversaciones largas, la IA puede gastar el **98% de sus recursos simplemente recordando lo que ya dijo**. El mensaje nuevo representa apenas el 2% del trabajo. Usar chats como documentos de trabajo persistentes es uno de los errores más caros que se pueden cometer.

Los **tokens** son la unidad real de medida. No palabras, no caracteres: fragmentos en que el modelo divide el texto. En español los tokens son más fragmentados que en inglés — el mismo texto cuesta más contexto. Regla práctica: **100 palabras ≈ 130 tokens**. Con eso puedes estimar cuánto contexto consume cada archivo o instrucción que adjuntas.

La jerarquía de tipos de IA importa para no confundir herramientas:
- **Modelos fundacionales**: cerebros entrenados con datos masivos (GPT-4, Claude, Gemini, Llama). Todo se construye encima de ellos.
- **Modelos generativos**: subconjunto que *produce* contenido nuevo — texto, imágenes, audio, video. Se distinguen porque no solo clasifican sino que crean.
- **LLMs (Large Language Models)**: el caso más relevante para este curso. Predicen el siguiente token en una secuencia, y de esa predicción emerge conversación, razonamiento, código y análisis.

Las **alucinaciones** no son bugs sino el comportamiento esperado del diseño: el modelo predice el siguiente token más probable dada la secuencia anterior, sin distinguir entre "sé esto" y "no sé esto". Hay dos situaciones que las disparan especialmente: cuando el dato no estaba en el entrenamiento, y cuando el contexto está tan saturado que el modelo pierde el hilo de sus propias instrucciones.

---

## ⚙️ Implementación Práctica

**Estimar consumo de contexto antes de subir un archivo:**
```
tokens ≈ palabras × 1.3

Ejemplo: un documento de 3,000 palabras consume ~3,900 tokens
Un contexto de 200k tokens = ~154,000 palabras disponibles
```

**Estrategias para preservar la ventana de contexto:**
- Empezar chats nuevos para tareas nuevas, no continuar el mismo chat infinitamente
- Usar Proyectos (Claude) o Proyectos (ChatGPT) para subir documentos de referencia — los archivos cacheados consumen muchos menos tokens que pegarlos en el chat
- Comprimir instrucciones recurrentes en un System Prompt o instrucciones personalizadas, no repetirlos en cada mensaje

**Reducir alucinaciones con instrucciones directas en el prompt:**
```
"Si no estás seguro de un dato, indícalo explícitamente."
"Cita la fuente de cada estadística que menciones."
"Revisa tu respuesta: ¿hay algo que deba verificar de forma independiente?"
```

**Señal de alerta de contexto saturado:** cuando el modelo empieza a contradecir instrucciones que le diste al principio del chat, o ignora restricciones que definiste, el contexto probablemente ya está degradado. La solución es abrir un chat nuevo con el contexto esencial resumido, no seguir en el mismo.

---

## 📌 Notas y Alertas

> ⚠️ **Advertencia:** El contexto no se "limpia" aunque borres mensajes en la interfaz. Lo que el modelo procesa es la conversación completa hasta ese punto. La única forma real de liberar contexto es abrir un chat nuevo.

> 🔴 **Importante:** En español, los textos consumen entre un 15% y un 30% más tokens que el equivalente en inglés. Si el presupuesto de tokens importa (APIs, automatizaciones), escribir prompts en inglés tiene un costo menor. En trabajo cotidiano la diferencia es manejable, pero en sistemas con miles de llamadas al día, suma.

> 📌 **Nota:** La saturación de contexto y las alucinaciones están relacionadas. No son fenómenos independientes: un contexto saturado hace que el modelo ignore sus propias instrucciones de comportamiento y aumente la tasa de errores. Los agentes que se vuelven "raros" al avanzar en una tarea suelen tener este problema, no un bug en el código.

> 💡 **Tip:** Para saber cuántos tokens tiene un texto antes de enviarlo, OpenAI tiene un tokenizer público en `platform.openai.com/tokenizer`. Anthropic tiene el suyo en la documentación de la API. Ambos sirven para calibrar el costo real de tus prompts.

---

## 🔗 Ver también

- [Panorama de Herramientas LLM](./panorama-herramientas-llm.md) — cómo aplica la lógica de tokens al elegir modelo según tarea
- [Claude: Funcionalidades Clave](./claude-funcionalidades.md) — los Proyectos y el caché de archivos son la respuesta práctica al problema de la ventana de contexto

---

[⬅️ Volver al Índice Principal](../README.md)
