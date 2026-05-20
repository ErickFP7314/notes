# [L29] Pagos, Deploy y el Desarrollador Potenciado

> **Módulo:** M4 — Creación de Productos, SDD y Monetización  
> **Lección:** L29  
> **Tags:** `pagos` `stripe` `deploy` `latam` `monetización` `modelos-de-negocio` `reflexión`

---

## 🧠 Core Insights

Construir el producto es la mitad del trabajo. La otra mitad es que alguien pague por él, y que el producto esté disponible cuando ese alguien quiera usarlo. Esta lección cierra el ciclo técnico-económico.

**Stripe** es el estándar de pagos para productos de software modernos, no porque sea la única opción, sino porque resuelve el problema completo en una sola integración: tarjetas de crédito/débito, suscripciones recurrentes, facturas automáticas, reembolsos, gestión de impuestos en múltiples jurisdicciones, y webhooks para cada evento relevante.

El principio de seguridad más importante de Stripe: el usuario completa el pago en la página de Stripe, nunca en tu servidor. Esto elimina completamente la responsabilidad de manejar datos de tarjetas — cumplimiento PCI sin el costo de implementarlo.

**La ruta LatAm** es la fricción real que no aparece en los tutoriales de Stripe. En países con restricciones cambiarias (Argentina, Bolivia), la ruta más usada es la plataforma puente:
- **Airtm:** billetera digital que recibe en USD desde Stripe y permite retiro local
- **Takenos:** integración específica para Argentina, mantiene dólares dentro de la plataforma hasta conversión a pesos a tasa de mercado
- **Uruguay:** tiene acceso completo a Stripe — registrar empresa ahí es la ruta más limpia para operar desde países sin acceso directo

**Deploy asistido:** Railway con los Skills del agente elimina la mayor parte de la complejidad de infraestructura. Para control total: VPS Ubuntu donde el agente se instala directamente, accede al repo vía GitHub CLI, crea los contenedores Docker, corre las migraciones, y usa PM2 para mantener procesos activos. Con Vercel para el frontend y Railway o VPS para el backend, el stack completo puede estar en producción en horas.

**Los 5 modelos de negocio que habilita este stack:**
1. **Agencia de servicios** — la monetización más rápida; construir para clientes con márgenes que el tiempo de implementación lineal no permitía
2. **SaaS** — producto de nicho en suscripción mensual; un MCP que nadie construyó, una automatización especializada en una industria
3. **E-commerce** — venta de productos físicos o digitales; storefront + inventario + pagos construibles con lo aprendido
4. **Infoproductos** — cursos, ebooks, comunidades; el tiempo de producción cae cuando el agente ayuda con investigación, organización y materiales
5. **Afiliados** — monetizar sin construir un producto; audiencia que dirige tráfico a productos de terceros por comisión

**La reflexión que cierra el módulo:** la IA no reemplaza al ingeniero de software. Lo que se commoditiza es la implementación — escribir código línea por línea, traducir requerimientos a sintaxis, generar tests. Lo que no se commoditiza es el criterio: saber qué construir, para quién, por qué, con qué arquitectura, con qué trade-offs. El developer que entiende este cambio no compite con la IA — orquesta el sistema de agentes que construye lo que sus usuarios necesitan.

---

## ⚙️ Implementación Práctica

### Integración Stripe — flujo técnico completo

```bash
# 1. Crear cuenta en Stripe → obtener claves API
# Clave de prueba:     sk_test_...
# Clave de producción: sk_live_...

# 2. Instalar SDK (ejemplo en Node.js)
npm install stripe

# 3. Backend: crear sesión de checkout
```

```javascript
const stripe = require('stripe')(process.env.STRIPE_SECRET_KEY);

// Crear sesión de pago
const session = await stripe.checkout.sessions.create({
  payment_method_types: ['card', 'paypal'], // PayPal nativo
  line_items: [{
    price: 'price_1234...',  // ID del precio en Stripe
    quantity: 1,
  }],
  mode: 'subscription',  // o 'payment' para pagos únicos
  success_url: 'https://tuproducto.com/success',
  cancel_url: 'https://tuproducto.com/cancel',
});

// Redirigir al usuario
res.redirect(session.url);
```

```javascript
// 4. Backend: escuchar webhooks de Stripe
app.post('/webhook', express.raw({type: 'application/json'}), (req, res) => {
  const event = stripe.webhooks.constructEvent(
    req.body,
    req.headers['stripe-signature'],
    process.env.STRIPE_WEBHOOK_SECRET
  );

  switch (event.type) {
    case 'checkout.session.completed':
      // Activar cuenta del usuario
      break;
    case 'customer.subscription.deleted':
      // Desactivar suscripción
      break;
    case 'invoice.payment_failed':
      // Notificar al usuario
      break;
  }

  res.json({received: true});
});
```

### Variables de entorno para Stripe

```bash
# .env (nunca en el repositorio)
STRIPE_SECRET_KEY=sk_live_...
STRIPE_WEBHOOK_SECRET=whsec_...
STRIPE_PUBLISHABLE_KEY=pk_live_...
```

### Deploy en Railway con Skills

```bash
# Instalar Skills de Railway
npx skills

# En el proyecto, activar Railway
/use railway

# Prompt al agente:
"Despliega este proyecto en Railway.
Configura la base de datos PostgreSQL que necesita,
establece las variables de entorno desde el .env.example,
y verifica que el deploy funciona correctamente.
Si algo falla, lee los logs y corrige."
```

### Deploy en VPS — flujo con Claude Code en servidor

```bash
# 1. Conectar al servidor Ubuntu via SSH
ssh root@tu-servidor-ip

# 2. Instalar Docker y Claude Code en el servidor
# (proceso de instalación estándar)

# 3. Darle al agente acceso al repo
gh auth login
gh repo clone usuario/proyecto

# 4. Prompt al agente (con bypass permissions):
"Despliega este proyecto:
- Crea los contenedores Docker con la configuración correcta
- Configura PostgreSQL con las variables del .env
- Corre las migraciones de base de datos
- Usa PM2 para mantener los procesos activos
- Verifica que la aplicación responde en el puerto 3000"
```

### Ruta LatAm — pasos prácticos

```
Bolivia, Venezuela, países con restricciones:
1. Registrar cuenta en Airtm (airtm.com)
2. Activar Stripe en el producto (cliente paga con tarjeta)
3. Los fondos se acreditan en USD en Airtm
4. Desde Airtm: transferir a cuenta bancaria local o retiro en efectivo

Argentina específicamente:
Opción A: Takenos (takenos.com)
  - Recibir en USD, mantener en USD, convertir cuando quieras

Opción B: registrar empresa en Uruguay
  - Stripe disponible directamente
  - Operar desde Uruguay, base en Argentina
  - Ruta más limpia para montos grandes y largo plazo
```

---

## 📌 Notas y Alertas

> 🔴 **Importante:** El usuario nunca debe completar el pago en tu servidor. Stripe redirige a su propio dominio para el checkout. Este diseño elimina completamente la responsabilidad de manejar datos de tarjetas (cumplimiento PCI) sin que tengas que implementar ninguna seguridad adicional para eso. Construir tu propio formulario de tarjetas es el error más común y costoso.

> ⚠️ **Advertencia:** Las variables de entorno de Stripe (especialmente `STRIPE_SECRET_KEY` en producción `sk_live_...`) nunca deben aparecer en el código ni en el repositorio. Si el agente las escribe hardcodeadas, ese es un incidente de seguridad activo. Verifica en el primer deploy que todas las claves vienen de variables de entorno.

> 📌 **Nota:** La ruta Airtm/Takenos no es un workaround temporal — es la infraestructura financiera real que usan decenas de creadores en LatAm con restricciones cambiarias. La tasa de conversión y las comisiones varían; compara ambas plataformas antes de comprometerte con una para un volumen significativo.

> 💡 **Tip:** Los webhooks de Stripe son más confiables que verificar el estado del pago solo en el redirect de éxito. El redirect puede fallar (usuario cierra el navegador, se pierde la conexión). El webhook llega independientemente de lo que haga el usuario después del pago. Diseña toda la lógica post-pago en el handler del webhook, no en la página de éxito.

---

## 🔗 Ver también

- [Arquitectura Web: Los 5 Pilares](./01-arquitectura-web.md) — el backend donde Stripe se integra (Pilar 2: APIs)
- [Startup Completa con n8n y Graphify](./06-startup-n8n-graphify.md) — el producto completo que este módulo monetiza

---

[⬅️ Volver al Índice Principal](../README.md)
