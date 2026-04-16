# DxCore Dispatch Action

Run a build command and pipe the task graph to the DxCore coordinator for distributed execution.

## Inputs

| Input             | Description                                                                          | Required | Default  |
|-------------------|--------------------------------------------------------------------------------------|----------|----------|
| `coordinator-url` | URL of the running DxCore coordinator                                                | Yes      |          |
| `token`           | Authentication token matching the coordinator                                        | Yes      |          |
| `command`         | Build command that outputs a task graph to stdout (e.g. `turbo run build test lint --dry=json`) | Yes |       |
| `session-id`      | Session identifier. Defaults to `gha-<run_id>`.                                      | No       | `""`     |
| `build-system`    | Build system adapter (`turbo` or `nx`)                                               | No       | `turbo`  |
| `work-dir`        | Working directory where the build command runs                                       | No       | `.`      |
| `timeout`         | Timeout in seconds for the dispatch operation                                        | No       | `900`    |
| `version`         | Release tag to download (e.g. `v0.1.0`). Defaults to latest.                        | No       | `latest` |

## Usage

```yaml
jobs:
  dispatch:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: join-with/dxcore-dispatch-action@main
        with:
          coordinator-url: http://<coordinator-host>:4000
          token: ${{ secrets.DXCORE_TOKEN }}
          command: "turbo run build test lint --dry=json"
          build-system: turbo
```

## How It Works

1. Resolves the release version (latest or pinned)
2. Downloads the CLI binary from [`join-with/dxcore`](https://github.com/join-with/dxcore) releases
3. Waits for the coordinator to become available
4. Runs the build command and pipes its task graph output to the dispatcher
5. The coordinator distributes tasks across connected agents

## Related

- [join-with/dxcore](https://github.com/join-with/dxcore) -- Main DxCore repository
- [join-with/dxcore-coordinator-action](https://github.com/join-with/dxcore-coordinator-action) -- Start coordinator
- [join-with/dxcore-agent-action](https://github.com/join-with/dxcore-agent-action) -- Connect build agents
- [join-with/dxcore-shutdown-action](https://github.com/join-with/dxcore-shutdown-action) -- Shutdown coordinator

## Disclaimer

DxCore is an independent project by Eyal Lapid ([JoinWith])(https://joinwith.com) and is not affiliated with, endorsed by, or sponsored by Vercel, Nrwl, Gradle, Docker, GitHub, or any other third-party project. All trademarks belong to their respective owners.
