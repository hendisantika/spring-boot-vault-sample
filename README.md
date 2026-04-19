# Spring Boot Vault Sample

A Spring Boot 4 sample CRUD application that stores product data in an in-memory
H2 database and reads application secrets from a HashiCorp Vault server.

- **Vault server:** `https://vault.persis.or.id:8270`
- **Database:** H2 (in-memory) with console at `/h2-console`
- **Java:** 25
- **Build:** Maven (wrapper included)

## Requirements

- JDK 25
- A Vault token with read access to `secret/spring-boot-vault-sample`
  (optional — the app boots without it)

## Configure

Set your Vault token as an environment variable before running:

```bash
export VAULT_TOKEN="hvs.xxxxxxxxxxxxxxxx"
```

All other Vault settings live in `src/main/resources/application.properties`.

## Run

```bash
./mvnw spring-boot:run
```

The app listens on `http://localhost:8080`.

## REST API

Base path: `/api/v1/products`

| Method | Path            | Description                          |
|--------|-----------------|--------------------------------------|
| GET    | `/`             | List all or `?name=` filter by name  |
| GET    | `/{id}`         | Get one product                      |
| POST   | `/`             | Create a product                     |
| PUT    | `/{id}`         | Update a product                     |
| DELETE | `/{id}`         | Delete a product                     |

### Sample payload

```json
{
  "name": "Mac Studio M4 Ultra",
  "description": "32-core CPU, 80-core GPU, 128GB RAM",
  "price": 75999000.00,
  "stock": 3
}
```

### cURL examples

```bash
# List
curl http://localhost:8080/api/v1/products

# Search
curl "http://localhost:8080/api/v1/products?name=ipad"

# Create
curl -X POST http://localhost:8080/api/v1/products \
  -H "Content-Type: application/json" \
  -d '{"name":"Mac Studio","description":"M4 Ultra","price":75999000.00,"stock":3}'

# Update
curl -X PUT http://localhost:8080/api/v1/products/1 \
  -H "Content-Type: application/json" \
  -d '{"name":"MacBook Pro 16","description":"M4 Max","price":52999000.00,"stock":4}'

# Delete
curl -X DELETE http://localhost:8080/api/v1/products/1
```

## H2 Console

Open `http://localhost:8080/h2-console`.

| Field    | Value                   |
|----------|-------------------------|
| JDBC URL | `jdbc:h2:mem:vaultdb`   |
| User     | `yu71`                  |
| Password | `53cret`                |

## Vault

On startup, `VaultSecretReader` reads the KV v2 secret at
`secret/data/spring-boot-vault-sample` and logs each key with a masked value.
If Vault is unreachable or `VAULT_TOKEN` is empty, the app logs a warning and
continues to boot.

Example of writing a secret to Vault:

```bash
vault kv put secret/spring-boot-vault-sample \
  db.username=yu71 \
  db.password=53cret \
  api.key=super-secret
```

## Project layout

```
src/main/java/id/my/persislabs/vault
├── SpringBootVaultSampleApplication.java
├── controller/ProductController.java
├── exception/
│   ├── GlobalExceptionHandler.java
│   └── ResourceNotFoundException.java
├── model/Product.java
├── repository/ProductRepository.java
├── service/ProductService.java
└── vault/VaultSecretReader.java
```

## Author

Hendi Santika — [s.id/hendisantika](https://s.id/hendisantika)
