# Google Calendar OAuth para AsisteClick

Integración de **Google Calendar API** mediante **OAuth 2.0** para AsisteClick.

Este proyecto documenta paso a paso la implementación de la autenticación OAuth con Google, el intercambio de tokens y el consumo de la API de Google Calendar para permitir la gestión de agendas desde AsisteClick.

---

# Objetivos

- Implementar OAuth 2.0 con Google.
- Obtener autorización del usuario de forma segura.
- Administrar Access Token y Refresh Token.
- Consumir la Google Calendar API.
- Crear una base reutilizable para futuras integraciones de AsisteClick.

---

# Características

- ✅ Autenticación OAuth 2.0
- ✅ Obtención de Access Token
- ✅ Obtención de Refresh Token
- ✅ Renovación automática del Access Token
- ✅ Consulta de calendarios
- ✅ Consulta de eventos
- ✅ Creación de eventos
- ✅ Actualización de eventos
- ✅ Eliminación de eventos

---

# Arquitectura

```text
┌──────────────┐
│ AsisteClick  │
└──────┬───────┘
       │
       │ OAuth
       ▼
┌────────────────────────┐
│ Google Authorization   │
└──────────┬─────────────┘
           │
           │ Authorization Code
           ▼
┌────────────────────────┐
│ OAuth Token Endpoint   │
└──────────┬─────────────┘
           │
           │
           ▼
 Access Token
 Refresh Token
           │
           ▼
┌────────────────────────┐
│ Google Calendar API    │
└────────────────────────┘
```

---

# Flujo de autenticación

```text
Usuario
   │
   ▼
Google Login
   │
   ▼
Consentimiento
   │
   ▼
Authorization Code
   │
   ▼
Token Endpoint
   │
   ├── Access Token
   └── Refresh Token
           │
           ▼
Google Calendar API
```

---

# Estructura del repositorio

```text
google-calendar-oauth/
│
├── docs/
│   ├── 01-google-cloud.md
│   ├── 02-oauth.md
│   ├── 03-tokens.md
│   ├── 04-calendar-api.md
│   ├── 05-eventos.md
│   └── ejemplos/
│
├── ejemplos/
│   ├── curl/
│   ├── node/
│   ├── php/
│   └── postman/
│
├── images/
│
├── README.md
└── LICENSE
```

---

# Documentación

| Documento | Descripción |
|------------|-------------|
| 01-google-cloud | Configuración del proyecto en Google Cloud |
| 02-oauth | Implementación de OAuth 2.0 |
| 03-tokens | Administración de Access Token y Refresh Token |
| 04-calendar-api | Consumo de Google Calendar API |
| 05-eventos | Operaciones CRUD sobre eventos |

---

# Flujo implementado

- Crear proyecto en Google Cloud.
- Habilitar Google Calendar API.
- Configurar pantalla de consentimiento.
- Crear credenciales OAuth.
- Solicitar autorización al usuario.
- Obtener Authorization Code.
- Intercambiar Authorization Code por Access Token y Refresh Token.
- Guardar el Refresh Token.
- Consumir Google Calendar API.
- Renovar automáticamente el Access Token cuando expire.

---

# Operaciones soportadas

## Calendarios

- Obtener calendarios disponibles.
- Obtener calendario principal.
- Consultar permisos del usuario.

## Eventos

- Listar eventos.
- Crear eventos.
- Actualizar eventos.
- Eliminar eventos.
- Buscar disponibilidad.

---

# Tecnologías

- Google Cloud Platform
- Google OAuth 2.0
- Google Calendar API
- HTTP REST
- JSON
- OAuth 2.0 Authorization Code Flow

---

# Consideraciones importantes

## Authorization Code

- Solo puede utilizarse una vez.
- Expira en pocos minutos.

---

## Access Token

- Duración aproximada de una hora.
- Debe enviarse en el header:

```http
Authorization: Bearer ACCESS_TOKEN
```

---

## Refresh Token

Debe almacenarse de forma segura.

Permite obtener nuevos Access Token sin requerir una nueva autorización del usuario.

---

## Content-Type

El endpoint de Google para intercambio de tokens requiere:

```http
Content-Type: application/x-www-form-urlencoded
```

No debe enviarse el cuerpo en formato JSON.

---

# Buenas prácticas

- No almacenar Access Token en bases de datos si puede regenerarse mediante Refresh Token.
- Nunca exponer Client Secret.
- Nunca registrar Refresh Token en logs.
- Centralizar la autenticación OAuth en un único servicio.
- Reutilizar el servicio para todos los bots de AsisteClick.

---

# Estado del proyecto

- ✅ OAuth funcionando.
- ✅ Intercambio de tokens funcionando.
- ✅ Consulta de calendarios funcionando.
- ⏳ Consulta de eventos.
- ⏳ Creación de eventos.
- ⏳ Actualización de eventos.
- ⏳ Eliminación de eventos.
- ⏳ Búsqueda de disponibilidad.

---

# Licencia

Este proyecto se distribuye bajo la licencia MIT.
