# Stage 2: Orchestrating Flask + MySQL with docker-compose

> This is Stage 2 of a 5-stage migration series:
> Stage 1: [containerize-flask-mysql-application](https://github.com/bilalamjad-devops/containerize-flask-mysql-application) → **Stage 2 (this repo): docker-compose** → Stage 3: Kubernetes → Stage 4: CI/CD → Stage 5: GitOps (ArgoCD)

## Problem

In Stage 1, running this same two-container app manually required a specific,
easy-to-get-wrong sequence of commands:

```bash
docker network create twotier
docker run -d --name mysql-dev --network=twotier -e MYSQL_ROOT_PASSWORD=changeme -e MYSQL_DATABASE=web_db -p 3306:3306 mysql:8.4
docker run -p 5000:5000 --network=twotier --env-file .env --name flask-app-dev flask-mysql-demo
```

In practice, this went wrong twice during real debugging:

1. **Network mismatch** — the MySQL container was originally created on a
   *different* network name than the Flask container was later run on
   (`demo-net` vs `twotier`). Docker gave no upfront warning; the failure only
   showed up as `Unknown MySQL server host 'mysql-dev'` at runtime, and required
   inspecting both containers' `NetworkSettings.Networks` to diagnose.
2. **No data persistence** — the MySQL container was started without a
   volume. Any data written during testing would have been silently lost
   the moment the container was removed (`docker rm`), since `/var/lib/mysql`
   was only part of the container's own writable layer, not a separate volume.

Both problems are structural: they happen because networking and storage are
handled as separate, manually-sequenced commands instead of one declared
state.

## Solution

`docker-compose.yml` declares both containers, their shared network, and a
named persistent volume in one file. One command:

```bash
docker compose up --build
```

replaces all three manual commands above, and makes both failure modes from
Stage 1 structurally impossible:

- **Networking**: compose creates a private network automatically and gives
  each service DNS resolution by its service name (`db`, `web`) — there's no
  separate `--network` flag to forget or mismatch.
- **Persistence**: the named volume `mysql-data` is declared once, in the
  same file as everything else, so it can't be silently omitted the way a
  `-v` flag can be forgotten on a long `docker run` command.
- **Startup order**: `depends_on` with `condition: service_healthy` ensures
  Flask doesn't even attempt to connect until MySQL's own healthcheck passes
  — removing the "container up but not actually ready yet" race condition.

## Run it

```bash
docker compose up --build
```

Visit `http://localhost:5000`.

Stop everything (keeps data):
```bash
docker compose down
```

Stop and wipe all data (start completely fresh):
```bash
docker compose down -v
```

## What changed vs. Stage 1

| | Stage 1 (Dockerfile only) | Stage 2 (docker-compose) |
|---|---|---|
| Start command | 3 separate `docker` commands, in a specific order | 1 command: `docker compose up` |
| Networking | Manual `docker network create` + `--network` flags | Automatic, declared once |
| Persistence | Not configured (data loss risk) | Named volume, declared once |
| Startup ordering | No ordering guarantee | `depends_on` + healthcheck |

## What's deliberately NOT here yet

- No Kubernetes (Stage 3)
- No CI/CD (Stage 4)
- No GitOps (Stage 5)

## Lessons Learned

- A tool that removes an entire class of manual-sequencing bugs (network
  mismatches, forgotten volume flags) is more valuable here than one that
  just saves typing — the real win of compose isn't convenience, it's
  eliminating two specific failure modes that actually occurred in Stage 1.
