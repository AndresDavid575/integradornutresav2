# 🍲 Sazón — Recetario digital de Grupo Nutresa

> **Ejercicio académico — CESDE.** Proyecto integrador de la media técnica en desarrollo de software.

**Sazón** es un recetario digital: el usuario cuenta qué tiene en su despensa y recibe **recetas con productos Nutresa**, **información nutricional**, un **plan de menús** y su **lista de mercado**.

🔗 **Landing del proyecto:** https://sazon-ten.vercel.app/

---

## 📑 Tabla de contenido

- [Arquitectura](#-arquitectura)
- [Tecnologías](#-tecnologías)
- [Módulos y células](#-módulos-y-células)
- [Componentes evaluados](#-componentes-evaluados)
- [Back II: las 5 capas por tabla](#-back-ii-las-5-capas-por-tabla)
- [Reglas de relaciones](#-reglas-de-relaciones)
- [Convenciones de base de datos](#-convenciones-de-base-de-datos)
- [Estructura del proyecto](#-estructura-del-proyecto)
- [Instalación y ejecución](#-instalación-y-ejecución)
- [Autenticación](#-autenticación)
- [Flujo de trabajo (GitFlow)](#-flujo-de-trabajo-gitflow)
- [Equipo](#-equipo)

---

## 🏗 Arquitectura

```
React (SPA por módulo)
        │  JSON + JWT
        ▼
API REST ── Spring Boot
        │   controller → service → repository
        ▼
Base de datos relacional (35 tablas de negocio)
```

- El módulo **Usuarios y Autenticación (JWT)** protege todo el sistema.
- Cada célula implementa su porción de **front**, **back** y sus **5 tablas**, con el mismo estilo de API REST y autenticación.

---

## 🧰 Tecnologías

| Capa | Tecnología |
|---|---|
| Backend | Java · Spring Boot · Spring Web (REST) · Spring Data JPA |
| Seguridad | Spring Security · JWT · BCrypt |
| Base de datos | Relacional (MySQL / PostgreSQL) |
| Frontend | React · Bootstrap 5.3.3 · Bootstrap Icons 1.11.3 |
| Tipografías | Poppins (titulares) · Inter (cuerpo) · IBM Plex Mono (datos) |
| Gestión | Kaizen (tablero ágil) · Git + GitHub (GitFlow) |

---

## 🧩 Módulos y células

**7 células de 5 integrantes.** Cada célula es dueña de **un módulo de negocio** y de **sus 5 tablas** → **7 × 5 = 35 tablas**.

| Código | Módulo | Prefijo de rama | Tablas |
|---|---|---|---|
| **Módulo 00** | Usuarios, Autenticación y Roles *(docentes)* | `usuarios` | `usuarios`, `roles`, `usuario_rol`, `sesiones`, `auditoria_acceso` *(ejemplo resuelto, no cuenta en las 35)* |
| **Célula 1** | Catálogo de Productos Nutresa | `catalogo` | 5 (las define la célula) |
| **Célula 2** | Recetario y Contenido | `recetario` | 5 (las define la célula) |
| **Célula 3** | Despensa Virtual y Sugerencias | `despensa` | 5 (las define la célula) |
| **Célula 4** | Nutrición y Perfiles Alimentarios | `nutricion` | 5 (las define la célula) |
| **Célula 5** | Planificador de Menús y Lista de Mercado | `planificador` | 5 (las define la célula) |
| **Célula 6** | Comunidad y Valoraciones | `comunidad` | 5 (las define la célula) |
| **Célula 7** | Mercadeo, Campañas y Fidelización | `mercadeo` | 5 (las define la célula) |

---

## 📋 Componentes evaluados

| Componente | Estado | Qué se entrega |
|---|---|---|
| **Back I** | ✅ Cerrado | Cada integrante modeló **una clase** (5 a 7 atributos) con su **interfaz de validaciones** y su **interfaz CRUD**. |
| **Back II** | 🚧 En curso | Cada integrante lleva **su tabla** por las **5 capas de Spring Boot**. |
| **Front I** | ⏳ Pendiente | SPA en React del módulo consumiendo la API. |
| **Front II** | ⏳ Pendiente | Evolución de la SPA del módulo. |
| **Agilismo** | 🔄 Transversal | Sprints en el tablero Kaizen y GitFlow con PR obligatorio. |

---

## 🧱 Back II: las 5 capas por tabla

Cada integrante implementa, para **su** tabla:

| # | Capa | Qué incluye |
|---|---|---|
| 1 | **Entidad JPA** | Clase con `@Entity`, `@Table`, `@Id` y las relaciones permitidas. |
| 2 | **Repositorio** | Interfaz que extiende `JpaRepository` con **3 consultas** propias (derived queries o `@Query`). |
| 3 | **Validaciones** | **3 validaciones** de negocio (Bean Validation y/o reglas en el servicio). |
| 4 | **Servicio** | Lógica de negocio y CRUD (`@Service`). |
| 5 | **Controlador REST** | Endpoints (`@RestController`) para listar, buscar, crear, actualizar y eliminar. |

### Ejemplo de endpoints por tabla

```http
GET    /api/{modulo}/{tabla}          # listar
GET    /api/{modulo}/{tabla}/{id}     # buscar por id
POST   /api/{modulo}/{tabla}          # crear
PUT    /api/{modulo}/{tabla}/{id}     # actualizar
DELETE /api/{modulo}/{tabla}/{id}     # eliminar
```

---

## 🔗 Reglas de relaciones

- Solo relaciones **1 a muchos**: `@OneToMany` / `@ManyToOne`.
- Solo **entre compañeros del mismo grupo** (tablas del mismo módulo).
- Lo que pertenece a **otros grupos** o al módulo de **usuarios** se guarda como **columna UUID** `idX` (sin relación JPA).

```java
// Relación dentro del mismo módulo
@ManyToOne
@JoinColumn(name = "receta_id")
private Receta receta;

// Referencia a otro módulo o a usuarios → solo UUID
@Column(name = "id_usuario", nullable = false)
private UUID idUsuario;
```

---

## 🗄 Convenciones de base de datos

- Nombres de tabla en **minúscula**, **sin tildes** y en **plural** para entidades.
- **Guion bajo** en tablas puente (ej. `usuario_rol`).
- Cada tabla tiene su **llave primaria propia**.
- Toda referencia a una persona apunta a **`usuarios`**.
- Las **migraciones** las hace la célula dueña de la tabla; las demás la consumen **por API**.

---

## 📁 Estructura del proyecto

```
sazon-backend/
├── src/main/java/com/cesde/sazon/
│   ├── SazonApplication.java
│   ├── config/                  # Seguridad, CORS, JWT
│   ├── usuarios/                # Módulo 00 (docentes)
│   ├── catalogo/                # Célula 1
│   ├── recetario/               # Célula 2
│   ├── despensa/                # Célula 3
│   ├── nutricion/               # Célula 4
│   ├── planificador/            # Célula 5
│   ├── comunidad/               # Célula 6
│   └── mercadeo/                # Célula 7
│       ├── entity/
│       ├── repository/
│       ├── validation/
│       ├── service/
│       └── controller/
├── src/main/resources/
│   └── application.properties
└── pom.xml
```

---

## 🚀 Instalación y ejecución

### Requisitos

- **JDK 17** o superior
- **Maven 3.9+** (o el wrapper `./mvnw` incluido)
- Motor de base de datos relacional (MySQL o PostgreSQL)
- Git

### Pasos

```bash
# 1. Clonar el repositorio
git clone https://github.com/<organizacion>/sazon-backend.git
cd sazon-backend

# 2. Cambiar a la rama de integración
git checkout develop

# 3. Configurar la base de datos en src/main/resources/application.properties

# 4. Ejecutar
./mvnw spring-boot:run
```

### `application.properties` de ejemplo

```properties
spring.application.name=sazon
server.port=8080

spring.datasource.url=jdbc:mysql://localhost:3306/sazon
spring.datasource.username=root
spring.datasource.password=TU_PASSWORD

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

jwt.secret=CAMBIAR_POR_UNA_CLAVE_SEGURA
jwt.expiration=86400000
```

> ⚠️ No subas contraseñas ni el `jwt.secret` real al repositorio. Usa variables de entorno.

La API queda disponible en `http://localhost:8080`.

---

## 🔐 Autenticación

El módulo 00 expone los endpoints base:

| Método | Endpoint | Descripción |
|---|---|---|
| `POST` | `/auth/register` | Registro de usuario |
| `POST` | `/auth/login` | Inicio de sesión, devuelve el JWT |
| `GET` | `/usuarios/perfil` | Perfil del usuario autenticado |

Para consumir los demás endpoints, envía el token en el encabezado:

```http
Authorization: Bearer <token>
```

---

## 🌿 Flujo de trabajo (GitFlow)

| Rama | Propósito | Sale de |
|---|---|---|
| `main` | Producción | — |
| `develop` | Integración | `main` |
| `feature/<modulo>/<codigo-HU>` | Desarrollo de una historia de usuario | `develop` |
| `release/<version>` | Preparación de entrega | `develop` |
| `hotfix/<descripcion>` | Corrección urgente | `main` |

**Ejemplo:** `feature/despensa/HU-03`

### Reglas

- 🚫 Sin push directo a `main` ni a `develop`.
- ✅ Todo entra por **Pull Request a `develop`**, revisado por otro compañero.
- 🔄 Sincroniza `develop` con frecuencia.
- 📝 Commits con **Conventional Commits**:

```bash
git commit -m "feat(despensa): agrega entidad IngredienteDespensa"
git commit -m "fix(recetario): corrige validación de tiempo de preparación"
git commit -m "docs(catalogo): actualiza README del módulo"
```

---

## 👥 Equipo

| Célula | Módulo | Integrantes |
|---|---|---|
| Docentes | Usuarios, Autenticación y Roles | Juan José Gallego Mesa, M.Sc. |
| Célula 1 | Catálogo de Productos Nutresa | _Por definir_ |
| Célula 2 | Recetario y Contenido | _Por definir_ |
| Célula 3 | Despensa Virtual y Sugerencias | _Por definir_ |
| Célula 4 | Nutrición y Perfiles Alimentarios | _Por definir_ |
| Célula 5 | Planificador de Menús y Lista de Mercado | _Por definir_ |
| Célula 6 | Comunidad y Valoraciones | _Por definir_ |
| Célula 7 | Mercadeo, Campañas y Fidelización | _Por definir_ |

---

<p align="center">
  Proyecto académico · <strong>CESDE</strong> · Medellín, Colombia<br>
  <em>Sazón no es un producto oficial de Grupo Nutresa.</em>
</p>
