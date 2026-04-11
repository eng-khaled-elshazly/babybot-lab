# Agent 05 — Gemini (Antigravity)
**Role:** Automated Auditor + File Executor
**Strengths:**
- Bulk file scanning with Python/regex
- Reading and editing existing files
- Running git operations
- Fast execution on clear tasks

**Limitations:**
- Not business-aware (needs task definition)
- Cannot build APEX pages from scratch
- Cannot generate wwv_flow_imp scripts
- Needs human approval before any action

**How to use:**
- Give it a clear, specific task
- Always review output before approving
- For audit tasks: give it the folder path
- For fixes: show before/after first

**First proven task:** 2026-04-11
Audited 57 APEX pages, found 4 TENANT_ID
violations, fixed them correctly.
