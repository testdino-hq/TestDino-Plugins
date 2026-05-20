# Local Testing Guide

How to test the TestDino plugin on **Claude Code**, **Cursor**, and **OpenAI Codex** before submitting to any marketplace. Run all three.

## Prerequisites

- A TestDino account with at least one project
- A TestDino PAT (or be ready to complete the OAuth-style connect flow)
- The three CLIs/IDEs installed:
  - Claude Code (`claude --version`)
  - Cursor (latest)
  - OpenAI Codex CLI (`codex --version`)
- You are in the repo root: `c:\Users\Alphabin\Desktop\testdino-cursor-plugin`

---

## 1. Test on Claude Code

### Load the plugin

From the repo root:

```
claude --plugin-dir .
```

Claude Code reads `.claude-plugin/plugin.json` and registers the skills under the `testdino` namespace.

### Validate the manifest

In a separate terminal (still in the repo root):

```
claude plugin validate
```

This is the **same check the Anthropic review pipeline runs**. Fix everything it flags before submitting. Common failures:
- Missing/invalid `description` in `SKILL.md` frontmatter (see note below)
- Bad JSON in `.claude-plugin/plugin.json` or `.mcp.json`
- Referenced files that don't exist

### Smoke test the skills

Inside the Claude Code session, try each skill:

```
/testdino:testdino-health
/testdino:testdino-runs
/testdino:testdino-audit
/testdino:testdino-manual-tests
/testdino:testdino-manual-runs
/testdino:testdino-releases
/testdino:testdino-sessions
```

Then test the MCP connection with a natural-language prompt:

```
Use the TestDino connector. Call health and tell me which projects I have access to.
```

You should be prompted to authorize the MCP server, then see your projects.

### ⚠️ Known issue to verify

Your current `skills/*/SKILL.md` files are **plain markdown without YAML frontmatter**. Claude Code's docs require frontmatter with a `description` field for model-invoked skills (so Claude knows when to use them). Reference: Resend's `skills/resend/SKILL.md` starts with:

```yaml
---
name: resend
description: Use when working with the Resend email API — ...
---
```

If `/testdino:testdino-health` works but Claude never *automatically* invokes the skill from natural-language prompts, this is why. Fix by adding frontmatter to each `SKILL.md`:

```yaml
---
name: testdino-health
description: Use when the user wants to check TestDino connection status, list organizations or projects, or find a projectId.
---
```

Repeat for all 7 skills.

### Iterate

After editing files, run `/reload-plugins` inside Claude Code — no restart needed.

---

## 2. Test on Cursor

### Load the plugin

Cursor reads `.cursor-plugin/plugin.json` and `mcp.json`.

Option A — open the folder directly:
1. Open Cursor → `File → Open Folder` → select this repo
2. Cursor should auto-detect `.cursor-plugin/plugin.json` and offer to enable the plugin

Option B — symlink for global testing:
1. Cursor → Settings → MCP → add `mcp.json` content manually if Cursor doesn't auto-load it

### Connect & authorize

When Cursor prompts to connect the TestDino MCP server, complete the OAuth-style flow (generate a new PAT or use an existing `tpu_` token).

If you see a "reload window" prompt, reload.

### Smoke test

Open a **new chat** (some MCP tools only attach at chat creation), then:

```
Use the TestDino connector. Call health and tell me which projects I have access to.
```

Run through the usage examples from `README.md`:
- `Show test runs from the last hour`
- `List failed test cases from the latest run`
- `Run a TestDino audit on this Playwright spec`

### What "working" looks like

- ✅ Plugin appears as enabled in Cursor's plugin manager
- ✅ MCP server shows green/connected
- ✅ Tools are listed in the chat sidebar (or `/` autocomplete)
- ✅ Natural-language prompts trigger TestDino MCP calls

---

## 3. Test on OpenAI Codex

### Load the plugin

Codex reads `.codex-plugin/plugin.json` — which points at `./skills/` and `./.mcp.json`.

From the repo root:

```
codex --plugin-dir .
```

(If your Codex version uses a different flag, check `codex --help` — the equivalent in current Codex CLI versions is usually `--plugin-dir` or `--load-plugin`.)

### Smoke test

Within the Codex session:

```
What TestDino tools do you have available?
```

Then:

```
Call testdino health and list my projects.
```

### Verify MCP connection

Codex also reads MCP servers from `~/.codex/config.toml` globally. The plugin's `.mcp.json` should register the TestDino MCP server **only for the duration of the session**, not globally.

Confirm by:
1. Running `codex --plugin-dir .` → tools should be available
2. Exiting and running plain `codex` → TestDino tools should NOT appear (proves the plugin scoping works)

---

## 4. Pre-submission checklist

Run through this before opening any submission form:

- [ ] `claude plugin validate` passes with no errors
- [ ] All 7 skills load and respond on Claude Code (after frontmatter fix if needed)
- [ ] Cursor connects to MCP and runs at least 3 natural-language test prompts successfully
- [ ] Codex loads the plugin and lists TestDino tools
- [ ] `version` field is **identical** across all 3 manifests (`1.0.0`)
- [ ] `repository` URL in all 3 manifests points to the actual GitHub repo
- [ ] `README.md` has install sections for each of the 3 platforms
- [ ] `LICENSE` is present
- [ ] No secrets, `.env` files, or PATs committed (check `git status` carefully)
- [ ] Repo is pushed to `main` branch on GitHub — marketplaces pull from default branch

---

## 5. Troubleshooting

| Symptom | Likely cause |
|---|---|
| Claude Code: `claude plugin validate` fails on SKILL.md | Missing YAML frontmatter — see Section 1 |
| Claude Code: skills never auto-invoke | Same — missing `description` frontmatter |
| Cursor: tools don't show in chat | Start a new chat — MCP tools attach at chat creation |
| Cursor: connection fails | Wrong TestDino account; reconnect with a PAT that has the right scopes |
| Codex: `--plugin-dir` flag unknown | Check Codex CLI version and updated flag name |
| Codex: MCP server doesn't start | Verify `.mcp.json` path in `.codex-plugin/plugin.json` is `./.mcp.json` (relative to repo root) |
| Any platform: "plugin name conflict" | Another plugin already uses `testdino` in that marketplace — rename in the relevant manifest |

---

## 6. After all three work locally

Proceed to submission — see `README.md` per-platform install sections (which you'll write after testing confirms what to document).
