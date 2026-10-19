# Proyecto Semestral: Segunda Entrega - Banco MVP (Monorepo)

Este repositorio contiene la arquitectura integral de **Banco MVP**, estructurada como un **Monorepo con submódulos de Git**, orquestación multi-servicio mediante **Docker Compose**, documentación interactiva con **OpenAPI / Swagger**, imágenes públicas en **DockerHub**, y suites completas de **pruebas unitarias y de extremo a extremo (Playwright)**.

---

## 1. Estructura del Monorepo

Siguiendo las directrices del proyecto, los componentes Frontend y Backend están vinculados como submódulos independientes con sus respectivos Dockerfiles y archivos de ignorados:

```text
your-project/
├── docker-compose.yml          # Orquestador general (Frontend, Backend, PostgreSQL)
├── .gitmodules                 # Definición de submódulos de Git
├── README.md                   # Documentación maestra del proyecto
├── frontend/                   # Submódulo: Aplicación cliente Web (React 18 + Vite)
│   ├── Dockerfile              # Construcción multi-stage en Node y NGINX Alpine
│   ├── .dockerignore           # Exclusiones de contexto Docker
│   ├── nginx.conf              # Configuración del servidor NGINX
│   ├── package.json            # Dependencias y scripts
│   ├── playwright.config.ts    # Configuración de pruebas E2E con Playwright
│   ├── e2e/                    # Pruebas End-to-End críticas
│   ├── tests/                  # Pruebas unitarias de estado y finanzas
│   └── src/                    # Código fuente UI con diseño Glassmorphic
└── backend/                    # Submódulo: Servidor API REST (Express + TypeScript)
    ├── Dockerfile              # Contenedor Node 20 Alpine
    ├── .dockerignore           # Exclusiones de contexto Docker
    ├── package.json            # Dependencias y scripts
    ├── db/                     # Esquemas SQL idempotentes y seed
    ├── tests/                  # Pruebas unitarias de lógica pura y dominio
    └── src/                    # Arquitectura bancaria, rutas y Swagger
```

---

## 2. Aplicaciones Almacenadas y Tecnologías con Versiones

### A. Backend API (`/backend`)
* **Node.js**: `v20.x` LTS (motor de ejecución de JavaScript en servidor)
* **TypeScript**: `5.7.2` (mecanografiado estático para control de tipos)
* **Express.js**: `4.21.2` (framework HTTP minimalista para endpoints REST)
* **PostgreSQL**: `16-alpine` (base de datos relacional para producción y Docker)
* **PGlite**: `0.5.8` (PostgreSQL embebido en WebAssembly para desarrollo local sin infraestructura)
* **pg**: `8.23.0` (cliente oficial de PostgreSQL con pool de conexiones)
* **Swagger UI Express / Swagger JSDoc**: `5.0.1` / `6.3.0` (documentación OpenAPI 3.0)
* **BcryptJS**: `3.0.3` (hashing criptográfico de contraseñas)
* **JSON Web Token (jsonwebtoken)**: `9.0.3` (gestión de sesiones stateless mediante Bearer Tokens)
* **TSX**: `4.19.2` (ejecutor TypeScript y ejecutor de pruebas unitarias nativas `node:test`)

### B. Frontend Web (`/frontend`)
* **React**: `18.3.1` (biblioteca UI para renderizado reactivo por componentes)
* **Vite**: `6.0.3` (empaquetador y servidor de desarrollo HMR ultrarrápido)
* **TypeScript**: `5.7.2` (tipado estático en componentes y servicios)
* **NGINX**: `alpine` (servidor HTTP de grado de producción para servir el bundle compilado)
* **Lucide React**: `0.468.0` (iconografía moderna vectorizada)
* **Playwright**: `1.64.1` (automatización de pruebas de navegador E2E)
* **CSS3 Moderno**: Vanilla CSS con variables nativas, efectos *Glassmorphism*, gradientes y microanimaciones fluidas.

### C. Base de Datos (`database`)
* **PostgreSQL 16 Alpine**: Esquema normalizado con libro mayor (*Double-Entry Ledger*), funciones almacenadas transaccionales, disparadores y control de concurrencia e idempotencia.

---

## 3. Imágenes Públicas en DockerHub

Ambos componentes cuentan con sus imágenes públicas pre-construidas en DockerHub:

| Componente | Repositorio DockerHub | Tag | Comando de Descarga |
| :--- | :--- | :--- | :--- |
| **Backend API** | `juanfer2006/banco-backend` | `latest` | `docker pull juanfer2006/banco-backend:latest` |
| **Frontend Web** | `juanfer2006/banco-frontend` | `latest` | `docker pull juanfer2006/banco-frontend:latest` |

### Enlaces en DockerHub:
* Backend: [https://hub.docker.com/r/juanfer2006/banco-backend](https://hub.docker.com/r/juanfer2006/banco-backend)
* Frontend: [https://hub.docker.com/r/juanfer2006/banco-frontend](https://hub.docker.com/r/juanfer2006/banco-frontend)

---

## 4. Cómo Descargar el Proyecto (Clonación)

Para clonar el Monorepo junto con todos sus submódulos en un solo paso:

```bash
git clone --recurse-submodules <URL_DE_ESTE_REPOSITORIO>
cd <CARPETA_DEL_PROYECTO>
```

Si ya se clonó sin el flag `--recurse-submodules`, inicialice los submódulos ejecutando:
```bash
git submodule update --init --recursive
```

### Repositorios individuales de los submódulos:
* **Backend:** [https://github.com/juanfer2006/banco-backend.git](https://github.com/juanfer2006/banco-backend.git)
* **Frontend:** [https://github.com/juanfer2006/banco-frontend.git](https://github.com/juanfer2006/banco-frontend.git)

---

## 5. Requisitos y Cómo Ejecutar la Aplicación

### Requisitos del Sistema
* **Docker** (v24 o superior) y **Docker Compose** (v2 o superior)
* *Opcional para desarrollo local sin Docker:* Node.js `>= 20.x` y npm `>= 10.x`.

---

### Opción 1: Ejecución Completa con Docker Compose (Recomendada)

Desde la raíz del monorepo, ejecute un único comando para levantar los tres servicios (Base de Datos, Backend API y Frontend Web):

```bash
docker compose up --build
```

Una vez levantados los servicios:
* 🌐 **Frontend Web:** [http://localhost:8080](http://localhost:8080)
* ⚙️ **Backend API (Healthcheck):** [http://localhost:3000/health](http://localhost:3000/health)
* 📖 **Documentación Swagger / OpenAPI:** [http://localhost:3000/api-docs](http://localhost:3000/api-docs)
* 🗄️ **Base de Datos PostgreSQL:** `localhost:5432` (usuario: `postgres`, contraseña: `postgrespassword`, db: `banco_db`)

Para detener los contenedores:
```bash
docker compose down
```

---

### Opción 2: Ejecución Individual en Desarrollo Local (Modo HMR)

#### 1. Iniciar el Backend
```bash
cd backend
npm install
npm run dev
```
*El backend inicia en `http://localhost:3000` utilizando PGlite o la base de datos configurada en `.env`.*

#### 2. Iniciar el Frontend
En otra terminal:
```bash
cd frontend
npm install
npm run dev
```
*El frontend estará disponible en `http://localhost:5173` con recarga en caliente.*

---

## 6. Flujo Completo de Prueba (Comunicación Frontend ↔ Backend ↔ BD)

Para validar la comunicación íntegra entre todos los niveles de la arquitectura, se diseñó el siguiente flujo funcional de extremo a extremo:

1. **Apertura y Registro:** Ingrese a `http://localhost:8080` (o `http://localhost:5173`). Haga clic en **"Regístrate aquí"**.
2. **Creación de Entidades en BD:** Ingrese nombre completo, correo y contraseña. El Frontend enviará un `POST /auth/register` al Backend; el Backend crea el usuario en `auth.users`, genera el perfil en `profiles` y abre automáticamente una cuenta corriente en `accounts` con número único.
3. **Autenticación con JWT:** El Backend responde con un token JWT y datos de sesión que el Frontend almacena de forma reactiva.
4. **Consulta en Tiempo Real (Dashboard):** El Dashboard realiza solicitudes autenticadas (`GET /cuentas`, `GET /tarjetas`, `GET /notificaciones`) calculando el saldo actual a partir de los asientos contables en la base de datos.
5. **Operaciones Financieras:**
   * **Depósito / Retiro:** Ingrese fondos a su cuenta desde el módulo de Depósitos. Se registra una transacción `POSTED` con asientos de partida doble.
   * **Transferencias:** Transfiera dinero a otra cuenta. El sistema valida fondos suficientes e idempotencia en la base de datos.
   * **Cajitas de Ahorro:** Cree una cajita de ahorro con meta financiera y traslade saldo entre su cuenta principal y la cajita.
   * **Simulador de Crédito:** Ingrese monto y plazo para obtener la tabla de amortización francesa y radicar la solicitud bajo el motor de scoring.
6. **Cierre e Inicio de Sesión:** Cierre la sesión con el botón **"Salir"** y vuelva a iniciar con las credenciales registradas para confirmar la persistencia en base de datos.

---

## 7. Historias de Usuario con Responsables

| Código | Historia de Usuario | Criterios de Aceptación y Alcance | Responsable Asignado |
| :--- | :--- | :--- | :--- |
| **HU-01** | Autenticación Segura y Registro de Clientes | Endpoint `/auth/register` y `/auth/login`, cifrado bcrypt, emisión de JWT y formulario web con validaciones. | **Juan Fernando Ramírez (juanfer2006)** |
| **HU-02** | Contabilidad por Partida Doble (*Double-Entry Ledger*) | Esquema de base de datos relacional para cuentas y asientos (`ledger_entries`), con verificación de cuadre a cero. | **Juan Fernando Ramírez (juanfer2006)** |
| **HU-03** | Transferencias Inmediatas e Idempotencia | Transferencia entre cuentas con control de concurrencia, idempotency keys y manejo de errores tipificados. | **Tomás Buriticá (buri033)** |
| **HU-04** | Motor de Créditos y Amortización Francesa | Algoritmo de cuota fija con tabla de amortización decreciente, cálculo de intereses y motor de reglas de riesgo. | **Tomás Buriticá (buri033)** |
| **HU-05** | Documentación de la API con Swagger / OpenAPI | Integración de Swagger UI en `/api-docs` documentando todos los esquemas, parámetros y códigos de respuesta. | **Juan Fernando Ramírez (juanfer2006)** |
| **HU-06** | Contenerización Multi-servicio y Docker Compose | Dockerfiles optimizados para backend y frontend con Alpine Linux, y archivo `docker-compose.yml` orquestador. | **Tomás Buriticá (buri033)** |
| **HU-07** | Dashboard Bancario y Resumen de Cuentas | Visualización de saldo consolidado, métricas financieras, movimientos recientes y estado de productos. | **Juan Fernando Ramírez (juanfer2006)** |
| **HU-08** | Emisión y Gestión de Tarjetas (Algoritmo Luhn) | Generación de números de tarjeta válidos por Luhn, visualización interactiva y opción de congelar/descongelar. | **Tomás Buriticá (buri033)** |
| **HU-09** | Alcancías Virtuales (Cajitas de Ahorro) | Creación de metas de ahorro, seguimiento porcentual y traslado de saldo entre cuenta corriente y cajita. | **Tomás Buriticá (buri033)** |
| **HU-10** | Pruebas Automatizadas Unitarias y E2E Playwright | Suite de 33 pruebas de dominio en backend, 8 pruebas de estado en frontend y 3 suites completas en Playwright. | **Tomás Buriticá (buri033)** & **Juan Fernando Ramírez (juanfer2006)** |

---

## 8. Pruebas Automatizadas

### A. Pruebas Unitarias del Backend (33 pruebas)
Verifican el Value Object `Dinero` (inmutabilidad y rechazo de decimales flotantes), cálculo funcional puro de saldos, fórmulas de amortización francesa, conversión de tasas efectivas, invariantes contables, motor de scoring y algoritmo Luhn.

```bash
cd backend
npm test
```

### B. Pruebas Unitarias del Frontend (8 pruebas)
Verifican el gestor de sesiones en `localStorage`, reactividad ante cambios de sesión, observadores de estado, formateo numérico a pesos colombianos (`COP`) y validación de rangos para solicitudes de crédito.

```bash
cd frontend
npm test
```

### C. Pruebas End-to-End con Playwright (Navegador Real)
Prueban de forma automatizada los flujos críticos en el navegador Chrome contra los servidores en ejecución:
* `e2e/auth.spec.ts`: Flujo completo de registro, dashboard, logout y login con credenciales guardadas.
* `e2e/creditos.spec.ts`: Simulación de cuotas, cálculo dinámico y solicitud de crédito.
* `e2e/navegacion.spec.ts`: Transición fluida entre módulos (dashboard, transferencias, tarjetas, cajitas).

```bash
cd frontend
npm run test:e2e
```

Para ejecutar con interfaz gráfica visual:
```bash
npm run test:e2e:ui
```
