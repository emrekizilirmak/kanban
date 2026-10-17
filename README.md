# Kanban

A lightweight, single-user Kanban board. One Go binary serves the API and the
embedded React frontend and stores everything in one SQLite file. No Docker,
no external services.

- Four fixed columns: Tasks, To Do, Doing, Done
- Drag and drop (mouse, touch, keyboard), one-time sort by priority or due date
- Cards with description, labels, priority and due date; overdue dates in red
- Label filter (Any / All), light and dark themes, Turkish UI
- Password login from an environment variable, server-side sessions

**Stack:** Go (`net/http`, `modernc.org/sqlite`) · React 19 + TypeScript + Vite · dnd-kit

## Build and run

Requires Go 1.22+, Node.js 20.19+ and `make` (for building only).

```sh
make build                  # builds the frontend, then the static ./kanban binary
read -rs KANBAN_PASSWORD && export KANBAN_PASSWORD
./kanban                    # http://localhost:8080
```

```sh
make test                   # Go and frontend tests
make dev                    # Go API + Vite dev server (http://localhost:5173)
make license-check          # all dependencies must be permissively licensed
```

## Configuration

| Variable | Default | |
|---|---|---|
| `KANBAN_PASSWORD` | required | login password |
| `KANBAN_ADDR` | `:8080` | listen address |
| `KANBAN_DB` | `./data/kanban.db` | SQLite file |
| `KANBAN_COOKIE_SECURE` | `false` | `true` when served over HTTPS |

## Deployment

Runs as a systemd service under an unprivileged user; see
[`deploy/`](deploy/) for the unit file and the config template.
[`deploy/SYSTEM_CHANGES.md`](deploy/SYSTEM_CHANGES.md) lists every step of the
installation, [`deploy/UNINSTALL.md`](deploy/UNINSTALL.md) how to undo it.

The app listens on `127.0.0.1:8080` and is reached through an SSH tunnel:

```sh
ssh -N -L 8080:127.0.0.1:8080 your-vps    # then open http://localhost:8080
```

Plain HTTP sends the password unencrypted, so never expose the port directly:
use a tunnel, a VPN or a TLS reverse proxy (with `KANBAN_COOKIE_SECURE=true`).

To update, build again, replace `/usr/local/bin/kanban` and run
`systemctl restart kanban`. Database migrations run automatically.
Back up `/var/lib/kanban/kanban.db` before updating.

## More

[DECISIONS.md](DECISIONS.md) · [THIRD_PARTY_LICENSES.md](THIRD_PARTY_LICENSES.md) · MIT licensed ([LICENSE](LICENSE))
