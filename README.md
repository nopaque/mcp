# Nopaque MCP server for TotalPath

[![mcptry](https://mcptry.com/badge/uk.co.nopaque/totalpath)](https://mcptry.com/servers/uk.co.nopaque/totalpath)

The official [Model Context Protocol](https://modelcontextprotocol.io) server for
**TotalPath**, Nopaque's voice testing platform. It lets an AI agent map, test and
load-test IVRs, AI voice agents and contact-centre journeys over real phone calls.

- **Server URL:** `https://mcp.nopaque.co.uk` (Streamable HTTP, protocol `2025-06-18`)
- **Docs:** [nopaque.co.uk/docs/mcp](https://www.nopaque.co.uk/docs/mcp)
- **Registry name:** `uk.co.nopaque/totalpath` ([official MCP Registry](https://registry.modelcontextprotocol.io))

This is a hosted remote server, so there is nothing to install or run. This repository
holds the server's registry listing (`server.json`) and connection guide.

## Authentication

Send a TotalPath workspace API key in the `x-api-key` header.

You don't need a key to connect. An agent can call `registerWorkspace` with no
credential to create a workspace and receive a key. It then calls
`requestAnchorCode` and `verifyAnchorCode` to have a person verify a mobile number,
which unlocks free calling capacity. A person can also create keys in the web app.
See [Authentication](https://www.nopaque.co.uk/docs/authentication).

## Connect

### Claude Code

```bash
claude mcp add --transport http totalpath https://mcp.nopaque.co.uk \
  --header "x-api-key: YOUR_API_KEY"
```

### Claude Desktop

```json
{
  "mcpServers": {
    "totalpath": {
      "command": "npx",
      "args": [
        "-y", "mcp-remote",
        "https://mcp.nopaque.co.uk",
        "--header", "x-api-key:YOUR_API_KEY"
      ]
    }
  }
}
```

### Cursor and other clients with remote HTTP support

```json
{
  "mcpServers": {
    "totalpath": {
      "url": "https://mcp.nopaque.co.uk",
      "headers": { "x-api-key": "YOUR_API_KEY" }
    }
  }
}
```

### MCP Inspector

```bash
npx @modelcontextprotocol/inspector
# Transport: Streamable HTTP
# URL:       https://mcp.nopaque.co.uk
# Header:    x-api-key: YOUR_API_KEY
```

## What the tools cover

The server exposes 87 tools. Call `tools/list` for the current set with full schemas.

| Area | Examples |
| --- | --- |
| IVR mapping | `createMapping`, `getMapping`, `getMappingTree`, `listMappingRuns`, `cancelMapping` |
| Mission and compliance tests | `runMissionTest`, `getMissionTestRun`, `runComplianceTest`, `listComplianceCatalogue` |
| Test configs and drafting | `listTestConfigs`, `draftMissionTestConfig`, `draftMissionTestConfigChange` |
| Results and reporting | `listTestRuns`, `getRunResults`, `aggregateTestRuns`, `listComplianceReports` |
| Load tests and sweeps | `listLoadTests`, `listLoadTestRuns`, `getLoadTestFailurePatterns`, `listSweeps`, `getSweepRun` |
| Survey tests | `listSurveyTestConfigs`, `listSurveyRuns`, `listSurveyResults`, `getSurveyResult` |
| Schedules, batches and alerts | `listSchedules`, `listBatches`, `listBatchRuns`, `listAlertRules`, `listNotificationDestinations` |
| Datasets, profiles and carriers | `listDatasets`, `listProfiles`, `listCarrierIntegrations`, `listCarrierRoutes` |
| Digital testing | `listDigitalTestConfigs`, `listDigitalTestRuns`, `listDigitalConnectors` |
| Imports, audio and numbers | `createImport`, `commitImport`, `listAudio`, `listNumbers` |
| Workspace, usage and billing | `getWorkspaceUsage`, `getWorkspaceUsageThisPeriod`, `getWorkspacePlan`, `listWorkspaceInvoices`, `listAuditEvents` |
| Onboarding | `registerWorkspace`, `requestAnchorCode`, `verifyAnchorCode`, `getClaimLink` |

## Calls are real

Tools that place calls (`createMapping`, `runMissionTest`, `runComplianceTest`) dial
real phone numbers over the PSTN. Those calls are billable and can't be undone once
dialled. Agents should confirm the number, the cost and consent with a person first.
The rules for agents are in [AGENTS.md](https://www.nopaque.co.uk/AGENTS.md).

## Related

- [Nopaque API documentation](https://www.nopaque.co.uk/docs): the REST API
- [OpenAPI 3.1 spec](https://api.nopaque.co.uk/openapi.json)
- [Node.js SDK](https://github.com/nopaque/node-sdk) (`@nopaque/sdk`) and
  [Python SDK](https://github.com/nopaque/python-sdk) (`nopaque`):
  [SDK docs](https://www.nopaque.co.uk/docs/sdks)
- [TotalPath](https://www.nopaque.co.uk/product)

## Publishing the registry listing

`server.json` is published to the official MCP Registry by
`.github/workflows/publish.yml` whenever a GitHub release is published, or when the
workflow is run by hand. To change the listing, edit `server.json`, bump `version`
and publish a release. The workflow proves Nopaque owns `nopaque.co.uk` through a
DNS TXT record on the domain. The matching private key is stored in the repository
secret `MCP_REGISTRY_PRIVATE_KEY`.
