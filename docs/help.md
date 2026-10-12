# Help output

The `--help` pages of Reason CLI 0.2.174, generated from the published package. The [command reference](commands.md) explains them with examples.

## reason

```text
Reason

  reason                 Open a session in this terminal
  reason "PROMPT"        Run a task on this computer, in this folder
  reason status          Your account and this computer's connection

Every command: reason help all
Sessions and chat: https://reasonmachines.com
```

## reason run

```text
Run Reason noninteractively

Usage:
  reason run --task TEXT [--repo OWNER/REPO] [--ref GIT_REF]
          [--model ID] [--reasoning-effort low|medium|high|xhigh] [--output-format text|json] [--detach]

By default, starts a cloud Session and waits for its outcome (this stays so
for scripts; the short `reason "PROMPT"` runs on this computer). --detach returns
the Session ID immediately. The agent never asks questions; when a person must
act, the Session ends with action required (exit 3). Text output prints only
the final result to stdout; progress goes to stderr. JSON output emits one
final machine-readable object.

While waiting, Ctrl+C cancels the Session gracefully (the agent stops and a
final workspace snapshot is saved) and keeps waiting until it ends. Press
Ctrl+C again to force stop it immediately, without that snapshot, and exit.

Authentication:
  Interactive machines use the Workspace selected by reason login.
  CI can set REASON_API_KEY. Set REASON_API_URL only for a non-production endpoint.

Exit codes:
  0  success (or a detached Session was accepted)
  1  task failure
  2  Reason, runtime, authentication, or usage failure
  3  action required
  130 interrupted locally (Ctrl+C)
```

## reason session

```text
Reason sessions

Usage:
  reason session list [--limit N] [--json]
  reason session start --prompt TEXT [--repo OWNER/REPO --provider github|gitlab] [--model ID] [--reasoning-effort low|medium|high|xhigh] [--json]
  reason session get SESSION_ID [--json]
  reason session watch SESSION_ID [--interval SECONDS] [--json]
  reason session send SESSION_ID --message TEXT [--json]
  reason session cancel SESSION_ID [--hard] [--json]
  reason session attachments SESSION_ID [--json]
  reason session download SESSION_ID [ATTACHMENT] [--output PATH] [--force] [--json]
  reason session share-link SESSION_ID [--label TEXT] [--expires-in SECONDS] [--json] < url

Every command uses the workspace of REASON_API_KEY when set, else the one selected
at `reason login`. Reason does not accept an organization override, another
user's identity, or arbitrary HTTP requests.
`session start` remains the detached compatibility spelling for existing
scripts; new noninteractive callers should use `reason run --detach`. Use
`start` without --repo for a scratch session; `session start` only creates
cloud sessions (run on this computer with `reason "PROMPT"`). `session cancel` stops the agent and tries to save a final workspace
snapshot first; `--hard` stops immediately without waiting for that snapshot
(the Session resumes from its last verified checkpoint) and upgrades a
cancellation that is already in progress.

A session started on this computer with `reason "PROMPT"` needs this computer
while it works. While no other command serves its folder, `watch` serves it
until the session finishes, and `send` serves it for the follow-up, following
it to its result like the prompt (Ctrl+C cancels, again to force stop). Keep
the terminal open. Watching a finished session, or one the command that
started it still serves, only reads it; a message to such a running session
is delivered there.

`attachments` lists the files a session kept (reports, patches, screenshots,
recordings) with their ids. `download` saves one, named by its id or its
file name, to --output (default: its own name in this folder), or every file
into the --output folder when no ATTACHMENT is given. An existing file is kept
unless --force is passed.

`share-link` reads one https URL from standard input, so it never appears in
shell history or the process list, and registers it with the session. The
agent sees only the printed reason-shared-link:// reference and can open it
once in the session's cloud browser; the URL itself is never shown again.
```

## reason connect

```text
Connect this computer to Reason

Usage:
  reason connect                 Your computer, kept connected in the background
  reason connect --to NAME       Add it to team machine NAME (as reason serve NAME --service)
  reason connect --token rmt_…   Join a machine with no login (as reason serve --token … --service)
  reason connect --once          Only while this command runs; nothing is saved
  reason connect --api-key KEY   Without reason login: a headless box, with an API key that has
                                 devices:write (or set REASON_API_KEY, which keeps it out of ps).
                                 The connection keeps the key to renew itself (macOS: Keychain).
  reason disconnect [NAME]       Stop and remove it (NAME: this computer's workers for machine NAME);
                                 it stays disconnected until you run reason connect

Settings, saved; run reason connect again to change them:
  --folder PATH                  Where Sessions may run (yours: your home folder; a machine: the current folder)
  --sessions N                   Sessions at once, 1-4 (yours: 4; a machine: 1)
  --name NAME                    This computer's name in Reason (default: its hostname)
  --api-url URL                  The Reason API to connect to (as REASON_API_URL)
  --proxy URL                    Reach Reason through this proxy (HTTPS_PROXY is not used)
  --proxy-ca-file PATH           The proxy's PEM CA bundle
  --user-chrome on|off           Let your Sessions use your own Chrome and its signed-in
                                 profiles (macOS, after `reason desktop install`; off by default)
  --cloud-sessions on|off        Let your own turns in cloud Sessions use this computer's
                                 desktop and browser (off by default)
  --local-logins on|off          Your Sessions run on the Claude Code, Codex and Grok logins
                                 on this computer (on by default; off with an API key).
                                 Reason gets each login's current access token, never
                                 its refresh token.

  --keep-awake on|off            macOS: keep this Mac awake while it runs Sessions (default on).
                                 Applies at once, without reconnecting.
  --add-folder PATH              Let Sessions run in another folder too, on the same computer in
  --remove-folder PATH           Reason; or stop that. Applies at once; running Sessions in other
                                 folders keep running. `reason status --json` lists the folders.

Your computer is Personal: it needs no machine and no admin role, and only you
start Sessions on it until you make it Team in Settings › Computers. They run commands
with your user permissions (there is no sandbox mode). It starts again at login (Linux:
at boot, when lingering is allowed), restarts if it fails, and `reason update`
restarts it on the new version. `reason status` shows it.

It is connected in the workspace you signed in to. It is also connected, by
itself, in up to 8 other workspaces you belong to, as each one's own computer,
when this computer was signed in with the setup command Reason shows you (it
ends in --pair CODE, a one-time code from your signed-in Reason) or by the
Reason app, and was connected after that. Signed in with `reason login`
alone, or connected with --api-key or --once, it stays in the one workspace:
run the setup command from Reason here, then `reason disconnect` and
`reason connect` if it was connected before. One sign-in covers one computer;
a second computer needs its own. `reason status` says when this applies.
```

## reason serve

```text
Share this computer as a machine

Usage:
  reason serve                     This computer, signed in (machine name: its hostname)
  reason serve --token rmt_…       Any server, no login: the token names the machine
  reason serve NAME [--dir PATH] [--once] [--sandbox] [--yes]
  reason serve [NAME | --token rmt_…] [--dir PATH] --service
  reason serve NAME --stop [--json]  (same as: reason stop NAME)

Adds this computer as a worker of a machine and waits for sessions. Start one
from anywhere with: reason "fix the flaky test" --on NAME
(To run one session here yourself, run `reason "PROMPT"` in the folder; it
needs no machine and serves only while that command runs.)
Sessions queue on the machine until one of its workers is free. Run
`reason serve` on more computers, or in more folders, to add workers.

Signed in, the machine is created if it doesn't exist (owners and admins).
Without a login, use a token from `reason machines token NAME`: --token, or
REASON_TOKEN. The token is used once to join and is never saved. Reconnect
later with `reason serve NAME` or the same command. Press Ctrl-C to stop.

With --service (macOS and Linux), this computer joins in the foreground, then
keeps the worker running in the background: it starts again at login (Linux:
at boot, when user lingering is allowed) and restarts if it fails. The service
runs `reason serve NAME --dir PATH` with this worker's saved identity; the
token is never saved. `reason stop NAME` stops and removes it.

Options:
  --dir PATH   Folder sessions work in (default: the current directory)
  --once       Keep the worker only while this command runs; nothing is saved
  --sandbox    Run confined. Sandboxed machines can't host sessions yet.
  --yes        Accept the first-run confirmation (required without a terminal;
               a token already carries it)
  --service    Keep this worker running as a background service (see above)
  --stop       Stop this computer's running workers for NAME and remove
               their background services. They stay in Reason and can
               start again; `reason disconnect NAME` removes them.

Sessions run commands in the folder with the permissions of the user who runs
the worker unless --sandbox is set. Settings live in
~/.reason/workers/NAME@<folder hash>/worker.json, one per folder, so every
folder served for NAME is its own worker.
```

## reason machines

```text
Manage machines: named queues of computers that run sessions

Usage:
  reason machines [--json]                   List machines
  reason machines token NAME [--json]        Create a token that adds servers to NAME
                                             (creates NAME if it doesn't exist)
  reason machines add NAME [--json]          Create a machine without a token
  reason machines rm NAME [--yes] [--json]   Remove it and disconnect its workers

Sessions started with `reason "…" --on NAME` wait in NAME's queue until one of
its workers is free. Add a worker by running `reason serve NAME` on a signed-in
computer, or the command `reason machines token NAME` prints on any server.
Sessions on a machine run with the permissions of the user who runs the worker.
Adding and removing machines and tokens requires a workspace owner or admin.
```

## reason desktop

```text
Reason desktop

Usage:
  reason desktop install [--permissions none|standard|all]
  reason desktop status [--json]

install  Add what a worker on this computer needs for a desktop, computer use
         and browser use: pinned Node.js, Open Computer Use, Open Browser Use
         with its browser extension, and on macOS Chrome for Testing and the
         ffmpeg desktop video uses, under ~/.local/share/reason/desktop. On
         Debian and Ubuntu it also installs the display, browser and recording
         packages with sudo, or prints the exact command. Safe to run again.
         When it makes a feature available, this computer's `reason connect`
         connection restarts so its worker serves it.
         --permissions  macOS only. Computer use and desktop video run as
                        Reason and share its grants. none (default): only
                        report missing ones. standard: ask now for
                        Accessibility and Screen Recording for Reason. all:
                        standard plus every other privacy pane.
status   Show which of desktop, computer use and browser use a worker can
         serve, and each component as missing, outdated, needs_permission or
         ready.
```

## reason device

```text
reason device is now reason connect.

Connect this computer to Reason

Usage:
  reason connect                 Your computer, kept connected in the background
  reason connect --to NAME       Add it to team machine NAME (as reason serve NAME --service)
  reason connect --token rmt_…   Join a machine with no login (as reason serve --token … --service)
  reason connect --once          Only while this command runs; nothing is saved
  reason connect --api-key KEY   Without reason login: a headless box, with an API key that has
                                 devices:write (or set REASON_API_KEY, which keeps it out of ps).
                                 The connection keeps the key to renew itself (macOS: Keychain).
  reason disconnect [NAME]       Stop and remove it (NAME: this computer's workers for machine NAME);
                                 it stays disconnected until you run reason connect

Settings, saved; run reason connect again to change them:
  --folder PATH                  Where Sessions may run (yours: your home folder; a machine: the current folder)
  --sessions N                   Sessions at once, 1-4 (yours: 4; a machine: 1)
  --name NAME                    This computer's name in Reason (default: its hostname)
  --api-url URL                  The Reason API to connect to (as REASON_API_URL)
  --proxy URL                    Reach Reason through this proxy (HTTPS_PROXY is not used)
  --proxy-ca-file PATH           The proxy's PEM CA bundle
  --user-chrome on|off           Let your Sessions use your own Chrome and its signed-in
                                 profiles (macOS, after `reason desktop install`; off by default)
  --cloud-sessions on|off        Let your own turns in cloud Sessions use this computer's
                                 desktop and browser (off by default)
  --local-logins on|off          Your Sessions run on the Claude Code, Codex and Grok logins
                                 on this computer (on by default; off with an API key).
                                 Reason gets each login's current access token, never
                                 its refresh token.

  --keep-awake on|off            macOS: keep this Mac awake while it runs Sessions (default on).
                                 Applies at once, without reconnecting.
  --add-folder PATH              Let Sessions run in another folder too, on the same computer in
  --remove-folder PATH           Reason; or stop that. Applies at once; running Sessions in other
                                 folders keep running. `reason status --json` lists the folders.

Your computer is Personal: it needs no machine and no admin role, and only you
start Sessions on it until you make it Team in Settings › Computers. They run commands
with your user permissions (there is no sandbox mode). It starts again at login (Linux:
at boot, when lingering is allowed), restarts if it fails, and `reason update`
restarts it on the new version. `reason status` shows it.

It is connected in the workspace you signed in to. It is also connected, by
itself, in up to 8 other workspaces you belong to, as each one's own computer,
when this computer was signed in with the setup command Reason shows you (it
ends in --pair CODE, a one-time code from your signed-in Reason) or by the
Reason app, and was connected after that. Signed in with `reason login`
alone, or connected with --api-key or --once, it stays in the one workspace:
run the setup command from Reason here, then `reason disconnect` and
`reason connect` if it was connected before. One sign-in covers one computer;
a second computer needs its own. `reason status` says when this applies.
```

## reason providers

```text
Reason providers

Usage:
  reason providers connect anthropic [--pair CODE]

Connect a Claude Pro or Max subscription to the current Reason workspace. Reason
opens Anthropic in your browser and receives the localhost callback on this
computer. No callback URL or token needs to be copied into Reason.
```

## reason update

```text
Usage: reason update [--check] [--quiet] [--json]

Install the latest Reason version the way this copy was installed (signed
managed runtime, install.sh, npm, or Bun). An installed desktop (reason desktop
install) is then brought to that version's requirements, without prompts.

Options:
  --check    Only compare the installed and latest versions; change nothing
  --quiet    Print nothing when already up to date (changes and errors still print)
  --json     With --check, print {installed, latest, update_available, install_method, channel}

Exit codes:
  0   Up to date, or updated (a version newer than the release counts as up to date)
  10  --check only: an update is available
  1   The check or update failed
  75  --update installed a version it could not verify; rerun the command

Aliases: reason upgrade, reason --upgrade

Before a session:
  reason update --quiet && reason "PROMPT"
  reason "PROMPT" --update       Update first, then start on the new version
  REASON_AUTO_UPDATE=1           Same as --update for reason "PROMPT", run, and serve
  REASON_NO_UPDATE_NOTICE=1      Turn off the daily "new version available" notice

If the release server cannot be reached, --update warns and starts the session
on the installed version.
```
