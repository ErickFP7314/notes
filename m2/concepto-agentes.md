# [L08] Concepto de Agentes: LLM + Herramientas + Bucle Autónomo

> **Módulo:** M2 — Agentes de IA y Entornos de Ejecución  
> **Lección:** L08  
> **Tags:** `agentes` `bucle-autonomo` `herramientas` `contexto` `texto-plano`

---

## 🧠 Core Insights

Un LLM responde texto. Un agente **hace cosas**. La diferencia es estructural, no de grado.

Un agente es un LLM al que se le suman dos piezas: **herramientas** (leer archivos, escribir, ejecutar comandos, llamar APIs, abrir navegadores) y un **bucle de ejecución autónomo**. Sin las herramientas, el modelo solo produce texto. Sin el bucle, el modelo espera que tú actúes en cada paso. Con ambas, el modelo puede planificar, ejecutar, observar resultados, corregirse, y repetir hasta terminar — sin que tengas que estar presente.

La consecuencia práctica: puedes delegarle una tarea compleja, salir a hacer otra cosa, y volver cuando esté terminada.

### Dos modos separados, no uno

Los agentes bien diseñados operan en dos modos que nunca deben mezclarse:

- **Modo planificación**: el agente analiza el problema, hace preguntas, identifica ambigüedades, y construye un plan técnico detallado. Aquí defines arquitectura, restricciones, y lo que la solución NO debe hacer. Si das instrucciones vagas aquí, el código que venga después será igualmente vago.
- **Modo implementación**: el plan ya está aprobado y el agente ejecuta. Interrumpirlo en este punto consume contexto adicional y aumenta el riesgo de que pierda coherencia con las decisiones del plan.

El flujo correcto siempre es: **planificación primero, implementación después**. Pídele al agente que te entreviste antes de proponer soluciones. Una conversación de cinco minutos en planificación puede ahorrar horas de refactorización.

### El repositorio como memoria

El agente vive dentro de tu sistema de archivos. El repositorio del proyecto es, literalmente, su memoria. Esto hace que el **tipo de archivo importe**:

- **Archivos binarios** (PDF, Excel, Word): el agente puede leerlos con herramientas especiales, pero no puede editarlos directamente ni hacer diffs entre versiones. Son opacos.
- **Texto plano** (Markdown, JSON, XML): el agente puede abrirlos, editarlos con precisión, comparar versiones con git diff. Son transparentes.

Regla práctica: si un archivo va a ser tocado por un agente, que sea texto plano. Documentos de diseño en Markdown. Configuraciones en JSON. Especificaciones en `.md`. El agente sabe exactamente qué hacer con eso.

### El degradado del contexto

El rendimiento de un agente no cae cuando su ventana de contexto está **llena**. Cae mucho antes: entre el **20% y el 40% de capacidad**. A partir de ese umbral, las respuestas pierden coherencia con las instrucciones iniciales, se repiten errores ya corregidos, y la calidad del código baja sin que el agente lo señale explícitamente.

Conclusión directa: las sesiones muy largas con un solo agente son sistemáticamente menos confiables que múltiples sesiones cortas y enfocadas. La solución es agentes de vida corta con una tarea específica y contexto mínimo.

---

## ⚙️ Implementación Práctica

### Flujo de trabajo recomendado por sesión

```
1. Abre sesión nueva con contexto mínimo (solo lo que esta tarea necesita)
2. Activa modo planificación
3. Pide al agente que te entreviste sobre el problema
4. Revisa el plan resultante — corrígelo antes de aprobar
5. Aprueba el plan → el agente entra en modo implementación
6. No interrumpas durante la implementación salvo emergencia real
7. Cierra la sesión al terminar la tarea (no la extiendas a otra tarea)
```

### Estructura de archivos para trabajo con agentes

```
mi-proyecto/
├── claude.md              # instrucciones del agente para este proyecto
├── docs/
│   ├── diseño.md          # especificaciones en texto plano
│   └── arquitectura.md
├── config/
│   └── settings.json      # configuración en JSON, no en Excel
└── src/
```

### Señales de que el contexto se está saturando

- El agente repite un error que ya corregiste antes
- Las respuestas ignoran reglas que definiste al inicio de la sesión
- El código generado no sigue el estilo del proyecto aunque lo especificaste
- El agente "olvida" restricciones que acordaste en planificación

Cuando ves estas señales: usa `/compact` para comprimir el historial, o cierra la sesión y abre una nueva con el contexto esencial.

---

## 📌 Notas y Alertas

> [!WARNING]
> El degradado de contexto ocurre entre el 20-40% de capacidad de la ventana, no al 100%. Planificar sesiones pensando que "aún le queda contexto" porque no se llenó es un error frecuente y costoso. El agente no avisa cuando empieza a perder coherencia.

> [!IMPORTANT]
> Modo planificación y modo implementación deben estar **estrictamente separados**. Preguntas o correcciones durante la implementación no solo interrumpen el flujo: consumen contexto adicional y aumentan la probabilidad de que el agente pierda el hilo del plan aprobado.

> [!TIP]
> Para forzar el modo planificación, usa este prompt: *"Antes de proponer ninguna solución, entrevístame sobre el problema. Identifica los puntos de decisión, las ambigüedades, y lo que la solución NO debe hacer. Luego genera el plan y espera mi aprobación antes de escribir código."*

> [!NOTE]
> No es casualidad que el ecosistema de agentes favorezca Markdown sobre cualquier otro formato de documentación. La decisión técnica (texto plano = editable por el agente) y la decisión de workflow (docs en el repositorio = memoria del agente) están alineadas. Diseñar tu sistema de archivos con esto en mente cambia el valor que obtienes del agente.

---

## 🔗 Ver también

- [Ecosistema de Agentes](./ecosistema-agentes.md) — tipos de agentes disponibles y cómo elegir entre ellos
- [Claude Code](./claude-code.md) — implementación concreta del bucle autónomo con Plan Mode y Agent View
- [M1: Contexto, Tokens y Tipos de IA](../m1/contexto-tokens-tipos-ia.md) — fundamentos del contexto y la ventana de tokens que explican el degradado

---

[⬅️ Volver al Índice Principal](../README.md)
