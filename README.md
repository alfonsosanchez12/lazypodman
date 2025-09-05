# 🐳 lazypodman

**`lazypodman`** is a Bash wrapper that makes it easy to launch [Lazydocker](https://github.com/jesseduffield/lazydocker) against **local** or **remote rootless Podman** instances using SSH-based tunnels.

- ✔️ Fancy interactive menu (Local / Remote / Tunnel-only)
- ✔️ Auto-configures SSH tunnels to `podman-remote` sockets
- ✔️ One-tunnel-per-remote with `--open`, `--persist`, `--stop`, `--restart`
- ✔️ Fully Docker-compatible (sets `DOCKER_HOST` + `DOCKER_API_VERSION`)
- ✔️ Self-contained: no installers or daemons required

> 📦 GitHub repo: https://github.com/alfonsosanchez12/lazypodman

---

## ⚙️ Prerequisites

Install the following on your **local system**:

| Tool             | Purpose                      | Install command                                   |
|------------------|-------------------------------|----------------------------------------------------|
| `bash`           | Script runner                | Included in all Unix systems                      |
| `docker`         | Optional: for other tooling  | `sudo apt install docker.io` or via Docker site   |
| `podman`         | Local container engine       | `sudo apt install podman` or `brew install podman`|
| `podman-remote`  | Connect to remote Podman     | Included with Podman 4+                           |
| `lazydocker`     | Docker/Podman TUI            | [Install instructions](https://github.com/jesseduffield/lazydocker#install) |
| `ssh`            | Tunnel creation              | Included on Linux/macOS                           |
| `jq`             | Parses `podman-remote` JSON  | `sudo apt install jq` or `brew install jq`        |

Optional:

| Tool      | Purpose                     |
|-----------|-----------------------------|
| `fzf`     | Fuzzy remote picker         |

---

## 🔐 Remote Podman Setup (one-time)

To manage **remote rootless Podman** instances, follow these steps.

### 1. Generate an SSH key

If you don't already have one:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519
ssh-copy-id user@remote-host
```

2. On the remote machine (as the same user):

Enable Podman’s rootless socket and lingering:

```bash
loginctl enable-linger $USER
systemctl --user enable --now podman.socket
```

Verify it's listening:

`curl --unix-socket $XDG_RUNTIME_DIR/podman/podman.sock http://d/v3.0.0/libpod/_ping`

3. Back on your local machine: create a podman-remote connection

```bash
podman-remote system connection add my-remote \
  --identity ~/.ssh/id_ed25519 \
  ssh://user@remote-host/run/user/1000/podman/podman.sock
```

Test it:

`podman-remote --connection my-remote info`

📥 Installation

No installer yet. For now, just:

```bash
curl -fsSL https://raw.githubusercontent.com/alfonsosanchez12/lazypodman/main/lazypodman > ~/.local/bin/lazypodman
chmod +x ~/.local/bin/lazypodman
```

Make sure ~/.local/bin is in your PATH. Add this to your ~/.bashrc or ~/.zshrc:

`export PATH="$HOME/.local/bin:$PATH"`

🧪 Usage
Interactive menu (recommended)
lazypodman

Option 1: Manage local rootless Podman

Option 2: Pick from remote connections (via podman-remote)

Option 3: Just open the tunnel (no Lazydocker)

CLI usage

```bash
lazypodman --local                     # Run against local Podman
lazypodman --remote my-remote         # Open tunnel and run Lazydocker
lazypodman --remote my-remote --persist  # Keep SSH tunnel alive after Lazydocker exits

lazypodman --open my-remote           # Just open the tunnel and print env exports
lazypodman --list-tunnels             # Show tunnels: age, PID, remote uptime
lazypodman --stop my-remote           # Close a specific tunnel
lazypodman --restart my-remote        # Restart and show DOCKER_HOST
lazypodman --kill-tunnels             # Kill all tunnels created by lazypodman
```

🌐 Example: remote connection
# Open tunnel only
`lazypodman --open my-remote`

# Export the env for docker/lazydocker
`export DOCKER_HOST=unix://$HOME/.lazypodman/my-remote.sock`
`export DOCKER_API_VERSION=1.41`

# Run
`docker ps`
`lazydocker`

🧼 Maintenance

List all tunnels
`lazypodman --list-tunnels`

Stop one
`lazypodman --stop my-remote`

Kill all
`lazypodman --kill-tunnels`

🛠️ To-Do

 ✅ Add --restart and --list-tunnels with PID/uptime ✔️

 ⏳ One-line installer (curl | sh)

 ⏳ Homebrew tap / AUR

 ⏳ Config file for defaults (API version, autossh, etc.)

📁 Project Structure
```bash
.
├── lazypodman                 # Main Bash script
├── README.md                  # You're here
└── LICENSE                    # MIT (suggested)
```

🧑‍💻 Author

Developed by Alfonso Sanchez

Contributions welcome!
