# Day 2: Working with containers

## Core commands
| Command | What it does |
|---|---|
| `docker run -d --name web -p 8080:80 nginx` | Run detached, named, with port mapping (host:container) |
| `docker ps` / `docker ps -a` | Running containers / all containers (incl. stopped) |
| `docker logs web` / `-f` | Container stdout / follow live |
| `docker exec -it web bash` | Shell inside a running container |
| `docker exec -it db psql -U postgres` | Run any command directly inside |
| `docker stop` / `docker start` | Stop (SIGTERM, then SIGKILL after 10s) / restart same container |
| `docker rm -f web` | Stop + delete container |
| `docker images` | List images |
| `docker container prune` | Delete all stopped containers |

## Key lessons
- **Containers share the host kernel.** nginx (Debian 13) reported `Linux ...-microsoft-standard-WSL2`, so a container is an isolated process, not a VM.
- **Port mapping:** `-p HOST:CONTAINER`. The container port must be the one the app listens on (nginx 80, postgres 5432).
- **Stopped ≠ deleted.** `stop`/`start` keeps changes; `rm` deletes them. The image is never modified.
- **Writable layer:** changes inside a container live in a thin layer that `rm` throws away, so the DB data disappeared after `rm` + `run`.
- **Layer sharing:** postgres reused layer `37fb86c2a767` from nginx (same Debian base), so it's stored once.
- **"Up" ≠ "ready":** Postgres needs a few seconds to initialize; exec too early fails. (Fix later: healthchecks.)
- **Bridge network:** containers get `172.17.x.x` IPs; requests via port mapping appear from `172.17.0.1`.

## Exit codes
- `0` = success (hello-world)
- `1` = app error, so check `docker logs` (postgres with no password)
- `137` = 128 + 9 = SIGKILL (stop timeout, or the out-of-memory killer in prod)

## Errors & fixes
| Error | Cause | Fix |
|---|---|---|
| postgres `Exited (1)` | No superuser password | `-e POSTGRES_PASSWORD=...` (found via `docker logs db`) |
| Couldn't connect on 5432 | `-p 5432:80`, wrong container port | `-p 5432:5432` |
| `psql: connection to server on socket ... failed` | Exec'd before Postgres finished init | Wait a few seconds |

## Debug loop
`docker ps -a` → see `Exited (N)` → `docker logs <name>` → fix → `docker rm` → re-run
