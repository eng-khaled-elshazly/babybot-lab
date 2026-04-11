# Database Standards — App 110 BabyBot ERP

## 1. Naming Conventions

| Object | Pattern | Example |
|--------|---------|---------|
| Table | `PREFIX_ENTITY` | `SEC_USERS`, `HR_EMPLOYEES`, `INS_POLICIES` |
| Sequence | `SEQ_TABLE_NAME` | `SEQ_SEC_USERS` |
| Primary Key | `ENTITY_ID` | `USER_ID`, `EMPLOYEE_ID` |
| Foreign Key | `PARENT_ENTITY_ID` | `GROUP_ID`, `TENANT_ID` |
| Index | `IDX_TABLE_COL` | `IDX_SEC_USERS_TENANT` |
| View | `V_ENTITY_NAME` | `V_ACTIVE_USERS` |
| Package | `BB_ENTITY_PKG` | `BB_SECURITY_PKG` |
| Procedure | `BB_ACTION_ENTITY` | `BB_CREATE_USER` |

## 2. Module Prefixes

| Prefix | Module |
|--------|--------|
| `SEC_` | Security & Access Control |
| `HR_` | Human Resources |
| `INS_` | Insurance / Riada Module |
| `FIN_` | Finance |
| `OPS_` | Operations |
| `SYS_` | System / Configuration |

## 3. Mandatory Table Structure

```sql
CREATE TABLE PREFIX_ENTITY (
    -- Primary Key
    ENTITY_ID        NUMBER         NOT NULL,
    
    -- Multi-Tenancy (mandatory)
    TENANT_ID        NUMBER         NOT NULL,
    COMPANY_ID       NUMBER         NOT NULL,
    BRANCH_ID        NUMBER,
    
    -- Business columns here...
    
    -- Audit (mandatory)
    CREATED_BY       NUMBER,
    CREATED_DATE     DATE           DEFAULT SYSDATE,
    UPDATED_BY       NUMBER,
    UPDATED_DATE     DATE,
    
    -- Status (mandatory)
    IS_ACTIVE        NUMBER(1)      DEFAULT 1,
    IS_DELETED       NUMBER(1)      DEFAULT 0,
    
    -- Spare columns (always include)
    ATTR1   VARCHAR2(4000), ATTR2  VARCHAR2(4000), ATTR3  VARCHAR2(4000),
    ATTR4   VARCHAR2(4000), ATTR5  VARCHAR2(4000), ATTR6  VARCHAR2(4000),
    ATTR7   VARCHAR2(4000), ATTR8  VARCHAR2(4000), ATTR9  VARCHAR2(4000),
    ATTR10  VARCHAR2(4000),
    ATTR11  NUMBER,  ATTR12 NUMBER,  ATTR13 NUMBER,  ATTR14 NUMBER,
    ATTR15  NUMBER,  ATTR16 NUMBER,  ATTR17 NUMBER,  ATTR18 NUMBER,
    ATTR19  NUMBER,  ATTR20 NUMBER,
    ATTR21  DATE,    ATTR22 DATE,    ATTR23 DATE,    ATTR24 DATE,    ATTR25 DATE,
    
    CONSTRAINT PK_PREFIX_ENTITY PRIMARY KEY (ENTITY_ID)
);
```

## 4. Sequence Standard

```sql
CREATE SEQUENCE SEQ_PREFIX_ENTITY
    START WITH 1
    INCREMENT BY 1
    NOCACHE
    NOCYCLE;
```

## 5. Mandatory Index

```sql
-- Always create this composite index for multi-tenancy performance
CREATE INDEX IDX_PREFIX_ENTITY_TENANT 
    ON PREFIX_ENTITY (TENANT_ID, COMPANY_ID);
```

## 6. Soft Delete Pattern

Never hard-delete records. Always soft-delete:

```sql
-- Delete
UPDATE PREFIX_ENTITY
SET IS_DELETED = 1,
    UPDATED_BY = :P0_USER_ID,
    UPDATED_DATE = SYSDATE
WHERE ENTITY_ID = :P_ENTITY_ID
AND TENANT_ID = :P0_TENANT_ID;

-- All SELECT queries must exclude deleted records
WHERE IS_DELETED = 0
AND TENANT_ID = :P0_TENANT_ID
```

## 7. Audit Pattern

Every INSERT and UPDATE must populate audit columns:

```plsql
-- INSERT
CREATED_BY   := TO_NUMBER(V('P0_USER_ID'));
CREATED_DATE := SYSDATE;
IS_ACTIVE    := 1;
IS_DELETED   := 0;

-- UPDATE
UPDATED_BY   := TO_NUMBER(V('P0_USER_ID'));
UPDATED_DATE := SYSDATE;
```

## 8. Current Tables Reference

### Security Module (SEC_)
```
SEC_USERS              USER_ID        SEQ_SEC_USERS
SEC_GROUPS             GROUP_ID       SEQ_SEC_GROUPS
SEC_PERMISSIONS        PERMISSION_ID  SEQ_SEC_PERMISSIONS
SEC_AUDIT_LOG          LOG_ID         SEQ_SEC_AUDIT_LOG
SEC_LOGIN_HISTORY      HISTORY_ID     SEQ_SEC_LOGIN_HISTORY
SEC_SESSIONS           SESSION_ID     SEQ_SEC_SESSIONS
SEC_PROFILE_OPTIONS    OPTION_ID      SEQ_SEC_PROFILE_OPTIONS
```

### Insurance Module (INS_) — In Progress
```
INS_CLIENTS            CLIENT_ID      SEQ_INS_CLIENTS
INS_POLICIES           POLICY_ID      SEQ_INS_POLICIES
INS_POLICY_LINES       LINE_ID        SEQ_INS_POLICY_LINES
INS_RENEWALS           RENEWAL_ID     SEQ_INS_RENEWALS
INS_CLAIMS             CLAIM_ID       SEQ_INS_CLAIMS
INS_COMMISSIONS        COMMISSION_ID  SEQ_INS_COMMISSIONS
INS_INSURERS           INSURER_ID     SEQ_INS_INSURERS
INS_PRODUCTS           PRODUCT_ID     SEQ_INS_PRODUCTS
```
