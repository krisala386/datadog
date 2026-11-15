# Private Agent Data Checker

An AI agent that checks whether your Datadog private agents are still sending data.
Its logic is written in plain English, so you change it by editing text, not code.

| File | What it is |
|---|---|
| `.claude/agents/private-agent-checker.md` | The agent's instructions in plain English. Edit this to change what it checks. |
| `.github/workflows/private-agent-check.yml` | Runs the agent every hour and on demand. |

## Setup (one time)

1. In GitHub, go to **Settings → Secrets and variables → Actions** and add:
   - `DD_ACCESS_TOKEN`: the Datadog token your other workflow already uses
   - `ANTHROPIC_API_KEY`: an API key from console.anthropic.com
2. In `private-agent-check.yml`, set `DD_SITE`. You can also list `EXPECTED_LOCATIONS` / `EXPECTED_HOSTS`
   so the agent notices when one disappears completely.

## Use it

- **Automatic:** it runs every hour. A red ❌ run means data collection stopped. GitHub emails you about failed runs.
- **Manual:** go to **Actions → Check Private Agents Data Collection → Run workflow**.
- **Chat:** in Claude Code, ask *"use the private-agent-checker agent to check our private agents"*.

The result is shown in the run's **Summary** tab as a table of each location or host, when it last sent data, and ✅/❌.

## What it checks

- **Synthetics private locations:** metric `synthetics.pl.worker.running`, per location
- **Datadog Agents on private hosts:** metric `datadog.agent.running`, per host

A source counts as stopped if it has sent no data for `STALE_MINUTES` (default 15).

## Option without AI: a Datadog monitor

If all you need is an alert, Datadog can do it alone. Create a **Metric monitor** on
`synthetics.pl.worker.running` grouped by `location`, and turn on **"Notify if data is missing"**.
