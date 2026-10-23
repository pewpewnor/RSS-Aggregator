# RSS Aggregator

A Go API that lets users register, add RSS feeds, subscribe to feeds, and
retrieve recent posts. A background scraper polls feeds every minute, while
authenticated API routes expose feed, subscription, user, and post operations.

## Stack

Go, Gin, PostgreSQL, sqlc-generated query code, UUID identifiers, and
dotenv-based configuration. The service can run HTTP-only for local development
or HTTPS when certificate files are present.

## Requirements and database

Install Go 1.21 or newer, Make, PostgreSQL, and psql. Create a database, then
apply the schema files in order:

~~~sh
for file in sql/schema/*.sql; do
  psql \
    "postgres://USER:PASSWORD@localhost:5432/rss_aggregator?sslmode=disable" \
    -f "$file"
done
~~~

Create a `.env` file in the repository root:

~~~dotenv
PORT=8080
DB_URL=postgres://USER:PASSWORD@localhost:5432/rss_aggregator?sslmode=disable
~~~

## Run

For local development without TLS:

~~~sh
make dev-http-only
~~~

In HTTP-only mode, Gin uses its default listener on port 8080. In TLS mode, the
configured PORT is used. The readiness endpoint is `GET /v1/healthz`; most user,
feed, subscription, and post endpoints require the API key returned during user
creation.

To run with HTTPS, generate a local certificate with `make generate-TLS` and
start with `make dev`. The generated certificate is self-signed and intended for
local use. Remove it with `make delete-TLS`.
