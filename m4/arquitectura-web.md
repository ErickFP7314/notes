# [L22] Arquitectura Web: Los 5 Pilares

> **Módulo:** M4 — Creación de Productos, SDD y Monetización  
> **Lección:** L22  
> **Tags:** `arquitectura` `backend` `frontend` `bases-de-datos` `apis` `autenticación` `deploy`

---

## 🧠 Core Insights

Toda aplicación web descansa sobre el mismo modelo: un **cliente** (navegador) que hace peticiones y un **servidor** que las procesa. El frontend es lo que el usuario ve; el backend es la lógica que decide qué puede hacer ese usuario; la base de datos es la memoria persistente del sistema; y Stripe es el componente de monetización que nadie construye desde cero.

Como creador que dirige agentes, no necesitas escribir cada capa. Necesitas entenderlas para **detectar errores del agente antes de que lleguen a producción** y tomar las decisiones de arquitectura que el agente no puede tomar por ti.

**Pilar 1 — Base de datos:** La decisión más cara de cambiar después. El modelo relacional (PostgreSQL) organiza datos en tablas relacionadas por claves foráneas. Cada modelo del producto — usuarios, pedidos, transacciones — necesita diseño explícito de campos, relaciones y queries antes de que el agente escriba una sola línea.

**Pilar 2 — APIs:** La interfaz entre capas. HTTP define los métodos (GET/POST/PUT/DELETE); los endpoints son las rutas; los códigos de estado comunican el resultado (200 éxito, 401 sin autorización, 404 no encontrado, 500 error del servidor). REST define la estructura predecible de esas rutas. Cuando el agente construye el backend, está construyendo estos endpoints — saber leerlos es la auditoría mínima.

**Pilar 3 — Autenticación y autorización:** Conceptos distintos con consecuencias distintas. Autenticación = ¿eres quien dices ser? (JWT, sesiones). Autorización = ¿tienes permiso para hacer esto? (roles). La vulnerabilidad más común en apps construidas con IA es la ausencia o mal diseño de este pilar.

**Pilar 4 — Arquitectura de código:** El principio de separación de responsabilidades dice que cada módulo tiene una sola razón para cambiar. MVC (Modelo-Vista-Controlador) organiza el código en tres capas independientes. Sin este principio, el agente puede romper funcionalidad existente al agregar algo nuevo.

**Pilar 5 — Deploy y entornos:** El código local no es producción. Las variables de entorno mantienen fuera del código las configuraciones sensibles (claves API, URLs de base de datos). Docker garantiza que la app funcione igual en cualquier servidor. El vocabulario básico de Linux (filesystem, procesos, logs) es el mínimo para entender qué pasa en el servidor.

---

## ⚙️ Implementación Práctica

### Modelo relacional — diseño mínimo por entidad

```sql
-- Antes de pedir al agente que construya, define esto por cada modelo:
-- ¿Qué campos necesita? ¿Qué relaciones tiene? ¿Qué queries harás?

-- Ejemplo: tabla de usuarios
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  role VARCHAR(50) DEFAULT 'user',
  created_at TIMESTAMP DEFAULT NOW()
);

-- Relación: pedidos vinculados a usuarios
CREATE TABLE orders (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id),
  status VARCHAR(50) DEFAULT 'pending',
  total DECIMAL(10,2) NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);
```

### Anatomy de un endpoint REST

```
GET    /api/users          → listar usuarios
GET    /api/users/:id      → obtener usuario por ID
POST   /api/users          → crear usuario
PUT    /api/users/:id      → actualizar usuario
DELETE /api/users/:id      → eliminar usuario
```

### JWT — flujo básico de autenticación

```
1. Usuario hace POST /auth/login con email + password
2. Servidor verifica credenciales
3. Servidor devuelve JWT firmado (expira en X horas)
4. Frontend guarda el JWT (localStorage o cookie httpOnly)
5. Cada petición siguiente incluye: Authorization: Bearer <token>
6. Servidor verifica firma del token antes de procesar
```

### Variables de entorno — estructura mínima

```bash
# .env.example — commiteado al repo (sin valores reales)
DATABASE_URL=postgresql://user:password@host:5432/dbname
STRIPE_SECRET_KEY=sk_...
JWT_SECRET=...
NODE_ENV=development

# .env — NUNCA en el repo (.gitignore)
```

### Docker — flujo de deploy básico

```bash
# Construir imagen
docker build -t mi-producto .

# Correr en producción con env
docker run -d --env-file .env -p 3000:3000 mi-producto

# Ver logs en tiempo real
docker logs -f <container_id>
```

---

## 📌 Notas y Alertas

> [!WARNING]
> El diseño de base de datos es la decisión más costosa de cambiar después del lanzamiento. Una tabla mal diseñada puede generar meses de deuda técnica. Deja que el agente te ayude a pensar el schema, pero aprueba tú el modelo de datos antes de que empiece a construir.

> [!IMPORTANT]
> La vulnerabilidad más común en apps construidas rápidamente con IA es la falla en autenticación/autorización. Sin la verificación de roles correcta, cualquier usuario puede leer datos de cualquier otro. Pide al agente que incluya tests de autorización explícitos, no solo tests de funcionalidad.

> [!NOTE]
> Las variables de entorno nunca deben vivir dentro del código. Cuando el agente escribe código que incluye claves API literales ("sk_live_..."), eso es una vulnerabilidad activa. Revisa siempre que las credenciales vengan de `process.env` o equivalente.

> [!TIP]
> Antes de pedir al agente que construya cualquier cosa, dale el diseño de base de datos aprobado y una lista de los endpoints que necesitas. Con esa información puede construir de forma coherente en lugar de inventar la arquitectura en el proceso.

---

## 🔗 Ver también

- [Frontend con IA: Stitch y Claude Design](./frontend-ia-stitch.md) — construcción de la capa de interfaz sobre esta arquitectura
- [Backend Automático y Git Worktrees](./backend-git-worktrees.md) — las 4 formas de implementar el backend descrito aquí
- [Pagos, Deploy y el Desarrollador Potenciado](./pagos-deploy-reflexion.md) — Stripe + deploy completo del producto

---

[⬅️ Volver al Índice Principal](../README.md)
