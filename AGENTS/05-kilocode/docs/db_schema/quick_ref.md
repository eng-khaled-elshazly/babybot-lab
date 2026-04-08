# 🗄️ Database Schema — مرجع سريع

**Agent 05 — Quick Reference**

---

## 📋 نظرة عامة

مرجع سريع لأهم الجداول في المشروع.

**Schema:** ERP2026 (Oracle 19c)  
**Current Module:** Insurance (RIADA)

---

## 📊 الجداول الرئيسية

### Insurance Module (RIADA)

#### Master Tables:
```
BB_INSURANCE_POLICIES          -- وثائق التأمين
BB_INSURANCE_CLAIMS            -- المطالبات
BB_INSURANCE_CLIENTS           -- العملاء
BB_INSURANCE_BROKERS           -- الوسطاء
BB_INSURANCE_COMPANIES         -- شركات التأمين
```

#### Lookup Tables:
```
BB_INSURANCE_POLICY_TYPES      -- أنواع الوثائق
BB_INSURANCE_CLAIM_STATUS      -- حالات المطالبات
BB_INSURANCE_CLIENT_TYPES      -- أنواع العملاء
BB_INSURANCE_COVERAGE_TYPES    -- أنواع التغطية
```

---

## 🔑 الحقول المشتركة (Standard Columns)

```sql
-- كل جدول master يحتوي على:
ID                NUMBER        -- Primary Key
TENANT_ID         NUMBER        -- Multi-tenancy
CREATED_BY        VARCHAR2(50)  -- Audit
CREATED_DATE      DATE          -- Audit
MODIFIED_BY       VARCHAR2(50)  -- Audit
MODIFIED_DATE     DATE          -- Audit
IS_ACTIVE         CHAR(1)       -- Y/N
```

---

## 📝 ملاحظات مهمة

### Naming Conventions:
- **Master Tables:** `BB_{MODULE}_{ENTITY}` (e.g., BB_INSURANCE_POLICIES)
- **Lookup Tables:** `BB_{MODULE}_{ENTITY}` (e.g., BB_INSURANCE_POLICY_TYPES)
- **Views:** `V_{MODULE}_{ENTITY}` (e.g., V_INSURANCE_POLICIES)

### Primary Keys:
- **Sequence Pattern:** `SEQ_{TABLE_NAME}`
- **Trigger Pattern:** `TRG_{TABLE_NAME}_BI` (Before Insert)

### Multi-Tenancy:
- كل جدول لازم فيه `TENANT_ID`
- كل query لازم يفلتر بـ `:P0_TENANT_ID`

---

## 🎯 Resources

**Complete Objects Inventory:**  
📄 [ERP2026_OBJECTS.md](./ERP2026_OBJECTS.md) — Full list of all 322 objects in schema

**Full Schema Export:**  
`mega-engineering-hub/tools/BB_BUILDER/context_exports/`

**DDL Scripts:**  
`mega-engineering-hub/analysis/modules/Insurance/ddl/`

---

**Last Updated:** 2026-04-08
