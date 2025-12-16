---
share:
  - "true"
---

## Limitación saltada per excepcións
> Puedo decir directamente que el esquema de diseño de la API es deficiente.

El principio habitual a seguir al nombrar rutas es
`/entidades/<id_entidad>/sub_entidades/<id_subentidad>/acción`
donde la acción es opcional ya que REST admite operaciones CRUD.

Entonces, si está obteniendo una reserva por usuario:
`GET /usuarios/<id_usuario>/reservas/<id_reserva>`

Aunque sí creo que el usuario nunca debería pasarse como parte de la URI o los datos de su API. Es información de identidad que debería obtenerse de su servicio de proveedor de identidad.

### Referència:
- https://www.reddit.com/r/golang/comments/15jhrvh/whats_your_general_rule_of_thumb_for_designing/
---
## To verb or not to verb:
Hay dos tipos de recursos:
- Colecciones de recursos
- Recursos

**Una colección consiste en 0-N recursos**.

La colección es `/users` (¡nunca singular, siempre plural!).

Puedes agregar, recuperar, actualizar y eliminar recursos de colecciones de recursos.

Un **recurso generalmente se identifica mediante un único identificador único** (`:id`). Por lo general, se evitan las claves naturales, incluso si son únicas, como la dirección de correo electrónico es única, pero no quieres ponerla en una URL. Usa una clave de reemplazo en su lugar.

Todo se puede describir en URL usando ese patrón:

`/collection/:id/subcollection/:subid`

Por ejemplo:
```
GET /users - devuelve todos los usuarios
GET /users?name=something - filtra el recurso de colección por nombre
POST /users - crea usuario
GET /users/:id - recupera usuarios por id
GET /users/:id/emails - recupera todas las direcciones de correo electrónico (un recurso de colección) para un usuario específico
POST /users/:id/emails - agrega una nueva dirección de correo electrónico a un usuario
PATCH /users/:id - actualiza una propiedad específica de un usuario
PUT /users/:id - crea un usuario con el ID especificado (201 Creado), o lo reemplaza si ya existe (200 OK)
DELETE /users/:id - elimina usuario específico
DELETE /users - elimina todos los usuarios
```

Si haces cosas como esta:

`POST /login`

Eso se considera similar a RPC y no es RESTful. A veces puedes evitar hacer eso haciendo algo como esto:

`POST /authtokens - iniciar sesión`
`DELETE /authtokens/:id - cerrar sesión`

_Pero eso obviamente puede volverse complicado. Entonces, generalmente uso mi propio juicio y dejo que dependa del contexto y las prácticas comunes cuándo agregar un método remoto a una colección de recursos._

Si descubres que para tu API todo debería describirse mejor como métodos RPC en lugar de colecciones de recursos y recursos, es posible que desees consultar Protobuf u otros métodos RPC (JSON RPC está bastante muerto en este momento, así que evitaría eso).

### Referència:
- https://www.reddit.com/r/golang/comments/15jhrvh/whats_your_general_rule_of_thumb_for_designing/