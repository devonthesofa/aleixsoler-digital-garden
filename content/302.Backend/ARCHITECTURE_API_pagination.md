---
tags: []
share:
  - "true"
---
## Paginación
### Tipos de paginación
- Offset pagination: `?limit=10&offset=20`
- Page-based pagination: `?page=3&pageSize=10`
- Keyset pagination (seek method): `?since_id=12345&limit=10`
- Time-based pagination
- Cursor-based pagination: `?cursor=abc123&limit=10`

### Page-based pagination vs Page-based pagination
Al implementar la paginación en una API, podemos ofrecer dos enfoques distintos para que los clientes elijan el que mejor se adapte a sus necesidades:
1. **Paginación basada en página y tamaño**(`page&pageSize`)
	- Es más intuitiva y fácil de entender para los usuarios.
	- Se basa en números de página(`page`) y la cantidad(`pageSize`) de elementos por página.
	- Ejemplo: `GET /api/v1/things?page=2&pageSize=20`
		- En este caso, el usuario solicita la segunda página con 20 elementos por página.
2. **Paginación basada en límite y desplazamiento**(`limit&offset`)
	- Permite un control más preciso sobre los datos recuperados.
	- Se basa en la cantidad de elementos(`limit`) y un desplazamiento(`offset`) que indica cuántos elementos saltar antes de comenzar a recuperar datos.
	- Ejemplo: `GET /api/v1/things?limit=20&offset=20`
		- En este caso, el usuario solicita 20 elementos, comenzando desde el elemento 21(ya que offset=20 omite los primeros 20 registros).

#### Cuál es la mejor opción?
Ambos enfoques tienen sus ventajas y desventajas:
- `page&size` es más intuitivo y fácil de usar para la mayoría de los desarrolladores y usuarios de la API.
- `limit&offset` ofrece un mayor control, especialmente útil en aplicaciones que necesitan paginación dinámica.

Dado que la implementación de ambos métodos es sencilla, **permitir ambas opciones en la API ofrece la mejor experiencia de usuario**.
#### Como se podría implementar
```javascript
// Código anterior omitido
	let { page, size, limit, offset } = req.query;

    if (page !== undefined && size !== undefined) {
        // Conversión de valores a enteros
        page = parseInt(page, 10);
        size = parseInt(size, 10);

        // Cálculo del offset basado en page y size
        offset = (page - 1) * size;
        limit = size;
    } else if (limit !== undefined && offset !== undefined) {
        // Conversión de valores a enteros
        limit = parseInt(limit, 10);
        offset = parseInt(offset, 10);
    } else {
        // Valores por defecto si no se envían parámetros
        limit = 10;
        offset = 0;
    }
// Código posterior omitido
```
### Keyset pagination
Keyset pagination is used to retrieve the next set of results in a sorted data set. To use keyset pagination, the results must be sorted by one or more columns, and each item in the result set must have a unique identifier that can be used to determine the next set of results.

For instance, if we have a database table with columns such as `id`, `name`, and `date_created`, we could use the `date_created` column to sort the data.

To implement keyset pagination, we need to choose one or more columns to sort the data. Let's say we choose to sort by the `date_created` column.

First, we'll fetch the first set of results by running the following query:

```sql
SELECT id, name, date_created FROM my_table ORDER BY date_created LIMIT 6;
```

This will return the first 10 results, sorted by `date_created`. We'll also retrieve the last item's value for the sorting column, in this case, `date_created`.
![[../Attachments/Pasted image 20250131094040.png|Pasted image 20250131094040.png]]
Now, to fetch the next set of results, we'll run the following query:

```sql
SELECT id, name, date_created FROM my_table WHERE date_created > '2022-01-07' ORDER BY date_created LIMIT 6;
```

This query uses the last `date_created` value from the previous query to get the next set of results. It fetches all the rows whose `date_created` value is greater than the last `date_created` value and returns the next 10 results.

By using the unique identifier (`date_created` in this example) instead of an offset value, keyset pagination provides better performance and avoids the issues of duplicate data and inconsistent results that can occur with offset pagination.
#### 4. Seek Pagination

Seek-based pagination is used to retrieve a subset of results in a sorted data set. It works by specifying a starting point and returning results that come after that starting point, up to a certain limit. Seek-based pagination is often used when dealing with large datasets and can be faster than other types of pagination.

For example, let's say we have a database table of user accounts with columns such as `id`, `username`, and `registration_date`. We want to retrieve the first 50 users who registered after a specific date. To use seek-based pagination, we would first sort the data by `registration_date` in ascending order. We would then use the `registration_date` of the first user who registered after our specified date as the starting point and retrieve the next 50 users from that point.

Let's say our specified date is January 1, 2022, and the first user who registered after that date has a `registration_date` of January 5, 2022. We would use the `registration_date` of this user as the starting point and retrieve the next 50 users from that point. If we want to retrieve the next 50 users after that, we would use the `registration_date` of the 50th user as the new starting point and continue the process.
### Cursor-based pagination
Cursor-based pagination is a common technique used in RESTful APIs for paginating through large sets of data. In this technique, a cursor is used as a pointer to a specific location in the data set, and the API client retrieves the next page of results using the cursor.

Here's an example of how cursor-based pagination works in a RESTful API:

Let's assume that we have a database of user profiles with millions of records, and we want to provide a paginated API to return these user profiles.

**Step-1:** The API client makes an initial request to the server, specifying the number of items to be returned per page and a starting cursor value.

```javascript
GET /users?limit=50&cursor=0
```

**Step-2:** The server returns the first 50 user profiles, along with a cursor value to use for the next page.

```json
{
  "data": [
    {"id": 1, "name": "Alice"},
    {"id": 2, "name": "Bob"},
    {"id": 3, "name": "Charlie"},
    ...
    {"id": 50, "name": "Zoe"}
  ],
  "cursor": "eyJpZCI6MzQ2NTAsInNlcXVlbmNlIjozNTYyMH0="
}
```

**Step-3:** The API client makes a subsequent request to retrieve the next page of data, using the cursor value returned by the server.

```javascript
GET /users?limit=50&cursor=eyJpZCI6MzQ2NTAsInNlcXVlbmNlIjozNTYyMH0=
```

**Step-4:** The server retrieves the next 50 user profiles from the database, starting from the position indicated by the cursor value. It then returns the next page of results, along with a new cursor value to use for the next page.

```json
{
  "data": [
    {"id": 51, "name": "Alex"},
    {"id": 52, "name": "Ben"},
    {"id": 53, "name": "David"},
    ...
    {"id": 100, "name": "Oliver"}
  ],
  "cursor": "eyJpZCI6MTAwMDEsInNlcXVlbmNlIjozNTYyMX0="
}
```

**Step-5:** The process repeats until all data has been retrieved.

Cursor-based pagination is a flexible and efficient way to paginate through large sets of data, and it can be customized to handle different sorting and filtering requirements.

## Retorn
siempre tiene que haber metadata en la respuesta para tener información adicional
```json
{
  "data": [
    // ...
  ],
  "meta": {
    "pagination": {
      "start": 0,
      "limit": 10,
      "total": 42
    }
  }
}
```
Esto es lo que retorna strapi
segons el que facis servir retorna el metadata que toca

  "metadata": {
    "total_count": 100,
    "limit": 10,
    "offset": 20
  }
  
### **Explicació dels camps**

- **`data`** → Conté els elements retornats segons la paginació aplicada.
- **`meta.pagination`** → Inclou informació detallada sobre la paginació:
    - 🔹 **`offset`**: Per `limit&offset`, indica el punt d'inici dels resultats.
    - 🔹 **`limit`**: Quantitat de resultats per consulta (equivalent a `pageSize`).
    - 🔹 **`page`**: Per `page&pageSize`, indica la pàgina actual.
    - 🔹 **`pageSize`**: Equival a `limit`, definint el nombre d’elements per pàgina.
    - 🔹 **`total`**: Nombre total d’elements en la base de dades.
    - 🔹 **`totalPages`**: Nombre total de pàgines disponibles (`Math.ceil(total / pageSize)`).
    - 🔹 **`hasNextPage`**: `true` si hi ha més pàgines després de la pàgina actual.
    - 🔹 **`hasPrevPage`**: `true` si hi ha pàgines abans de la pàgina actual.

s'ha de seguir complimentant

## Referencias:
- https://developer.atlassian.com/server/confluence/pagination-in-the-rest-api/
- https://stackoverflow.com/questions/56058882/what-pagination-method-is-better-for-rest-apis-page-size-or-limit-offset
- https://www.merge.dev/blog/rest-api-pagination
- https://docs.strapi.io/dev-docs/api/rest/sort-pagination
- https://www.atatus.com/blog/rest-api-design-filtering-sorting-and-pagination/