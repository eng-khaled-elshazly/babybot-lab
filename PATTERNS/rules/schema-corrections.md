# ⚠️ Schema Corrections — Reality vs Documentation
**Generated:** April 2026
**Purpose:** Corrections discovered after reading actual DB schema.
**All agents must read this — overrides any other documentation.**

---

## CORRECTION 1 — SEC_USERS Primary Key

**What was documented:** `USER_ID`
**Actual column name:** `ID`

```sql
-- WRONG
WHERE USER_ID = :P0_USER_ID

-- CORRECT
WHERE ID = :P0_USER_ID
```

**Impact:** All queries, processes, and session state references to SEC_USERS PK.

---

## CORRECTION 2 — No SEC_GROUPS Table

**What was documented:** `SEC_GROUPS` table for roles/groups
**Reality:** The table is `SEC_ROLES`

```sql
-- WRONG
FROM SEC_GROUPS

-- CORRECT
FROM SEC_ROLES
```

**SEC_ROLES columns:** ID, TENANT_ID, COMPANY_ID, BRANCH_ID, CODE, NAME_EN, NAME_AR, PARENT_ROLE_ID, ROLE_LEVEL, IS_SYSTEM, IS_DEFAULT, STATUS

---

## CORRECTION 3 — No SEC_PERMISSIONS Table

**What was documented:** `SEC_PERMISSIONS` for page-level access
**Reality:** The table is `SEC_ROLE_PERMISSIONS`

```sql
-- WRONG
FROM SEC_PERMISSIONS

-- CORRECT
FROM SEC_ROLE_PERMISSIONS
```

**SEC_ROLE_PERMISSIONS columns:** ID, TENANT_ID, ROLE_ID, PAGE_ID, FUNC_CODE, IS_ALLOWED, GRANTED_BY, GRANTED_DATE

---

## CORRECTION 4 — SEC_USERS Status Column

**What was documented:** `IS_ACTIVE NUMBER(1)`
**Reality:** `STATUS VARCHAR2(1)` — values are 'Y'/'N' not 1/0

```sql
-- WRONG
WHERE IS_ACTIVE = 1

-- CORRECT
WHERE STATUS = 'Y'
```

Same applies to: SEC_ROLES, SEC_BRANCHES, SEC_COMPANIES, SEC_PAGES, SEC_MODULES_CATALOG

---

## CORRECTION 5 — SEC_USERS PK Column Naming

**SEC_BRANCHES PK:** `ID` (not BRANCH_ID)
**SEC_COMPANIES PK:** `ID` (not COMPANY_ID)
**SEC_ROLES PK:** `ID` (not ROLE_ID)
**SEC_PAGES PK:** `ID` (not PAGE_ID)

**Pattern:** All SEC_ tables use `ID` as PK column name.
**Exception:** INS_ tables use descriptive names (POLICY_ID, CLIENT_ID, etc.)

---

## CORRECTION 6 — User-Role Relationship

**What was documented:** SEC_USERS has GROUP_ID column
**Reality:** Many-to-many via SEC_USER_ROLES table

```sql
-- Get user's roles
SELECT R.NAME_AR, R.CODE
FROM SEC_USER_ROLES UR
JOIN SEC_ROLES R ON R.ID = UR.ROLE_ID
WHERE UR.USER_ID = :P0_USER_ID
AND UR.TENANT_ID = :P0_TENANT_ID
AND UR.STATUS = 'Y'
```

---

## CORRECTION 7 — BB_HAS_ACCESS Must Use Correct Tables

Authorization scheme must query SEC_ROLE_PERMISSIONS via SEC_USER_ROLES:

```plsql
DECLARE
    v_count NUMBER;
BEGIN
    SELECT COUNT(*)
    INTO v_count
    FROM SEC_ROLE_PERMISSIONS RP
    JOIN SEC_USER_ROLES UR ON UR.ROLE_ID = RP.ROLE_ID
    JOIN SEC_PAGES PG ON PG.ID = RP.PAGE_ID
    WHERE UR.USER_ID = TO_NUMBER(V('P0_USER_ID'))
    AND PG.APEX_PAGE_ID = :APP_PAGE_ID
    AND RP.IS_ALLOWED = 'Y'
    AND UR.TENANT_ID = TO_NUMBER(V('P0_TENANT_ID'));

    RETURN v_count > 0;
END;
```

---

## CORRECTION 8 — INS_CLIENTS IS_ACTIVE Type

**INS_ tables:** `IS_ACTIVE VARCHAR2(1)` — values 'Y'/'N'
**Not** `NUMBER(1)` with 1/0

```sql
-- WRONG
WHERE IS_ACTIVE = 1

-- CORRECT
WHERE IS_ACTIVE = 'Y'
```

---

## CORRECTION 9 — Backup Tables in Schema

Two backup tables exist — **never query or modify these:**
- `SEC_USERS_BACKUP_20260323_192148`
- `SEC_BRANCHES_BACKUP_20260323_192148`

These are point-in-time snapshots. Ignore them in all queries.

---

## CORRECTION 10 — BB_SECURITY_PKG Status

`BB_SECURITY_PKG` package body is **INVALID**.
Do not call any procedure from this package until recompiled and verified.

---

## Summary Table

| What agents think | Actual reality |
|-------------------|----------------|
| SEC_USERS.USER_ID | SEC_USERS.ID |
| SEC_GROUPS | SEC_ROLES |
| SEC_PERMISSIONS | SEC_ROLE_PERMISSIONS |
| IS_ACTIVE = 1 | STATUS = 'Y' |
| USER has GROUP_ID | Many-to-many via SEC_USER_ROLES |
| BB_SECURITY_PKG works | INVALID — do not use |
