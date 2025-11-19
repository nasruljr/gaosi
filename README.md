# Go All-in-One Starter Infrastructure (GAOSI)

GAOSI is a batteries-included project generator that spins up a production-ready Go microservice with a single command. It handles repo scaffolding, Docker assets, CI workflows, observability wiring, and key infrastructure adapters so you can immediately focus on your domain logic.

## Features

- Style layout (`cmd`, `configs`, `internal`, `pkg`) ready for CRUD-style APIs across Gin, Fiber, Echo, or Chi.
- Gin HTTP server + sample user module, validation helpers, logging, and unified response utilities.
- Built-in GORM wiring for PostgreSQL or MySQL, Redis connection helpers (with `RedisSet`/`RedisGet`), and optional Kafka/RabbitMQ producers.
- Configurable templates for config files, go.mod, README, and Postman collection.
- CLI flags for framework/database/message queue selection, dan Swagger UI plus OpenAPI sample yang hanya ikut ter-generate ketika Anda menambahkan `--swagger`.
- Multi-stage Dockerfile dan `.dockerignore` siap pakai (aktif secara default via `--docker`) supaya layanan langsung bisa dibangun menjadi container.

## Usage

```bash
./gaosi new test-services --module test-services --framework gin --database postgres --message-queue kafka --swagger --output ..
```

### Flags

| Flag | Description | Default |
|------|-------------|---------|
| `--module` | Go module path for the generated service | `<service-name>` |
| `--framework` | HTTP framework: `gin`, `fiber`, `echo`, `chi` | `gin` |
| `--database` | SQL database: `postgres`, `mysql`, `sqlite` | `postgres` |
| `--message-queue` | Message queue: `kafka`, `rabbit`, or `none` | `none` |
| `--swagger` | Include Swagger UI + sample OpenAPI spec | `false` |
| `--docker` | Include Dockerfile + `.dockerignore` scaffolding | `true` |
| `--output` | Target directory where the service folder is created | `.` |
| `--force` | Overwrite the destination if it already exists | `false` |

### Swagger output

Gunakan flag `--swagger` apabila Anda ingin scaffold menambahkan paket `pkg/swagger`, file OpenAPI sample, dan dua endpoint baru:

- `GET /swagger` &rarr; menampilkan Swagger UI (memakai CDN `swagger-ui-dist`).
- `GET /swagger/doc.json` &rarr; menyajikan konten OpenAPI 3.0 yang menggambarkan modul user bawaan.

Kedua endpoint ini otomatis terpasang di setiap framework yang Anda pilih (Gin, Fiber, Echo, Chi) tanpa perlu dependensi eksternal tambahan.

### Docker output

Secara default (atau dengan `--docker`), GAOSI menambahkan Dockerfile multi-stage (`golang:1.23` → `gcr.io/distroless/static:nonroot`) plus `.dockerignore`, sehingga image dapat dibangun dengan:

```bash
docker build -t my-service:latest .
docker run --rm -p 8585:8585 -v $(pwd)/configs:/app/configs my-service:latest
```

## Generated project highlights

- `cmd/main.go`: HTTP bootstrapper (Gin/Fiber/Echo/Chi) with optional message queue initialization.
- `configs/`: environment configs, Redis + message queue settings, and message catalog.
- `internal/router.go` + `internal/user`: sample module (controller/service/repository/tests) showcasing layering.
- `pkg/config`, `pkg/conn`, and `pkg/utils`: config loader, DB + Redis connectors, Redis helpers, HTTP clients, logging, and response helpers.
- `go.mod`, README, and Postman collection so new services can run immediately after `go mod tidy`.
