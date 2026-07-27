## Module 4: Docker Compose — Orchestrating Multi-Container Apps

### The problem Compose solves

Real apps aren't one container — they're a web app + database + cache + maybe a queue. Running each with long `docker run` commands manually is error-prone and unrepeatable. Compose lets you define your **entire multi-container setup declaratively** in one YAML file.

```yaml
version: "3.8"
services:
  web:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - db
    environment:
      - DATABASE_URL=postgres://db:5432/mydb

  db:
    image: postgres:15
    volumes:
      - dbdata:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=secret

volumes:
  dbdata:
```

Run it all with one command: `docker compose up`.

**Analogy:** If a single Dockerfile/image is like a recipe for *one dish*, Docker Compose is the **menu for an entire meal** — appetizer, main course, dessert — all prepared and served together, coordinated to arrive at the table (network) at the right time, sharing the same kitchen (host) but each cooked in its own pot (container).

### What Compose does automatically (interview-relevant)

1. **Creates a custom bridge network** for you — this is *why* `web` can reach `db` just by hostname `db` (remember Module 3's DNS gotcha — this is exactly why Compose "just works" without you manually creating a network).
2. **`depends_on`** controls *start order*, but — critical gotcha — it does **NOT** wait for the app inside the container to be *ready* (e.g., Postgres process fully accepting connections). It only waits for the container to *start*. This causes a classic bug: your `web` service starts, tries to connect to `db`, and fails because Postgres is still initializing internally.

   **Fix:** Use healthchecks:
   ```yaml
   db:
     image: postgres:15
     healthcheck:
       test: ["CMD-SHELL", "pg_isready -U postgres"]
       interval: 5s
       retries: 5

   web:
     depends_on:
       db:
         condition: service_healthy
   ```

   **Analogy:** `depends_on` without healthcheck is like telling a waiter "serve the soup after the chef starts cooking" instead of "serve the soup after the chef says it's done." The chef *starting* isn't the same as the soup being *ready*.

3. **Scaling**: `docker compose up --scale web=3` spins up 3 replicas of the `web` service — good for local load-testing, though real horizontal scaling in production is usually Kubernetes/Swarm territory, not Compose.

## Interview Question (this one separates "used Docker" from "understands Docker")

**Q: Your `docker-compose.yml` defines a `web` service and a `db` service on the same custom network. You also have another *unrelated* Compose project on the same machine with its own `web` and `db` services. Will there be a naming/networking conflict between the two projects? Why or why not?**

## Answer: Compose project isolation

**No conflict** — and here's why: Docker Compose automatically **namespaces everything by project name**. By default, the project name is derived from the folder name containing the `docker-compose.yml` (or explicitly set via `-p myproject` / `COMPOSE_PROJECT_NAME`).

So if you have two separate Compose projects, each with a `web` and `db` service, Compose actually creates resources like:

```
project1_web, project1_db, project1_default (network)
project2_web, project2_db, project2_default (network)
```

Each project gets its **own isolated custom bridge network**, so `project1`'s `web` container resolves `db` to *its own* db container, and `project2`'s `web` resolves `db` to a completely different container — because they're on entirely separate networks. Containers on `project1_default` can't even see containers on `project2_default` unless you explicitly connect them.

**Analogy:** It's like two different companies both having an employee named "Dave" and an internal extension "101." There's no conflict because each company has its own separate phone system (network) — dialing 101 inside Company A's building never accidentally rings Company B's Dave.

**The gotcha interviewers probe for:** People assume container *names* are globally unique across the whole Docker host and get confused — they're only unique **within a project's network namespace** by default (Compose actually prefixes container names too, like `project1_web_1` in older versions, or `project1-web-1` in newer Compose v2 syntax).
