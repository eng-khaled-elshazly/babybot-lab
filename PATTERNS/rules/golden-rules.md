# Golden Rules — App 110 BabyBot ERP
**These rules are NON-NEGOTIABLE.**  
**Every agent must follow them on every task. No exceptions.**  
**Violating any rule = broken code that will not run.**

---

## RULE SET A — Script Header (Every SQL/PLSQL Script)

Every script MUST start with these two lines. Always. First thing. No exceptions.

```sql
SET DEFINE OFF
SET VERIFY OFF
```

**Why:** Without these, APEX substitution variables like `&P0_TENANT_ID.` break the script during import. SQLPlus will prompt for variable values and the script will fail.

---

## RULE SET B — Arabic Text

**NEVER write Arabic text directly in SQL strings.**  
Always use `UNISTR()` function.

```sql
-- ❌ WRONG
INSERT INTO SEC_GROUPS (GROUP_NAME) VALUES ('مدير النظام');

-- ✅ CORRECT
INSERT INTO SEC_GROUPS (GROUP_NAME) VALUES (UNISTR('\0645\062F\064A\0631 \0627\0644\0646\0638\0627\0645'));
```

**Why:** Direct Arabic in SQL scripts causes encoding corruption when running via SQLPlus or importing APEX files.

---

## RULE SET C — Interactive Grid (IG) Rules

### C1 — Never use SELECT * in an IG source

```sql
-- ❌ WRONG — causes ORA-01403 and unpredictable behavior
SELECT * FROM SEC_USERS

-- ✅ CORRECT — always list columns explicitly
SELECT USER_ID, USERNAME, FULL_NAME, EMAIL, IS_ACTIVE
FROM SEC_USERS
WHERE TENANT_ID = :P0_TENANT_ID
```

### C2 — IG primary key must be declared explicitly
The primary key column must be marked in the IG column attributes. Never leave it auto-detected.

---

## RULE SET D — Session State

**Never assign a value to a bind variable without also calling APEX_UTIL.SET_SESSION_STATE.**

```plsql
-- ❌ WRONG — value not persisted in session
:P1_USER_ID := v_user_id;

-- ✅ CORRECT — both required together
:P1_USER_ID := v_user_id;
APEX_UTIL.SET_SESSION_STATE('P1_USER_ID', v_user_id);
```

---

## RULE SET E — Process Conditions

**Never use `PLSQL_EXPRESSION` for process conditions.**  
Always use `ITEM_IS_NULL` or `ITEM_IS_NOT_NULL` or `NEVER` or `ALWAYS`.

```
-- ❌ WRONG condition type
Type: PLSQL_EXPRESSION
Value: :P2_USER_ID IS NULL

-- ✅ CORRECT
Type: ITEM_IS_NULL
Item: P2_USER_ID
```

---

## RULE SET F — Template IDs

**Never guess Template IDs. Never use IDs from other apps or documentation.**  
Use only these verified IDs for App 110:

```
Button:               4072362960822175091
Field Required:       2526760615038828570
Field Optional:       2318601014859922299
Region IR:            2100526641005906379
Region Form:          4072358936313175081
Modal Dialog:         2101157952850466385
Workspace ID:         1802032451027025
Application ID:       110
Schema:               ERP2026
```

---

## RULE SET G — Page Items API

**Never use `p_html_form_element_attributes` — it does not exist in APEX 24.2.**  
Use `p_tag_attributes` instead.

```plsql
-- ❌ WRONG
APEX_ITEM.TEXT(1, :COL, p_html_form_element_attributes => 'class="..."')

-- ✅ CORRECT
APEX_ITEM.TEXT(1, :COL, p_tag_attributes => 'class="..."')
```

---

## RULE SET H — Multi-Tenancy Filter

**Every query touching business data MUST filter by TENANT_ID.**  
No exceptions. This is the security isolation boundary.

```sql
-- ❌ WRONG — exposes all tenants' data
SELECT * FROM SEC_USERS

-- ✅ CORRECT
SELECT USER_ID, USERNAME, FULL_NAME
FROM SEC_USERS
WHERE TENANT_ID = :P0_TENANT_ID
AND IS_DELETED = 0
```

---

## RULE SET I — Theme Injection

**Never inject CSS theme via JavaScript.**  
Theme must be loaded server-side only — via HTML region with `<link>` tag.

```html
<!-- ✅ CORRECT — HTML Region at AFTER_NAVIGATION_BAR -->
<link rel="stylesheet" href="#APP_FILES#css/babybot_&P_THEME_STYLE..css">
```

```javascript
// ❌ WRONG — JS injection causes flash of unstyled content
document.head.innerHTML += '<link rel="stylesheet" href="...">';
```

---

## RULE SET J — AJAX Calls and Session Variables

**Never read `P_THEME_STYLE` or other session items via `V()` inside AJAX PL/SQL.**  
Pass them as `x01`, `x02` parameters and read via `APEX_APPLICATION.G_X01`.

```plsql
-- ❌ WRONG inside AJAX
v_theme := V('P_THEME_STYLE');  -- returns NULL in AJAX context

-- ✅ CORRECT
v_theme := APEX_APPLICATION.G_X01;  -- passed from JS as apex.server.process('..', {x01: apex.item('P_THEME_STYLE').getValue()})
```

---

## RULE SET K — Authorization

The system uses a custom authorization scheme: `BB_HAS_ACCESS`

It checks `SEC_PERMISSIONS` table before rendering any page.  
**Never bypass it. Never set a page authorization to "No checks"** unless explicitly approved by خالد.

---

## RULE SET L — Git Commit Standards

```
feat:     new feature
fix:      bug fix
refactor: restructure without behavior change
docs:     documentation only
chore:    maintenance task

Examples:
feat(security): add user deactivation process
fix(p1002): resolve modal close issue
docs(patterns): add IG golden rules
```

---

## RULE SET M — What Agents Must Never Do

```
❌ Never delete any file without explicit approval from خالد
❌ Never rename pages or change page IDs
❌ Never modify SEC_PERMISSIONS data directly
❌ Never deploy to production without خالد sign-off
❌ Never assume a Template ID — always use the verified list above
❌ Never take initiative on a task not explicitly assigned
❌ Never use SELECT * in any IG source query
```

---

## Quick Reference Card

| Situation | Rule |
|-----------|------|
| Starting any script | `SET DEFINE OFF` + `SET VERIFY OFF` first |
| Arabic text in SQL | Always `UNISTR()` |
| IG source query | Never `SELECT *` |
| Session value | `UNISTR()` + `APEX_UTIL.SET_SESSION_STATE` |
| Process condition | Never `PLSQL_EXPRESSION` |
| Template ID | Only use verified list |
| Query filter | Always `TENANT_ID = :P0_TENANT_ID` |
| Theme loading | Server-side HTML region only |
| AJAX + session vars | Use `G_X01`, not `V()` |
| Tag attributes | `p_tag_attributes` not `p_html_form_element_attributes` |

---

*Last updated: April 2026 — based on accumulated project experience.*  
*Any new rule discovered during development must be added here immediately.*
