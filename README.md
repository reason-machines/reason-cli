<h1 align="center">Reason CLI</h1>

<p align="center">The autonomous software engineer in your terminal: run it on your laptop, in Reason's cloud, or on any server you share.</p>

<p align="center">
  <a href="https://reasonmachines.com">Website</a> ·
  <a href="https://docs.reasonmachines.com">Docs</a> ·
  <a href="docs/commands.md">Commands</a> ·
  <a href="docs/scripts-and-ci.md">Scripts and CI</a> ·
  <a href="CHANGELOG.md">Releases</a>
</p>

## Install

```sh
curl -fsSL https://reasonmachines.com/install.sh | bash
```

Or with npm (Node.js 18 or newer):

```sh
npm install -g @reason-machines/cli
```

Both install the `reason` command. Supported platforms: macOS (Apple Silicon and Intel), Linux (x64 and ARM64, glibc or musl), Windows (x64 and ARM64).

## Quickstart

```sh
reason login                     # approve a Workspace in your browser
reason                           # open a session in this terminal and type prompts
reason "fix the flaky test"      # run once in this folder and print the result
```

No browser, for CI and servers: create an API key at [reasonmachines.com/customize?tab=api](https://reasonmachines.com/customize?tab=api), then

```sh
export REASON_API_KEY=reason_…
reason "hello world"
```

## Choose where it runs

| Command | Where the agent's commands run |
| --- | --- |
| `reason "PROMPT"` | This computer, in this folder (your checkout as it is, uncommitted changes included) |
| `reason "PROMPT" --cloud` | Reason's cloud, on this checkout's GitHub or GitLab repository |
| `reason "PROMPT" --on NAME` | A machine you share with `reason serve`, queued for a free worker |

The agent itself (model calls, planning, memory, tools) runs in Reason's cloud. The CLI attaches your computer to a session over an outbound connection, so it needs no open ports and no model API keys on the machine.

## Follow and steer

```sh
reason ls                          # recent sessions
reason watch ID                    # follow until done
reason send ID "also add a test"   # steer mid-run
reason cancel ID                   # stop; send resumes it
```

Every session also appears in the web app at [reasonmachines.com](https://reasonmachines.com), where your team can follow and continue it.

## Share a computer as a machine

```sh
# On your laptop: create a token for the machine "build"
reason machines token build

# On the server: join "build" and keep it running as a service
curl -fsSL https://reasonmachines.com/install.sh | bash
reason serve --token rmt_… --service

# From anywhere: send work to it
reason "run the nightly migration check" --on build
```

## Learn more

- [Command reference](docs/commands.md)
- [Scripts and CI](docs/scripts-and-ci.md): `reason run`, JSON output, exit codes
- [HTTP API](https://docs.reasonmachines.com/api-reference): start and steer sessions without the CLI
- `reason --help`, and `--help` on any command

## Support

Questions and bug reports: [open an issue](../../issues) or email contact@reasonmachines.com.

This repository holds documentation for the Reason CLI, not its source. Reason CLI is proprietary software; see [LICENSE](LICENSE).
