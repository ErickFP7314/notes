# [L32] Ecosistema Técnico de Obsidian

> **Módulo:** M5 — Obsidian y Gestión Empresarial  
> **Lección:** L32  
> **Tags:** `obsidian` `git` `llm-wiki` `vs-code` `arquitectura` `graphify` `mcp`

---

## 🧠 Core Insights

Hay una verdad que los tutoriales de Obsidian + IA omiten sistemáticamente: **Claude Code no lee el grafo visual de Obsidian, ni le importa**. El grafo es una herramienta de comprensión para el humano, no para la IA. El agente solo lee los archivos Markdown subyacentes. Esta aclaración cambia cómo diseñas el sistema: el valor está en la estructura y los backlinks de los archivos, no en la visualización.

El **LLM Wiki** (basado en el trabajo de Andrej Karpathy) es la arquitectura de tres capas más potente para dar memoria real a un agente desde archivos Markdown:

- **Raw**: fuentes crudas sin procesar — artículos, PDFs, transcripciones, datos sin estructurar. Todo lo que llega al sistema entra aquí primero.
- **Wiki**: el directorio procesado. Claude Code toma los archivos de Raw, extrae conceptos clave, genera resúmenes en Markdown limpio, crea backlinks entre conceptos relacionados, y produce notas atómicas — una por concepto.
- **Schema** (`cloud.md`): el alma del sistema. Define reglas de comportamiento, cómo se procesa información nueva, qué relaciones deben existir entre archivos.

El componente que hace eficiente al sistema no es las tres capas — es el **Índice**. Es una página maestra que correlaciona todos los archivos de la Wiki por categoría y tema. Cuando el agente recibe una pregunta, lee el Índice primero para construir un camino hacia la respuesta en lugar de buscar linealmente por todos los archivos. El resultado reportado es hasta un **95% de ahorro en tokens por consulta** — el agente navega solo los archivos relevantes en lugar de tokenizar el repositorio completo.

Las cuatro operaciones del sistema LLM Wiki tienen nombres específicos: **Ingest** (procesar información nueva individual), **Query** (consultar la base de conocimiento), **Lint** (encontrar archivos huérfanos sin conexiones e integrarlos), y **Bulk Ingest** (procesar múltiples archivos masivamente cuando incorporas una fuente grande nueva).

Para quien prefiere no depender de la interfaz de Obsidian, existe una **arquitectura nativa de VS Code** con tres archivos equivalentes: `Index.md` como catálogo central, carpeta de Source Files con el contenido real, y `cloud.md` como schema. Los cross-references funcionan con rutas relativas — sin dependencias externas. Si se quiere la visualización de grafo igual que en Obsidian, la extensión **FOAM** para VS Code logra exactamente ese resultado visual sin salir del entorno de desarrollo.

## ⚙️ Implementación Práctica

**Backup y sincronización gratuita con Git:**
```bash
# En la raíz de tu vault
git init
echo ".obsidian/workspace.json" >> .gitignore
echo ".obsidian/cache" >> .gitignore

git add .
git commit -m "feat: vault inicial"

# Repositorio privado en GitHub → costo cero
git remote add origin git@github.com:tu-usuario/vault-privado.git
git push -u origin main

# Sincronizar desde otro dispositivo
git pull origin main
```

**Estructura del LLM Wiki en la vault:**
```
vault/
├── cloud.md          ← Schema: reglas del sistema
├── Index.md          ← Mapa maestro de navegación
├── Raw/              ← Fuentes crudas sin procesar
│   ├── articulo-x.md
│   └── transcripcion-reunion.md
└── Wiki/             ← Conocimiento procesado y atómico
    ├── concepto-a.md
    └── concepto-b.md
```

**Operaciones del LLM Wiki (prompts a Claude Code):**
```
INGEST (un archivo):
"Procesa /Raw/articulo-x.md: extrae conceptos clave, genera notas 
atómicas en /Wiki/, crea backlinks con notas existentes, actualiza Index.md"

QUERY (consulta con navegación eficiente):
"Consultando el sistema: [pregunta]. Lee Index.md primero para 
identificar los archivos relevantes, luego responde con contexto completo."

LINT (mantenimiento):
"Recorre /Wiki/ y encuentra: notas sin backlinks entrantes, 
notas sin backlinks salientes, y conceptos duplicados. 
Genera reporte con sugerencias de integración."

BULK INGEST (fuente grande nueva):
"Procesa todos los archivos en /Raw/ que no tengan correspondencia 
en /Wiki/. Crea notas atómicas, backlinks y actualiza Index.md."
```

**Estructura del `Index.md` para navegación eficiente:**
```markdown
# Índice del Sistema

## Proyectos Activos
- [[Proyecto Alpha]] — descripción en 2 líneas de qué hay aquí
- [[Proyecto Beta]] — ídem

## Conceptos Técnicos
- [[Concepto X]] — cuándo acceder a este archivo
- [[Concepto Y]] — ídem

## Personas y Contexto
- [[Nombre Persona]] — relación, contexto relevante

## Decisiones Clave
- [[Decisión YYYY-MM-DD]] — resumen de 1 línea
```

**Arquitectura nativa VS Code (sin Obsidian):**
```
workspace/
├── cloud.md          ← Schema idéntico al de Obsidian
├── Index.md          ← Catálogo central con rutas relativas
└── notes/
    ├── daily/
    ├── projects/
    └── research/
```
- Instalar extensión **FOAM** en VS Code para grafo visual equivalente al de Obsidian
- Cross-references: `[Nota relacionada](./notes/projects/proyecto-x.md)`

**Setup Graphify + MCP de Obsidian (para grandes volúmenes):**
```bash
# Requiere Claude Code en bypass permissions mode
# Instalar servidor MCP de Obsidian
npm install -g cloud_obsidian

# Instalar Graphify
pip install graphify

# En settings.json de Claude Code:
{
  "mcpServers": {
    "obsidian": {
      "command": "cloud_obsidian",
      "args": ["--vault", "/ruta/a/vault"]
    }
  }
}
```

## 📌 Notas y Alertas

> 🔴 **Importante:** El Índice centralizado no es una conveniencia — es el componente que hace sostenible el sistema a escala. Sin Índice, el agente tokeniza todos los archivos para responder cada consulta (costoso y lento). Con Índice bien estructurado, navega directamente a los archivos relevantes. La diferencia reportada es hasta 95% menos tokens por consulta. Mantener el Índice actualizado es la tarea de mantenimiento más importante del sistema.

> ⚠️ **Advertencia:** El grafo visual de Obsidian (Graph View) es para el usuario humano, no para la IA. Si diseñas tu sistema pensando en "qué ve el agente en el grafo", estás construyendo sobre una premisa falsa. El agente lee texto plano en archivos `.md`. El grafo es consecuencia de los backlinks, no una estructura que el agente navega.

> 💡 **Tip:** `obsidian.nvim` habilita todas las funciones nativas de Obsidian dentro de Neovim: crear notas, insertar plantillas, navegar backlinks — sin salir del editor. Si vives en la terminal, elimina completamente el context switching de tener que cambiar de aplicación para gestionar la vault. La vault sigue siendo la misma carpeta, solo cambia el frontend.

> 📌 **Nota:** Graphify + MCP de Obsidian está justificado cuando el volumen de datos es grande y heterogéneo (transcripciones de llamadas, videos, PDFs masivos). Para uso personal con cientos de notas, la arquitectura de tres capas o la nativa de VS Code son más que suficientes y tienen cero overhead de configuración. No sobre-ingenierices el sistema desde el inicio.

## 🔗 Ver también

- [Obsidian: Tu Segundo Cerebro](./01-obsidian-intro.md) — fundamentos de vault, backlinks, notas atómicas
- [Obsidian + Claude Code: El Cerebro que Aprende](./02-cerebro-digital-notebooklm.md) — cloud.md, brain dump, integración con Claude Code
- [Gestión Empresarial con Obsidian](./04-gestion-empresarial-obsidian.md) — cómo Obsidian escala para dirigir equipos
- [06-claude-code.md](../m2/06-claude-code.md) — Claude Code como motor de operaciones sobre la vault

---

[⬅️ Volver al Índice Principal](../README.md)
