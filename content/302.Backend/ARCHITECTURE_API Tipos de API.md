---
tags: []
share:
  - "true"
---
## Restful APIs
**Descripción**: REST (Representational State Transfer) es un estilo arquitectónico para APIs que utiliza HTTP para interactuar con recursos identificados por URLs. Sigue principios como cliente-servidor, comunicación sin estado, caché, y una estructura uniforme de recursos.
- **GET /products/42** → `{"id": 42, "name": "Laptop", "price": 1200}`
- **POST /products** → Request: `{"name": "Tablet", "price": 300}` → Response: `{"id": 43, "status": "created"}`
## Simple JSON APIs
**Descripción**: Similar a RESTful APIs, pero suelen ser más simples y no necesariamente siguen todos los principios REST. Usan JSON como formato principal de intercambio de datos.
- **GET /api/items** → `{"items": [{"id": 1, "name": "Book"}, {"id": 2, "name": "Pen"}]}`
- **POST /api/items** → Request: `{"name": "Notebook"}` → Response: `{"id": 3, "status": "success"}`
## SOAP APIs
**Descripción**: SOAP (Simple Object Access Protocol) es un protocolo que usa XML para intercambiar información estructurada entre sistemas. Es más rígido que REST, pero ofrece mayor seguridad y es ideal para aplicaciones empresariales.
- **Request**:
```xml
<AddProductRequest>
	<Name>Laptop</Name>
	<Price>1200</Price>
</AddProductRequest>
```
- **Response**:
```xml
<AddProductResponse>
	<Id>42</Id>
	<Status>Success</Status>
</AddProductResponse>
```
## GraphQL APIs
**Descripción**: GraphQL es un lenguaje de consulta que permite a los clientes especificar exactamente los datos que necesitan, obteniendo respuestas en una sola solicitud.
- **Query**:
```graphql
query {   
	product(id: 42) {    
		name     
		price   
	} 
}
```
**Response**: `{"data": {"product": {"name": "Laptop", "price": 1200}}` 
- **Mutation**:
 ```
mutation {   
	addProduct(name: "Tablet", price: 300) {
		 id     
		 status   
	 } 
 }
```
**Response**: `{"data": {"addProduct": {"id": 43, "status": "created"}}}`
## gRPS APIs
**Descripción**: gRPC (Google Remote Procedure Call) es un marco de llamadas a procedimientos remotos que usa Protocol Buffers (protobuf) para serializar datos y permite comunicación eficiente entre servicios.

**Request (Protobuf definition)**:

```protobuf
message AddProductRequest {
  string name = 1;
  int32 price = 2;
}

message AddProductResponse {
  int32 id = 1;
  string status = 2;
}
```
**Client Call**:
```python
response = product_service.AddProduct(AddProductRequest(name="Laptop", price=1200))
print(response.id, response.status)
```
- **Response**: `id: 42, status: "created"`


## Comparación clave:

- **RESTful y Simple JSON**: Flexibles y usan HTTP, pero RESTful respeta principios estándar como métodos (GET, POST, etc.).
- **SOAP**: Más estructurado, usa XML, y es ideal para interoperabilidad empresarial.
- **GraphQL**: Consulta y recibe solo lo necesario, evita múltiples llamadas.
- **gRPC**: Muy eficiente, pero requiere clientes específicos y usa Protocol Buffers.