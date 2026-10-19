# ms-usuarios (Gas Mapocho)

Microservicio de autenticación y gestión de usuarios. Versión rápida para presentar en clase (AP1) — usa una base H2 en memoria para no depender de tener PostgreSQL en la nube ya configurado. Cuando se tenga la base definitiva, solo se cambian 4 líneas en `application.properties`.

## Requisitos
- JDK 25 (si da error de versión, revisar `JAVA_HOME`)

## Cómo correrlo
```
./mvnw spring-boot:run
```
Queda escuchando en `http://localhost:8081`.

## Usuarios de prueba (ya sembrados al arrancar)
| email | password | rol |
|---|---|---|
| admin@gasmapocho.cl | Admin123 | ADMIN |
| almacen@gasmapocho.cl | Almacen123 | ALMACEN |

## Endpoints
- `POST /api/auth/login` → `{ "email": "...", "password": "..." }` → devuelve JWT + nombre + rol. 401 si falla.
- `GET /api/usuarios` → lista todos
- `GET /api/usuarios/{id}`
- `POST /api/usuarios` → `{ "nombre", "email", "password", "rol": "ADMIN|ALMACEN" }`
- `PUT /api/usuarios/{id}` → `{ "nombre", "email", "rol" }`
- `DELETE /api/usuarios/{id}`

Probado end-to-end (login válido, login con clave incorrecta → 401, crear, listar, actualizar, eliminar). 5/5 pruebas unitarias de la capa de servicio (JUnit + Mockito) pasando: `./mvnw test`.

## Seguridad (actualizado, igual al patrón de "tema 4" del profesor)
- Login real vía `AuthenticationManager` + `DaoAuthenticationProvider` + `UserDetailsServiceImpl` (no validación manual).
- Filtro `FiltroJwtAuth` (`OncePerRequestFilter`) valida el JWT en cada request y llena el `SecurityContextHolder`.
- `/api/usuarios/**` requiere rol `ADMIN` (`@PreAuthorize`) — ALMACEN recibe 403, igual que indica el cuadro de permisos del informe.
- Probado en vivo: login malo → 401, sin token → 403, token ALMACEN → 403, token ADMIN → 200/404 reales según corresponda.


# Gas Mapocho - Microservicio de Usuarios

Este repositorio contiene el microservicio de usuarios del proyecto **Gas Mapocho**, desarrollado para el curso de Desarrollo de Aplicaciones Web II de Cibertec.

Este servicio se encarga de gestionar los usuarios del sistema, permitir el inicio de sesión y controlar el acceso según el rol de cada usuario.

Para esta primera versión estamos utilizando una base de datos H2 en memoria, lo que nos permite realizar las pruebas sin depender todavía de una base de datos externa.

## Tecnologías utilizadas

- Java 25
- Spring Boot
- Spring Security
- Spring Data JPA
- JWT (JSON Web Token)
- H2 Database
- Maven
- JUnit y Mockito
- Docker

## Funcionalidades

El microservicio permite realizar las siguientes operaciones:

- Inicio de sesión mediante correo y contraseña.
- Generación de tokens JWT para la autenticación.
- Registro de nuevos usuarios.
- Consulta de usuarios registrados.
- Actualización de datos de usuarios.
- Eliminación de usuarios.
- Control de acceso según los roles ADMIN y ALMACEN.

## ¿Cómo funciona?

Cuando un usuario inicia sesión, el sistema verifica sus credenciales y genera un token JWT si son correctas.

Este token se utiliza para identificar al usuario en las siguientes peticiones.

También se valida el rol asignado. Por ejemplo, un usuario con rol ADMIN puede acceder a las operaciones de administración de usuarios, mientras que un usuario con rol ALMACEN no tiene permisos para realizar estas acciones.

La autenticación se realiza utilizando Spring Security.

## Requisitos

Para ejecutar el proyecto necesitamos:

- JDK 25
- Git
- Maven (o utilizar el Maven Wrapper incluido)
- Docker (opcional)

## Instalación

Clonamos el repositorio:

```bash
git clone https://github.com/Sophia-1827/DAW-II_Grupo09_gasmapocho_ms-usuarios.git
```

Ingresamos a la carpeta:

```bash
cd DAW-II_Grupo09_gasmapocho_ms-usuarios
```

Compilamos el proyecto:

```bash
.\mvnw.cmd clean install
```

Iniciamos el microservicio:

```bash
.\mvnw.cmd spring-boot:run
```

Una vez iniciado, el servicio estará disponible en:

```text
http://localhost:8081
```

## Endpoints

Estas son las rutas disponibles para trabajar con el microservicio:

| Método | Endpoint | Descripción |
|---|---|---|
| POST | `/api/auth/login` | Iniciar sesión |
| GET | `/api/usuarios` | Listar usuarios |
| GET | `/api/usuarios/{id}` | Buscar usuario por ID |
| POST | `/api/usuarios` | Registrar usuario |
| PUT | `/api/usuarios/{id}` | Actualizar usuario |
| DELETE | `/api/usuarios/{id}` | Eliminar usuario |

### Ejemplo de inicio de sesión

Petición:

```http
POST http://localhost:8081/api/auth/login
Content-Type: application/json
```

Body:

```json
{
  "email": "admin@gasmapocho.cl",
  "password": "Admin123"
}
```

Si las credenciales son correctas, el servicio devuelve un token JWT junto con los datos del usuario, como su nombre y rol.

Si las credenciales son incorrectas, devuelve un error 401.

## Usuarios de prueba

Para facilitar las pruebas, el proyecto cuenta con dos usuarios registrados automáticamente al iniciar.

| Correo | Contraseña | Rol |
|---|---|---|
| admin@gasmapocho.cl | Admin123 | ADMIN |
| almacen@gasmapocho.cl | Almacen123 | ALMACEN |

Estas credenciales son únicamente para el entorno de pruebas y no deben utilizarse en producción.

## Seguridad

Para la autenticación utilizamos Spring Security junto con JWT.

El proyecto cuenta con un filtro que revisa el token enviado en las peticiones y comprueba si el usuario tiene los permisos necesarios.

Las operaciones de `/api/usuarios/**` están reservadas para el rol ADMIN.

De esta manera, evitamos que los usuarios con otros roles puedan realizar cambios en las cuentas del sistema.

## Base de datos

Actualmente utilizamos **H2**, una base de datos en memoria que nos permite trabajar sin configurar un servidor externo.

Esto es útil durante el desarrollo y las pruebas del proyecto.

Para la versión final tenemos previsto migrar a PostgreSQL, de manera que la información pueda almacenarse de forma permanente.

## Pruebas

Se realizaron pruebas para comprobar el funcionamiento del inicio de sesión y las operaciones de usuarios.

Entre los casos revisados tenemos:

- Inicio de sesión con credenciales correctas.
- Inicio de sesión con contraseña incorrecta.
- Registro y consulta de usuarios.
- Actualización y eliminación de usuarios.
- Acceso a rutas protegidas según el rol.

También contamos con pruebas unitarias utilizando JUnit y Mockito.

Para ejecutarlas:

```bash
.\mvnw.cmd test
```

## Integración con los demás servicios

Este microservicio forma parte del sistema Gas Mapocho y está pensado para trabajar junto con el API Gateway, Eureka Server y Config Server.

Por el momento funciona de manera independiente para facilitar las pruebas.

La integración con Eureka y API Gateway queda pendiente para la versión final del proyecto.

## Próximos cambios

- Migrar la base de datos de H2 a PostgreSQL.
- Registrar el microservicio en Eureka Server.
- Configurar el acceso mediante API Gateway.
- Completar la integración con los demás módulos de Gas Mapocho.

## Integrantes

**Grupo 09 - Cibertec**
