# [L21] Hooks y Arquitectura de Contexto

> **Módulo:** M3 — Prácticas Avanzadas, Automatización y MCPs  
> **Lección:** L21  
> **Tags:** `hooks` `claude-code` `context-engineering` `closed-loop` `R&D` `builder-validator` `settings.json` `subagentes`

---

## 🧠 Core Insights

El problema más común al trabajar con agentes no es la calidad del modelo. Es la falta de control sobre lo que el agente hace en cada paso: qué herramientas ejecuta, qué escribe, si valida su propio trabajo antes de devolverlo. Sin observabilidad sobre el ciclo de vida del agente, tienes un contratista que trabaja sin supervisión. Los hooks de Claude Code añaden esa capa de control.

**Los 5 tipos de hooks — cada uno con un caso de uso canónico:**

- **`pre-tool-use`:** se ejecuta *antes* de que el agente use cualquier herramienta. Caso canónico: seguridad. Un script que verifica que el agente no está intentando ejecutar `rm -rf`, leer archivos `.env`, o acceder a rutas fuera del workspace. Si el script devuelve error, la herramienta no se ejecuta.

- **`post-tool-use`:** se ejecuta *después* de que el agente usa una herramienta. Caso canónico: el closed loop. Disparar el linter automáticamente después de cada escritura de código; el resultado se inyecta de vuelta al agente, que se autocorrige en la siguiente iteración. El agente valida su propio trabajo sin intervención humana.

- **`notification`:** intercepta las notificaciones del agente. Caso canónico: text-to-speech. Convierte las notificaciones en audio para saber cuándo el agente termina una tarea larga sin estar mirando la pantalla constantemente.

- **`stop`:** se ejecuta cuando el agente finaliza la sesión completa. Casos: guardar automáticamente la conversación, loggear el estado final, disparar un script de verificación de integridad sobre los archivos modificados.

- **`sub agent stop`:** se ejecuta cuando termina un subagente específico. En flujos con múltiples agentes en paralelo, consolida los resultados de cada subagente en el orquestador principal.

**El closed loop es el patrón más potente que habilitan los hooks.** El ciclo: agente escribe código → `post-tool-use` dispara el linter → si hay errores, se inyectan de vuelta al contexto → agente lee el error y escribe la versión corregida → el hook se dispara de nuevo. Continúa hasta que el linter pasa. El agente no necesita instrucción para revisar el código: la arquitectura lo fuerza. Esto elimina una categoría entera de revisiones humanas.

**El framework R&D: Reduce y Delega.** No son Investigación y Desarrollo — son Reduce y Delegate. Dos principios para gestionar el contexto del agente con precisión quirúrgica.

*Reduce* el contexto innecesario. Un servidor MCP activo globalmente puede consumir hasta 10,000 tokens — entre el 5 y el 10% de la ventana completa — aunque ese servidor no sea necesario para la tarea. Los archivos `claude.md` que crecen sin control cargan tokens en cada sesión sin valor proporcional. La solución es **Context Priming**: en lugar de cargar todo el contexto del proyecto en cada sesión, usas slash commands dedicados que cargan exactamente lo que la tarea inmediata necesita. Un comando `/prime bug` carga contexto de debugging (archivos relevantes, logs, tests fallidos). Un comando `/prime feature` carga el contexto de implementación (spec, archivos a modificar, convenciones). Cada tarea recibe el contexto preciso, no todo el contexto del proyecto.

*Delega* las tareas que contaminarían tu ventana. Scraping de documentación extensa, exploración de directorios grandes, lectura de múltiples archivos para entender un patrón — todo eso va a subagentes con burbujas de contexto aisladas. El subagente explora, genera un resumen conciso, y muere. El orquestador recibe el resumen sin haber gastado su ventana en el proceso.

**Builder + Validator: doblar el cómputo para multiplicar la confianza.** Cada tarea en un grafo de dependencias tiene dos subagentes: uno que construye la implementación y otro que la revisa y testea de forma independiente. La lógica es contraintuitiva: ¿por qué gastar el doble de cómputo? Porque un Builder que autocorrige su propio trabajo tiene sesgo de confirmación — normaliza los errores que cometió. Un Validator que llega sin contexto del proceso de construcción ve los errores que el Builder normalizó. La confianza que genera una revisión independiente no es lineal — es multiplicativa.

## ⚙️ Implementación Práctica

**Configuración de hooks en `settings.json`:**
```json
{
  "hooks": {
    "pre-tool-use": [
      {
        "matcher": "Bash",
        "command": "python /ruta/a/hooks/security_check.py"
      }
    ],
    "post-tool-use": [
      {
        "matcher": "Write",
        "command": "python /ruta/a/hooks/linter_feedback.py"
      }
    ],
    "stop": [
      {
        "matcher": "*",
        "command": "python /ruta/a/hooks/session_logger.py"
      }
    ]
  }
}
```

**Hook de seguridad `security_check.py` (pre-tool-use):**
```python
#!/usr/bin/env python3
"""
UV Script — sin dependencias externas.
Verifica que el comando Bash no sea peligroso.
"""
import sys, json

data = json.load(sys.stdin)
command = data.get("tool_input", {}).get("command", "")

DANGEROUS_PATTERNS = [
    "rm -rf", "rm -r /", "> /dev/sda",
    "chmod 777 /", "curl | bash", "wget | bash"
]

SENSITIVE_PATHS = [".env", "id_rsa", "credentials", "secrets"]

for pattern in DANGEROUS_PATTERNS:
    if pattern in command:
        print(json.dumps({"error": f"Comando bloqueado: patrón peligroso '{pattern}'"}))
        sys.exit(1)

for path in SENSITIVE_PATHS:
    if path in command:
        print(json.dumps({"warning": f"Acceso a archivo sensible detectado: {path}"}))
        # No bloquea, solo alerta

sys.exit(0)
```

**Hook de linting `linter_feedback.py` (post-tool-use):**
```python
#!/usr/bin/env python3
import sys, json, subprocess

data = json.load(sys.stdin)
file_path = data.get("tool_input", {}).get("path", "")

if not file_path.endswith(".py"):
    sys.exit(0)

result = subprocess.run(
    ["ruff", "check", file_path, "--format=json"],
    capture_output=True, text=True
)

if result.returncode != 0:
    errors = json.loads(result.stdout or "[]")
    feedback = "\n".join([f"Línea {e['location']['row']}: {e['message']}" for e in errors])
    print(json.dumps({
        "feedback": f"El linter encontró errores en {file_path}:\n{feedback}\nCorrege estos errores antes de continuar."
    }))

sys.exit(0)
```

**Context Priming — slash commands en Claude Code:**
```bash
# En CLAUDE.md o en los settings de Claude Code, definir:
# /prime bug → carga contexto de debugging
# /prime feature → carga contexto de implementación

# Ejemplo de implementación como script:
cat logs/last-error.log tests/failing-tests.txt relevant-files.py
```

**Patrón Builder + Validator en un grafo de tareas:**
```
Tarea A
├── Builder A (subagente)  → implementa
└── Validator A (subagente) → revisa y testea independientemente
         ↓
     Resultado validado → pasa a Tarea B
```

## 📌 Notas y Alertas

> 🔴 **Importante:** El closed loop con `post-tool-use` + linter no es solo una herramienta de calidad — es un cambio de arquitectura. Elimina una categoría completa de revisiones humanas: ya no necesitas verificar si el agente siguió convenciones de formato, si el código pasa el linter, o si los tipos están correctos. El sistema lo hace en cada iteración. Esto es observable y determinista, a diferencia de instruirle al agente que "recuerde revisar el código".

> ⚠️ **Advertencia:** Los scripts de hooks deben ser UV scripts de Python de un solo archivo sin dependencias externas. Si el hook depende de librerías instaladas en el proyecto, fallará en cualquier entorno donde esas librerías no estén. La fiabilidad del hook es más importante que su sofisticación.

> 💡 **Tip:** Context Priming con slash commands es la solución a los `claude.md` que crecen hasta volverse contraproducentes. Un archivo de 2,000 palabras que se carga en cada sesión consume tokens que podrían ser contexto de trabajo real. Mejor un `claude.md` mínimo con comandos `/prime` que cargan contexto específico cuando se necesita.

> 📌 **Nota:** El framework R&D (Reduce & Delegate) no es una técnica de productividad personal — es una forma de pensar el diseño de sistemas multi-agente. La pregunta que debes hacerte antes de cualquier tarea inline es: "¿Esto infla mi contexto sin necesidad?" Si la respuesta es sí, la tarea va a un subagente.

## 🔗 Ver también

- [Agente 24/7: VPS, Telegram y las 4 Capas de Seguridad](./08-agente-247-vps.md) — aplicación de estos principios en un agente autónomo en producción
- [06-claude-code.md](../m2/06-claude-code.md) — fundamentos del agente y el sistema de settings donde viven los hooks
- [04-sdd-harness-engineering.md](../m4/04-sdd-harness-engineering.md) — el harness de ingeniería que combina hooks, Builder+Validator y Context Priming en un sistema completo

---

[⬅️ Volver al Índice Principal](../README.md)
