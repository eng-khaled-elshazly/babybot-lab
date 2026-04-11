# BabyBot ERP — Project Overview
**Document Version:** 1.0  
**Last Updated:** April 2026  
**Owner:** خالد الشازلي (Project Lead)  
**Read this first. Every agent must understand this before touching any task.**

---

## 1. What Is BabyBot ERP?

BabyBot ERP is a full enterprise resource planning system built on **Oracle APEX 24.2**.  
It is designed for **security and guarding companies** — managing employees, contracts, clients, posts, payroll, insurance brokerage, and operations.

The system is **multi-tenant** — meaning a single installation serves multiple companies, each fully isolated from the others.

This is not a prototype. It is a **production-grade system** actively used and continuously developed.

---

## 2. Technical Stack

| Component | Details |
|-----------|---------|
| Database | Oracle 19c |
| Schema | ERP2026 |
| APEX Workspace | WS1 |
| App ID | **110** |
| APEX Version | 24.2 |
| Theme | Universal Theme (42) |
| CSS Theme | BabyBot White Gold + Dark Mode |
| Git Repo | babybot-lab (GitHub) |

---

## 3. Database Standards — MANDATORY

Every table in this system follows these exact standards. No exceptions.

### 3.1 Naming Conventions

```
Tables:      PREFIX_ENTITY_NAME     (e.g. SEC_USERS, HR_EMPLOYEES, INS_POLICIES)
Sequences:   SEQ_TABLE_NAME         (e.g. SEQ_SEC_USERS)
Primary Key: TABLE_ID               (e.g. USER_ID, EMPLOYEE_ID)
Indexes:     IDX_TABLE_COL          (e.g. IDX_SEC_USERS_TENANT)
```

### 3.2 Mandatory Columns — Every Table Must Have These

```sql
-- Identity
TABLE_ID         NUMBER PRIMARY KEY    -- from dedicated sequence
TENANT_ID        NUMBER NOT NULL       -- multi-tenancy root
COMPANY_ID       NUMBER NOT NULL       -- company isolation
BRANCH_ID        NUMBER                -- branch level (nullable)

-- Audit
CREATED_BY       NUMBER
CREATED_DATE     DATE DEFAULT SYSDATE
UPDATED_BY       NUMBER
UPDATED_DATE     DATE

-- Status
IS_ACTIVE        NUMBER(1) DEFAULT 1   -- 1=active, 0=inactive
IS_DELETED       NUMBER(1) DEFAULT 0   -- soft delete

-- Spare Columns (for future use — always add these)
ATTR1  VARCHAR2(4000),  ATTR2  VARCHAR2(4000),  ..., ATTR10 VARCHAR2(4000)
ATTR11 NUMBER,          ATTR12 NUMBER,          ..., ATTR20 NUMBER
ATTR21 DATE,            ATTR22 DATE,            ..., ATTR25 DATE
```

### 3.3 Sequence Standard

```sql
CREATE SEQUENCE SEQ_TABLE_NAME
  START WITH 1
  INCREMENT BY 1
  NOCACHE
  NOCYCLE;
```

### 3.4 Composite Index Standard

```sql
CREATE INDEX IDX_TABLE_TENANT ON TABLE_NAME (TENANT_ID, COMPANY_ID);
```

---

## 4. APEX Application Structure

### 4.1 Page ID Ranges

```
0        → Global Page
1        → Home / Dashboard  
9999     → Login Page
1000-1099 → Security Module
1100-1199 → HR Module (planned)
2000-2099 → Riada Insurance Module (in progress)
```

### 4.2 Application Items (Global Session State)

These items exist on Page 0 and are populated at login via BB_POST_AUTH:

```
P0_TENANT_ID
P0_COMPANY_ID
P0_USER_ID
P0_USER_NAME
P0_ROLE_ID
P0_IS_SUPER_ADMIN
P0_IS_TENANT_ADMIN
P_LANG              → 'AR' or 'EN'
P_THEME_STYLE       → 'DARK' or 'WHITE_GOLD'
```

### 4.3 Verified Template IDs (App 110 — DO NOT GUESS)

```
Button Template:          4072362960822175091
Field Required:           2526760615038828570
Field Optional:           2318601014859922299
Region IR:                2100526641005906379
Region Form:              4072358936313175081
Modal Dialog:             2101157952850466385
Workspace ID:             1802032451027025
```

---

## 5. The Two Active Modules

### MODULE 1: Security Module (90% Complete)

**Purpose:** Full user management, role-based access control, audit, and monitoring.

**Status:** Built and functional. Some items still being finalized.

**Pages Built (Page IDs 1000–1016):**

| Page | Name | Status |
|------|------|--------|
| 1000 | Security Dashboard | ✅ Done |
| 1001 | User List (IR) | ✅ Done |
| 1002 | User Form (Modal) | ✅ Done |
| 1003 | Groups List (IR) | ✅ Done |
| 1004 | Group Form (Modal) | ✅ Done |
| 1005 | Permissions Matrix | ✅ Done |
| 1006 | Profile Settings | ✅ Done |
| 1008 | Audit Log | ✅ Done |
| 1009 | Login History | ✅ Done |
| 1010 | Active Sessions | ✅ Done |
| 1011 | Failed Logins | ✅ Done |
| 1012 | Permission Changes | ✅ Done |
| 1013 | Data Changes | ✅ Done |
| 1014 | System Events | ✅ Done |
| 1015 | Security Alerts | ✅ Done |
| 1016 | Reports | ✅ Done |

**Key Tables:**

```
SEC_USERS           → system users
SEC_GROUPS          → user groups / roles
SEC_PERMISSIONS     → permissions per group per page
SEC_AUDIT_LOG       → every action logged
SEC_LOGIN_HISTORY   → login/logout history
SEC_SESSIONS        → active sessions
SEC_PROFILE_OPTIONS → system configuration
```

**Authorization Scheme:** `BB_HAS_ACCESS`
— checks SEC_PERMISSIONS before rendering any page.

**Known Open Issues:**
- Login page (9999) theming not applied yet
- Bilingual labels (AR/EN) incomplete on some pages
- RTL/LTR menu direction switching unresolved
- Session timeout page needs work

---

### MODULE 2: Riada Insurance Module (In Progress)

**Purpose:** Full insurance brokerage management system.  
This module serves **ريادة لوساطة التأمين** as a client.  
It covers: clients, policies, renewals, claims, commissions, and reports.

**Status:** Under active development. Architecture defined. DB tables being built.

**Page Range:** 2000–2099

**Key Entities:**

```
INS_CLIENTS         → insurance clients
INS_POLICIES        → insurance policies
INS_POLICY_LINES    → policy coverage lines
INS_RENEWALS        → renewal tracking
INS_CLAIMS          → claims management
INS_COMMISSIONS     → broker commissions
INS_INSURERS        → insurance companies
INS_PRODUCTS        → insurance products
```

**Business Rules:**
- Every policy linked to one client
- Renewal alert 30/60/90 days before expiry
- Commission calculated automatically on policy issue
- Claims tracked with status workflow (Open → In Review → Settled → Closed)

---

## 6. Multi-Theme Architecture

The app supports two visual themes switchable per user:

```
WHITE_GOLD   → professional light theme (default)
DARK         → dark mode
```

Theme is stored in `SEC_USERS.MODULE_CARD_STYLE` and loaded server-side via HTML region with `<link>` tag using `&P_THEME_STYLE.` substitution.

**Rule:** Never inject theme via JavaScript. Always server-side.

---

## 7. The Agent Team

| Agent | Role | Responsibilities |
|-------|------|-----------------|
| **01-claude-leader** | Architect + Task Definer | System design, task specs, APEX script templates |
| **02-cursor** | Execution Engine | SQL, PL/SQL, APEX scripts — runs what 01 defines |
| **03-copilot** | Speed Assistant | CSS fixes, JS snippets, quick refinements |
| **04-chatgpt** | Second Opinion | Consulted only when there is genuine ambiguity |
| **09-claude-assi** | Support | Documentation, research, analysis tasks |

**Golden Rule for all agents:**
> خالد is the Orchestrator. No agent takes initiative without explicit instruction.

---

## 8. Development Workflow

```
خالد defines the task
    ↓
01-claude-leader produces:
    - Task definition (what, why, rules)
    - APEX script skeleton (ready for execution)
    ↓
02-cursor executes the script
    ↓
03-copilot handles CSS/JS refinements
    ↓
خالد reviews and approves
    ↓
git commit by 01 or 02
```

---

## 9. Where to Find Everything

```
PROJECT/          → understand the system (start here)
PATTERNS/rules/   → mandatory rules before writing any code
PATTERNS/templates/ → APEX script skeletons
PATTERNS/error-kb/  → if you hit an error, check here first
SCREENS/done/     → completed screens documentation
SCREENS/in-progress/ → screens with known issues
SCREENS/planned/  → upcoming screens
WORKSPACE/        → current tasks and status
```

---

**If you read nothing else — read PATTERNS/rules/golden-rules.md before writing a single line.**
