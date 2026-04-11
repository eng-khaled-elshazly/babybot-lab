# App 110 — Screens Inventory
**Total Pages:** 56
**Last Updated:** April 2026
**Source:** apex_application_pages (live query)

---

## Security Module — Pages 1000–1099

| Page ID | Name | Type | Authorization | Status | Notes |
|---------|------|------|---------------|--------|-------|
| 1000 | Security Dashboard | Normal | BB_HAS_ACCESS | ✅ Done | |
| 1001 | Users List | Normal | BB_HAS_ACCESS | ✅ Done | IR |
| 1002 | User Form | Modal | — | ✅ Done | Create/Edit |
| 1003 | Groups | Normal | — | ⚠️ Check Auth | Missing BB_HAS_ACCESS |
| 1004 | Group Form | Normal | — | ⚠️ Check Auth | Missing BB_HAS_ACCESS |
| 1005 | Permissions Matrix | Normal | ADMIN_ONLY | ✅ Done | |
| 1006 | Profile Settings | Normal | — | ⚠️ In Progress | RTL/LTR unresolved |
| 1007 | Profile Setting Form | Modal | BB_HAS_ACCESS | ✅ Done | |
| 1008 | Audit Log | Normal | BB_HAS_ACCESS | ✅ Done | Read-only IR |
| 1009 | Login History | Normal | BB_HAS_ACCESS | ✅ Done | Read-only IR |
| 1010 | Active Sessions | Normal | BB_HAS_ACCESS | ✅ Done | |
| 1011 | IP Rules | Normal | BB_HAS_ACCESS | ✅ Done | |
| 1012 | IP Rule Form | Modal | BB_HAS_ACCESS | ✅ Done | |
| 1013 | Security Alerts | Normal | BB_HAS_ACCESS | ✅ Done | |
| 1014 | Temp Elevations | Normal | BB_HAS_ACCESS | ✅ Done | |
| 1015 | Roles | Normal | BB_HAS_ACCESS | ✅ Done | |
| 1016 | Role Form | Modal | BB_HAS_ACCESS | ✅ Done | |
| 1017 | SOD Rules List | Normal | BB_HAS_ACCESS | ✅ Done | |
| 1018 | SOD Rule Form | Modal | BB_HAS_ACCESS | ✅ Done | |
| 1019 | Role Permissions | Normal | BB_HAS_ACCESS | ✅ Done | |
| 1020 | Security Reports | Normal | BB_HAS_ACCESS | ✅ Done | |
| 1021 | System Settings | Normal | ADMIN_ONLY | ✅ Done | |
| 1022 | Branches List | Normal | ADMIN_ONLY | ✅ Done | |
| 1023 | Branch Form | Modal | — | ⚠️ Check Auth | Missing auth |
| 1024 | Companies List | Normal | ADMIN_ONLY | ✅ Done | |
| 1025 | Company Form | Modal | ADMIN_ONLY | ✅ Done | |
| 1026 | Modules Catalog | Normal | BB_HAS_ACCESS | ✅ Done | |
| 1027 | Module Form | Modal | BB_HAS_ACCESS | ✅ Done | |

---

## Insurance Module — Pages 2000–2099

| Page ID | Name | Type | Authorization | Status | Notes |
|---------|------|------|---------------|--------|-------|
| 2001 | Quotations List | Normal | — | ⚠️ No Auth | Add BB_HAS_ACCESS |
| 2002 | Quotation Form | Modal | — | ⚠️ No Auth | Add BB_HAS_ACCESS |
| 2003 | Policies List | Normal | — | ⚠️ No Auth | Add BB_HAS_ACCESS |
| 2004 | Policy Form | Modal | — | ⚠️ No Auth | Add BB_HAS_ACCESS |
| 2005 | Policy Details | Normal | — | ⚠️ No Auth | Add BB_HAS_ACCESS |
| 2006 | Endorsements View | Normal | — | ⚠️ No Auth | |
| 2007 | Endorsement Form | Modal | — | ⚠️ No Auth | |
| 2008 | Claims View | Normal | — | ⚠️ No Auth | |
| 2009 | Claim Form | Modal | — | ⚠️ No Auth | |
| 2010 | Loss Ratio Report | Normal | — | ⚠️ No Auth | |
| 2011 | Commissions List | Normal | — | ⚠️ No Auth | |
| 2014 | Cancellations View | Normal | — | ⚠️ No Auth | |
| 2015 | Cancellation Form | Modal | — | ⚠️ No Auth | |
| 2016 | Endorsements List | Normal | — | ⚠️ No Auth | |
| 2017 | Endorsement Details | Normal | — | ⚠️ No Auth | |
| 2018 | Clients List | Modal | — | ⚠️ Wrong Type | Should be Normal not Modal |

---

## Admin / Reports — Pages 9000+

| Page ID | Name | Type | Authorization | Status | Notes |
|---------|------|------|---------------|--------|-------|
| 9051 | User Roles Report | Normal | — | ⚠️ Check | |
| 9052 | Security Reports | Normal | — | ⚠️ Check | |
| 9053 | Roles View | Normal | — | ⚠️ Check | |
| 9060 | Tenant Modules | Normal | — | ⚠️ Check | |
| 9073 | Roles IR | Normal | — | ⚠️ Check | Duplicate of 1015? |
| 9080 | BB Users View | Normal | — | ⚠️ Check | |
| 9081 | BB Users Form | Modal | — | ⚠️ Check | |

---

## System Pages

| Page ID | Name | Notes |
|---------|------|-------|
| 0 | Global Page | Shared components |
| 1 | Home | Landing dashboard |
| 999 | Debug Session | Dev only — restrict in prod |
| 9998 | Change Password | Modal |
| 9999 | Login | Theme not applied yet |

---

## Issues Summary

### 🔴 High Priority
- Page 2018 (Clients List) is Modal type — should be Normal
- All Insurance pages (2001–2018) have NO authorization scheme

### 🟡 Medium Priority
- Pages 1003, 1004, 1023 missing BB_HAS_ACCESS
- Page 9999 (Login) theme not applied
- Page 6 RTL/LTR direction switching unresolved
- Pages 9051–9081 need audit — possible duplicates

### 🟢 Low Priority
- Page 999 (Debug) should be restricted in production
- Bilingual labels incomplete on multiple pages

---

## Pages Still Needed (Planned)

| Page ID | Name | Module |
|---------|------|--------|
| 2000 | Insurance Dashboard | Insurance |
| 2012 | Clients List (Normal) | Insurance |
| 2013 | Client Form | Insurance |
| 2019 | Installments List | Insurance |
| 2020 | Documents List | Insurance |
| 2021 | Notifications | Insurance |
