# [L04] Antigravity CLI: El Agente Nativo de Terminal

> **Módulo:** M2 — Agentes de IA y Entornos de Ejecución  
> **Lección:** L04  
> **Tags:** `antigravity-cli` `terminal` `subagentes` `yolo-mode` `sdd`

---

## 🧠 Core Insights

Hay una enorme diferencia entre abrir una ventana extra para pedirle a una IA que escriba código, y tener un agente nativo directamente en tu consola que ejecuta comandos, prueba su propio código y nunca te saca de tu flujo de trabajo. Antigravity CLI, desarrollado por Google y reescrito en Go, es exactamente eso. Es extremadamente rápido y gratuito, dándote acceso a los mejores modelos de la industria sin salir de la terminal.

La interacción principal ocurre directo en la consola. Puedes usar la versión gratuita autenticándote con tu cuenta de Google o conectar tu propio proyecto de Google Cloud para usar modelos potentes sin límites de uso. El salto de productividad ocurre cuando delegas tareas: mientras el agente escribe código, puedes seguir conversando, ejecutar subagentes especialistas, o ver cómo itera autónomamente probando, leyendo errores de consola y corrigiéndolos hasta que el proceso funcione, todo sin intervención manual.

## ⚙️ Implementación Práctica

Para arrancar y exprimir Antigravity CLI, estos son los comandos y flujos esenciales:

```bash
# Iniciar el agente en la terminal actual
agi

# Configurar "Yolo Mode" (evita que pregunte antes de tocar cada archivo o comando)
/config
# -> Navega a la sección de permisos de herramientas -> "proceder siempre"

# Hacer una consulta rápida en medio de un proceso sin interrumpirlo
/btw ¿qué estilos CSS estás aplicando?

# Forzar el modo arquitecto: la IA planificará antes de escupir código
/planning

# Visualizar el historial y revisar o editar un plan generado por el agente
/artifact

# Desplegar la lista de procesos activos (ej. un servidor colgado)
/task
# -> K para matarlo, X para limpiar la vista

# Viajar en el tiempo a un estado anterior de la sesión si el enfoque falló
/rewind

# Retomar exactamente donde te quedaste tras cerrar la terminal
agi -c

# Ver cuánta memoria útil le queda al agente antes de empezar a olvidar
/context
```

Si necesitas usar Bash nativo en pleno desarrollo, solo debes escribir el símbolo de numeral `#` al inicio de tu mensaje para que se ejecute el comando (ej. `# git status`) sin abrir otra terminal.

## 📌 Notas y Alertas

> 🔴 **Importante:** Si dejas desactivado el "Yolo Mode" en `/config`, el agente te preguntará permiso antes de tocar cualquier archivo o ejecutar cada comando. En un flujo de desarrollo real, eso te frena por completo.

> ⚠️ **Advertencia:** Si pides arquitecturas complejas y dejas que la IA empiece a generar código a ciegas, fallará. Usa el comando `/planning` para obligarla a detenerse y generar primero un plan de implementación estructurado que tú apruebes.

> 📌 **Nota:** Puedes inyectar conocimiento experto descargando habilidades creadas por la comunidad usando el comando `/skills` (ej. perfiles avanzados de frontend), que se guardan en tu carpeta local para mejorar la capacidad del agente.

> 💡 **Tip:** Usa `Control + Enter` para escribir instrucciones de múltiples líneas antes de enviarlas; esto es vital para prompts detallados.

## 🔗 Ver también

- [Antigravity: instalación, interfaz y Skills](./03-antigravity-skills.md) — contexto del entorno de extensión en el editor
- [Ollama y Modelos Locales](./05-ollama-modelos-locales.md) — cómo conectar modelos locales para tareas repetitivas sin costo de tokens

---

[⬅️ Volver al Índice Principal](../README.md)
