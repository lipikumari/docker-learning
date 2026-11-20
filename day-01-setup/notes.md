# Day 1 — Setting up Docker on Windows 11 (WSL 2)

## What I did
1. Installed WSL (`wsl --version` → prompted install, auto-elevated via UAC)
2. Rebooted, tried `wsl --install -d Ubuntu` → failed (virtualization off)
3. Enabled CPU virtualization in BIOS
4. Installed Ubuntu in WSL, created Linux user
5. Installed git + GitHub CLI, authenticated, created this repo
6. Installed Docker Desktop (WSL 2 backend), enabled Ubuntu integration
7. Fixed socket permission error → `docker run hello-world` works

## Key concepts
- **Image** = read-only template (like a class)
- **Container** = running instance of an image (like an object)
- **Registry** = image storage (Docker Hub is default)
- Containers need a Linux kernel → on Windows, Docker runs inside WSL 2
- Docker = **client** (`docker` CLI) + **daemon** (engine), talking via `/var/run/docker.sock`
- Images are built from **layers**; pulled once, then cached

## Errors & fixes
| Error | Cause | Fix |
|---|---|---|
| `HCS_E_HYPERV_NOT_INSTALLED` / "virtualization is not enabled" | VT-x/AMD-V off in BIOS | Enable it in BIOS. Check: `systeminfo \| findstr /i "Virtualization"` → "Enabled In Firmware: Yes" |
| `gh` "Failed opening a web browser" | WSL has no browser | Open github.com/login/device manually, paste code |
| `git push` won't authenticate | Said "No" to gh git credentials | `gh auth setup-git` |
| `permission denied ... /var/run/docker.sock` | User not in `docker` group | `sudo usermod -aG docker $USER`, then `wsl --shutdown` in PowerShell |

## Tips
- Work in `~` (Linux filesystem), NOT `/mnt/c/...`, which is slow
- `wsl` commands go in PowerShell; Linux commands go in Ubuntu
- Group changes need a new session (`wsl --shutdown`)
- `code .` from Ubuntu opens VS Code connected to WSL

## Commands learned

wsl --install -d Ubuntu wsl -l -v wsl --shutdown
docker version docker run hello-world
gh auth login gh repo create

