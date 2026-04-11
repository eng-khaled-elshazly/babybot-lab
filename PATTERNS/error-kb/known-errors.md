# Error Knowledge Base — App 110 BabyBot ERP
**Every error we hit — documented with cause, fix, and prevention.**
**Check here BEFORE debugging anything.**

---

## ERR-001 | ORA-28528 — NVARCHAR via DB Link

```
Error:    ORA-28528: Heterogeneous Services datatype conversion error
Cause:    NVARCHAR columns in SQL Server Views accessed via DB Link
Fix:      CAST(column AS VARCHAR(200)) in the SQL Server View itself
Example:  CAST(ACCOUNT_NAME AS VARCHAR(200)) AS ACCOUNT_NAME
Prevention:
  - Never select NVARCHAR columns directly through DB Link
  - Always create SQL Server Views with VARCHAR casts
```

---

## ERR-002 | ORA-28500 — HS Fetch Rows

```
Error:    ORA-28500: connection from ORACLE to non-Oracle system returned error
Cause:    HS_FDS_FETCH_ROWS > 1 in initdg4odbc.ora
Fix:      Set HS_FDS_FETCH_ROWS=1 in C:\DB19c\hs\admin\initdg4odbc.ora
Prevention:
  - Keep HS_FDS_FETCH_ROWS=1 always
  - This value must never change
```

---

## ERR-003 | ORA-00904 — Mixed Case via DG4ODBC

```
Error:    ORA-00904: "column_name": invalid identifier
Cause:    DG4ODBC uppercases unquoted identifiers — SQL Server rejects them
Fix:      Quote ALL column names: SELECT "column_name" FROM "dbo"."Table"@LINK
Example:
  -- WRONG
  SELECT date_insert_db FROM dbo.GL_Accounts@MSSQL_LINK_2019
  -- CORRECT
  SELECT "date_insert_db" FROM "dbo"."GL_Accounts"@MSSQL_LINK_2019
Prevention:
  - Always quote column and table names when using DB Link to SQL Server
```

---

## ERR-004 | ORA-01403 — SELECT * in Interactive Grid

```
Error:    ORA-01403: no data found (or unpredictable IG behavior)
Cause:    Using SELECT * as the source query in an Interactive Grid
Fix:      Always list columns explicitly in IG source
Example:
  -- WRONG
  SELECT * FROM SEC_USERS
  -- CORRECT
  SELECT USER_ID, USERNAME, FULL_NAME, EMAIL, IS_ACTIVE FROM SEC_USERS
Prevention:
  - NEVER use SELECT * in any IG source query — ever
```

---

## ERR-005 | PLS-00306 — Wrong APEX API Parameters

```
Error:    PLS-00306: wrong number or types of arguments in call to 'CREATE_PAGE'
Cause:    AI-generated SQL used parameters not available in APEX 24.2
Fix:      Use verified templates with confirmed APEX 24.2 API signatures
Prevention:
  - Never let AI generate wwv_flow_imp_page calls from scratch
  - Always use templates from PATTERNS/templates/
  - Use p_tag_attributes NOT p_html_form_element_attributes
```

---

## ERR-006 | Arabic Text Corruption — Missing UNISTR

```
Error:    Arabic text appears as: Ø¹Ø±ÙˆØ¶ Ø£Ø³Ø¹Ø§Ø±
Cause:    Arabic text written directly in SQL scripts without UNISTR()
Fix:      Convert ALL Arabic strings to UNISTR() notation
Example:
  -- WRONG
  p_page_name => 'عروض أسعار'
  -- CORRECT
  p_page_name => UNISTR('\0639\0631\0648\0636 \0623\0633\0639\0627\0631')
Prevention:
  - NEVER write Arabic directly in SQL/PLSQL scripts
  - ALWAYS use UNISTR() — no exceptions
```

---

## ERR-007 | Session State Not Persisted

```
Error:    Page item shows correct value in process but resets after
Cause:    Only assigned bind variable, did not call APEX_UTIL.SET_SESSION_STATE
Fix:      Always call both together:
  :P1_ITEM := v_value;
  APEX_UTIL.SET_SESSION_STATE('P1_ITEM', v_value);
Prevention:
  - Every session state assignment needs both lines
```

---

## ERR-008 | Process Fires When It Should Not

```
Error:    Process fires even when item is not null (or vice versa)
Cause:    Used PLSQL_EXPRESSION as condition type
Fix:      Use ITEM_IS_NULL or ITEM_IS_NOT_NULL condition types instead
Prevention:
  - Never use PLSQL_EXPRESSION for simple null checks on process conditions
```

---

## ERR-009 | APEX_ITEM Tag Attribute Error

```
Error:    PLS-00306 or ORA-06550 on APEX_ITEM calls
Cause:    Used p_html_form_element_attributes (removed in APEX 24.2)
Fix:      Replace with p_tag_attributes
Example:
  -- WRONG
  APEX_ITEM.TEXT(1, :COL, p_html_form_element_attributes => 'class="x"')
  -- CORRECT
  APEX_ITEM.TEXT(1, :COL, p_tag_attributes => 'class="x"')
Prevention:
  - Always use p_tag_attributes in APEX 24.2
```

---

## ERR-010 | Theme Flash on Page Load

```
Error:    Page shows default theme for a split second before switching
Cause:    CSS theme injected via JavaScript (executes after page renders)
Fix:      Load theme via HTML region with <link> tag (server-side)
  HTML Region content: <link rel="stylesheet" href="#APP_FILES#css/babybot_&P_THEME_STYLE..css">
  Display Point: AFTER_NAVIGATION_BAR
Prevention:
  - NEVER inject theme CSS via JavaScript
  - ALWAYS use server-side HTML region
```

---

## ERR-011 | V() Returns NULL in AJAX Context

```
Error:    Session variable returns NULL when read inside AJAX PL/SQL
Cause:    V('P_THEME_STYLE') does not work reliably in AJAX calls
Fix:      Pass value as x01 parameter from JavaScript, read via APEX_APPLICATION.G_X01
JS:   apex.server.process('MY_PROC', {x01: apex.item('P_THEME_STYLE').getValue()})
PLSQL: v_theme := APEX_APPLICATION.G_X01;
Prevention:
  - Never use V() for session vars inside AJAX PL/SQL processes
```

---

## ERR-012 | Script Fails on Import — Substitution Variable Prompt

```
Error:    SQLPlus prompts "Enter value for P0_TENANT_ID:" during import
Cause:    Missing SET DEFINE OFF at top of script
Fix:      Add these two lines as FIRST lines of every script:
  SET DEFINE OFF
  SET VERIFY OFF
Prevention:
  - These two lines are MANDATORY — first thing in every script
  - No exceptions
```

---

## ERR-013 | ORA-00001 on INSERT — Missing Required Fields

```
Error:    ORA-00001: unique constraint violated
          OR: Cannot insert NULL into NOT NULL column
Cause:    INSERT statement missing required audit/system columns
Fix:      Always include: CREATED_BY, CREATED_DATE, TENANT_ID, COMPANY_ID
Prevention:
  - Check table schema for NOT NULL columns before writing INSERT
  - Use standard INSERT template from PATTERNS/templates/
```

---

## Quick Lookup Table

| Symptom | Error | Rule |
|---------|-------|------|
| Arabic shows as garbage | ERR-006 | Always UNISTR() |
| IG shows wrong data / errors | ERR-004 | Never SELECT * |
| Script prompts for variable | ERR-012 | SET DEFINE OFF first |
| Session value resets | ERR-007 | Use SET_SESSION_STATE |
| Process fires wrong | ERR-008 | Use ITEM_IS_NULL not PLSQL |
| Theme flash on load | ERR-010 | Server-side HTML region |
| AJAX gets NULL | ERR-011 | Use G_X01 not V() |
| APEX_ITEM error | ERR-009 | p_tag_attributes |
| API wrong params | ERR-005 | Use verified templates |
