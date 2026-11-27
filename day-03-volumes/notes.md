# Day 3: Volumes (keeping data safe)

## The problem
Data written inside a container is deleted when the container is deleted (`docker rm`).

## The fix: volumes
A volume is like a **locker outside the container**.
Delete the container, and the locker stays. A new container can use the same locker.

## What I did
1. Created a volume: `docker volume create pgdata`
2. Started Postgres using it:
   `docker run -d --name db -e POSTGRES_PASSWORD=password -p 5432:5432 -v pgdata:/var/lib/postgresql/data postgres:17`
3. Created a table, added 2 rows
4. Deleted the container: `docker rm -f db`
5. Started a new container with the same volume, and the 2 rows were still there

## The -v flag
-v pgdata:/var/lib/postgresql/data
   (locker) : (where the app saves inside the container)

Note: the path depends on the image. Postgres 17 = /var/lib/postgresql/data.
Wrong path = data is NOT saved, even with -v.

## 3 places data can live
| Type | What it is | Survives rm? | Use for |
|---|---|---|---|
| Inside container | sticky note on the container | No | Nothing important |
| Volume | locker managed by Docker | Yes | Databases |
| Bind mount | a folder on my own PC | Yes | Code I'm editing |

## Volume commands
| Command | What it does |
|---|---|
| `docker volume create pgdata` | Make a volume |
| `docker volume ls` | List volumes |
| `docker volume inspect pgdata` | Details about a volume |
| `docker volume rm pgdata` | Delete a volume (data gone!) |
| `docker volume prune` | Delete all unused volumes (careful!) |

## Errors & fixes
| Error | Cause | Fix |
|---|---|---|
| `failed to connect to the docker API at npipe:...` | Docker Desktop not running | Start Docker Desktop, wait for "Engine running" |
| `Conflict. The container name "/db" is already in use` | Old container with that name still exists | `docker rm -f db` |
| `psql: connection ... failed` right after start | Postgres still starting | `sleep 5` then try again |
