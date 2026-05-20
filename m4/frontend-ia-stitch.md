# [L23] Frontend con IA: Stitch, Claude Design y Playwright

> **Módulo:** M4 — Creación de Productos, SDD y Monetización  
> **Lección:** L23  
> **Tags:** `frontend` `diseño` `stitch` `claude-design` `playwright` `testing` `ui`

---

## 🧠 Core Insights

El bottleneck del frontend — necesitar saber HTML, CSS y diseño visual — desaparece con dos herramientas que operan de formas complementarias. La clave es entender que generan cosas distintas y elegir la correcta según el momento del proyecto.

**Stitch (Google Labs):** Herramienta gratuita y experimental que usa Gemini para generar el diseño visual completo desde lenguaje natural. Su diferenciador real no es la descripción textual sino la **inspiración visual**: subes capturas de pantalla de páginas que te gustan y Stitch genera algo coherente con esa referencia. El output es un archivo `design.md` en Markdown — los planos del arquitecto, no el edificio. No es código funcional; es el diseño que luego entregas a Claude Code.

**Claude Design (Anthropic):** Genera código directamente (componentes JSX/React) en lugar de Markdown intermedio. Su ventaja real es el **Sistema de Diseño persistente**: inyectas colores de marca, logotipo, archivos Figma, tono de comunicación, y Claude Design mantiene esa coherencia entre sesiones. Para proyectos con marca establecida, la consistencia es significativamente mejor que empezar sin ese contexto.

La distinción más importante: Stitch es para exploración cuando el diseño no está definido; Claude Design es para generación final cuando sabes exactamente qué quieres y tienes marca establecida.

**El comportamiento proactivo de Claude Design** vale la pena notar: al pedirle una landing para una clínica dental, generó textos persuasivos, un temporizador de cuenta atrás para urgencia, y añadió una sección de fotos antes/después **sin que nadie se la pidiera**. La IA infirió que era necesaria para ese tipo de campaña. Eso no es autocomplete — es razonamiento sobre contexto.

**Playwright** cierra el ciclo: automatiza la verificación de que el frontend construido funciona como se describió. El MCP de Playwright conecta al agente directamente con el navegador, permitiendo navegar páginas en tiempo real, leer el DOM, interactuar con elementos y generar reportes de auditoría visual completos — incluyendo capturas de pantalla de cada página y detección de regresiones.

---

## ⚙️ Implementación Práctica

### Flujo Stitch → Claude Code → Producción

```
1. Stitch: Subir 2-3 capturas de inspiración visual
   → describir la página en lenguaje natural
   → exportar como design.md

2. Claude Code: "Construye el frontend según este diseño"
   → adjuntar design.md
   → el agente genera HTML/CSS/JS o React components

3. GitHub: commit del código generado

4. Vercel: deploy automático desde el repo de GitHub
   → preview URL en segundos
   → producción en minutos
```

### Prompt para extraer máximo valor de Stitch

```
Tengo tres páginas de referencia visual [adjuntar capturas].
Quiero una landing page para [descripción del producto].
Estilo: [describir el tono visual que ves en las referencias].
Secciones: hero, beneficios, testimonios, CTA, footer.
Genera el diseño completo.
```

### Configurar Sistema de Diseño en Claude Design

```
Antes de generar cualquier componente, proporciona:
- Colores primarios y secundarios de la marca: #XXXXXX
- Tipografías: headings [fuente], body [fuente]
- Logo: [adjuntar archivo]
- Tono de comunicación: [profesional/casual/urgente/cálido]
- Ejemplos de copy previo: [adjuntar 2-3 textos existentes]
```

### Playwright — auditoría visual autónoma

```bash
# Instalar Playwright
npm install -D @playwright/test
npx playwright install

# El agente puede escribir y ejecutar tests como este:
# test('landing page carga correctamente', async ({ page }) => {
#   await page.goto('http://localhost:3000');
#   await page.screenshot({ path: 'screenshots/landing.png' });
#   await expect(page.locator('h1')).toBeVisible();
#   await expect(page.locator('[data-testid="cta-button"]')).toBeEnabled();
# });
```

### Prompt para auditoría visual completa con Playwright MCP

```
Navega toda la aplicación en http://localhost:3000.
Para cada página:
1. Toma una captura de pantalla
2. Verifica que los elementos principales son visibles
3. Prueba el flujo principal (registro, login, acción core)
4. Reporta qué funciona y qué no

Genera un reporte markdown con screenshots embebidos.
```

---

## 📌 Notas y Alertas

> 🔴 **Importante:** Stitch genera diseño, **no código funcional**. Su output es un `design.md` que describes la interfaz visualmente. Pasar ese archivo a Claude Code es el paso que convierte planos en software. Saltarse ese paso y entregar el Markdown directo al usuario es un error común.

> 📌 **Nota:** Stitch es experimental y gratuita de Google Labs — esto significa que puede cambiar o desaparecer. Claude Design es el producto oficial de Anthropic con más estabilidad a largo plazo, aunque las preferencias de uso en el flujo de trabajo permanecen.

> 💡 **Tip:** El Live Mode de Stitch permite dictar cambios por voz. Para iteraciones rápidas de diseño donde quieres explorar variaciones sin escribir, dictar "hazlo más minimalista, elimina el sidebar, pon el CTA más grande" es significativamente más rápido que redactar el prompt.

> ⚠️ **Advertencia:** El MCP de Playwright requiere que el servidor de desarrollo esté corriendo en local antes de que el agente pueda navegar. Si el agente reporta que no puede conectarse, verifica primero que `localhost:3000` (o el puerto configurado) está activo.

---

## 🔗 Ver también

- [Arquitectura Web: Los 5 Pilares](./arquitectura-web.md) — el contexto de dónde encaja el frontend en el stack completo
- [Backend Automático y Git Worktrees](./backend-git-worktrees.md) — la capa que el frontend consulta
- [Claude Code: Fundamentos](../wiki/claude-code.md) — el agente que convierte el design.md en código real

---

[⬅️ Volver al Índice Principal](../README.md)
