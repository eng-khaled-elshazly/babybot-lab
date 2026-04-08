# 📄 APEX Pages — مرجع الصفحات

**Agent 05 — Pages Reference**

---

## 📋 نظرة عامة

قائمة بصفحات APEX المنفذة في App 110.

**App:** BabyBot ERP (ID: 110)  
**APEX Version:** 24.2.0  
**Current Module:** Insurance (RIADA)

---

## 🏠 الصفحة الرئيسية

| Page | Type | Title | Status |
|------|------|-------|--------|
| 0 | Global Page | Global Settings | ✅ Active |

---

## 🛡️ Security Module (1000-1999)

| Page | Type | Title | Status |
|------|------|-------|--------|
| 1001 | IR | Users | ✅ Working |
| 1002 | Modal Form | User Form | ✅ Working |
| 1003 | IR | Roles | ✅ Working |
| 1004 | Modal Form | Role Form | ✅ Working |
| 1005 | IR | Permissions | ✅ Working |
| ... | ... | ... | ... |

---

## 🏢 Insurance Module (2000-2999)

### ✅ Working Pages:

| Page | Type | Title (AR) | Table | Status |
|------|------|------------|-------|--------|
| 2003 | IR | وثائق التأمين | BB_INSURANCE_POLICIES | ✅ IR Working |
| 2004 | Modal Form | نموذج الوثيقة | BB_INSURANCE_POLICIES | ✅ CRUD Complete |

### 🟡 Need Fixes:

| Page | Type | Title (AR) | Issue |
|------|------|------------|-------|
| 2001 | IR | أنواع الوثائق | Missing Edit Link + Create + Refresh DA |
| 2005 | IR | شركات التأمين | Missing Edit Link + Create + Refresh DA |
| 2006 | IR | الوسطاء | Missing Edit Link + Create + Refresh DA |
| 2008 | IR | أنواع العملاء | Missing Edit Link + Create + Refresh DA |
| 2011 | IR | أنواع التغطية | Missing Edit Link + Create + Refresh DA |
| 2014 | IR | حالات المطالبات | Missing Edit Link + Create + Refresh DA |

### ⏳ Not Started:

| Page | Type | Title (AR) | Table |
|------|------|------------|-------|
| 2002 | Modal Form | نموذج نوع الوثيقة | BB_INSURANCE_POLICY_TYPES |
| 2007 | Modal Form | نموذج شركة التأمين | BB_INSURANCE_COMPANIES |
| 2009 | Modal Form | نموذج نوع العميل | BB_INSURANCE_CLIENT_TYPES |
| 2012 | Modal Form | نموذج نوع التغطية | BB_INSURANCE_COVERAGE_TYPES |
| 2015 | Modal Form | نموذج حالة المطالبة | BB_INSURANCE_CLAIM_STATUS |
| 2010 | IR | العملاء | BB_INSURANCE_CLIENTS |
| 2013 | IR | المطالبات | BB_INSURANCE_CLAIMS |
| 2000 | Dashboard | لوحة التحكم | Multiple |

---

## 📝 Page Patterns

### Interactive Report (IR):
- Region Type: Interactive Report
- Template: `2100526641005906379`
- Edit Link: Required (f?p=&APP_ID.:FORM_PAGE:&SESSION.::&DEBUG.:FORM_PAGE:P_ID:\#ID#)
- Refresh DA: Required after modal close

### Modal Form:
- Page Mode: Modal Dialog
- Template: `2101157952850466385`
- Size: 600x400 (default)
- Primary Key Item: Hidden, Source = Sequence

---

## 🎯 Common Issues & Solutions

### Issue 1: Edit Link Missing
**Solution:** Add link column in IR with proper f?p syntax

### Issue 2: Create Button Missing
**Solution:** Add button (BTN_CREATE) with redirect to modal

### Issue 3: Refresh DA Missing
**Solution:** Add DA on modal close → Refresh IR region

---

## 📊 Progress

**Total Pages Planned:** 17  
**Completed:** 2 (12%)  
**Need Fixes:** 6 (35%)  
**Not Started:** 9 (53%)

---

**Last Updated:** 2026-04-08
