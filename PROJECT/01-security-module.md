# Security Module — Full Reference
**Module Prefix:** SEC_  
**Page Range:** 1000–1099  
**Status:** 90% Complete  

---

## 1. Purpose

The Security Module controls everything related to:
- Who can log into the system
- What each user can see and do
- Full audit trail of all actions
- Session monitoring and alerts

---

## 2. Database Tables

### SEC_USERS — System Users
```sql
USER_ID          NUMBER PK
TENANT_ID        NUMBER NOT NULL
COMPANY_ID       NUMBER NOT NULL
USERNAME         VARCHAR2(100) UNIQUE
PASSWORD_HASH    VARCHAR2(500)
FULL_NAME        VARCHAR2(500)
EMAIL            VARCHAR2(200)
PHONE            VARCHAR2(50)
GROUP_ID         NUMBER FK → SEC_GROUPS
IS_ACTIVE        NUMBER(1) DEFAULT 1
IS_DELETED       NUMBER(1) DEFAULT 0
LAST_LOGIN       DATE
FAILED_ATTEMPTS  NUMBER DEFAULT 0
IS_LOCKED        NUMBER(1) DEFAULT 0
THEME_PREF       VARCHAR2(20)         -- 'DARK' or 'WHITE_GOLD'
LANG_PREF        VARCHAR2(5)          -- 'AR' or 'EN'
MODULE_CARD_STYLE VARCHAR2(20)        -- used for theme loading
CREATED_BY, CREATED_DATE, UPDATED_BY, UPDATED_DATE
ATTR1..ATTR25
```

### SEC_GROUPS — User Groups / Roles
```sql
GROUP_ID         NUMBER PK
TENANT_ID        NUMBER NOT NULL
COMPANY_ID       NUMBER NOT NULL
GROUP_NAME       VARCHAR2(200)
GROUP_DESC       VARCHAR2(4000)
IS_SYSTEM        NUMBER(1) DEFAULT 0  -- system groups cannot be deleted
IS_ACTIVE        NUMBER(1) DEFAULT 1
IS_DELETED       NUMBER(1) DEFAULT 0
CREATED_BY, CREATED_DATE, UPDATED_BY, UPDATED_DATE
```

### SEC_PERMISSIONS — Page-Level Permissions per Group
```sql
PERMISSION_ID    NUMBER PK
TENANT_ID        NUMBER NOT NULL
GROUP_ID         NUMBER FK → SEC_GROUPS
PAGE_ID          NUMBER               -- APEX page_id
CAN_VIEW         NUMBER(1) DEFAULT 0
CAN_INSERT       NUMBER(1) DEFAULT 0
CAN_UPDATE       NUMBER(1) DEFAULT 0
CAN_DELETE       NUMBER(1) DEFAULT 0
CREATED_BY, CREATED_DATE
```

### SEC_AUDIT_LOG — Every Action Logged
```sql
LOG_ID           NUMBER PK
TENANT_ID        NUMBER NOT NULL
USER_ID          NUMBER
ACTION_TYPE      VARCHAR2(50)         -- INSERT, UPDATE, DELETE, VIEW
TABLE_NAME       VARCHAR2(100)
RECORD_ID        NUMBER
OLD_VALUES       CLOB
NEW_VALUES       CLOB
ACTION_DATE      DATE DEFAULT SYSDATE
IP_ADDRESS       VARCHAR2(50)
SESSION_ID       VARCHAR2(200)
```

### SEC_LOGIN_HISTORY
```sql
HISTORY_ID       NUMBER PK
TENANT_ID        NUMBER NOT NULL
USER_ID          NUMBER
USERNAME         VARCHAR2(100)
LOGIN_TIME       DATE
LOGOUT_TIME      DATE
IP_ADDRESS       VARCHAR2(50)
STATUS           VARCHAR2(20)         -- SUCCESS, FAILED, LOCKED
BROWSER_INFO     VARCHAR2(500)
```

### SEC_SESSIONS — Active Sessions
```sql
SESSION_ID_PK    NUMBER PK
TENANT_ID        NUMBER NOT NULL
USER_ID          NUMBER
APEX_SESSION_ID  VARCHAR2(200)
LOGIN_TIME       DATE
LAST_ACTIVITY    DATE
IP_ADDRESS       VARCHAR2(50)
IS_ACTIVE        NUMBER(1) DEFAULT 1
```

---

## 3. Pages Built

| Page ID | Name | Type | Status | Notes |
|---------|------|------|--------|-------|
| 1000 | Security Dashboard | Normal | ✅ Done | Charts + KPIs |
| 1001 | User List | IR | ✅ Done | Search + filters |
| 1002 | User Form | Modal | ✅ Done | Create/Edit user |
| 1003 | Groups List | IR | ✅ Done | |
| 1004 | Group Form | Modal | ✅ Done | Create/Edit group |
| 1005 | Permissions Matrix | Normal | ✅ Done | Grid of page/group |
| 1006 | Profile Settings | Normal | ✅ Done | Theme + language |
| 1008 | Audit Log | IR | ✅ Done | Read-only |
| 1009 | Login History | IR | ✅ Done | Read-only |
| 1010 | Active Sessions | IR | ✅ Done | With kill session |
| 1011 | Failed Logins | IR | ✅ Done | Alert view |
| 1012 | Permission Changes | IR | ✅ Done | Audit subset |
| 1013 | Data Changes | IR | ✅ Done | Audit subset |
| 1014 | System Events | IR | ✅ Done | Audit subset |
| 1015 | Security Alerts | IR | ✅ Done | Dashboard alerts |
| 1016 | Security Reports | Normal | ✅ Done | Printable reports |

---

## 4. Authorization Scheme — BB_HAS_ACCESS

```plsql
-- Scheme Name: BB_HAS_ACCESS
-- Type: PL/SQL Function Body returning Boolean
-- Applied to: All pages except Login (9999) and Global (0)

DECLARE
    v_count NUMBER;
BEGIN
    SELECT COUNT(*)
    INTO v_count
    FROM SEC_PERMISSIONS P
    JOIN SEC_USERS U ON U.GROUP_ID = P.GROUP_ID
    WHERE U.USER_ID = TO_NUMBER(V('P0_USER_ID'))
    AND P.PAGE_ID = :APP_PAGE_ID
    AND P.CAN_VIEW = 1
    AND P.TENANT_ID = TO_NUMBER(V('P0_TENANT_ID'));
    
    RETURN v_count > 0;
END;
```

---

## 5. Post-Authentication Procedure — BB_POST_AUTH

Runs after every successful login. Populates all session items.

```plsql
PROCEDURE BB_POST_AUTH IS
    v_user SEC_USERS%ROWTYPE;
BEGIN
    SELECT * INTO v_user
    FROM SEC_USERS
    WHERE USERNAME = V('APP_USER')
    AND IS_DELETED = 0
    AND IS_ACTIVE = 1;
    
    APEX_UTIL.SET_SESSION_STATE('P0_USER_ID',    v_user.USER_ID);
    APEX_UTIL.SET_SESSION_STATE('P0_TENANT_ID',  v_user.TENANT_ID);
    APEX_UTIL.SET_SESSION_STATE('P0_COMPANY_ID', v_user.COMPANY_ID);
    APEX_UTIL.SET_SESSION_STATE('P0_USER_NAME',  v_user.FULL_NAME);
    APEX_UTIL.SET_SESSION_STATE('P0_ROLE_ID',    v_user.GROUP_ID);
    APEX_UTIL.SET_SESSION_STATE('P_LANG',        NVL(v_user.LANG_PREF, 'AR'));
    APEX_UTIL.SET_SESSION_STATE('P_THEME_STYLE', NVL(v_user.THEME_PREF, 'WHITE_GOLD'));
END;
```

---

## 6. Known Open Issues

| Issue | Page | Priority |
|-------|------|----------|
| Login page (9999) theme not applied | 9999 | Medium |
| Bilingual labels incomplete | Multiple | Low |
| RTL/LTR menu direction unresolved | Global | Medium |
| Session timeout page styling | 9999 | Low |
| BB_HAS_ACCESS not enforced on all pages | Multiple | High |
| Dynamic navigation not fully wired | Global | Medium |

---

## 7. What Still Needs to Be Done

1. **BB_HAS_ACCESS** — verify it is applied to ALL pages 1000–1016
2. **Login page theming** — apply White Gold / Dark to page 9999
3. **Navigation** — dynamic menu based on user permissions
4. **Session timeout** — styled timeout page
5. **Bilingual labels** — complete AR/EN for all field labels
