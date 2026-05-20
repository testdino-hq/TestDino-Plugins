---
name: testdino-audit
description: Use only when the user explicitly asks for a TestDino audit of Playwright automated test code. Routes through the `test_audit` tool flow. For generic code review or non-Playwright targets, do a normal review instead.
---

# TestDino Audit

Use the TestDino audit flow only when the user explicitly asks for a TestDino audit.

Rules:

- If the user asks for a generic review or audit, do a normal review in chat.
- If the user explicitly asks for a TestDino audit and the target is Playwright code, use the `test_audit` tool flow.
- If the user names TestDino but the target is not Playwright code, explain that the TestDino audit flow is for Playwright automated tests and offer a normal review instead.

Preferred flow:

1. Call `health` if `projectId` is not known.
2. Call `test_audit` with the initial analyze action.
3. Review the local Playwright target using the returned context.
4. Submit the completed audit report through `test_audit`.

Important:

- keep generic reviews as normal chat reviews
- use the TestDino audit flow only for explicit TestDino audit requests
- keep this flow centered on Playwright automated test code
