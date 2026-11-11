# Help output

The `--help` pages of Reason CLI 0.2.736, generated from the published package. The [command reference](commands.md) explains them with examples.

## reason

```text
Usage: reason "PROMPT" [options]

  reason "PROMPT"        Run a task on this computer, in this folder
  reason status          Show your account and this computer's connection

Options:
  --workspace SLUG|ID    Use a signed-in Workspace (or REASON_WORKSPACE)

Every command: reason help all
Sessions and chat: https://reasonmachines.com
Build an app or editor on Reason (agents: https://reasonmachines.com/llms.txt):
  https://docs.reasonmachines.com/docs/build/editor-app-acp
```

## reason run

```text
Run Reason noninteractively: start a cloud Session and wait for its result.

Usage:
  reason run "TASK" [--wait] [--json] [--repo OWNER/REPO] [--ref GIT_REF]
  reason run --task TEXT [--repo OWNER/REPO] [--ref GIT_REF] [--model ID]
            [--reasoning-effort EFFORT] [--output-format text|json] [--detach]

Arguments:
  TASK  The task, quoted as one argument (or --task TEXT)

Global Options:
  --workspace SLUG|ID  Use a signed-in Workspace (or REASON_WORKSPACE)
  --non-interactive    Never ask a question; fail with what to pass instead
  --no-color           No color or animation (also NO_COLOR)
  -h, --help           Show this help (--json: as JSON)
  --version            Print the version

Examples:
  # The one call for an agent: start, wait, print JSON
  reason run "fix the flaky test" --repo acme/api --json
  # Start and return the Session ID
  reason run --task "triage new issues" --detach

Notes:
  `reason run "TASK" --wait --json` is the one call for an agent: it starts a
  cloud Session, waits, and prints one JSON object with session_id, status,
  outcome, result, pr_url and url. Without --repo the Session has no repository:
  pass the repository the task is about. Follow up with
  `reason steer|queue|resume ID`.

  By default, starts a cloud Session and waits for its outcome (this stays so
  for scripts; the short `reason "PROMPT"` runs on this computer). --detach
  returns the Session ID immediately. The agent never asks questions; when a
  person must act, the Session ends with action required (exit 3). Text output
  prints only the final result to stdout; progress goes to stderr.

  While waiting, Ctrl+C cancels the Session gracefully (the agent stops and a
  final workspace snapshot is saved) and keeps waiting until it ends. Press
  Ctrl+C again to force stop it immediately, without that snapshot, and exit.

  Authentication: interactive machines use the Workspace selected by reason
  login. CI can set REASON_API_KEY. Set REASON_API_URL only for a non-production
  endpoint.

  When the server requires cloud activation, an interactive terminal opens
  Reason's web page and waits up to 15 minutes, then retries the refused
  submission once. Headless and JSON callers receive action_required with
  activation_url (exit 3). Checkout and payment confirmation stay on the web;
  Ctrl+C cancels the wait.

  Exit codes:
    0   success (or a detached Session was accepted)
    1   task failure
    2   Reason, runtime, authentication, or usage failure
    3   action required
    130 interrupted locally (Ctrl+C)

Learn more:
  reason session --help: watch, message and cancel a Session
```

## reason session

```text
List, start, watch, message, resume and cancel Reason sessions.

Usage:
  reason session list [--limit N] [--status active|archived|all]
            [--origin ORIGINS] [--fields NAMES] [--json]
  reason session start --prompt TEXT
            [--repo OWNER/REPO --provider github|gitlab] [--model ID]
            [--reasoning-effort EFFORT] [--json]
  reason session get SESSION_ID [--json]
  reason session watch SESSION_ID [--interval SECONDS] [--json]
  reason session send SESSION_ID [--message] TEXT [--queue] [--wait] [--json]
  reason session steer SESSION_ID TEXT [--wait] [--json]
  reason session queue SESSION_ID TEXT [--wait] [--json]
  reason session resume SESSION_ID [TEXT] [--wait] [--json]
  reason session cancel SESSION_ID [--hard] [--yes] [--json]
  reason session attachments SESSION_ID [--json]
  reason session download SESSION_ID [ATTACHMENT] [--output PATH] [--force]
            [--json]
  reason session share-link SESSION_ID [--label TEXT] [--expires-in SECONDS]
            [--json] < url

Commands:
  list                            Your sessions, newest first
  start --prompt TEXT             Start a cloud session and return at once
  get SESSION_ID                  One session's status and result
  watch SESSION_ID                Print its events until it finishes
  send SESSION_ID TEXT            Message it now, or start its next turn
  steer SESSION_ID TEXT           The same as send (reason steer)
  queue SESSION_ID TEXT           send --queue: deliver when the turn ends
  resume SESSION_ID [TEXT]        Continue a finished, failed or stopped one
  cancel SESSION_ID               Stop the agent
  attachments SESSION_ID          The files a session kept
  download SESSION_ID [ATTACHMENT]
                                  Save one file, or all of them
  share-link SESSION_ID < url     Let the agent open a URL once

Arguments:
  SESSION_ID  A session's id (reason session list shows them)
  TEXT        The message (or --message TEXT)
  ATTACHMENT  An attachment's id or file name

Options:
  --limit N                     How many sessions, at most 200 (default: 50)
  --status active|archived|all  Which sessions (default: active)
  --origin ORIGINS              Where they started: ui, external, external:cli,
                                external:mcp, external:api, or a comma-separated
                                list
  --fields NAMES                Only these properties, comma-separated
  --prompt TEXT                 The task (required)
  --repo OWNER/REPO             The repository; needs --provider
  --provider github|gitlab      Where --repo lives; needs --repo
  --model ID                    The model
  --reasoning-effort EFFORT     low, medium, high or xhigh (also minimal, max)
  --interval SECONDS            Seconds between polls, at most 60 (default: 2)
  --message TEXT                The message, when not given after the id
  --queue                       Hold it until the running turn ends
  --wait                        Follow the session and print its latest outcome
  --hard                        Stop at once, without the final snapshot
  --yes                         Cancel without asking
  --output PATH                 Where to save: a file, or a folder for all files
                                (default: its own name in this folder)
  --force                       Replace an existing file
  --label TEXT                  A name for the link
  --expires-in SECONDS          How long the link lasts, at most 86400
  --json                        Print JSON
  --output-format json          The same as --json

Global Options:
  --workspace SLUG|ID  Use a signed-in Workspace (or REASON_WORKSPACE)
  --non-interactive    Never ask a question; fail with what to pass instead
  --no-color           No color or animation (also NO_COLOR)
  -h, --help           Show this help (--json: as JSON)
  --version            Print the version

Examples:
  # Poll a few properties
  reason session list --fields title,status,finished_at --json
  # Message a running session and follow it
  reason steer SESSION_ID "also update the docs" --wait
  # Let the agent open a private URL
  echo https://example.com/report | reason session share-link SESSION_ID

Notes:
  Every command uses the workspace of REASON_API_KEY when set, else the one
  selected at `reason login`. Reason does not accept an organization override,
  another user's identity, or arbitrary HTTP requests.

  `session list --fields title,status,finished_at` lists only those properties
  of each Session, after its session_id. Made for polling: the last page is kept
  beside the login, and the server answers that it is unchanged without reading
  the list again until a listed property, or who can see a Session, changes; the
  page printed is at most 5 minutes old (10 seconds with usage, context,
  cost_usd, duration_ms or model).

  `session start` remains the detached compatibility spelling for existing
  scripts; new noninteractive callers should use `reason run --detach`. Use
  `start` without --repo for a scratch session. `session start` only creates
  cloud sessions (run on this computer with `reason "PROMPT"`).

  `session cancel` stops the agent and tries to save a final workspace snapshot
  first. `--hard` stops immediately without waiting for that snapshot (the
  Session resumes from its last verified checkpoint) and upgrades a cancellation
  that is already in progress.

  A session started on this computer with `reason "PROMPT"` needs this computer
  while it works. While no other command serves its folder, `watch` serves it
  until the session finishes, and `send` serves it for the follow-up, following
  it to its result like the prompt (Ctrl+C cancels, again to force stop). Keep
  the terminal open. Watching a finished session, or one the command that
  started it still serves, only reads it; a message to such a running session is
  delivered there.

  `send` hands the message to the running turn now (`reason steer`), or starts
  the next turn of a finished session. `--queue` (`reason queue`) holds it until
  the running turn ends instead; the printed message_id is the queued message.
  `resume` (`reason resume`) continues a finished, failed or stopped session:
  with a message as its next turn, without one by running its last turn again.

  `--wait` observes accepted delivery and execution, then prints the Session's
  latest outcome. Concurrent steers share that Session. If the restart or
  message delivery cannot be confirmed within the observation grace, it exits 2
  with status unconfirmed and no result; accepted work is not cancelled or
  resubmitted.

  `attachments` lists the files a session kept (reports, patches, screenshots,
  recordings) with their ids. `download` saves one, named by its id or its file
  name, to --output (default: its own name in this folder), or every file into
  the --output folder when no ATTACHMENT is given. An existing file is kept
  unless --force is passed.

  `share-link` reads one https URL from standard input, so it never appears in
  shell history or the process list, and registers it with the session. The
  agent sees only the printed reason-shared-link:// reference and can open it
  once in the session's cloud browser; the URL itself is never shown again.

Learn more:
  reason run --help: start a session and wait for its result
```

## reason connect

```text
Connect this computer to Reason, kept connected in the background.

Usage:
  reason connect [--folder PATH] [--name NAME] [--user-chrome on|off]
            [--local-logins on|off] [--minimal]
  reason connect --to NAME [--folder PATH] [--yes]
  reason connect --token rmt_… [--folder PATH]
  reason connect --once
  reason connect --api-key KEY
  reason connect --keep-awake on|off
  reason connect --add-folder PATH | --remove-folder PATH
  reason disconnect [NAME]

Options:
  --to NAME                Add this computer to team machine NAME (as reason
                           serve NAME --service)
  --token rmt_…            Join a machine with no login (as reason serve --token
                           … --service)
  --once                   Connect only while this command runs; nothing is
                           saved
  --api-key KEY            Without reason login: a headless box, with an API key
                           that has devices:write (or set REASON_API_KEY, which
                           keeps it out of ps). The connection keeps the key to
                           renew itself (macOS: Keychain).
  --folder PATH            Where Sessions may run (default: your home folder; a
                           machine: the current folder)
  --name NAME              This computer's name in Reason (1 to 160 characters)
                           (default: its hostname)
  --api-url URL            The Reason API to connect to (as REASON_API_URL)
  --proxy URL              Reach Reason through this proxy (HTTPS_PROXY is not
                           used)
  --proxy-ca-file PATH     With --proxy: the proxy's PEM CA bundle
  --user-chrome on|off     Your Sessions may use your own Chrome and its
                           signed-in profiles (macOS, through the Reason
                           extension) (default: on)
  --local-logins on|off    Your Sessions run on the Claude Code, Codex and Grok
                           logins on this computer (off by default with an API
                           key). Reason gets each login's current access token,
                           never its refresh token.
  --minimal                Keep this computer minimal: files, shell and
                           terminal, no desktop, computer use or browser use
                           (reason desktop install adds them)
  --keep-awake on|off      macOS: keep this Mac awake while it runs Sessions.
                           Applies at once, without reconnecting. Run it on its
                           own. (default: on)
  --add-folder PATH        Let Sessions run in another folder too, on the same
                           computer in Reason. Applies at once. Run it on its
                           own.
  --remove-folder PATH     Stop Sessions running in that folder. Applies at
                           once; running Sessions in other folders keep running.
                           Run it on its own.
  -y, --yes                With --to or --token: accept the machine's first-run
                           confirmation (required without a terminal)
  --sessions N             No longer needed: your computer runs every Session
                           you start (takes 1 to 4)
  --cloud-sessions on|off  No longer needed: Reason does not read it

Global Options:
  --workspace SLUG|ID  Use a signed-in Workspace (or REASON_WORKSPACE)
  --non-interactive    Never ask a question; fail with what to pass instead
  --no-color           No color or animation (also NO_COLOR)
  -h, --help           Show this help (--json: as JSON)
  --version            Print the version

Examples:
  # Connect your computer
  reason connect
  # Add it to a team machine
  reason connect --to build-box --folder ~/src
  # Let this Mac sleep
  reason connect --keep-awake off

Notes:
  Settings are saved; run reason connect again to change them.
  `reason status --json` lists the folders.

  Your computer is Personal: it needs no machine and no admin role, and only you
  start Sessions on it until you make it Team in Settings › Computers. They run
  commands with your user permissions (there is no sandbox mode). It starts
  again at login (Linux: at boot, when lingering is allowed), restarts if it
  fails, and `reason update` restarts it on the new version. `reason status`
  shows it.

  It is connected in the workspace you signed in to. It is also connected, by
  itself, in up to 8 other workspaces you belong to, as each one's own computer,
  when this computer was signed in with the setup command Reason shows you (it
  ends in --pair CODE, a one-time code from your signed-in Reason) or by the
  Reason app, and was connected after that. Signed in with `reason login` alone,
  or connected with --api-key or --once, it stays in the one workspace: run the
  setup command from Reason here, then `reason disconnect` and `reason connect`
  if it was connected before. One sign-in covers one computer; a second computer
  needs its own. `reason status` says when this applies.

  One computer serves several Reason accounts. Signed in as another account than
  the one it is connected for, reason connect connects it for that account too,
  as that account's own computer in the workspace it signed in to; the other
  accounts stay connected. That account's `reason logout` removes only its own.

  To stop it and remove it from Reason, run `reason disconnect`; it stays
  disconnected until you run reason connect. `reason disconnect NAME` does the
  same for this computer's workers for machine NAME.

Learn more:
  reason disconnect --help
  reason serve --help
```

## reason serve

```text
Share this computer as a machine: a worker that waits for Sessions.

Usage:
  reason serve [NAME] [--dir PATH] [--once] [--sandbox] [--yes]
  reason serve --token rmt_… [--dir PATH] [--once] [--sandbox]
  reason serve [NAME | --token rmt_…] [--dir PATH] --service
  reason serve NAME --stop [--yes] [--json]
  reason stop NAME [--yes] [--json]

Arguments:
  NAME  The machine (default: this computer's hostname). With a token, the token
        names it.

Options:
  --token rmt_…  Join with no login, using a token from
                 `reason machines token NAME` (or REASON_TOKEN). Used once to
                 join and never saved.
  --dir PATH     Folder Sessions work in (default: the current directory)
  --once         Keep the worker only while this command runs; nothing is saved
  --sandbox      Run confined. Sandboxed machines can't host Sessions yet.
  -y, --yes      Accept the first-run confirmation (required without a terminal;
                 a token already carries it). With --stop: confirm without
                 asking.
  --service      Keep this worker running as a background service (macOS and
                 Linux; not with --once)
  --stop         Stop this computer's running workers for NAME and remove their
                 background services. They stay in Reason and can start again;
                 `reason disconnect NAME` removes them.
  --json         With --stop: print the result as JSON

Global Options:
  --workspace SLUG|ID  Use a signed-in Workspace (or REASON_WORKSPACE)
  --non-interactive    Never ask a question; fail with what to pass instead
  --no-color           No color or animation (also NO_COLOR)
  -h, --help           Show this help (--json: as JSON)
  --version            Print the version

Examples:
  # Add this folder as a worker of machine build-box
  reason serve build-box --dir ~/src
  # Start a Session on it from anywhere
  reason "fix the flaky test" --on build-box
  # Join a server with no login, as a background service
  REASON_TOKEN=rmt_… reason serve --service

Notes:
  Adds this computer as a worker of a machine and waits for Sessions. Sessions
  queue on the machine until one of its workers is free. Run `reason serve` on
  more computers, or in more folders, to add workers. To run one Session here
  yourself, run `reason "PROMPT"` in the folder; it needs no machine and serves
  only while that command runs.

  Signed in, the machine is created if it doesn't exist (owners and admins).
  Without a login, use a token. Reconnect later with `reason serve NAME` or the
  same command. Press Ctrl-C to stop.

  With --service, this computer joins in the foreground, then keeps the worker
  running in the background: it starts again at login (Linux: at boot, when user
  lingering is allowed) and restarts if it fails. The service runs
  `reason serve NAME --dir PATH` with this worker's saved identity; the token is
  never saved. `reason stop NAME` stops and removes it.

  Sessions run commands in the folder with the permissions of the user who runs
  the worker unless --sandbox is set. Settings live in
  ~/.reason/workers/NAME@<folder hash>/worker.json, one per folder, so every
  folder served for NAME is its own worker.

Learn more:
  reason machines --help
```

## reason machines

```text
Manage machines: named queues of computers that run Sessions.

Usage:
  reason machines [ls] [--json]
  reason machines token NAME [--json]
  reason machines add NAME [--json]
  reason machines rm NAME [--yes] [--json]

Commands:
  ls          List machines (also: list; the default)
  token NAME  Create a token that adds servers to NAME (creates NAME if it
              doesn't exist)
  add NAME    Create a machine without a token
  rm NAME     Remove it and disconnect its workers (also: remove)

Arguments:
  NAME  A machine's name

Options:
  -y, --yes  Confirm without asking (required without a terminal)
  --json     Print the result as JSON

Global Options:
  --workspace SLUG|ID  Use a signed-in Workspace (or REASON_WORKSPACE)
  --non-interactive    Never ask a question; fail with what to pass instead
  --no-color           No color or animation (also NO_COLOR)
  -h, --help           Show this help (--json: as JSON)
  --version            Print the version

Examples:
  # Make a machine and a token to add servers to it
  reason machines token build-box
  # Start a Session on it
  reason "fix the flaky test" --on build-box

Notes:
  Sessions started with `reason "…" --on NAME` wait in NAME's queue until one of
  its workers is free. Add a worker by running `reason serve NAME` on a
  signed-in computer, or the command `reason machines token NAME` prints on any
  server. Sessions on a machine run with the permissions of the user who runs
  the worker.

  Adding and removing machines and tokens requires a workspace owner or admin.

Learn more:
  reason serve --help
```

## reason desktop

```text
Install and check what this computer needs for desktop, computer use and browser
use.

Usage:
  reason desktop install [--permissions none|standard|all]
  reason desktop minimal
  reason desktop status [--json]
  reason desktop browser-use [--json]
  reason desktop mentions --json

Commands:
  install          Install what a worker needs for a desktop, computer use and
                   browser use
  minimal          Keep this computer minimal: files, shell and terminal only
  status           Which features a worker can serve, and each component's state
  browser-use      macOS: whether browser use in your own Chrome is ready
  mentions --json  What Sessions on this Mac can mention

Options:
  --permissions none|standard|all
                                  macOS only, as `reason permissions allow`
                                  without waiting. none: only report missing
                                  ones; macOS asks at first computer use.
                                  standard: ask for Screen Recording and
                                  Accessibility. all: standard plus Full Disk
                                  Access. (default: none)
  --json                          Print the result as JSON (mentions requires
                                  it)

Global Options:
  --workspace SLUG|ID  Use a signed-in Workspace (or REASON_WORKSPACE)
  --non-interactive    Never ask a question; fail with what to pass instead
  --no-color           No color or animation (also NO_COLOR)
  -h, --help           Show this help (--json: as JSON)
  --version            Print the version

Examples:
  # Install and ask for macOS permissions
  reason desktop install --permissions standard
  # See what is ready
  reason desktop status

Notes:
  install: every install and update sets these up unless this computer is
  minimal or only on Reason Network (installed with `install.sh … network`, no
  worker). It installs pinned Node.js, Open Computer Use, Open Browser Use with
  its browser extension, and on macOS and Windows Chrome for Testing, ffmpeg and
  ffprobe, under ~/.local/share/reason/desktop. On Debian and Ubuntu it also
  installs the display, browser and recording packages with sudo, or prints the
  exact command. Safe to run again; undoes minimal. When it makes a feature
  available, this computer's `reason connect` connection restarts so its worker
  serves it.

  minimal: installs and updates no longer add desktop components (an installed
  profile stays and is kept current).

  status shows which of desktop, computer use and browser use a worker can
  serve, and each component as missing, outdated, needs_permission or ready.

  browser-use (macOS) reports ready (a profile has Reason's Chrome extension
  connected), needs_setup or unavailable.

  mentions lists running apps and the tabs of Chrome profiles connected to
  Reason. It reads no page contents.

Learn more:
  reason permissions --help
```

## reason device

```text
reason device is now reason connect.

Connect this computer to Reason, kept connected in the background.

Usage:
  reason connect [--folder PATH] [--name NAME] [--user-chrome on|off]
            [--local-logins on|off] [--minimal]
  reason connect --to NAME [--folder PATH] [--yes]
  reason connect --token rmt_… [--folder PATH]
  reason connect --once
  reason connect --api-key KEY
  reason connect --keep-awake on|off
  reason connect --add-folder PATH | --remove-folder PATH
  reason disconnect [NAME]

Options:
  --to NAME                Add this computer to team machine NAME (as reason
                           serve NAME --service)
  --token rmt_…            Join a machine with no login (as reason serve --token
                           … --service)
  --once                   Connect only while this command runs; nothing is
                           saved
  --api-key KEY            Without reason login: a headless box, with an API key
                           that has devices:write (or set REASON_API_KEY, which
                           keeps it out of ps). The connection keeps the key to
                           renew itself (macOS: Keychain).
  --folder PATH            Where Sessions may run (default: your home folder; a
                           machine: the current folder)
  --name NAME              This computer's name in Reason (1 to 160 characters)
                           (default: its hostname)
  --api-url URL            The Reason API to connect to (as REASON_API_URL)
  --proxy URL              Reach Reason through this proxy (HTTPS_PROXY is not
                           used)
  --proxy-ca-file PATH     With --proxy: the proxy's PEM CA bundle
  --user-chrome on|off     Your Sessions may use your own Chrome and its
                           signed-in profiles (macOS, through the Reason
                           extension) (default: on)
  --local-logins on|off    Your Sessions run on the Claude Code, Codex and Grok
                           logins on this computer (off by default with an API
                           key). Reason gets each login's current access token,
                           never its refresh token.
  --minimal                Keep this computer minimal: files, shell and
                           terminal, no desktop, computer use or browser use
                           (reason desktop install adds them)
  --keep-awake on|off      macOS: keep this Mac awake while it runs Sessions.
                           Applies at once, without reconnecting. Run it on its
                           own. (default: on)
  --add-folder PATH        Let Sessions run in another folder too, on the same
                           computer in Reason. Applies at once. Run it on its
                           own.
  --remove-folder PATH     Stop Sessions running in that folder. Applies at
                           once; running Sessions in other folders keep running.
                           Run it on its own.
  -y, --yes                With --to or --token: accept the machine's first-run
                           confirmation (required without a terminal)
  --sessions N             No longer needed: your computer runs every Session
                           you start (takes 1 to 4)
  --cloud-sessions on|off  No longer needed: Reason does not read it

Global Options:
  --workspace SLUG|ID  Use a signed-in Workspace (or REASON_WORKSPACE)
  --non-interactive    Never ask a question; fail with what to pass instead
  --no-color           No color or animation (also NO_COLOR)
  -h, --help           Show this help (--json: as JSON)
  --version            Print the version

Examples:
  # Connect your computer
  reason connect
  # Add it to a team machine
  reason connect --to build-box --folder ~/src
  # Let this Mac sleep
  reason connect --keep-awake off

Notes:
  Settings are saved; run reason connect again to change them.
  `reason status --json` lists the folders.

  Your computer is Personal: it needs no machine and no admin role, and only you
  start Sessions on it until you make it Team in Settings › Computers. They run
  commands with your user permissions (there is no sandbox mode). It starts
  again at login (Linux: at boot, when lingering is allowed), restarts if it
  fails, and `reason update` restarts it on the new version. `reason status`
  shows it.

  It is connected in the workspace you signed in to. It is also connected, by
  itself, in up to 8 other workspaces you belong to, as each one's own computer,
  when this computer was signed in with the setup command Reason shows you (it
  ends in --pair CODE, a one-time code from your signed-in Reason) or by the
  Reason app, and was connected after that. Signed in with `reason login` alone,
  or connected with --api-key or --once, it stays in the one workspace: run the
  setup command from Reason here, then `reason disconnect` and `reason connect`
  if it was connected before. One sign-in covers one computer; a second computer
  needs its own. `reason status` says when this applies.

  One computer serves several Reason accounts. Signed in as another account than
  the one it is connected for, reason connect connects it for that account too,
  as that account's own computer in the workspace it signed in to; the other
  accounts stay connected. That account's `reason logout` removes only its own.

  To stop it and remove it from Reason, run `reason disconnect`; it stays
  disconnected until you run reason connect. `reason disconnect NAME` does the
  same for this computer's workers for machine NAME.

Learn more:
  reason disconnect --help
  reason serve --help
```

## reason providers

```text
Connect a Claude Pro or Max subscription to the current Reason workspace.

Usage:
  reason providers connect anthropic [--pair CODE] [--browser]
  reason providers connect anthropic --token-stdin < token.txt

Commands:
  connect anthropic  Sign in to Anthropic and save the subscription to this
                     workspace

Options:
  --pair CODE    Finish a pairing the Reason app started with this code
  --browser      Use Reason's own browser sign-in, even where Claude Code's
                 would run
  --token-stdin  Save a `claude setup-token` token read from standard input (not
                 with --browser)

Global Options:
  --workspace SLUG|ID  Use a signed-in Workspace (or REASON_WORKSPACE)
  --non-interactive    Never ask a question; fail with what to pass instead
  --no-color           No color or animation (also NO_COLOR)
  -h, --help           Show this help (--json: as JSON)
  --version            Print the version

Examples:
  # Sign in and connect
  reason providers connect anthropic
  # Add an account from a token you made
  reason providers connect anthropic --token-stdin < token.txt

Notes:
  Where your workspace has it on (it is rolling out) and Claude Code is
  installed, Reason signs in with Claude Code's own `claude setup-token`, run
  out of sight with a config folder of its own, so your Claude Code login is
  never read or changed. claude.ai opens in your browser; sign in to the Claude
  account to connect. If claude.ai shows a code instead of finishing, paste it
  here. Reason saves the long-lived token it makes.

  Without Claude Code, or with --browser, Reason opens Anthropic in your browser
  and receives the localhost callback on this computer. That sign-in can be
  refreshed and shows the subscription's usage in Reason; Claude Code's token
  can't show usage.

  With --token-stdin, Reason saves a token you already made with
  `claude setup-token` (sk-ant-oat...), read from standard input, as a new
  account instead of signing in. Run it once per token to add several accounts.
```

## reason update

```text
Install the latest Reason version the way this copy was installed.

Usage:
  reason update [--check [--json]] [--quiet]

Options:
  --check      Only compare the installed and latest versions; change nothing
  -q, --quiet  Print nothing when already up to date (changes and errors still
               print)
  --json       With --check: print {installed, latest, update_available,
               install_method, channel}

Global Options:
  --workspace SLUG|ID  Use a signed-in Workspace (or REASON_WORKSPACE)
  --non-interactive    Never ask a question; fail with what to pass instead
  --no-color           No color or animation (also NO_COLOR)
  -h, --help           Show this help (--json: as JSON)
  --version            Print the version

Examples:
  # See whether a newer version is out
  reason update --check
  # Update before a session
  reason update --quiet && reason "PROMPT"
  # Update first, then start on the new version
  reason "PROMPT" --update

Notes:
  reason upgrade and reason --upgrade are the same command.

  It updates through whatever installed this copy: the signed managed runtime,
  install.sh, npm or Bun. Then it installs desktop components, or brings them up
  to what that version needs, without asking. It skips them on a minimal
  computer (reason desktop minimal) or on one that is only on Reason Network
  (installed with install.sh … network, running no worker).

  Exit codes:
    0   Up to date, or updated (newer than the release counts as up to date)
    10  --check only: an update is available
    1   The check or update failed
    75  --update installed a version it could not verify; rerun the command

  Environment:
    REASON_AUTO_UPDATE=1       Same as --update for reason "PROMPT", run and
                               serve
    REASON_NO_UPDATE_NOTICE=1  Turn off the daily "new version available" notice

  If the release server cannot be reached, --update warns and starts the session
  on the installed version.
```
