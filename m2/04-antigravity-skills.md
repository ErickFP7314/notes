# [L04] Antigravity: Instalación, claude.md y el Sistema de Skills

> **Módulo:** M2 — Agentes de IA y Entornos de Ejecución  
> **Lección:** L04  
> **Tags:** `antigravity` `claude-md` `skills` `vs-code` `configuracion`

---

## 🧠 Core Insights

Instalar un agente tarda minutos. **Configurarlo bien** —para que entienda tu proyecto, tu estilo de trabajo y tus reglas— es lo que distingue a alguien que usa un agente de alguien que dirige uno.

### Antigravity vs Claude en el navegador

La diferencia no es cosmética. En el navegador, copias código, pegas código, describes cambios en texto. En Antigravity (extensión de VS Code), el agente puede leer el archivo directamente, hacer el cambio, correr el test, y reportarte el resultado sin que toques nada. Es la diferencia entre describir lo que quieres y delegar la ejecución.

### claude.md: el cerebro del agente para tu proyecto

El archivo `claude.md` en la raíz del proyecto es lo que transforma a un agente genérico en uno que sabe cómo trabajar contigo. Contiene las instrucciones que el agente lee al inicio de cada sesión: qué es el proyecto, qué stack usa, qué reglas deben respetarse, cómo debe comportarse.

Sin `claude.md`, el agente empieza cada conversación sin contexto. Con él, empieza ya sabiendo cómo funcionas.

**La regla más importante**: mantenerlo bajo **1000 tokens**. No es documentación para humanos — es un archivo de instrucciones para la IA. Las instrucciones contradictorias confunden al modelo. El exceso de contexto lo satura. Un `claude.md` de 200 palabras con reglas claras es más efectivo que uno de 2000 palabras con todo lo que se te ocurrió documentar.

### Skills: del agente genérico al especializado

Las Skills son archivos Markdown con instrucciones específicas para una tarea. No están siempre cargadas en el contexto: **se cargan solo cuando la tarea lo requiere**. Eso es lo que las hace eficientes.

Dos tipos que vale distinguir desde el principio:

- **Skills de conocimiento**: enseñan al agente algo que no sabe hacer bien de forma nativa. El estilo visual de tu marca. Las convenciones arquitectónicas de tu equipo. El formato exacto de tus documentos de diseño.
- **Skills de proceso**: codifican cómo haces las cosas en tu negocio. El flujo de una revisión de código. Los pasos de un deploy. La secuencia de validaciones antes de publicar.

Cada Skill tiene dos metadatos críticos:
- **Trigger**: cuándo se carga ("cuando crees un componente React", "cuando hagas un commit")
- **Scope**: en qué parte del proyecto aplica (solo en el frontend, solo en la API, en todo el repositorio)

El agente lee trigger y scope automáticamente. Si la tarea coincide, carga la Skill. Si no, no la carga. El contexto se mantiene limpio.

---

## ⚙️ Implementación Práctica

### Instalación paso a paso

```
1. Abre VS Code
2. Ve al panel de Extensiones (Ctrl+Shift+X)
3. Busca "Anthropic" o "Claude"
4. Instala la extensión oficial de Antigravity
5. Autentícate con tu cuenta de Claude
   → Si ya tienes suscripción Claude Pro, la detecta automáticamente
6. Aparece el panel lateral del agente: listo para usar
```

### Primer comando obligatorio: /init

```bash
# En cualquier proyecto nuevo, lo primero que ejecutas:
/init
```

El agente escanea el repositorio completo —estructura de carpetas, archivos de configuración, stack tecnológico, dependencias— y genera automáticamente `claude.md` en la raíz. Este archivo es el punto de partida para todo el trabajo posterior.

### Ejercicio de verificación (5 minutos)

```
1. Abre cualquier carpeta de trabajo en VS Code con Antigravity activo
2. Ejecuta /init → espera a que genere claude.md
3. Pídele: "Lista los archivos más importantes de este repositorio
   y explica qué hace cada uno"
4. Observa: qué archivos lee, qué herramientas usa, cómo estructura la respuesta
```

Ese primer ejercicio da intuición sobre cómo trabaja el agente antes de delegarle tareas complejas.

### Estructura de una Skill bien diseñada

```markdown
---
trigger: cuando crees un componente React
scope: src/components/
---

# Convenciones de Componentes React

## Estructura
- Un archivo por componente
- Nombre del archivo en PascalCase
- Exports nombrados, no default

## Estilo
- Usa Tailwind para estilos, nunca CSS inline
- Variantes de color definidas en tailwind.config.js

## Tests
- Un archivo .test.tsx por cada componente
- Cubre el caso base y el caso de error
```

### Mantenimiento del claude.md

```
# Cuando el archivo creció demasiado, pide al agente:
"Actualiza mi claude.md eliminando lo que ya no sea necesario,
lo que sea redundante o lo que haya quedado obsoleto."
```

### Las tres áreas de la interfaz

| Área | Qué muestra | Para qué sirve |
|------|-------------|----------------|
| Panel de chat | Conversación con el agente en tiempo real | Dar instrucciones, ver qué archivos lee y qué ejecuta |
| Historial de sesión | Acciones del agente en orden cronológico | Auditar qué hizo y en qué orden si algo salió mal |
| Permisos | Nivel de autonomía del agente | Configurar confirmación manual vs modo autónomo |

---

## 📌 Notas y Alertas

> 🔴 **Importante:** El límite de 1000 tokens para `claude.md` no es una recomendación de estilo — es un límite funcional. Un `claude.md` que se carga al inicio de cada sesión y consume 3000 tokens significa que empiezas cada sesión con el 3% del contexto ya gastado, y con instrucciones que el modelo tiene que procesar completamente antes de cualquier trabajo real. Mantenlo corto y actualizado.

> ⚠️ **Advertencia:** El panel de **Permisos** es la sección que más gente ignora. Por defecto, el agente pide confirmación antes de ejecutar comandos críticos. Apagar esa confirmación prematuramente, antes de entender bien cómo trabaja el agente en tu proyecto, puede resultar en cambios no deseados sin posibilidad de revisión. Activa el modo autónomo gradualmente y solo en tipos de tarea donde ya tienes confianza.

> 💡 **Tip:** Skills de conocimiento vs Skills de proceso es una distinción útil al crear tu primer set de Skills. Empieza con las de conocimiento (lo que el agente no puede saber solo): las convenciones de tu equipo, los patrones de tu codebase, tu guía de estilo. Las de proceso vienen después, cuando ya entiendes cuáles son los flujos repetitivos que vale la pena codificar.

> 📌 **Nota:** Antigravity es la capa visual sobre Claude Code. Tienen acceso al mismo motor: los mismos modelos, el mismo sistema de `claude.md`, el mismo sistema de Skills. La diferencia es la interfaz. Si en el futuro migras a Claude Code desde terminal, toda tu configuración de Skills y tu `claude.md` migra sin cambios.

---

## 🔗 Ver también

- [Ecosistema de Agentes](./02-ecosistema-agentes.md) — contexto sobre los tipos de agentes y por qué Antigravity es el punto de entrada del curso
- [Ollama y Modelos Locales](./05-ollama-modelos-locales.md) — cómo conectar modelos locales gratuitos a Antigravity para tareas repetitivas
- [Suscripciones y Precios](./06-suscripciones-precios.md) — qué plan necesitas para usar Antigravity con Claude Pro

---

[⬅️ Volver al Índice Principal](../README.md)
