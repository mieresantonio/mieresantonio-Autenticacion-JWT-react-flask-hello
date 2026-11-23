# Autenticación JWT con React + Flask

Aplicación full-stack con registro, inicio de sesión y una zona privada protegida por token JWT. El frontend en React consume una API en Flask: el usuario se registra, inicia sesión, recibe un token y solo con ese token puede entrar a la página privada.

Ejercicio del bootcamp Full Stack de 4Geeks Academy, completado en septiembre de 2026. La autenticación JWT que se practica aquí es la misma competencia que usa el proyecto final [Block Ethos](https://github.com/mieresantonio/block-ethos).

## Qué hace

- **Registro** (`/signup`): crea un usuario con correo y contraseña. Rechaza correos ya registrados y peticiones sin JSON.
- **Inicio de sesión** (`/login`): valida las credenciales y devuelve un token de acceso JWT, que el frontend guarda en `sessionStorage`.
- **Zona privada** (`/private`): solo accesible con un token válido. Sin token, con token inválido o expirado, redirige al login.
- **Contraseñas con hash**: nunca se guardan en texto plano; se usa `werkzeug.security` (`generate_password_hash` / `check_password_hash`) y el serializador del usuario excluye la contraseña.

## Endpoints de la API

| Método | Ruta | Descripción |
|--------|------|-------------|
| `POST` | `/api/signup` | Registra un usuario (`email`, `password`). Devuelve 201 o 400 si el correo ya existe. |
| `POST` | `/api/token` | Inicia sesión y devuelve `{ token, user }`. Devuelve 401 si las credenciales son incorrectas. |
| `GET` | `/api/private` | Ruta protegida con `@jwt_required()`. Requiere `Authorization: Bearer <token>`. |
| `GET`/`POST` | `/api/hello` | Endpoint de ejemplo de la plantilla. |

## Stack

| Capa | Tecnología |
|------|-----------|
| Frontend | React 18.2, React Router 6, Vite 4, Bootstrap |
| Backend | Python 3.13, Flask, Flask-SQLAlchemy, Flask-JWT-Extended 4.6.0, Flask-Migrate, Flask-CORS |
| Base de datos | SQLite por defecto (sin `DATABASE_URL` usa `/tmp/test.db`); Postgres con `DATABASE_URL` |
| Herramientas | Pipenv (backend), npm (frontend), Node ≥ 20 |

## Cómo correrlo

### 1. Backend (puerto 3001)

```bash
pipenv install
cp .env.example .env
pipenv run migrate   # solo si cambiaste los modelos en src/api/models.py
pipenv run upgrade
pipenv run start
```

La API queda en `http://localhost:3001`, con el sitemap de endpoints en `/`.

### 2. Frontend

```bash
npm install
npm run start
```

> ⚠️ Antes de arrancar el frontend, revisa la variable `VITE_BACKEND_URL` en tu `.env`: el valor de ejemplo apunta a una URL de Codespaces que ya no existe. En local debe ser `http://localhost:3001`.

### Usuarios de prueba

```bash
flask insert-test-users 5
```

Crea usuarios `test_user1@test.com` … `test_user5@test.com`.

## Estructura

```
src/
├── api/
│   ├── routes.py     # Endpoints /signup, /token, /private, /hello
│   ├── models.py     # Modelo User con hash de contraseña
│   ├── app.py        # Configuración de Flask + JWT + CORS
│   └── commands.py   # Comando CLI insert-test-users
└── front/
    ├── pages/
    │   ├── Signup.jsx  # Formulario de registro
    │   ├── Login.jsx   # Formulario de login, guarda el token
    │   └── Private.jsx # Valida el token contra /api/private
    ├── routes.jsx      # Rutas /, /signup, /login, /private
    └── store.js        # Store de la plantilla
```

## Qué demuestra

- Autenticación completa con JWT: emisión del token en el login, envío como `Bearer` y validación en rutas protegidas.
- Hash de contraseñas y serialización segura del usuario.
- Manejo de errores con códigos HTTP correctos (400, 401, 404) y mensajes en español en el frontend.
- Separación frontend/backend con variables de entorno y CORS.

## Notas

- Las dependencias del backend se instalan con **Pipenv** (`Pipfile`); el `requirements.txt` del repo es de una versión anterior de la plantilla y está desactualizado.
- Construido sobre la plantilla `react-flask-hello` de 4Geeks Academy. El trabajo propio es la autenticación completa (backend + las tres páginas) en un solo commit, el 24 de septiembre de 2026.
