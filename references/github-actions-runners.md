# GitHub Actions Runners
Vojto's self-hosted runners all live on the Mac Studio. See `references/mac-studio.md` for how to reach that machine.
**Keep this file current.** Record new conventions and gotchas here rather than leaving them in the conversation.
***
## 🔑 Scoping
- GitHub scopes runners at repository, organization, and enterprise level only. There is no user-account level — `user/actions/runners` returns 404.
- Repos under the personal `vojto` account therefore need **one runner instance per repo**. A runner binds to its repo at registration and cannot be widened afterwards.
- One runner serving many repos requires moving them into an organization. The free plan supports org-level runners.
- Extra instances are cheap: ~200 MB and a launchd service each, sharing the machine's Homebrew, mise tools, and dependency caches. Don't avoid a second runner to save space.
***
## ➕ Adding a runner for a new repo
Run locally — the token is minted on the spot and expires in an hour, so it never gets stored.
```bash
TOKEN=$(gh api -X POST repos/<owner>/<repo>/actions/runners/registration-token -q .token)
ssh 100.81.82.82 "zsh -lc '
  set -e
  mkdir -p ~/actions-runners/<owner>-<repo>
  cd ~/actions-runners/<owner>-<repo>
  curl -fsSL -o r.tar.gz https://github.com/actions/runner/releases/download/v<VERSION>/actions-runner-osx-arm64-<VERSION>.tar.gz
  tar xzf r.tar.gz && rm r.tar.gz
  ./config.sh --url https://github.com/<owner>/<repo> --token $TOKEN \
    --name mac-studio-<repo> --labels mac-studio,<repo> --work _work --unattended
  ./svc.sh install && ./svc.sh start
'"
```
Confirm it registered with `gh api repos/<owner>/<repo>/actions/runners`. Get `<VERSION>` from the latest actions/runner release; 2.336.0 was current in July 2026.
***
## 📛 Conventions
- Directory `~/actions-runners/<owner>-<repo>`, runner name `mac-studio-<repo>`, custom labels `mac-studio,<repo>`.
- `self-hosted`, `macOS`, and `ARM64` are added automatically. Target all five in `runs-on`.
- `svc.sh install` writes a **user** LaunchAgent to `~/Library/LaunchAgents`, so no sudo — which matters because sudo on the Studio needs a password and can't run unattended. Check with `./svc.sh status`.
***
## 📝 Workflow conventions
- `runs-on: [self-hosted, macOS, ARM64, mac-studio, <repo>]`
- Pin action SHAs, set `permissions: contents: read` and `persist-credentials: false` on checkout.
- Install tools with `jdx/mise-action` (`cache: false`), driven by the repo's `.tool-versions`.
- Keep dependency caches outside `_work`, which checkout cleanup wipes: `~/actions-caches/<repo>/...`, or `~/actions-caches/shared/pnpm-store` when shared across repos.
- Use a non-cancelling `concurrency` group per contended resource. Jobs touching the same database must share one group, with `cancel-in-progress: false` so a new run never kills an in-flight deploy.
***
## 🔐 Deploy SSH keys
The runner runs as `vojto`, so `$HOME` **is** the Studio's own home directory. A job that writes `$HOME/.ssh/id_rsa` overwrites Vojto's key, and a cleanup step that `rm`s it destroys the real file — this is almost certainly what wiped the Studio's `known_hosts` (see `mac-studio.md`). `foodmark`'s deploy workflow still does this; don't copy that part.
Instead, load the key into an ssh-agent and append the host key only when missing. Kamal/net-ssh picks the agent up automatically:
```bash
deploy_host="${KAMAL_SSH_KNOWN_HOSTS%% *}"   # secret is a full known_hosts line
ssh-keygen -F "$deploy_host" > /dev/null || printf '%s\n' "$KAMAL_SSH_KNOWN_HOSTS" >> "$HOME/.ssh/known_hosts"
eval "$(ssh-agent -s)" > /dev/null
printf '%s\n' "$KAMAL_SSH_PRIVATE_KEY" | ssh-add - 2> /dev/null
echo "SSH_AUTH_SOCK=$SSH_AUTH_SOCK" >> "$GITHUB_ENV"
echo "SSH_AGENT_PID=$SSH_AGENT_PID" >> "$GITHUB_ENV"
```
Give each repo its own key (`~/.ssh/<repo>_github_actions`, ed25519, no passphrase) appended to the target's `authorized_keys`, and store it as the `KAMAL_SSH_PRIVATE_KEY` secret rather than reusing Vojto's personal key.
***
## ⚠️ Gotchas
- GitHub-hosted runners on the personal account are **unusable as of July 2026** — every `ubuntu-latest` job fails instantly with "recent account payments have failed or your spending limit needs to be increased". Self-hosted jobs are unaffected and don't consume minutes, so the Studio is the only working option until billing is fixed.
- A job whose labels match no registered runner sits `queued` rather than failing, and GitHub only gives up after 24 hours. Register the runner before pushing the workflow, or expect a pending run.
- GitHub evaluates `${{ }}` inside a composite action's **input descriptions**, so a description like "pass `${{ job.status }}`" fails the whole action with `Unrecognized named-value: 'job'`. `foodmark`'s `telegram-notify` still carries this bug; the growbase copy has it removed. Never write an expression in a description.
- A fresh checkout has none of the repo's gitignored generated files. Growbase's feature specs build the Vite bundle, which imports the generated `app/javascript/routes.js`, so the workflow runs `rake js:routes` before RSpec. Check for generated-but-ignored artifacts whenever tests pass locally and fail on the runner.
- The Studio's system mise can lag far behind the version `mise-action` pins, and the two resolve tools differently — a version that fails locally may be fine in CI. Reproduce CI problems with the pinned version, not the system one.
- Scheduled and `workflow_dispatch` workflows only run from the default branch. A workflow on a feature branch won't appear in the Actions UI or fire on cron.
- Add `workflow_dispatch` to scheduled workflows so they can be tested without waiting for the cron.
- Check what a job actually does before test-firing it. A workflow whose job sends mail, deploys, or posts externally will really do that on a manual dispatch.
