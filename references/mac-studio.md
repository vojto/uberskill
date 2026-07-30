# Mac Studio
Vojto's always-on Mac Studio, reachable over Tailscale. Use it for long-running builds, Docker and Kamal deployments, and self-hosted services.
**Keep this file current.** Whenever you learn something new about the Mac Studio — a service it runs, a path, a gotcha, a tool that is or isn't installed — record it here rather than leaving it in the conversation.
***
## 🔌 Access
- Tailscale IP: `100.81.82.82`, IPv6 `fd7a:115c:a1e0::7235:5252`.
- MagicDNS name: `mac-studio.taila816f8.ts.net`.
- User `vojto`, public key `~/.ssh/id_rsa`, already authorized. No password prompt.
- Confirm the host is up with `tailscale status`; it appears as `mac-studio`.
```bash
ssh 100.81.82.82 'hostname'                  # works
ssh mac-studio.taila816f8.ts.net 'hostname'  # works
ssh mac-studio                               # broken, see below
```
The local `~/.ssh/config` `Host mac-studio` block points at `vojtechs-mac-studio.taila816f8.ts.net`, which does not resolve. Use the IP or the correct FQDN until that block is fixed.
**Git over SSH from the Studio.** Its `~/.ssh/known_hosts` had gone missing (only a stale `known_hosts.old`), so every `git@github.com:` fetch died with `Host key verification failed`. Restored 2026-07-27 by appending verified github.com keys. If it disappears again, re-add and check the fingerprints against GitHub's published values before trusting them:
```bash
ssh 100.81.82.82 'zsh -lc "ssh-keyscan -t rsa,ecdsa,ed25519 github.com > /tmp/k && ssh-keygen -lf /tmp/k"'
# RSA uNiVztksCsDhcc0u9e8BujQXVUpKZIDTMczCvj3tD2s
# ECDSA p2QAMXNIC1TJYWeIOttrVc98/R1BUFWu3/LiyKgUfQM
# ED25519 +DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU
```
This matters for any audit of "is this repo pushed?" — without it, `git fetch` fails and stale remote-tracking refs give false answers.
The `team-reflect/*` GitHub repos return `Repository not found` — that account no longer has access. Local checkouts of `reflect`, `reflect-mobile`, `reflect-ipad`, `reflect-firebase`, `reflect-publish`, `audio-recorder`, `live-transcription`, and `wa-sqlite-kysely` cannot be verified against a remote and may be the only surviving copies.
***
## 🖥️ Machine
- Local hostname `Studio1.local`, Apple M2 Max, 12 cores, 32 GB RAM, arm64.
- macOS 26.3, build 25D125.
- 460 GB volume with roughly 81 GB free.
- Stays up for weeks. Treat it as a persistent server, not a scratch box.
***
## 🛠️ Environment
A plain `ssh host 'cmd'` runs a non-login shell with a minimal PATH that misses Homebrew tools. Wrap remote commands in a login shell:
```bash
ssh 100.81.82.82 'zsh -lc "your command here"'
```
- Homebrew lives at `/opt/homebrew`.
- Installed: `git`, `node`, `npm`, `pnpm`, `python3`, `brew`, `docker`, `claude`, `gh`, `go`, `swift`, `xcodebuild`, `mise`.
- `sudo` requires a password, so privileged steps cannot run unattended.
***
## 📁 Layout
- `~/Code` holds the project checkouts. Cleaned out 2026-07-27: 97 entries → 61, 13 GB → 7.3 GB. Everything removed was verified fully pushed to a live remote with a clean tree. What remains is deliberately kept — repos with uncommitted work, unpushed commits, stashes, no remote, no git at all, or a dead remote. Do not bulk-delete from `~/Code` without re-auditing.
- `~/Code/whisper-rails` is not a source checkout; it currently contains only `tmp/`. Whisper Rails Kamal deploys build from the laptop checkout and target Docker on the Studio, so the laptop Docker engine must be running for the image build.
- `~/Library/Caches` is 21 GB and is the biggest reclaimable target on the machine, well ahead of `~/Code`.
- `~/actions-runners` and `~/actions-caches` back the self-hosted GitHub Actions runners.
- `~/Scripts`, `~/Sync`, `~/conductor`, `~/clawd`, and `~/Moss` are other working directories.
- `~/private_keys` holds keys. Don't read or copy them without an explicit ask.
***
## 🏃 GitHub Actions runners
One runner instance per repo, under `~/actions-runners/<owner>-<repo>`, each a user LaunchAgent named `actions.runner.<dir>.<name>`. Dependency caches sit in `~/actions-caches/<repo>` and `~/actions-caches/shared`. Currently registered: `vojto-foodmark`, `vojto-axios-emails`, `vojto-growbase` (added 2026-07-28; `growbase` label, deploys to Hetzner on push to main, full run ≈2.5 min).
See `references/github-actions-runners.md` for the conventions and the procedure for adding one.
***
## 🐳 Services
- OrbStack provides Docker and binds ports 80 and 443. Its VM can be **stopped while the GUI app is still running** — `docker info` fails with "no such file or directory" on `/var/run/docker.sock` and `open -a OrbStack` does nothing because the app is already up. Start the engine with `orb start`; it reports "timed out waiting for VM to start" but Docker is usually alive seconds later, so check `docker info` before believing the error. Once running, `/var/run/docker.sock` symlinks to `~/.orbstack/run/docker.sock` and the `default` context works — no need to switch to the `orbstack` context.
- Kamal deploys run here: `kamal-proxy` fronts the `whisper-rails` and `whisper-memos-advisor` containers pulled from `ghcr.io/vojto`.
- Postgres on `127.0.0.1:5432` and Redis on `127.0.0.1:6379`, both loopback only. The `postgres` superuser role accepts password `postgres`, which is what CI jobs use. `postgresql@18` is keg-only, so `psql` and `pg_isready` are **not on PATH** — call them at `/opt/homebrew/opt/postgresql@18/bin/`.
- Inspect what's running with `ssh 100.81.82.82 'docker ps'`.
