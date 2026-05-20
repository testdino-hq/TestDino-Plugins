# TestDino Plugin

This repository ships the TestDino Model Context Protocol (MCP) integration as a plugin for **Claude Code**, **Cursor**, and **OpenAI Codex** — from a single codebase. The plugin connects your AI coding agent to TestDino's hosted remote MCP server so it can inspect test runs, debug failing or flaky test cases, manage manual test assets, work with releases and sessions, and run TestDino audits through natural language.

Platform manifests:

- Claude Code → [`.claude-plugin/`](./.claude-plugin/)
- Cursor → [`.cursor-plugin/`](./.cursor-plugin/)
- OpenAI Codex → [`.codex-plugin/`](./.codex-plugin/)

Skills in [`skills/`](./skills/) are shared across all three platforms.

## Features

The TestDino MCP server provides the following capabilities:

- Connection and access checks: validate access and discover organizations and projects
- Test run analysis: list test runs, inspect run details, and review failures across branches, commits, authors, environments, and time ranges
- Test case debugging: inspect test case details, historical failures, retries, artifacts, and debugging context
- Manual test management: list, create, and update manual test cases and suites
- Releases: list, inspect, create, and update releases or milestones
- Manual runs: list, inspect, create, and update manual runs, plus inspect and update per-case run results
- Exploratory sessions: list, inspect, create, and update exploratory testing sessions
- Audit workflows: run the TestDino audit flow for Playwright test code

## MCP Server

The plugin includes the [Testdino MCP server](https://github.com/testdino-hq/testdino-mcp), giving agents tool access to the full Testdino API.

## Prerequisites

Before setting up the TestDino MCP server, ensure you have:

- Claude Code, Cursor, or OpenAI Codex installed (any one or more)
- Access to a TestDino workspace
- A TestDino account you can authorize during the connect flow

## Installation Instructions for Cursor

Follow these steps to configure the TestDino MCP server in Cursor.

### Step 1: Install the plugin

Install or load the TestDino plugin in Cursor.

### Step 2: Use the hosted TestDino MCP server

The plugin uses the hosted remote MCP server in [mcp.json](./mcp.json):

```json
{
  "mcpServers": {
    "testdino": {
      "url": "https://mcp.testdino.com"
    }
  }
}
```

### Step 3: Connect and authorize

When Cursor prompts you to connect the TestDino MCP server, complete the authorization flow with your TestDino account.

During authorization, you can:

- generate a new Personal Access Token with the scopes you need
- or use an existing `tpu_` token

### Step 4: Reload Cursor if needed

If Cursor asks for a reload after enabling the server, reload the window.

### Step 5: Start a new chat

Open a new chat after connecting so the TestDino tools are available in that conversation.

## Installation Instructions for Claude Code

The plugin's Claude Code manifest lives in [`.claude-plugin/plugin.json`](./.claude-plugin/plugin.json) and the MCP config in [`.mcp.json`](./.mcp.json).

### Option A — Install from the community marketplace (after submission is approved)

```
/plugin marketplace add anthropics/claude-plugins-community
/plugin install testdino@claude-community
```

### Option B — Install directly from this repo

```
/plugin marketplace add NandiniMonpara/TestDino-Cursor-Plugin
/plugin install testdino@NandiniMonpara/TestDino-Cursor-Plugin
```

### Option C — Load locally for development

```
claude --plugin-dir <path-to-this-repo>
```

After install, complete the OAuth-style connect flow to authorize the MCP server with your TestDino account, then start a new chat.

## Installation Instructions for OpenAI Codex

The plugin's Codex manifest lives in [`.codex-plugin/plugin.json`](./.codex-plugin/plugin.json).

### Load locally for development

```
codex --plugin-dir <path-to-this-repo>
```

Confirm the TestDino tools are available within the Codex session, then connect with your TestDino PAT when prompted.

> Marketplace submission for Codex is platform-dependent — see OpenAI's Codex docs for the current submission URL.

## Verify the Connection

Open a new chat and ask:

`Use the TestDino connector. Call health and tell me which projects I have access to.`

This should return:

- your user identity
- connector scope
- organizations and projects you can access
- one or more `projectId` values for follow-up prompts

## Usage Examples

Once configured, you can interact with TestDino through Cursor using natural language:

- Connection check: `Use the TestDino connector. Call health and tell me which projects I have access to.`
- Project discovery: `Show my TestDino projects`
- Test run analysis: `Show test runs from the last hour`
- Failure analysis: `List failed test cases from the latest run`
- Flaky test debugging: `Find flaky tests in TestDino from the last 3 days`
- Test case debugging: `Debug the failing test case "visual.spec.js" in TestDino`
- Manual test management: `Create a manual test case in TestDino for checkout`
- Releases: `List releases in TestDino for project xyz`
- Release details: `Show me release MS-12 in TestDino`
- Manual runs: `List manual runs for release MS-12 in TestDino`
- Run test cases: `Show all test cases in run RUN-12`
- Run case update: `Mark TC-156 as passed in RUN-12`
- Run assignment: `Assign TC-156 in RUN-12 to alice@example.com`
- Sessions: `Create an exploratory session for auth regression in TestDino`
- Session update: `Update session SES-12 to under review`
- Audit workflow: `Run a TestDino audit on this Playwright spec`

## Documentation & Resources

- TestDino: `https://app.testdino.com`
- Remote MCP Documentation: `https://docs.testdino.com/mcp/remote`
- TestDino Documentation: `https://docs.testdino.com`
- MCP Server Repository: `https://github.com/testdino-hq/testdino-mcp`

## Notes & Limitations

- Remote MCP flow: this plugin is configured for TestDino's hosted remote MCP server
- OAuth-style connect flow: users authorize the connection instead of manually editing a PAT into `mcp.json`
- New chat required: some clients attach MCP tools at chat creation, so start a new chat after connecting
- Audit scope: the TestDino audit flow is intended for Playwright automated test code

## Troubleshooting

- If authorization fails, make sure you are signing in with the correct TestDino account.
- If the server connects but tools do not appear, start a new chat.
- If project access is missing, reconnect with a token that has the correct scope.

## Questions or Issues?

For questions about the TestDino MCP server or setup issues, please use the resources below:

- TestDino Support: `support@testdino.com`
- TestDino Documentation: `https://docs.testdino.com`
