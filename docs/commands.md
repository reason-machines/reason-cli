# Command reference

Taken from the help output of Reason CLI 0.2.591. Run `reason --help`, or `--help` after any command, for the version you have installed; [help.md](help.md) has every help page as published.

- [Start a session](#start-a-session)
- [Follow and steer](#follow-and-steer)
- [Connect this computer](#connect-this-computer)
- [Machines](#machines)
- [Desktop, computer use and browser use](#desktop-computer-use-and-browser-use)
- [Device (macOS)](#device-macos)
- [Account and install](#account-and-install)
- [Model providers](#model-providers)
- [Scripts and CI](#scripts-and-ci)
- [Environment variables](#environment-variables)

## Start a session

```text
reason                      Open a session in this terminal and type prompts
reason --resume ID          Open session ID in this terminal and continue it
reason "PROMPT"             Run on this computer, in this folder; prints the result
reason "PROMPT" --cloud     Run in Reason's cloud (repo: this checkout's origin)
reason "PROMPT" --on NAME   Run on machine NAME, queued for a free worker
```

Without a prompt, the same flags open an interactive session: `reason`, `reason --cloud`, `reason --on NAME`. Inside a session, `?` lists the keys and `/` lists the commands (`/machine` moves between computers).

On this computer, the agent thinks in the cloud and its commands run here, in the current folder, with your user permissions. The first run sets the folder up and asks you to confirm; later runs reuse it. Keep the terminal open while the session works.

| Option | |
| --- | --- |
| `--cloud` | Run in Reason's cloud (same as `--on cloud`) |
| `--on NAME` | Run on machine NAME, queued for a free worker |
| `--dir PATH` | Folder on this computer (default: the current directory) |
| `-y`, `--yes` | Confirm running commands on this computer without a terminal (first run only) |
| `-d`, `--detach` | Print the session ID and return (`--cloud` or `--on` only) |
| `--repo OWNER/REPO` | Cloud repository (default: this checkout's GitHub/GitLab origin) |
| `--no-repo` | Cloud session without a repository |
| `--update` | Update Reason first, then start on the new version |
| `--model ID` | Model to use |
| `--reasoning-effort LEVEL` | `low`, `medium`, `high` or `xhigh` |
| `--output-format text\|json` | Result format |

Ctrl+C cancels the session gracefully; press it again to force stop.

## Follow and steer

```text
reason ls                   List recent sessions
reason watch ID             Follow a session until it finishes
reason send ID "MESSAGE"    Send a follow-up message
reason cancel ID [--hard]   Cancel a session (--hard: stop now, skip the final snapshot)
```

A session that runs on this computer uses it again for `watch` and `send`, so keep the terminal open.

`reason session` holds every session command, each with `--json`:

```text
reason session list [--limit N]
reason session start --prompt TEXT [--repo OWNER/REPO --provider github|gitlab] [--model ID] [--reasoning-effort low|medium|high|xhigh]
reason session get SESSION_ID
reason session watch SESSION_ID [--interval SECONDS]
reason session send SESSION_ID --message TEXT
reason session cancel SESSION_ID [--hard]
reason session share-link SESSION_ID [--label TEXT] [--expires-in SECONDS] < url
```

- `session start` creates cloud sessions only and returns immediately. New scripts should prefer `reason run --detach`.
- `session cancel` stops the agent and saves a final workspace snapshot first; `--hard` stops immediately, and the session resumes from its last verified checkpoint.
- `share-link` reads one https URL from standard input, so it never appears in shell history, and registers it with the session. The agent sees only a `reason-shared-link://` reference and can open it once in the session's cloud browser.

Every command uses the workspace of `REASON_API_KEY` when set, otherwise the one selected at `reason login`.

## Connect this computer

Keep your own computer available for your sessions, in the background:

```text
reason connect                 Your computer, kept connected in the background
reason connect --to NAME       Add it to team machine NAME (as reason serve NAME --service)
reason connect --token rmt_…   Join a machine with no login (as reason serve --token … --service)
reason connect --once          Only while this command runs; nothing is saved
reason disconnect [NAME]       Stop and remove it (NAME: this computer's workers for machine NAME)
```

Saved settings (run `reason connect` again to change them):

| Option | |
| --- | --- |
| `--folder PATH` | Where sessions may run (yours: your home folder; a machine: the current folder) |
| `--sessions N` | Sessions at once, 1–4 (yours: 4; a machine: 1) |
| `--sandbox` / `--no-sandbox` | Run confined, or with your user permissions (the default) |
| `--yes` | Confirm without a terminal |

Your computer is yours alone: only you start sessions on it, by picking it as the machine in Reason. It starts again at login (Linux: at boot, when lingering is allowed), restarts if it fails, and `reason update` restarts it on the new version.

## Machines

A machine is a named queue of computers that run sessions. Sessions started with `--on NAME` wait in NAME's queue until one of its workers is free.

```text
reason machines                  List machines, workers online, queued and running sessions
reason machines token NAME       Create a token that adds servers to NAME (creates NAME)
reason machines add NAME         Create a machine without a token
reason machines rm NAME [--yes]  Remove it and disconnect its workers
```

Adding and removing machines and tokens requires a workspace owner or admin.

### `reason serve`

Adds this computer as a worker of a machine and waits for sessions.

```text
reason serve                       This computer, signed in (machine name: its hostname)
reason serve --token rmt_…         Any server, no login: the token names the machine
reason serve NAME [--dir PATH] [--once] [--sandbox] [--yes]
reason serve [NAME | --token rmt_…] [--dir PATH] --service
reason stop NAME                   Stop this computer's workers for NAME and remove their services
```

| Option | |
| --- | --- |
| `--dir PATH` | Folder sessions work in (default: the current directory) |
| `--once` | Keep the worker only while this command runs; nothing is saved |
| `--sandbox` | Run confined |
| `--yes` | Accept the first-run confirmation (required without a terminal; a token already carries it) |
| `--service` | Keep the worker running in the background (macOS and Linux): starts at login, restarts if it fails |

The token (from `--token` or `REASON_TOKEN`) is used once to join and is never saved. Each folder served for NAME is its own worker; run `reason serve` on more computers or in more folders to add workers. Sessions run with the permissions of the user who runs the worker unless `--sandbox` is set.

## Desktop, computer use and browser use

```text
reason desktop install [--permissions none|standard|all]
reason desktop status [--json]
```

`install` adds what a worker on this computer needs for a desktop, computer use and browser use: a pinned Node.js, Open Computer Use, Open Browser Use with its browser extension, and Chrome for Testing on macOS. On Debian and Ubuntu it also installs the display, browser and recording packages with sudo, or prints the exact command. Safe to run again.

`--permissions` (macOS only): `none` (default) asks at first computer use; `standard` asks now for Accessibility and Screen Recording; `all` also asks for every other privacy pane.

`status` shows which of desktop, computer use and browser use a worker can serve, and each component as `missing`, `outdated`, `needs_permission` or `ready`.

## Device (macOS)

```text
reason device [--name NAME] [--no-sandbox]
reason device connect [--name NAME]
reason device status
reason device disconnect
```

Connects this Mac to the workspace selected by `reason login`. The first run authorizes Device execution, enrolls the computer and installs the background worker. Device work is confined to `~/.reason/workspace` unless you attach another folder; `--no-sandbox` lets Reason use your login shell and environment with your account's authority.

## Account and install

```text
reason login                Sign in to Reason in your browser
reason logout               Sign this computer out
reason whoami               Show the signed-in account and organization
reason status [--json]      Show version, account, this computer's connection and automatic-update status
reason update               Install the latest Reason version
reason update --check       Only check (exit 10 when an update is available)
reason update --quiet       Print nothing when already up to date
```

`reason login` uses a short-lived browser pairing and stores a revocable CLI login. It does not scan local agent configuration, environment files, browser data or secrets.

`reason update` installs the latest version the same way this copy was installed (install.sh, npm or Bun), then brings an installed desktop up to that version's requirements. Aliases: `reason upgrade`, `reason --upgrade`.

| `reason update` exit code | |
| --- | --- |
| `0` | Up to date, or updated |
| `10` | `--check` only: an update is available |
| `1` | The check or update failed |
| `75` | Installed a version it could not verify; rerun the command |

## Model providers

```text
reason providers connect anthropic
```

Connects a Claude Pro or Max subscription to the current Reason workspace. Reason opens Anthropic in your browser and receives the callback on this computer; nothing needs to be copied.

## Scripts and CI

See [Scripts and CI](scripts-and-ci.md) for `reason run`, JSON output and exit codes.

## Environment variables

| Variable | |
| --- | --- |
| `REASON_API_KEY` | Sign in without a browser; selects that key's workspace |
| `REASON_TOKEN` | Machine token for `reason serve` (instead of `--token`) |
| `REASON_AUTO_UPDATE=1` | Update before every session (same as `--update`) |
| `REASON_NO_UPDATE_NOTICE=1` | Hide the daily "new version available" notice |
