# [L02] Arneses de IA y Arquitectura del Agente

> **Módulo:** M2 — Agentes de IA y Entornos de Ejecución  
> **Lección:** L02  
> **Tags:** `arneses-ia` `agentes` `bucle-repl` `memoria` `polimorfismo`

---

## 🧠 Core Insights

Para que un LLM se convierta en un agente autónomo, necesita un **arnés**: la estructura de software externa que actúa como su cuerpo, sentidos y manos. Mientras que el modelo (el núcleo) procesa la información y genera texto, el arnés define qué herramientas existen, cómo se interactúa con el sistema operativo y de qué manera se ejecutan los subagentes. 

El corazón de esta autonomía es el **bucle interno del agente** o bucle REPL (Read, Eval, Print, Loop). Cuando haces una petición compleja, el arnés no devuelve una respuesta inmediata. En su lugar, el modelo le pide al arnés ejecutar herramientas (como leer un archivo), el arnés lo hace físicamente, le devuelve el resultado al modelo y este evalúa si necesita usar más herramientas o si ya tiene la respuesta final.

Además, un arnés robusto implementa **polimorfismo**, lo que permite cambiar de proveedor (OpenAI, Anthropic, modelos locales) sin reescribir nada, traduciendo respuestas específicas a un formato estándar. También gestiona la **delegación**, donde instanciar un subagente es simplemente otra herramienta a disposición del agente principal, y maneja la **memoria** compactando el contexto o guardando datos de forma persistente.

## ⚙️ Implementación Práctica

El flujo de trabajo del bucle interno (REPL) se puede entender como una secuencia de pasos que el arnés gestiona automáticamente:

```text
1. Read  → El arnés captura la petición del usuario.
2. Eval  → El modelo evalúa qué herramientas necesita.
3. Tool  → El arnés ejecuta físicamente la herramienta (ej. leer un archivo).
4. Feed  → El arnés inyecta el resultado de la herramienta de vuelta al modelo.
5. Loop  → El modelo decide si necesita otra herramienta o formula la respuesta.
6. Print → El arnés muestra el resultado final en la interfaz.
```

## 📌 Notas y Alertas

> 🔴 **Importante:** El modelo de IA nunca ejecuta código por sí mismo. Solo genera una petición en texto estructurado pidiendo usar una herramienta; es el arnés el que realiza la acción en tu máquina.

> 🚨 **Precaución:** Como regla crítica de diseño, el arnés siempre debe incluir un gateway de seguridad. Antes de ejecutar herramientas peligrosas (como comandos de terminal que alteren el sistema), el arnés debe pausar el bucle y pedir aprobación explícita del usuario.

> 💡 **Tip:** Gracias al polimorfismo del arnés, puedes usar el mismo entorno y herramientas para modelos de distintos proveedores o incluso modelos locales, simplemente cambiando la configuración de la API.

> 📌 **Nota:** Para la IA principal, crear y delegar trabajo a un subagente no es más que utilizar otra herramienta de su catálogo, iniciando un nuevo bucle interno independiente.

## 🔗 Ver también

- [Concepto de agentes y diferencias clave](./01-concepto-agentes.md) — para repasar qué separa a un LLM estándar de un agente
- [Model Context Protocol: conceptos base](../m3/03-mcp-conceptos.md) — para entender cómo se estructuran y comunican las herramientas que el arnés ejecuta

---

[⬅️ Volver al Índice Principal](../README.md)
