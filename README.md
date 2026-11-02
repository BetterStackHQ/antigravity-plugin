# Better Stack plugin for Antigravity

Connect Antigravity to your [Better Stack](https://betterstack.com) Incidents and Telemetry data through the Model Context Protocol (MCP). Your agent can investigate incidents, check who is on call, manage uptime monitors, query logs, metrics, traces, errors and real user monitoring (RUM), and build dashboards, all in natural language.

## Install

```bash
agy plugin install https://github.com/BetterStackHQ/antigravity-plugin
```

The plugin adds the Better Stack MCP server (`https://mcp.betterstack.com`) and its skills. Sign in once with your Better Stack account, no token needed:

**In the Antigravity app or IDE:**

1. Open the settings with Cmd + , (Ctrl + , on Windows and Linux) and go to the **Customizations** tab.
2. Click **Authenticate** next to the Better Stack server and sign in in the browser.
3. Copy the authorization code the browser shows, paste it in the settings panel and click **Submit**.
4. Start a new conversation so the tools load.

**In the `agy` CLI:**

1. Run `/mcp`, select `betterstack_betterstack` and choose **Authenticate**.
2. Sign in with your Better Stack account at the link it shows.
3. Copy the authorization code the browser shows and paste it at the prompt.
4. Start a new conversation with `/new` so the tools load.

The tools show up as `betterstack_betterstack/<tool>`, for example `betterstack_betterstack/on_calls`.

### Not connected yet?

The plugin's **connect-betterstack** skill checks whether the Better Stack tools are connected. When a Better Stack request comes in and they aren't, the agent tells you how to connect instead of failing quietly. You can also ask it directly: *"Set up Better Stack."*

## What you can do

Try asking your agent things like:

- *"Show me all monitors that are currently down."*
- *"What's the availability of my website this month?"*
- *"What incidents occurred yesterday?"*
- *"Who's on-call right now?"*
- *"Acknowledge incident #1234 and add a comment about the fix."*
- *"Build an explore query to find HTTP 500 errors in the last hour."*
- *"Create a dashboard showing error rates for my API service."*

## Tools

The plugin exposes the full Better Stack MCP toolset:

- **Incidents** (formerly Uptime): incidents, on-call schedules and escalation, uptime monitors, heartbeats, status pages.
- **Telemetry**: dashboards, charts, alerts, log/metric/error queries, sources and applications.
- **Documentation**: search Better Stack docs from within Antigravity.

The complete tool reference and example prompts live in the [Better Stack MCP integration docs](https://betterstack.com/docs/getting-started/integrations/mcp/).

## Skills

The plugin comes with skills that teach your agent how to work with Better Stack. It loads each one when the task calls for it.

**Investigate**

- **investigate-incident**: investigates an incident or alert end to end. It finds the incident, checks who is on call, pulls the errors, logs, traces, metrics and releases around the start time, and posts a short situation report. It stays read-only unless asked to act.
- **health-check**: a quick health assessment of a server or service (CPU, memory, disk, network, log levels, dependencies) with charts.

**Query**

- **logs-query**, **spans-query**, **metrics-query**, **errors-query**: the SQL dialect for each data type, with the mistakes that make queries fail or return wrong numbers.
- **collector-queries**: proven host, container and service queries for Better Stack Collector sources.

**Dashboards and alerts**

- **dashboard-builder**, **dashboard-layout**: build dashboards that read well, with template variables and a clean grid.
- **chart-generation**, **chart-styling**, **map-hierarchy-charts**: chart types, units and settings.
- **chart-alerts**: threshold, relative and anomaly alerts on charts.
- **uptime-monitors**: monitor types, valid settings and escalation policies.

**Migrate**

- **grafana-import**: recreate Grafana dashboards in Better Stack.

## Authentication

OAuth is the recommended flow. If you prefer an API token, add the server to your own `~/.gemini/config/mcp_config.json` with an `Authorization` header instead. See [API authentication](https://betterstack.com/docs/uptime/api/getting-started-with-uptime-api/):

```json
{
  "mcpServers": {
    "betterstack": {
      "serverUrl": "https://mcp.betterstack.com",
      "headers": {
        "Authorization": "Bearer $TOKEN"
      }
    }
  }
}
```

## Limiting available tools

Restrict which tools the agent can use with one of these headers:

- `X-MCP-Tools-Only`: allowlist (only the listed tools are available)
- `X-MCP-Tools-Except`: blocklist (all tools except the listed ones)

```json
{
  "mcpServers": {
    "betterstack": {
      "serverUrl": "https://mcp.betterstack.com",
      "headers": {
        "X-MCP-Tools-Only": "monitors,monitor,incidents,incident"
      }
    }
  }
}
```

Useful for giving your agent read-only access, scoping it to a workflow, or trimming the initial context size.

## Links

- [Better Stack](https://betterstack.com)
- [MCP integration docs](https://betterstack.com/docs/getting-started/integrations/mcp/)

## Maintainers

- **One version for all four plugins.** The Claude ([claude-plugin](https://github.com/BetterStackHQ/claude-plugin)), Codex ([codex-plugin](https://github.com/BetterStackHQ/codex-plugin)), Cursor ([cursor-plugin](https://github.com/BetterStackHQ/cursor-plugin)) and Antigravity ([antigravity-plugin](https://github.com/BetterStackHQ/antigravity-plugin)) plugins always release with the same version number. A change that bumps one plugin's version bumps all four to that same number, in PRs opened together, even when the others have nothing else to change. The version is set in `.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json` (Claude), `.codex-plugin/plugin.json` (Codex), `.cursor-plugin/plugin.json` (Cursor) and `plugin.json` (Antigravity). Run `agy plugin validate --strict .` here after every change, before you open the PR.
- **Skills from AI SRE.** All skills except `investigate-incident` and `connect-betterstack` are built from AI SRE's skills in aisre-proxy with the sync tool in the maintainers' repository, `BetterStackHQ/mcp-plugins` (`sync-skills/`). Change them in aisre-proxy, then sync.

## License

MIT. See [LICENSE](LICENSE).
