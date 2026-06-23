# TestDino — MCP Directory Submission Form (Reference)

Filled values for the **Anthropic MCP Directory Submission Form**, for the TestDino
remote MCP connector. Every value below is verified against source of truth:

- **Code** — `testdino/server/src/mcp-server/**` (tools, resource, prompt, server info)
- **Live server** — `https://mcp.testdino.com` (`tools/list`, OAuth metadata)
- **Docs** — https://docs.testdino.com/mcp/overview · /remote · /local · /tools-reference
- **Security** — https://testdino.com/security

> ⚠️ **Submit only AFTER the new 28-tool server (with titles + annotations and the
> `get_audit_report` / `submit_audit_report` split) is deployed to production.**
> Until then prod still serves the old surface (`test_audit`, no annotations) and the
> tool list below will not match what reviewers see → review blocked.

---

## Page 1 — Connector / Server Details

| Field | Value |
|---|---|
| **Connector name** | TestDino |
| **Server URL** | `https://mcp.testdino.com` |
| **Transport** | Streamable HTTP (POST `/mcp`; GET/DELETE → 405) |
| **Server identity** | name `testdino-mcp`, version `1.0.0` (`SERVER_INFO`) |
| **Tagline** | Query, debug and manage Playwright + manual test data in TestDino. |
| **Description** | Connect AI assistants to TestDino for querying test results and debugging failures. Exposes test runs & artifacts, manual test cases/suites, releases, manual runs, and exploratory sessions over natural language. |
| **Category** | Testing / Developer Tools |
| **Privacy policy** | ✅ `https://testdino.com/privacy-policy` (verified 200; the `/privacy` path 404s — use `-policy`) |
| **Terms of service** | ✅ `https://testdino.com/terms-conditions` (verified 200) |
| **Logo** | TestDino brand icon (`assets/logo.png`) |

### 3 Example use cases
1. "List the latest test runs and break the failures down by error category."
2. "Debug why test X is failing — show the flakiness pattern and root cause."
3. "Create a manual test case in suite Y, then add it to this sprint's manual run."

---

## Page 2 — Authentication

| Field | Value |
|---|---|
| **Auth method** | OAuth 2.0 / 2.1 + PKCE (S256). PAT (Bearer) also supported. |
| **Dynamic Client Registration (DCR)** | **Yes** — no static client. `registration_endpoint` present. |
| **Static Client ID** | None (DCR) |
| **Static Client Secret** | None (DCR) |
| **Issuer** | `https://api.testdino.com` |
| **Authorization endpoint** | `https://app.testdino.com/connect/mcp` |
| **Token endpoint** | `https://api.testdino.com/api/mcp/oauth/token` |
| **Registration endpoint** | `https://api.testdino.com/api/mcp/oauth/register` |
| **Scopes** | `mcp` |
| **Token auth methods** | `client_secret_post`, `client_secret_basic`, `none` |
| **Protected-resource metadata** | `.well-known/oauth-protected-resource` |
| **PAT format** | `Authorization: Bearer tpu_<token>` (prod prefix `tpu_`) |

---

## Third-party Connections & Web Access

| Question | Answer | Why |
|---|---|---|
| Connects to third-party services? | **Yes — Jira (read only)** | `manualTest.controller.js` calls `integrationClient.jira.getIssue()` to resolve linked issues. Read only; no write-back. |
| Accesses the public web / browses? | **No** | Server only talks to TestDino's own API + the project's connected Jira. |
| Geographic restriction? | **No geographic restriction** | Service is globally available. |
| GDPR compliant? | **Yes** | testdino.com/security: "processes personal data in line with the EU GDPR"; signs DPAs; SCCs for international transfers; AES-256 at rest on Azure. |

---

## Page 3 — Test Account Access & Server Technical Details

### Testing Account Credentials (PROD — provided)
```
Personal Access Token (ready to use):
  tpu_ba626d3173a08b1e344e963193e505777a601548fd191f26699a652b99200261

OR OAuth sign-in at app.testdino.com:
  Email:        ashish@testdino.com
  Password:     Claude@demo123

Organization: Test Org
Project:      Claude Connector - MCP   (holds the sample data)
```
> Reviewers can paste the PAT directly, or sign in via OAuth and generate a token.
> A staging token (`tpu_staging_…`) would NOT work — reviewers hit prod
> `mcp.testdino.com`, which validates the `tpu_` prod prefix. This token is `tpu_`. 

### Test Account Server URL (if different)
`https://mcp.testdino.com` — same as main (leave blank or repeat).

### Test Account Setup Instructions
```
1. Add the connector with URL https://mcp.testdino.com (remote MCP / Streamable HTTP).
2. Authenticate using EITHER:
     a) Personal Access Token (fastest) — choose "Use existing token" and paste:
            tpu_ba626d3173a08b1e344e963193e505777a601548fd191f26699a652b99200261
     b) OAuth sign-in on TestDino's authorization page with:
            Email: ashish@testdino.com
            Password: Claude@demo123
        then "Generate new token" with the per-project Test Runs and Manual Tests
        toggles enabled for project "Claude Connector - MCP".
3. Call the `health` tool first — it returns a tree of every organization and
   project the token can reach, each with a projectId. Under organization
   "Test Org", pick the project "Claude Connector - MCP" and save its projectId;
   nearly all other tools require it.
4. That project is pre-loaded with sample data — automated Playwright test runs,
   manual test cases & suites, a release, a manual run, and an exploratory
   session — so every tool can be exercised.
```

### Test Data Availability (check both — once the account is seeded)
- [x] Test account includes sample data
- [x] All tools can be tested with provided data

### Tool Titles & Annotations (check both — verified in code)
- [x] User-friendly titles specified for all tools (every tool has top-level `title`)
- [x] Accurate annotations for all tools (`readOnlyHint` on reads, `destructiveHint` on writes)

---

## List of Tools (28) — `tool_name (Human-readable title)`

Comma-separated, copy-paste for the form:

```
health (Health Check), list_testruns (List Test Runs), get_run_details (Get Test Run Details), list_testcase (List Test Cases), get_testcase_details (Get Test Case Details), debug_testcase (Debug Test Case), list_manual_test_cases (List Manual Test Cases), get_manual_test_case (Get Manual Test Case), create_manual_test_case (Create Manual Test Case), update_manual_test_case (Update Manual Test Case), list_manual_test_suites (List Manual Test Suites), create_manual_test_suite (Create Manual Test Suite), get_audit_report (Get TestDino Audit), submit_audit_report (Submit TestDino Audit Report), list_releases (List Releases), get_release (Get Release), create_release (Create Release), update_release (Update Release), list_manual_runs (List Manual Test Runs), get_manual_run (Get Manual Test Run), create_manual_run (Create Manual Test Run), update_manual_run (Update Manual Test Run), list_run_test_cases (List Test Cases In A Run), update_run_test_case (Update Test Case In A Run), list_sessions (List Exploratory Sessions), get_session (Get Exploratory Session), create_session (Create Exploratory Session), update_session (Update Exploratory Session)
```

### Tool annotations breakdown (source: `mcp-server/tools/*.tools.js`)

| Tool | Title | readOnlyHint | destructiveHint |
|---|---|:---:|:---:|
| health | Health Check | ✅ | — |
| list_testruns | List Test Runs | ✅ | — |
| get_run_details | Get Test Run Details | ✅ | — |
| list_testcase | List Test Cases | ✅ | — |
| get_testcase_details | Get Test Case Details | ✅ | — |
| debug_testcase | Debug Test Case | ✅ | — |
| list_manual_test_cases | List Manual Test Cases | ✅ | — |
| get_manual_test_case | Get Manual Test Case | ✅ | — |
| create_manual_test_case | Create Manual Test Case | false | false |
| update_manual_test_case | Update Manual Test Case | false | true |
| list_manual_test_suites | List Manual Test Suites | ✅ | — |
| create_manual_test_suite | Create Manual Test Suite | false | false |
| get_audit_report | Get TestDino Audit | ✅ | — |
| submit_audit_report | Submit TestDino Audit Report | false | false |
| list_releases | List Releases | ✅ | — |
| get_release | Get Release | ✅ | — |
| create_release | Create Release | false | false |
| update_release | Update Release | false | true |
| list_manual_runs | List Manual Test Runs | ✅ | — |
| get_manual_run | Get Manual Test Run | ✅ | — |
| create_manual_run | Create Manual Test Run | false | false |
| update_manual_run | Update Manual Test Run | false | true |
| list_run_test_cases | List Test Cases In A Run | ✅ | — |
| update_run_test_case | Update Test Case In A Run | false | true |
| list_sessions | List Exploratory Sessions | ✅ | — |
| get_session | Get Exploratory Session | ✅ | — |
| create_session | Create Exploratory Session | false | false |
| update_session | Update Exploratory Session | false | true |

**Counts:** 16 read-only · 6 create (non-destructive write) · 5 update (destructive write) + health = 28.

---

## List of Resources (1)

```
testdino_skills_guide (TestDino Skills Guide)
```
Registered name `testdino_skills_guide`, URI `testdino://skills/guide`, mimeType
`text/markdown` — the AI-agent skills guide (tool-selection rules, decision trees,
audit protocol).

## List of Prompts (1)

```
testdino_guide (TestDino Guide)
```
Same skills guide, exposed as an injectable prompt.

---

## Page 4 — Launch Readiness & Listing Media

| Field | Value / Action |
|---|---|
| **Server GA Date** | = date the new 28-tool surface is deployed to prod. If deployed before submitting, server is already GA → leave blank. Do **not** claim GA on an undeployed surface. |
| **Tested & works in** (required) | ☑ Claude.ai (web) · ☑ Claude Desktop · ☑ Cowork (already tested). Claude Code optional. Re-test against the **deployed new surface**, not staging. |
| **Server Logo** | ✅ **Verified 2026-06-23:** square SVG (`viewBox 0 0 90 90`, `image/svg+xml`, HTTP 200, 7.8 KB), TestDino mark on `#171717` black bg. `https://testdinostr.blob.core.windows.net/public/testdino-brand-icon-black-background.svg` |
| **Server Logo URL** | `https://testdinostr.blob.core.windows.net/public/testdino-brand-icon-black-background.svg` |
| **Favicon verification** | ⚠️ **Verified 2026-06-23:** `mcp.testdino.com/favicon.ico` → **404 (no favicon)**, so `domain=mcp.testdino.com` returns a blank globe. `testdino.com/favicon.ico` → 200, 15 KB ✅. **Fix:** either add a favicon to `mcp.testdino.com`, or use `domain=testdino.com` in the favicon URL field. Don't tick the box until fixed. |
| **Promotional Images** | 3–5 PNG screenshots of TestDino MCP responses in Claude.ai (failure breakdown, debug_testcase root-cause, created manual test case). Share via Drive link. |
| **Link to Promotional Materials** | Optional — same Drive link + matching prompt per screenshot. |

**You must supply:** SVG logo + host, screenshots, GA date, Claude.ai/Desktop test runs.

---

## Page 5 — Skills & Plugins (optional)

| Field | Value |
|---|---|
| **Skill Name** | TestDino Skills for Claude — Playwright & manual test management |
| **GitHub URL** | `https://github.com/testdino-hq/TestDino-Plugins/tree/main/skills` (public, verified 200) |
| **Skills (7)** | testdino-health, testdino-runs, testdino-manual-tests, testdino-manual-runs, testdino-releases, testdino-sessions, testdino-audit |
| **Related Plugins** | Repo `https://github.com/testdino-hq/TestDino-Plugins`; marketplace `testdino-plugins`; plugin `testdino` (submitted to claude-community); Cursor variant in same repo |

---

## Page 6 — Submission Requirements Checklist

**Verified true now (✅):** no cross-service automation (only read-only Jira lookup) ·
no financial transactions · company controls the endpoints · OAuth 2.1 + DCR + PKCE ·
28/28 tools annotated · HTTPS · CORS allows `https://claude.ai` (preflight 204) ·
no IP allowlist (N/A) · docs public (`docs.testdino.com/mcp/*`) ·
privacy `testdino.com/privacy-policy` · terms `testdino.com/terms-conditions`.

**Action before ticking (⚠️):**
- Deploy 28-tool surface (+ favicon) to prod → "server live/ready", annotations live, tested-surface ready
- Update `docs.testdino.com/mcp/tools-reference` (drop `test_audit`/27, add split)
- Test on Claude.ai web against the deployed surface
- Confirm "Claude Connector - MCP" populated across all 6 data types
- Ensure PAT `tpu_ba626…` + login valid ≥ 30 days
- Read & agree to the Software Directory Policy

---

## Pre-submission checklist

- [ ] New 28-tool server deployed to **production** (`mcp.testdino.com`)
- [ ] `tools/list` on prod shows `get_audit_report` + `submit_audit_report` (no `test_audit`)
- [ ] Plugin republished in sync with the server deploy
- [ ] Prod test account created with sample data across all data types
- [ ] Prod `tpu_` PAT (or OAuth login, no 2FA) ready for the credentials field
- [ ] Privacy policy URL confirmed
- [ ] CI "MCP Inspector" test updated for new surface (deferred — OK per plan)
- [ ] **Public docs updated** — `docs.testdino.com/mcp/tools-reference` still lists
      27 tools incl. `test_audit` (action: analyze/list/get). Update to the 28-tool
      surface (`get_audit_report` + `submit_audit_report`) so docs match the connector
- [x] **Privacy policy** ✅ `testdino.com/privacy-policy` · **Terms** ✅ `testdino.com/terms-conditions`
- [x] **Square SVG logo** hosted ✅ (Azure blob, verified square + SVG)
- [ ] **Favicon fixed** — `mcp.testdino.com` has none (404); add one there or use `domain=testdino.com` in the form
- [ ] **3–5 promo screenshots** captured from Claude.ai + hosted
- [ ] **Connector tested on Claude.ai (web)** against the deployed new surface

---

*Last verified: 2026-06-22. Re-verify tool list against live `tools/list` after each deploy.*
