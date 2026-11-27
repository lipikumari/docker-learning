# Docker Interview Q&A
Simple answers. Updated every day.

## Basics (Day 1)
**What is Docker?**
A tool that packs your app and everything it needs into one box, so it runs the same on any computer.

**Container vs VM?**
A container shares the computer's core (the kernel), so it's light and starts in seconds.
A VM brings its own full OS, so it's heavy and slow to start.

**Image vs container?**
Image = recipe (never changes). Container = dish made from the recipe.
One image can make many containers.

**How does Docker run on Windows?**
Containers need Linux. Docker Desktop runs a small Linux system (WSL 2) in the background.

**What are the parts of Docker?**
- Client: the `docker` command you type
- Daemon (engine): the background service that actually runs containers
- Registry: where images are stored online (Docker Hub)

## Containers (Day 2)
**docker run vs docker exec?**
`run` = make a new container and start it.
`exec` = run a command inside a container that's already running.

**docker stop vs docker kill?**
`stop` = asks politely, waits 10 seconds, then forces it.
`kill` = forces it right away.

**What does -p 8080:80 mean?**
Port 8080 on my PC connects to port 80 inside the container.

**A container stops right after starting. What do you do?**
1. `docker ps -a` to see the exit code
2. `docker logs <name>` to read the error
The answer is almost always in the logs (e.g., a missing password).

**What does exit code 137 mean?**
The container was force-killed. Often it ran out of memory.

**Container is "Up" but the app doesn't respond. Why?**
- The app is still starting ("Up" doesn't mean "ready")
- Wrong port in -p
- The app is listening on the wrong address inside the container

**What are image layers?**
An image is built in layers, like a stack of sheets.
Images that share layers store them only once, which saves space and download time.

## Volumes (Day 3)
**How do you keep data when a container is deleted?**
Use a volume. The data lives outside the container.

**Volume vs bind mount?**
Volume = Docker manages it, best for databases.
Bind mount = a folder on your own PC, best for code you're editing.

**Does docker rm delete volumes?**
No. You must delete them yourself (`docker volume rm`).
Good for safety, but old volumes can fill up the disk.

**How do you upgrade a database container without losing data?**
Keep the data in a volume. Delete the old container, start the new version with the same volume.
(Take a backup first. Big version jumps like 16 → 17 need extra steps.)
