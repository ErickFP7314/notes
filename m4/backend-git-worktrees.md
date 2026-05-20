# [L24] Backend Automático y Git Worktrees

> **Módulo:** M4 — Creación de Productos, SDD y Monetización  
> **Lección:** L24  
> **Tags:** `backend` `git-worktrees` `deploy` `baas` `railway` `agentes-paralelos` `serverless`

---

## 🧠 Core Insights

Construir un backend completo desde cero puede tomar semanas. Hay cuatro enfoques modernos que reducen ese tiempo a días, y el criterio para elegir entre ellos determina si el producto escala o colapsa.

**Opción 1 — Funciones serverless (AWS Lambda, Netlify/Vercel Functions):** Fragmentos de código aislados que se ejecutan bajo demanda. Pagas por ejecución, no por tiempo activo. Correctas para lógica puntual y discreta: procesar un pago, enviar un email, gestionar un webhook. Incorrectas para aplicaciones con estado complejo o lógica que necesita compartir datos entre funciones.

**Opción 2 — BaaS (Firebase, Supabase, Inforce):** Base de datos, autenticación, hosting y funciones ya construidos. Cambias control por velocidad. El MVP más rápido posible sale de aquí. Inforce es la variante AI-native: integra habilidades MCP directamente en el BaaS y permite ejecutar instrucciones a nivel de backend vía lenguaje natural — la opción para proyectos que van a interactuar intensamente con modelos de IA desde el día uno.

**Opción 3 — Headless CMS (Strapi):** Si el producto tiene contenido administrable — artículos, productos, catálogos — el CMS elimina la necesidad de construir el panel de administración desde cero. El agente conecta el frontend a las APIs del CMS sin construir el backend manualmente.

**Opción 4 — Todo en uno (OneEntry):** BaaS con módulos comerciales sin código incluidos: carrito de compras, membresías, gestión de clientes. Menor flexibilidad, mayor velocidad de lanzamiento. Para un primer producto donde validar la idea importa más que la arquitectura perfecta.

**Regla práctica:** empieza con BaaS o Inforce para el MVP. Evalúa migrar a backend propio solo cuando los límites de la plataforma empiecen a bloquearte activamente.

**Git Worktrees** resuelve el problema del trabajo paralelo con agentes: Git solo puede tener una rama activa por directorio. Un Worktree es un directorio adicional vinculado al mismo repositorio donde puedes tener otra rama activa simultáneamente. Con esto, un agente trabaja en `feature/payments` mientras otro trabaja en `fix/auth-bug` — sin conflictos, sin esperas.

---

## ⚙️ Implementación Práctica

### Deploy automático con Railway CLI + Skills

```bash
# 1. Instalar Skills de Railway en el agente
npx skills

# 2. En el proyecto, activar Railway
/use railway

# El agente hace el resto:
# - Analiza la aplicación
# - Determina qué backend y base de datos necesita
# - Crea el entorno en Railway
# - Configura variables de entorno
# - Despliega
# - Si algo falla, lee logs de Railway y corrige
```

### Deploy en VPS con Claude Code (control total)

```bash
# 1. Adquirir servidor Ubuntu + conectar via SSH
ssh user@tu-servidor

# 2. Instalar Docker y Claude Code en el servidor
# (el agente puede hacer esto una vez conectado)

# 3. Dar al agente acceso al repo y modo bypass
# El agente interpreta el código, crea contenedores Postgres,
# instala dependencias, corre migraciones, usa PM2 para procesos

# Resultado: producción activa sin que sigas conectado
```

### Setup de Git Worktrees — estructura recomendada

```bash
# Clonar como "bare" — el directorio padre limpio
git clone --bare https://github.com/usuario/proyecto.git proyecto.git

# Crear worktrees en directorios paralelos (NUNCA dentro del proyecto)
cd proyecto.git
git worktree add ../worktrees/payments feature/payments
git worktree add ../worktrees/auth-fix fix/auth-bug

# Estructura resultante:
# proyecto.git/          ← repositorio bare (no se trabaja aquí)
# worktrees/
#   payments/            ← rama feature/payments activa
#   auth-fix/            ← rama fix/auth-bug activa

# Listar worktrees activos
git worktree list

# Eliminar worktree cuando termines (SIEMPRE con este comando, no rm -rf)
git worktree remove ../worktrees/payments

# Si ya lo borraste con rm, limpiar los enlaces rotos
git worktree prune
```

### Copiar .env al nuevo worktree (obligatorio)

```bash
# Los .env NO se copian automáticamente al crear un worktree
# Hacerlo manualmente cada vez:
cp proyecto/.env worktrees/payments/.env
cp proyecto/.env worktrees/auth-fix/.env
```

### Claude Code con soporte nativo de worktrees

```bash
# Flag --worktree gestiona creación y limpieza automáticamente
claude code --worktree feature/nueva-funcionalidad
```

### Worktrunk — alternativa ergonómica para repos pesados

```bash
# Para proyectos con node_modules de cientos de MB
# Worktrunk abstrae los worktrees y gestiona dependencias automáticamente
npm install -g worktrunk

worktrunk create feature/nueva-feature
# Instala dependencias automáticamente
# Copia .env y otros archivos ignorados

worktrunk list
worktrunk remove feature/nueva-feature
```

---

## 📌 Notas y Alertas

> [!WARNING]
> Nunca borres un worktree con `rm -rf`. Git mantiene registros internos del worktree que `rm` no limpia, dejando el repo en estado corrupto. Siempre usa `git worktree remove <path>`. Si ya cometiste el error, ejecuta `git worktree prune` para reparar.

> [!IMPORTANT]
> Los archivos `.env` no se copian automáticamente al crear un worktree. Si el agente que corre en el nuevo worktree no tiene acceso a las variables de entorno, fallará de formas no obvias — errores de conexión a base de datos, claves API ausentes. Copiar `.env` manualmente es el primer paso después de `git worktree add`.

> [!NOTE]
> Inforce (BaaS AI-native con MCP integrado) es la opción menos conocida de las cuatro pero la más relevante para proyectos que van a interactuar con modelos de IA desde el inicio. Permite ejecutar instrucciones a nivel de backend en lenguaje natural, lo que elimina una capa completa de traducción entre lo que quieres y lo que se ejecuta.

> [!TIP]
> La estructura bare (`git clone --bare`) es el setup correcto para proyectos serios con múltiples worktrees. El repositorio bare actúa como directorio padre limpio y evita confusión sobre qué directorio tiene qué rama activa. Para un proyecto pequeño o temporal, el worktree simple sin bare es suficiente.

---

## 🔗 Ver también

- [Arquitectura Web: Los 5 Pilares](./arquitectura-web.md) — los conceptos que el backend implementa
- [Frontend con IA: Stitch y Claude Design](./frontend-ia-stitch.md) — la capa que consume las APIs del backend
- [SDD y Harness Engineering](./sdd-harness-engineering.md) — la metodología para construir este backend de forma estructurada
- [Claude Code: Fundamentos](../wiki/claude-code.md) — el agente que ejecuta el deploy y gestiona los worktrees

---

[⬅️ Volver al Índice Principal](../README.md)
