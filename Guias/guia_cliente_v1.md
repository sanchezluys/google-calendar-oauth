# Integración OAuth 2.0 con Google Calendar para AsisteClick

## Objetivo

Implementar la autenticación **OAuth 2.0** con Google para permitir que AsisteClick acceda a la **Google Calendar API** en nombre del usuario de forma segura.

---

# Paso 1. Crear el proyecto en Google Cloud

1. Ingresar a **Google Cloud Console**.
2. Crear un nuevo proyecto.
3. Seleccionar el proyecto creado.

---

# Paso 2. Habilitar Google Calendar API

Ir a:

```text
APIs y Servicios
    ↓
Biblioteca
    ↓
Google Calendar API
```

Presionar **Habilitar**.

---

# Paso 3. Configurar la pantalla de consentimiento OAuth

Ir a:

```text
APIs y Servicios
    ↓
Pantalla de consentimiento OAuth
```

Configurar los siguientes campos:

- Nombre de la aplicación.
- Correo electrónico de soporte.
- Dominio (opcional).
- Correo electrónico del desarrollador.

Durante el desarrollo puede dejarse el estado en:

```text
Testing
```

Agregar los usuarios que podrán probar la aplicación.

---

# Paso 4. Crear las credenciales OAuth

Ir a:

```text
APIs y Servicios
    ↓
Credenciales
    ↓
Crear credenciales
    ↓
OAuth Client ID
```

Seleccionar:

```text
Aplicación Web
```

Agregar el **Redirect URI autorizado**:

```text
http://localhost:3000/oauth2callback
```

Google generará automáticamente:

- Client ID
- Client Secret

---

# Paso 5. Solicitar autorización al usuario

Abrir la siguiente URL:

```text
https://accounts.google.com/o/oauth2/v2/auth
```

## Parámetros

| Parámetro | Valor |
|-----------|-------|
| client_id | CLIENT_ID |
| redirect_uri | http://localhost:3000/oauth2callback |
| response_type | code |
| scope | https://www.googleapis.com/auth/calendar |
| access_type | offline |
| prompt | consent |

## Ejemplo

```text
https://accounts.google.com/o/oauth2/v2/auth?
client_id=XXXXX.apps.googleusercontent.com&
redirect_uri=http://localhost:3000/oauth2callback&
response_type=code&
scope=https://www.googleapis.com/auth/calendar&
access_type=offline&
prompt=consent
```

---

# Paso 6. Obtener el Authorization Code

Después de aceptar los permisos, Google redireccionará al navegador hacia:

```text
http://localhost:3000/oauth2callback?code=XXXXXXXX
```

Debe extraerse únicamente el valor del parámetro:

```text
code
```

Ejemplo:

```text
4/0AXEQxIDYDfdIj9-AYcSUMDSrIr3RU-FapOp9G6C8Yo_75XLfvNpfTTbYKML99C1kMLJR3g
```

> **Importante**
>
> - El Authorization Code solo puede utilizarse una vez.
> - Expira después de pocos minutos.
> - Si expira o ya fue utilizado, será necesario solicitar uno nuevo.

---

# Paso 7. Intercambiar el Authorization Code por los tokens

Realizar una petición **POST** al endpoint:

```text
https://oauth2.googleapis.com/token
```

## Headers

```http
Content-Type: application/x-www-form-urlencoded
```

## Body

```text
code=AUTHORIZATION_CODE&
client_id=CLIENT_ID&
client_secret=CLIENT_SECRET&
redirect_uri=http://localhost:3000/oauth2callback&
grant_type=authorization_code
```

## Error encontrado durante la implementación

Inicialmente el cuerpo de la petición se enviaba en formato JSON:

```json
{
  "code": "...",
  "client_id": "...",
  "client_secret": "...",
  "redirect_uri": "...",
  "grant_type": "authorization_code"
}
```

Google respondía con:

```text
HTTP 400 Bad Request
```

### Causa

El endpoint `/token` **no acepta JSON**.

### Solución

Enviar el cuerpo utilizando:

```http
Content-Type: application/x-www-form-urlencoded
```

Una vez realizado el cambio, la autenticación funcionó correctamente.

---

# Paso 8. Respuesta correcta

Google responderá con un objeto similar al siguiente:

```json
{
  "access_token": "ya29....",
  "expires_in": 3599,
  "refresh_token": "1//0....",
  "scope": "https://www.googleapis.com/auth/calendar",
  "token_type": "Bearer"
}
```

Donde:

- **access_token**: Token utilizado para consumir la API.
- **refresh_token**: Token utilizado para generar nuevos Access Token.
- **expires_in**: Tiempo de vida del Access Token (aproximadamente una hora).

---

# Paso 9. Guardar el Refresh Token

Guardar de forma segura únicamente el:

```text
refresh_token
```

Mientras el Refresh Token siga siendo válido, no será necesario volver a solicitar autorización al usuario.

---

# Paso 10. Utilizar el Access Token

Todas las llamadas posteriores a la API deberán incluir el siguiente header:

```http
Authorization: Bearer ACCESS_TOKEN
```

---

# Paso 11. Verificar acceso a Google Calendar

Realizar la siguiente consulta:

```http
GET https://www.googleapis.com/calendar/v3/users/me/calendarList
```

## Headers

```http
Authorization: Bearer ACCESS_TOKEN
```

## Respuesta esperada

```json
{
  "items": [
    {
      "summary": "Días feriados en Colombia",
      "accessRole": "reader"
    },
    {
      "summary": "luyscolombia@gmail.com",
      "primary": true,
      "accessRole": "owner"
    }
  ]
}
```

## Verificación

Si la respuesta contiene la lista de calendarios significa que:

- ✅ OAuth está funcionando correctamente.
- ✅ El Access Token es válido.
- ✅ La aplicación tiene permisos sobre Google Calendar.
- ✅ Es posible consumir la API de Google Calendar.

---

# Próximos pasos

Una vez autenticado el usuario se pueden implementar las operaciones disponibles sobre Google Calendar.

## Obtener calendarios

```http
GET /users/me/calendarList
```

---

## Obtener eventos

```http
GET /calendars/{calendarId}/events
```

---

## Crear eventos

```http
POST /calendars/{calendarId}/events
```

---

## Modificar eventos

```http
PATCH /calendars/{calendarId}/events/{eventId}
```

---

## Eliminar eventos

```http
DELETE /calendars/{calendarId}/events/{eventId}
```

---

# Recomendaciones

- Nunca registrar en los logs el **Client Secret**, **Access Token** o **Refresh Token** completos.
- Almacenar de forma segura únicamente el **Refresh Token**.
- Generar automáticamente un nuevo **Access Token** utilizando el Refresh Token cuando expire.
- No solicitar nuevamente autorización al usuario mientras el Refresh Token siga siendo válido.
- Centralizar toda la lógica de autenticación OAuth y consumo de Google Calendar en un único servicio reutilizable para facilitar el mantenimiento y permitir que múltiples bots o aplicaciones compartan la misma implementación.

---

# Resultado

Al finalizar esta guía se dispone de una integración OAuth completamente funcional con Google Calendar, incluyendo:

- Autenticación OAuth 2.0.
- Obtención de Access Token.
- Obtención de Refresh Token.
- Consulta de calendarios.
- Base preparada para crear, consultar, modificar y eliminar eventos mediante la Google Calendar API.
