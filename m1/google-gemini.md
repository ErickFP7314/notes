# [L04] Google Gemini

> **Módulo:** M1 — Fundamentos de IA y Ecosistema LLM  
> **Lección:** L04  
> **Tags:** `gemini` `google` `deep-search` `gems` `canvas` `google-workspace` `imagen` `veo`

---

## 🧠 Core Insights

La ventaja de Gemini no es el modelo en sí: es el ecosistema en que vive. Ningún otro modelo tiene acceso nativo al motor de búsqueda más usado del mundo, al calendario, al correo, al almacenamiento de archivos y a las herramientas de productividad de miles de millones de personas. Esa integración no es una feature opcional — es la razón por la que existe Gemini.

**Deep Search** no es simplemente "buscar en internet". Cuando lo activas, Gemini lanza múltiples consultas en paralelo, sintetiza los resultados y devuelve un informe coherente con fuentes citadas — en ~30 segundos en lugar de leer diez páginas manualmente. Funciona bien para: investigación técnica, comparativas de herramientas, contexto de mercado y validación de datos. La limitación es la misma que con cualquier modelo: Gemini puede parafrasear distorsionando el original. El hábito de verificar la fuente primaria cuando el dato importa no es negociable.

**Gems** son la versión de Gemini de los GPTs personalizados: versiones configuradas del modelo con instrucciones, personalidad y contexto predefinidos para una tarea. Google ofrece Gems preconfigurados (redacción, idiomas, código), pero los más útiles son los que construyes tú porque incorporan el contexto específico de tu trabajo y de tu empresa. La configuración es simple: descripción + instrucciones de comportamiento + archivos de referencia opcionales.

**Canvas** es el modo de creación colaborativa: un entorno entre el chat y el editor donde puedes generar la estructura de una presentación, editarla sección por sección en la misma interfaz, y exportarla directamente a Google Slides. Para wireframes, genera planos estructurales de interfaces — no código de frontend, sino la arquitectura visual: qué secciones, cómo se navega, qué elementos van dónde. Útil antes de involucrar a un diseñador o a un agente de código.

La integración con **Google Workspace** en la app móvil es lo más cercano a un asistente personal real: puede leer tu calendario, resumir emails de Gmail, buscar archivos en Drive y ejecutar acciones en apps de Google sin que tengas que copiar y pegar nada. La pregunta "¿qué reuniones tengo esta semana y qué materiales necesito preparar?" produce una respuesta con contexto real de tu cuenta, no una respuesta genérica.

**Imagen** (generación de imágenes integrada) tiene una diferenciación técnica relevante: coherencia en texto dentro de las imágenes. Carteles, mockups de interfaces, infografías con texto legible — Imagen maneja eso mejor que la mayoría de alternativas. **Veo** (generación de video) produce clips cortos competitivos con los mejores modelos del mercado y se integra con Google Workspace.

---

## ⚙️ Implementación Práctica

**Cómo crear un Gem útil:**
```
1. Descripción: qué debe hacer este Gem (una oración)
2. Instrucciones de comportamiento:
   - Qué tono usar
   - Qué tipo de proyectos tiene contexto
   - Cómo estructurar las respuestas
   - Qué NO debe hacer
3. Archivos de referencia (opcional):
   - Guías de estilo de la empresa
   - Documentación técnica del stack
   - Criterios del negocio
```

**Flujo Deep Search para investigación técnica:**
```
1. Activar Deep Search antes de escribir la pregunta
2. Hacer la pregunta completa con contexto: 
   "Necesito comparar [herramienta A] vs [herramienta B] 
   para [caso de uso específico] en 2025"
3. Revisar las fuentes citadas — clic en cada referencia 
   antes de usar el dato en trabajo serio
4. Pedir refinamiento: "¿Hay estudios de rendimiento recientes 
   que complementen esto?"
```

**Uso de Canvas para presentaciones:**
```
Prompt inicial: "Crea la estructura de una presentación de 
10 slides sobre [tema] para [audiencia]. Incluye: 
título, objetivo de cada slide, y 2-3 puntos clave por slide."
→ Editar cada sección en Canvas
→ Exportar a Google Slides
→ Pulir en Slides
```

**Integración con ecosistema Google desde el móvil — casos de uso:**
- "¿Qué tengo hoy en el calendario y cuánto tiempo libre hay entre reuniones?"
- "Resume los emails sin leer de esta mañana, prioriza por urgencia"
- "Busca en mi Drive el documento sobre [tema] que edité la semana pasada"
- "Crea un evento en mi calendario para [tarea] con recordatorio 30 min antes"

---

## 📌 Notas y Alertas

> 🔴 **Importante:** Gemini en Google Docs, Sheets y Slides (versión avanzada) reduce la fricción de cambiar entre herramientas. Puedes pedirle que redacte, analice o reformate directamente dentro del documento que estás usando. Esto no es solo comodidad — elimina el ciclo de copiar/pegar que interrumpe el flujo de trabajo.

> ⚠️ **Advertencia:** Deep Search cita fuentes pero puede parafrasear de forma que distorsiona el original. Especialmente en datos técnicos, estadísticas y fechas: nunca uses un dato de Deep Search en un entregable serio sin haber revisado el documento primario. La síntesis es valiosa para orientarse, no para citar.

> 📌 **Nota:** La ventana de contexto de Gemini es una de las más grandes del mercado. Combinada con la integración con Drive, esto significa que puedes subir repositorios de documentación extensos y hacer preguntas que abarcan el corpus completo — algo que con ventanas más cortas requeriría fragmentar y hacer múltiples consultas.

> 💡 **Tip:** Los Gems preconfigurados de Google son un punto de partida, no el destino. El valor real está en construir un Gem que ya sabe que trabajas con [tu stack], que conoce el tono de tu empresa y que tiene tus criterios de calidad como instrucciones base. Ese Gem reemplaza el párrafo de presentación que escribirías al inicio de cada chat.

---

## 🔗 Ver también

- [Panorama de Herramientas LLM](./panorama-herramientas-llm.md) — contexto de por qué Google juega en los dos tableros (propietario y open source)
- [NotebookLM y RAG — Google Labs](./notebooklm-rag-google-labs.md) — la herramienta del ecosistema Google para investigación con documentos propios, sin alucinaciones

---

[⬅️ Volver al Índice Principal](../README.md)
