# 📋 BabyBot ERP (App 110) — Complete Reference

**App ID:** 110  
**APEX Version:** 24.2.0  
**Workspace ID:** 1802032451027025  
**Owner:** ERP2026

---

## 🎯 Page Groups

| ID | Name |
|----|------|
| 32716297034904487 | Administration |

---

## 📱 Pages Overview

### Authentication & System Pages
| Page | Name (Arabic) | Purpose |
|------|--------------|---------|
| 9998 | تغيير كلمة المرور | Password Change |
| 9999 | حساب مغلق | Account Locked |

### Home
| Page | Name | Purpose |
|------|------|---------|
| 1 | Home | Main landing page |

### Security Module (Pages 1000-1026)
| Page | Name (Arabic) | Purpose |
|------|--------------|---------|
| 1000 | لوحة التحكم | Dashboard |
| 1001 | المستخدمين | Users |
| 1003 | الأدوار | Roles |
| 1005 | الأمان | Security |
| 1006 | الملف الشخصي | Profile |
| 1007 | سجل الصفحات | Page Registry |
| 1008 | سجل الدخول | Login Audit |
| 1009 | سجل العمليات | Audit Log |
| 1010 | صندوق الوارد | Inbox |
| 1011 | قواعد IP | IP Rules |
| 1013 | الأجهزة | Devices |
| 1014 | الجلسات النشطة | Sessions |
| 1015 | الفروع | Branches |
| 1017 | قواعد الأمان | Security Rules |
| 1019 | تصعيدات الأمان | Security Escalations |
| 1020 | تتبع SOP | SOP Tracking |
| 1021 | صلاحيات المستخدمين | User Permissions |
| 1022 | صحة النظام | System Health |
| 1024 | إعدادات الوحدة | Module Settings |
| 1026 | إعدادات النمط | Theme Settings |

### Insurance Module (Pages 2000-2025)
| Page | Name (Arabic) | Purpose |
|------|--------------|---------|
| 2001 | طلبات التأمين | Insurance Requests |
| 2003 | سجل الوثائق | Policies Record |
| 2004 | نموذج الوثيقة | Policy Form (Modal) |
| 2005 | العملاء | Clients |
| 2006 | شركات التأمين | Insurance Companies |
| 2008 | طلبات الأسعار | Quote Requests |
| 2011 |优惠政策 | Endorsements |
| 2014 | الوثائق | Documents |
| 2016 | المطالemons | Claims |
| 2017 | الإلغاءات | Cancellations |
| 2018 | العمولات | Commissions |
| 2021 | التنبيهات | Notifications |

---

## 🔧 Shared Components IDs (Template IDs)

### Templates
| Component | ID |
|-----------|-----|
| Button Template | 4072362960822175091 |
| IR Region | 2100526641005906379 |
| Form Region | 4072358936313175081 |
| Field Required | 2526760615038828570 |
| Field Optional | 2318601014859922299 |
| Modal Page | 2101157952850466385 |

### Navigation List
| List | ID |
|------|-----|
| Navigation Menu | 32711283373904440 |

### Application Processes
| Process | Point | Purpose |
|---------|-------|---------|
| BB_INIT_SESSION | BEFORE_HEADER | Initialize user session, tenant, context |
| BB_SET_LANGUAGE | ON_DEMAND | Change language |
| SAVE_AVATAR | ON_DEMAND | Save user avatar |
| SAVE_PREF | AJAX | Save preferences |

---

## 🔗 Navigation Menu Structure

### Security (Parent: 1000000001)
- Dashboard → Page 1000
- Users → Page 1001
- Roles → Page 1003
- User Permissions → Page 1021
- الصلاحيات → Page 1005
- الإعدادات → Page 1006
- Page Registry → Page 1007
- سجل الدخول → Page 1008
- سجل العمليات → Page 1009
- صندوق الوارد → Page 1010
- قواعد IP → Page 1011
- الأجهزة → Page 1013
- الجلسات النشطة → Page 1014
- الفروع → Page 1015
- صحة النظام → Page 1022
- إعدادات الوحدة → Page 1024
- إعدادات النمط → Page 1026

### التأمين (Parent: 64432962624293671)
- طلبات التأمين → Page 2001
- سجل الوثائق → Page 2003
- طلبات الأسعار → Page 2005
-优惠政策 → Page 2006
- الوثائق → Page 2008
- المطالعات → Page 2011
- الإلغاءات → Page 2014
- العمولات → Page 2016
- التنبيهات → Page 2017

---

## 🔑 Key Application Items (Page 0)

| Item | Purpose |
|------|---------|
| P0_TENANT_ID | Current Tenant ID |
| P0_COMPANY_ID | Current Company ID |
| P0_IS_SUPER_ADMIN | Super Admin Flag |
| P0_IS_TENANT_ADMIN | Tenant Admin Flag |
| P_USER_ID | Current User ID |
| P_USER_NAME | Username |
| P_FULL_NAME | Full Name |
| P_BRANCH_ID | Branch ID |
| P_BRANCH_NAME | Branch Name |
| P_PROFILE_IMAGE | User Image Path |
| P_LANG | Language (ar/en) |
| P_THEME_STYLE | Theme (dark/light) |
| P_MUST_CHANGE_PWD | Password Change Required |

---

## 🎨 Global Page (Page 0) Features

- **RTL/LTR Support:** Dynamic based on P_LANG
- **CSS Framework:** White-Gold custom CSS
- **BB_INIT_SESSION:** Runs on every page
- **Language Switch:** bbSwitchLang() function

---

## 📊 Database Tables (42 total)

### Insurance (INS_*)
- INS_AUDIT_LOG, INS_CANCELLATIONS, INS_CLAIMS
- INS_CLIENTS, INS_COMMISSIONS, INS_DOCUMENTS
- INS_ENDORSEMENTS, INS_INSURER_QUOTES
- INS_LOOKUPS, INS_NOTIFICATIONS
- INS_POLICIES, INS_POLICY_DETAILS
- INS_POLICY_INSTALLMENTS, INS_QUOTATIONS

### Security (SEC_*)
- SEC_USERS, SEC_TENANTS, SEC_ROLES
- SEC_BRANCHES, SEC_COMPANIES
- SEC_MODULES_CATALOG, SEC_PAGES
- SEC_PAGE_FUNCTIONS, SEC_PAGE_HELP
- SEC_PROFILE_OPTIONS, SEC_PROFILE_VALUES
- SEC_USER_ROLES, SEC_USER_SESSIONS
- SEC_AUDIT_LOG, SEC_LOGIN_ATTEMPTS

---

## 🔐 Security Patterns

### Access Control Function
```sql
ERP2026.BB_SECURITY_PKG.HAS_ACCESS(
    TO_NUMBER(V('P_USER_ID')),
    'MODULE-CODE',
    'VIEW/INSERT/UPDATE/DELETE'
)
```

### Display Condition Example
```sql
V('P0_IS_SUPER_ADMIN') = 'Y' OR
ERP2026.BB_SECURITY_PKG.HAS_ACCESS(
    TO_NUMBER(V('P_USER_ID')),
    'SEC-Dashboard',
    'VIEW'
)
```

---

*Last Updated: 2026-04-08 | Source: f110 export*