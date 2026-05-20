# [L21b] Agente 24/7: VPS, Telegram y las 4 Capas de Seguridad

> **Módulo:** M3 — Prácticas Avanzadas, Automatización y MCPs  
> **Lección:** L21b  
> **Tags:** `vps` `telegram` `seguridad` `openclaw` `autonomia` `identidad-separada` `permisos-minimos` `produccion`

---

## 🧠 Core Insights

Un agente que solo funciona cuando tienes la computadora abierta no es un agente autónomo — es un asistente muy rápido. La autonomía real comienza cuando el agente trabaja mientras duermes, mientras viajas, mientras haces otra cosa. Eso requiere un servidor que esté siempre encendido, una interfaz de control accesible desde cualquier lugar, y un modelo de seguridad que limite el daño si algo sale mal.

**VPS vs tu computadora.** Un VPS (servidor privado virtual) es una máquina virtual en un datacenter que está siempre encendida y siempre conectada, accessible por SSH desde cualquier lugar. El costo está entre 3 y 10 dólares al mes en HostGator o Digital Ocean. Comparado con tu laptop: no consume tu batería ni tus recursos, no se desconecta cuando cierras la computadora, no depende de tu conexión local, y si el agente comete un error que cae el proceso, el VPS sigue funcionando y puedes reconectar.

**Las 4 capas de seguridad.** Dar a un agente acceso a un servidor con credenciales reales y capacidad de ejecutar comandos en repos de producción es un riesgo real. Las cuatro capas aplican el principio de menor privilegio a la IA:

1. **`agents.md` con reglas en lenguaje natural.** No es código — son directrices que el modelo lee y sigue como cualquier otra instrucción de contexto: "nunca hagas push a main directamente", "nunca leas archivos con contraseñas o tokens", "no expongas credenciales en ningún output". El modelo las acata porque forman parte de su contexto de instrucciones.

2. **Workspace aislado.** El agente opera solo dentro de una carpeta específica del servidor. No tiene acceso al sistema de archivos completo, no puede navegar fuera de ese directorio, no puede leer archivos de otros proyectos o configuraciones del sistema.

3. **Identidad separada.** No le das las credenciales de tu cuenta de GitHub al agente. Creas una cuenta secundaria exclusiva para él, con su propio correo, nombre de usuario y llaves SSH. El agente firma sus commits con esa identidad. En el historial de git queda claro qué hizo el humano y qué hizo el agente.

4. **Permisos mínimos.** La cuenta secundaria tiene rol de Collaborator — no de propietario — en los repos donde puede trabajar. Solo tiene acceso a los repos explícitamente autorizados. Sin acceso a repos privados personales, sin configuraciones de organización, sin capacidad de borrar repos.

**Telegram como interfaz de control remoto.** Telegram es la interfaz ideal porque es ligera, accesible desde cualquier dispositivo, y tiene una API robusta para bots. La restricción crítica en la configuración de OpenClaw: `allow from <tu_usuario_de_telegram>`. El bot solo responde a mensajes que vengan de tu usuario, ignorando completamente cualquier otro. La gestión es completamente asíncrona: mandas la instrucción, haces otra cosa, y cuando el agente termina te notifica en el mismo chat.

**Pi como agente vacío para aprender el protocolo.** Antes de desplegar un agente completo con acceso real a herramientas, Pi es el punto de partida sin riesgos: comienza sin ninguna skill configurada, sin herramientas externas, sin contexto de ningún proyecto. Es el equivalente de estudiar una función antes de ponerla en producción — entiendes cómo el agente responde a instrucciones y cómo cambia de comportamiento a medida que le añades contexto, sin la complejidad de las integraciones.

## ⚙️ Implementación Práctica

**Instalación de OpenClaw en VPS (Ubuntu/Debian):**
```bash
# 1. Conectarse al VPS
ssh usuario@ip-del-vps

# 2. Instalar FNM (gestor de versiones de Node)
curl -fsSL https://fnm.vercel.app/install | bash
source ~/.bashrc

# 3. Instalar Node 22
fnm install 22
fnm use 22

# 4. Instalar OpenClaw
npm install -g openclaw@latest --legacy-peer-deps

# 5. Instalar GitHub CLI
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
sudo apt update && sudo apt install gh
```

**Configuración de identidad separada para el agente:**
```bash
# Crear llaves SSH con el correo de la cuenta secundaria del agente
ssh-keygen -t ed25519 -C "agente@tu-dominio.com" -f ~/.ssh/id_agente

# Mostrar la llave pública (agregar a la cuenta secundaria en GitHub)
cat ~/.ssh/id_agente.pub

# Configurar git con la identidad del agente
git config --global user.name "nombre-agente"
git config --global user.email "agente@tu-dominio.com"

# Autenticar GitHub CLI con la cuenta secundaria
gh auth login  # elegir SSH, usar la llave del agente
```

**Configurar el bot de Telegram con BotFather:**
```
1. Abrir Telegram → buscar @BotFather
2. Enviar /newbot → seguir instrucciones
3. BotFather devuelve el TOKEN del bot
4. Obtener tu user ID: @userinfobot → enviar cualquier mensaje
```

**Onboarding de OpenClaw con daemon:**
```bash
openclaw onboard --install-daemon
# El wizard configura interactivamente:
# - Modelo a usar (claude-sonnet recomendado)
# - Memoria de sesión
# - Token de Telegram
# - Restricción allow from tu_usuario
```

**Ejemplo de `agents.md` (reglas de comportamiento):**
```markdown
# Reglas del Agente

## Restricciones de Git
- NUNCA hagas push directamente a la rama `main` o `master`
- SIEMPRE crea una rama nueva con formato `agente/descripcion-breve`
- SIEMPRE abre un Pull Request — no mergees directamente

## Restricciones de Seguridad
- NUNCA leas, expongas ni imprimas el contenido de archivos `.env`, `secrets.*`, `*.key`, `*.pem`
- NUNCA incluyas tokens, contraseñas o credenciales en commits, outputs o mensajes de Telegram
- Si encuentras credenciales expuestas accidentalmente, reporta inmediatamente sin mostrarlas

## Límites de Operación
- Solo opera dentro del directorio `/home/agente/workspace/`
- No instales paquetes globalmente en el sistema
- No modifiques configuraciones del sistema operativo

## Comunicación
- Reporta el progreso de tareas largas en Telegram cada 15 minutos
- Al terminar una tarea, incluye: qué hiciste, archivos modificados, PR creado (si aplica)
```

**Verificar que el agente está corriendo como daemon:**
```bash
systemctl status openclaw
journalctl -u openclaw -f  # ver logs en tiempo real
```

**Comandos típicos desde Telegram:**
```
"Revisa los issues abiertos del repo frontend y dame un resumen"
"Corre los tests del proyecto y reporta qué falla"
"Crea una rama y arregla el bug #142 — el botón de login en móvil"
"Abre un PR con los cambios de la rama agente/fix-mobile-login"
```

## 📌 Notas y Alertas

> 🔴 **Importante:** La restricción `allow from <tu_usuario_de_telegram>` es la barrera de acceso principal al agente. Sin ella, cualquier persona que descubra el username del bot puede darle instrucciones. No es opcional — es la primera configuración a verificar antes de darle al agente acceso a credenciales reales.

> ⚠️ **Advertencia:** La identidad separada (capa 3) no es solo buena práctica — es necesaria para auditoría. Si el agente comete un error grave en producción (borrar datos, romper un build, exponer algo), necesitas poder distinguir en el historial qué fue el agente y qué fuiste tú. Sin identidad separada, esa distinción no existe.

> 💡 **Tip:** Empieza con Pi antes de OpenClaw en producción. Pi sin skills activas te enseña exactamente cómo el agente procesa instrucciones, dónde necesita contexto adicional, y cómo responde a casos límite — sin el riesgo de que cometa errores con acceso real a tus repos. Es la versión de prueba del protocolo.

> 📌 **Nota:** El `agents.md` con reglas en lenguaje natural (capa 1) no es una medida de seguridad técnica — es una medida de comportamiento. El modelo las sigue porque forman parte de su contexto, pero un modelo malicioso o con un jailbreak podría ignorarlas. Las capas 2, 3 y 4 (workspace aislado, identidad separada, permisos mínimos) son las que tienen enforcement técnico real. Las cuatro capas son necesarias juntas.

## 🔗 Ver también

- [Hooks y Arquitectura de Contexto](./hooks-arquitectura-contexto.md) — los mismos principios de control y seguridad aplicados a nivel de hooks dentro de una sesión
- [claude-code.md](../m2/claude-code.md) — el agente base sobre el que se construye OpenClaw y el sistema de autonomía
- [ollama-modelos-locales.md](../m2/ollama-modelos-locales.md) — alternativa para agentes autónomos con modelos locales: sin costo por token, sin datos en la nube

---

[⬅️ Volver al Índice Principal](../README.md)
