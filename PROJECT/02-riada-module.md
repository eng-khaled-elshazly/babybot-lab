# Riada Insurance Module — Full Reference
**Module Prefix:** INS_
**Page Range:** 2000–2099
**Status:** ~35% Complete — Under Active Development
**Client:** ريادة لوساطة التأمين

---

## 1. Purpose

Full insurance brokerage management system covering:
- Client management
- Quotations and price comparison across insurers
- Policy issuance and management
- Endorsements (policy modifications)
- Claims management
- Cancellations and refunds
- Commission tracking
- Installment payment plans
- Document management
- Renewal notifications

---

## 2. Database — 14 Tables

### 2.1 Table Relationship Map

```
INS_CLIENTS (root)
    ↓
INS_QUOTATIONS ──→ INS_INSURER_QUOTES (one quotation, many insurer offers)
    ↓
INS_POLICIES (core) ──→ INS_POLICY_DETAILS (vehicle/property/cargo/medical details)
    ↓                ──→ INS_POLICY_INSTALLMENTS (payment schedule)
    ↓                ──→ INS_COMMISSIONS (broker earnings)
    ↓                ──→ INS_ENDORSEMENTS (policy modifications)
    ↓                ──→ INS_CANCELLATIONS (policy termination)
    ↓                ──→ INS_CLAIMS (loss events)
    ↓                ──→ INS_DOCUMENTS (files attached to any entity)

INS_LOOKUPS (central reference data — types, statuses, codes)
INS_NOTIFICATIONS (alerts and reminders)
INS_AUDIT_LOG (every change tracked)
```

---

### 2.2 INS_CLIENTS — Insurance Clients

**PK:** CLIENT_ID | **Sequence:** INS_CLIENTS_SEQ | **Last#:** 5

| Column | Type | Null | Notes |
|--------|------|------|-------|
| CLIENT_ID | NUMBER | NOT NULL | PK |
| CLIENT_TYPE | VARCHAR2(20) | NOT NULL | 'INDIVIDUAL' or 'CORPORATE' |
| CLIENT_NAME_AR | VARCHAR2(500) | NOT NULL | Arabic name (required) |
| CLIENT_NAME_EN | VARCHAR2(500) | Y | English name |
| MOBILE | VARCHAR2(20) | Y | |
| PHONE | VARCHAR2(20) | Y | |
| EMAIL | VARCHAR2(200) | Y | |
| NATIONAL_ID | VARCHAR2(50) | Y | For individuals |
| DATE_OF_BIRTH | DATE | Y | For individuals |
| COMMERCIAL_REG | VARCHAR2(50) | Y | For corporates |
| TAX_CARD | VARCHAR2(50) | Y | For corporates |
| ADDRESS | VARCHAR2(1000) | Y | |
| CITY | VARCHAR2(200) | Y | |
| LOSS_RATIO_3Y | NUMBER | Y | 3-year loss ratio % |
| RISK_SCORE | NUMBER | Y | Internal risk scoring |
| IS_ACTIVE | VARCHAR2(1) | NOT NULL | 'Y'/'N' |
| TENANT_ID | NUMBER | NOT NULL | Multi-tenancy |
| CREATED_BY/DATE | | | Audit |
| ATTR1–ATTR25 | | Y | Spare columns |

---

### 2.3 INS_QUOTATIONS — Quotation Requests

**PK:** QUOTATION_ID | **Sequence:** INS_QUOTATIONS_SEQ | **Last#:** 3

| Column | Type | Null | Notes |
|--------|------|------|-------|
| QUOTATION_ID | NUMBER | NOT NULL | PK |
| CLIENT_ID | NUMBER | NOT NULL | FK → INS_CLIENTS |
| INS_TYPE_ID | NUMBER | NOT NULL | FK → INS_LOOKUPS (insurance type) |
| INS_SUBTYPE_ID | NUMBER | Y | FK → INS_LOOKUPS (sub-type) |
| QUOTATION_NO | VARCHAR2(100) | Y | Auto-generated reference |
| REQUEST_DATE | DATE | NOT NULL | |
| REQUIRED_START | DATE | Y | Required coverage start |
| REQUIRED_END | DATE | Y | Required coverage end |
| SUM_INSURED_REQ | NUMBER | Y | Requested sum insured |
| RISK_DESCRIPTION | VARCHAR2(4000) | Y | |
| SALES_REP_ID | NUMBER | Y | FK → SEC_USERS |
| QUOTATION_STATUS | VARCHAR2(50) | Y | PENDING/SENT/RECEIVED/CONVERTED/CANCELLED |
| SELECTED_QUOTE_ID | NUMBER | Y | FK → INS_INSURER_QUOTES (chosen offer) |
| NOTES | VARCHAR2(4000) | Y | |

---

### 2.4 INS_INSURER_QUOTES — Insurer Offers per Quotation

**PK:** INSURER_QUOTE_ID | **Sequence:** INS_INSURER_QUOTES_SEQ

| Column | Type | Null | Notes |
|--------|------|------|-------|
| INSURER_QUOTE_ID | NUMBER | NOT NULL | PK |
| QUOTATION_ID | NUMBER | NOT NULL | FK → INS_QUOTATIONS |
| INSURER_ID | NUMBER | NOT NULL | FK → insurer (lookup) |
| SENT_DATE | DATE | Y | When sent to insurer |
| RECEIVED_DATE | DATE | Y | When offer received |
| NET_PREMIUM | NUMBER | Y | |
| GROSS_PREMIUM | NUMBER | Y | |
| COMMISSION_RATE | NUMBER | Y | % |
| COVERAGE_DETAILS | VARCHAR2(4000) | Y | |
| CONDITIONS | VARCHAR2(4000) | Y | |
| VALID_UNTIL | DATE | Y | Quote expiry |
| QUOTE_STATUS | VARCHAR2(50) | Y | PENDING/RECEIVED/SELECTED/REJECTED |

---

### 2.5 INS_POLICIES — Insurance Policies (Core Table)

**PK:** POLICY_ID | **Sequence:** INS_POLICIES_SEQ | **Last#:** 93

| Column | Type | Null | Notes |
|--------|------|------|-------|
| POLICY_ID | NUMBER | NOT NULL | PK |
| POLICY_REF_NO | VARCHAR2(100) | Y | Internal reference |
| POLICY_NO | VARCHAR2(100) | NOT NULL | Official policy number from insurer |
| CLIENT_ID | NUMBER | NOT NULL | FK → INS_CLIENTS |
| INS_TYPE_ID | NUMBER | NOT NULL | FK → INS_LOOKUPS |
| INS_SUBTYPE_ID | NUMBER | Y | FK → INS_LOOKUPS |
| INSURER_ID | NUMBER | NOT NULL | Insurance company |
| SALES_REP_ID | NUMBER | Y | FK → SEC_USERS |
| QUOTATION_ID | NUMBER | Y | FK → INS_QUOTATIONS (if from quote) |
| RENEWAL_OF_ID | NUMBER | Y | FK → INS_POLICIES (if renewal) |
| ISSUE_DATE | DATE | NOT NULL | |
| START_DATE | DATE | NOT NULL | Coverage start |
| END_DATE | DATE | NOT NULL | Coverage end — renewal alerts based on this |
| SUM_INSURED | NUMBER | Y | |
| NET_PREMIUM | NUMBER | Y | |
| GROSS_PREMIUM | NUMBER | Y | |
| COMMISSION_RATE | NUMBER | Y | % broker commission |
| COMMISSION_AMT | NUMBER | Y | Calculated amount |
| EXTERNAL_COMM_RATE | NUMBER | Y | % external agent commission |
| EXTERNAL_COMM_AMT | NUMBER | Y | |
| DEDUCTIBLES | VARCHAR2(1000) | Y | |
| BENEFITS | VARCHAR2(4000) | Y | |
| VALIDITY | VARCHAR2(500) | Y | |
| BENEFICIARY | VARCHAR2(500) | Y | |
| RISK_DESCRIPTION | VARCHAR2(4000) | Y | |
| POLICY_STATUS | VARCHAR2(50) | Y | ACTIVE/EXPIRED/CANCELLED/SUSPENDED |
| PAYMENT_STATUS | VARCHAR2(50) | Y | PAID/PARTIAL/UNPAID |
| DELIVERY_STATUS | VARCHAR2(50) | Y | PENDING/DELIVERED/RETURNED |
| NOTES | VARCHAR2(4000) | Y | |

---

### 2.6 INS_POLICY_DETAILS — Policy-Specific Risk Details

**⚠️ This is a polymorphic table** — stores different data depending on insurance type.

**PK:** DETAIL_ID | **Sequence:** INS_POLICY_DETAILS_SEQ

| Detail Type | Relevant Columns |
|-------------|-----------------|
| VEHICLE | VEHICLE_TYPE, VEHICLE_MAKE, VEHICLE_MODEL, VEHICLE_YEAR, CHASSIS_NO, ENGINE_NO, PLATE_NO, DRIVE_LICENSE_NO/EXP, VEH_LICENSE_NO/EXP |
| PROPERTY | PROPERTY_ADDRESS, PROPERTY_AREA, BUSINESS_TYPE, STOCK_VALUE, BUILDING_VALUE, CONTENTS_VALUE |
| CARGO | CARGO_TYPE, TRANSPORT_MODE, PORT_FROM, PORT_TO, VESSEL_NAME, BILL_OF_LADING |
| ENGINEERING | PROJECT_TYPE, PROJECT_VALUE, PROJECT_DURATION, CONTRACTOR_NAME, PROJECT_LOCATION |
| MEDICAL | GROUP_NAME, MEMBERS_COUNT, TPA_NAME, COVERAGE_SCOPE, NETWORK_TYPE |
| LIFE | INSURED_NAME, INSURED_DOB, INSURED_AGE, COVERAGE_TYPE, MATURITY_DATE |
| GUARANTEE | GUARANTEE_TYPE, BENEFICIARY_BANK, LOAN_AMOUNT |

**Rule:** Always filter by DETAIL_TYPE when querying this table.

---

### 2.7 INS_CLAIMS — Claims Management

**PK:** CLAIM_ID | **Sequence:** INS_CLAIMS_SEQ

| Column | Type | Null | Notes |
|--------|------|------|-------|
| CLAIM_ID | NUMBER | NOT NULL | PK |
| POLICY_ID | NUMBER | NOT NULL | FK → INS_POLICIES |
| INCIDENT_DATE | DATE | NOT NULL | When incident happened |
| INCIDENT_DESC | VARCHAR2(4000) | NOT NULL | |
| NOTIFY_DATE | DATE | Y | When broker notified |
| CLAIM_FILE_NO | VARCHAR2(100) | Y | Insurer claim reference |
| OUTSTANDING_AMT | NUMBER | Y | Estimated claim amount |
| SETTLED_AMT | NUMBER | Y | Final settlement |
| SETTLEMENT_DATE | DATE | Y | |
| PAYMENT_METHOD | VARCHAR2(50) | Y | CHEQUE/TRANSFER |
| CLAIM_STATUS | VARCHAR2(50) | Y | OPEN/IN_REVIEW/SETTLED/CLOSED/REJECTED |

**Status Workflow:** OPEN → IN_REVIEW → SETTLED → CLOSED

---

### 2.8 INS_ENDORSEMENTS — Policy Modifications

**PK:** ENDORSEMENT_ID | **Sequence:** INS_ENDORSEMENTS_SEQ

| Column | Type | Notes |
|--------|------|-------|
| ENDORSEMENT_ID | NUMBER | PK |
| POLICY_ID | NUMBER | FK → INS_POLICIES |
| ENDORS_TYPE_ID | NUMBER | FK → INS_LOOKUPS |
| ENDORS_NO | VARCHAR2(100) | Official endorsement number |
| ENDORS_DATE | DATE | NOT NULL |
| EFFECTIVE_DATE | DATE | When change takes effect |
| DESCRIPTION | VARCHAR2(4000) | NOT NULL — what changed |
| ADDITIONAL_PREMIUM | NUMBER | Extra premium if any |
| NEW_GROSS_PREMIUM | NUMBER | New total premium after endorsement |
| NEW_END_DATE | DATE | If date extended |
| NEW_SUM_INSURED | NUMBER | If coverage increased |
| ENDORS_STATUS | VARCHAR2(50) | PENDING/APPROVED/REJECTED |

---

### 2.9 INS_CANCELLATIONS

**PK:** CANCEL_ID | **Sequence:** INS_CANCELLATIONS_SEQ

| Column | Type | Notes |
|--------|------|-------|
| CANCEL_ID | NUMBER | PK |
| POLICY_ID | NUMBER | FK → INS_POLICIES |
| REQUEST_DATE | DATE | NOT NULL |
| CANCEL_DATE | DATE | Actual cancellation date |
| CANCEL_REASON_ID | NUMBER | FK → INS_LOOKUPS |
| ORIGINAL_PREMIUM | NUMBER | |
| REMAINING_DAYS | NUMBER | Days left in policy |
| REFUND_AMOUNT | NUMBER | Calculated refund |
| REFUND_STATUS | VARCHAR2(50) | PENDING/PROCESSED/REJECTED |

---

### 2.10 INS_COMMISSIONS — Broker Commission Tracking

**PK:** COMMISSION_ID | **Sequence:** INS_COMMISSIONS_SEQ

Key columns: POLICY_ID, COMMISSION_RATE, NET_PREMIUM_AFTER_TAX, SALES_REP_COMM_RATE/AMT, EXTERNAL_COMM_RATE/AMT, NET_COMMISSION, PAYMENT_STATUS, INVOICE_NO, INVOICE_DATE

---

### 2.11 INS_POLICY_INSTALLMENTS — Payment Schedule

**PK:** INSTALLMENT_ID | **Sequence:** INS_POLICY_INSTALLMENTS_SEQ

Key columns: POLICY_ID, INSTALLMENT_NO, DUE_DATE (NOT NULL), AMOUNT (NOT NULL), PAID_DATE, PAID_AMOUNT, PAYMENT_REF, INSTALLMENT_STATUS (PENDING/PAID/OVERDUE)

---

### 2.12 INS_LOOKUPS — Central Reference Data

**PK:** LOOKUP_ID | **Sequence:** INS_LOOKUPS_SEQ | **Last#:** 57 (already seeded)

**Structure:** LOOKUP_TYPE + LOOKUP_CODE + LOOKUP_NAME_AR + LOOKUP_NAME_EN

**LOOKUP_TYPE values used across the system:**
```
INS_TYPE        → Motor / Fire / Marine / Medical / Engineering / Life / Guarantee
INS_SUBTYPE     → Sub-types per insurance type
CANCEL_REASON   → Reasons for cancellation
ENDORS_TYPE     → Types of endorsements
CLAIM_STATUS    → Claim workflow statuses
POLICY_STATUS   → Policy statuses
PAYMENT_STATUS  → Payment statuses
DELIVERY_STATUS → Document delivery statuses
```

**Standard LOV Query Pattern:**
```sql
SELECT LOOKUP_NAME_AR, LOOKUP_CODE
FROM INS_LOOKUPS
WHERE LOOKUP_TYPE = 'INS_TYPE'
AND IS_ACTIVE = 'Y'
AND TENANT_ID = :P0_TENANT_ID
ORDER BY SORT_ORDER
```

---

### 2.13 INS_DOCUMENTS — Document Management

Polymorphic — attaches to any entity via ENTITY_TYPE + ENTITY_ID.

| ENTITY_TYPE values |
|-------------------|
| POLICY |
| CLAIM |
| QUOTATION |
| CLIENT |
| ENDORSEMENT |

---

### 2.14 INS_NOTIFICATIONS — Renewal Alerts & Reminders

Automated notifications for: renewal alerts (30/60/90 days), payment due dates, claim updates.

---

### 2.15 INS_AUDIT_LOG — Full Change History

Every INSERT/UPDATE/DELETE on any INS_ table is logged here with OLD_VALUE and NEW_VALUE.

---

## 3. Pages Built (Current Status)

| Page | Name | Type | Status | Issues |
|------|------|------|--------|--------|
| 2001 | Quotations List | Normal IR | ⚠️ Built | No authorization |
| 2002 | Quotation Form | Modal | ⚠️ Built | No authorization |
| 2003 | Policies List | Normal IR | ⚠️ Built | No authorization |
| 2004 | Policy Form | Modal | ⚠️ Built | No authorization |
| 2005 | Policy Details | Normal | ⚠️ Built | No authorization |
| 2006 | Endorsements View | Normal IR | ⚠️ Built | No authorization |
| 2007 | Endorsement Form | Modal | ⚠️ Built | No authorization |
| 2008 | Claims View | Normal IR | ⚠️ Built | No authorization |
| 2009 | Claim Form | Modal | ⚠️ Built | No authorization |
| 2010 | Loss Ratio Report | Normal | ⚠️ Built | No authorization |
| 2011 | Commissions List | Normal IR | ⚠️ Built | No authorization |
| 2014 | Cancellations View | Normal IR | ⚠️ Built | No authorization |
| 2015 | Cancellation Form | Modal | ⚠️ Built | No authorization |
| 2016 | Endorsements List | Normal IR | ⚠️ Built | Duplicate of 2006? |
| 2017 | Endorsement Details | Normal | ⚠️ Built | No authorization |
| 2018 | Clients List | **Modal** ❌ | ⚠️ Wrong type | Should be Normal |

---

## 4. Pages Still Needed

| Page | Name | Priority |
|------|------|----------|
| 2000 | Insurance Dashboard | HIGH |
| 2012 | Clients List (Normal — replace 2018) | HIGH |
| 2013 | Client Form (Modal) | HIGH |
| 2019 | Installments List | MEDIUM |
| 2020 | Documents List | MEDIUM |
| 2021 | Notifications | LOW |

---

## 5. Critical Issues to Fix (Immediate Tasks)

### 🔴 Priority 1 — Authorization
All Insurance pages (2001–2018) have NO authorization scheme.
**Fix:** Add `BB_HAS_ACCESS` to all pages.

### 🔴 Priority 2 — Page 2018 Wrong Type
Page 2018 (Clients) is Modal — should be a Normal page.
**Fix:** Rebuild as Normal page, assign ID 2012.

### 🟡 Priority 3 — Possible Duplicates
Pages 2006 and 2016 both appear to be Endorsements views.
**Fix:** Audit both pages, keep correct one.

---

## 6. Business Rules

- Every policy must have a client (CLIENT_ID NOT NULL)
- Every policy must have an insurer (INSURER_ID NOT NULL)
- Renewal alerts fire at 90, 60, and 30 days before END_DATE
- Commission calculated automatically when policy status becomes ACTIVE
- Claims can only be filed against ACTIVE policies
- Cancellation refund = (REMAINING_DAYS / total days) × ORIGINAL_PREMIUM
- Endorsements that add premium must update GROSS_PREMIUM on parent policy
- All financial amounts stored as raw numbers — no currency symbol in DB

---

## 7. Sequences Summary

| Sequence | Last# | Notes |
|----------|-------|-------|
| INS_CLIENTS_SEQ | 5 | 4 clients exist |
| INS_POLICIES_SEQ | 93 | 92 policies created |
| INS_LOOKUPS_SEQ | 57 | 56 lookup values seeded |
| INS_QUOTATIONS_SEQ | 3 | 2 quotations |
| All others | 1 | Not yet used |
