# ask-the-trail

Rails 8.1.4 · Ruby 3.4.11 · PostgreSQL 17 · Hotwire (Turbo + Stimulus) · Propshaft

The development environment runs entirely in Docker — there is no need
to install Ruby or Postgres on your machine.

## Getting started

```bash
docker compose up          # starts the app (port 3000) and the database
```

The app is available at http://localhost:3000

First time (or after cloning the repository):

```bash
docker compose build
docker compose run --rm web bundle install
docker compose run --rm web bin/rails db:prepare
docker compose up
```

## Everyday commands

With the containers running (`docker compose up -d`):

```bash
docker compose exec web bin/rails console
docker compose exec web bin/rails generate model Post title:string
docker compose exec web bin/rails db:migrate
docker compose exec web bin/rails test
docker compose exec web bin/rubocop
docker compose exec web bash              # shell inside the container
```

With the containers stopped, replace `exec web` with `run --rm web`.

After adding a gem to the `Gemfile`:

```bash
docker compose exec web bundle install
docker compose restart web
```

## Database

| | |
|---|---|
| Host (from inside the containers) | `db:5432` |
| Host (from your machine) | `localhost:5433` |
| User / password | `postgres` / `postgres` |
| Databases | `ask_the_trail_development`, `ask_the_trail_test` |

Connecting with an external client:

```bash
docker compose exec db psql -U postgres -d ask_the_trail_development
```

Data persists in the `ask-the-trail_pgdata` volume. To reset the database:

```bash
docker compose down -v && docker compose run --rm web bin/rails db:prepare
```

## Structure

- `Dockerfile.dev` + `compose.yaml` — development environment.
- `config/database.yml` — credentials come from `DB_HOST`, `DB_USER`, `DB_PASSWORD`.

Gems live in the `ask-the-trail_bundle` volume, outside the project tree. The container
runs with UID/GID 1000, the same as the host, so files generated inside it
(`Gemfile.lock`, `db/schema.rb`, migrations) belong to your user.
