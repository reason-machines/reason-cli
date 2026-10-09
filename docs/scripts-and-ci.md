# Scripts and CI

Anything that can run a command can start a Reason session: a CI step that fixes a failing test, a cron job, a git hook, or your own internal tools.

## Sign in without a browser

Create an API key at [reasonmachines.com/customize?tab=api](https://reasonmachines.com/customize?tab=api) and set it in the job's secrets:

```sh
export REASON_API_KEY=reason_…
```

## `reason run`

```text
reason run --task TEXT [--repo OWNER/REPO] [--ref GIT_REF]
           [--model ID] [--reasoning-effort low|medium|high|xhigh]
           [--output-format text|json] [--detach]
```

`reason run` starts a cloud session and waits for its outcome. The agent never asks questions; when a person must act, the session ends with "action required" (exit `3`).

- Text output prints only the final result to stdout; progress goes to stderr.
- `--output-format json` emits one final machine-readable object.
- `--detach` returns the session ID immediately.
- Ctrl+C cancels gracefully (the agent stops and a final workspace snapshot is saved); press it again to force stop.

To run in the job's own checkout instead of the cloud, use `reason "PROMPT" --yes`. To queue on a shared machine, use `reason "PROMPT" --on NAME`.

## Exit codes

`reason run` and `reason "PROMPT"`:

| Code | Meaning |
| --- | --- |
| `0` | Success, or a detached session was accepted |
| `1` | Task failed |
| `2` | Reason, runtime, authentication or usage failure |
| `3` | Action required |
| `130` | Interrupted locally (Ctrl+C) |

## GitHub Actions example

```yaml
name: Fix failing tests
on: workflow_dispatch

jobs:
  reason:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: curl -fsSL https://reasonmachines.com/install.sh | bash
      - run: ~/.local/bin/reason run --repo "$GITHUB_REPOSITORY" --task "Make the failing tests pass and open a pull request"
        env:
          REASON_API_KEY: ${{ secrets.REASON_API_KEY }}
```

## Stay up to date

```sh
reason update --quiet && reason run --task "…"
```

Or set `REASON_AUTO_UPDATE=1` to update before every session.

## HTTP API

The same sessions can be started and steered over HTTP. See the [API reference](https://docs.reasonmachines.com/api-reference).
