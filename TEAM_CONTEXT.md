# TEAM CONTEXT — BabyBot AI Lab
## Updated: 2026-04-08
## For: All Agents (09 + 01 + 03 + 05)

---

## 👥 الفريق

| Agent | Platform | الدور |
|-------|----------|-------|
| **MEGA** (خالد الشازلي) | Human | Project Owner — القرارات النهائية |
| **09** | Claude (claude.ai) | Senior Architect |
| **01** | Claude Code (CLI/VS Code) | Executor — Oracle + Git + BB_BUILDER |
| **03** | ChatGPT | APEX Builder + CSS + Front-end |
| **05** | Kilo Code (VS Code) | Code Generation + Second Opinion |

---

## 🧠 فلسفة الفريق — القاعدة الأساسية

```
كلنا بنفكر سوا في كل تاسك.
كل agent يقرأ التاسك ويبدي رأيه.
لو شفت خطأ — قوله فوراً مع الحل.
لو عندك اقتراح أحسن — قوله.
ما فيش حد صح وحده — الفريق أذكى من أي فرد.
```

**قبل أي تنفيذ:**

```
09  → يحلل ويقترح الأفضل
01  → يشوف لو في مشاكل تنفيذية
03  → يشوف لو في مشاكل UI/UX
05  → يقترح بدائل أو يراجع الكود
MEGA → يوافق ويتخذ القرار النهائي
```

---

## 🏗️ البنية التقنية

```
Database:   Oracle 19c — Schema: ERP2026
APEX:       24.2.0 — App 110 (BabyBot ERP)
ORDS:       localhost:8080
Git Repo:   mega-engineering-hub (private — الكود الحقيقي)
Lab Repo:   babybot-lab (public — مكتب الفريق)
OS:         Windows 11
Python:     3.x (BB_BUILDER)
```

---

## 📦 BB_BUILDER — أداة التشغيل الآلي

```
أداة Python بتولد شاشات APEX تلقائياً.

المكونات:
context_export.py  → يصدّر schema الجداول الحقيقية
claude_bridge.py   → يتصل بـ Claude API
oracle_runner.py   → ينفذ SQL على Oracle
bb_build.py        → يولد صفحة واحدة
bb_batch.py        → يولد module كامل من JSON

قاعدة ذهبية:
context_export.py أولاً دايماً — قبل أي BB_BUILDER batch
```

---

## 🗂️ مكتب الفريق (babybot-lab)

```
babybot-lab/ (public)
├── README.md
├── TEAM_CONTEXT.md        ← أنت بتقرأه دلوقتي
├── PATTERNS.md            ← القواعد الذهبية المتراكمة
├── DECISIONS.md           ← القرارات الكبيرة وسببها
├── TEMP/                  ← ملفات مؤقتة (بتتحذف بعد الاستخدام)
│   ├── onboarding/        ← ملفات تعريف مؤقتة
│   └── exports/           ← exports مؤقتة
├── WORKSPACE/
│   ├── TASKS.md           ← التاسكات النشطة
│   └── STATUS.md          ← حالة كل شاشة
└── AGENTS/
    ├── 09-claude/
    │   ├── notes.md
    │   └── designs/
    ├── 01-claude/
    │   ├── notes.md
    │   └── scripts/
    ├── 03-chatgpt/
    │   ├── notes.md
    │   └── css/
    └── 05-kilocode/
        ├── notes.md
        └── snippets/
```

**قاعدة الـ TEMP:**

```
أي ملف في TEMP/ = مؤقت.
بعد ما الـ agent يقرأه وينتهي منه → يتحذف.
الكود الحقيقي في mega-engineering-hub (private).
babybot-lab = مكتب تواصل مش مخزن كود.
```

---

## 🔄 طريقة العمل

```
1. MEGA يكتب التاسك في WORKSPACE/TASKS.md
2. كل agent يقرأ ويبدي رأيه
3. نتناقش ونحدد الخطة الأفضل سوا
4. MEGA يوافق
5. ننفذ
6. 01 يعمل git commit في الريبو الخاص
7. نحدث STATUS.md
```

---

## 🧠 القواعد الذهبية (8 Rules — من تجارب حقيقية)

```
Rule 1: BB_BUILDER — context_export أولاً دايماً
Rule 2: Navigation — يدوي من APEX Builder فقط (SQL مش بيشتغل)
Rule 3: Modal CSS — JavaScript في "Execute when Page Loads"
Rule 4: LOV في Modal — :P0_TENANT_ID مش V('P0_TENANT_ID')
Rule 5: DML Process — process_when_button_id = NULL
Rule 6: Auto-Generated PK — item_default = NULL
Rule 7: BB_INIT_SESSION — BB_CTX_PKG wrapped في EXCEPTION
Rule 8: LOV Display — CODE || ' - ' || NAME_AR
```

---

## 🎯 Template IDs — App 110 (لا تخمن — دي الأرقام الحقيقية)

```
Button Template:   4072362960822175091
IR Region:         2100526641005906379
Form Region:       4072358936313175081
Field Required:    2526760615038828570
Field Optional:    2318601014859922299
Modal Page:        2101157952850466385
Workspace ID:      1802032451027025
App ID:            110
```

---

## 📋 وضع المشروع الحالي

### Insurance Module (RIADA):

```
Client:     ريادة لوساطة التأمين
Tenant:     RIADA (ID=3)
Progress:   12% (2/17 شاشة)

✅ مكتمل:
- 15 جدول + 53 Lookup + بيانات تجريبية
- Navigation (10 entries)
- Page 2003: IR شغال
- Page 2004: Modal Form (INSERT+UPDATE+DELETE)

🟡 محتاجة fixes:
- Pages 2001, 2005, 2006, 2008, 2011, 2014 → Edit Link + Create + Refresh DA

⏳ لسه ما بدأتش:
- Forms: 2002, 2007, 2009, 2012, 2015
- Reports: 2010, 2013
- Dashboard: 2000
```

---

## 💡 الرؤية المستقبلية

```
دلوقتي:    Insurance Module لريادة
بعدين:     GL → HR → STK → CRM
الهدف:     BabyBot SaaS (babybot.me)
            Oracle APEX ERP للسوق المصري
            فريق AI يطور — MEGA يقود
```

---

## 📞 التواصل

```
كل التواصل عبر MEGA.
MEGA = الحلقة الرابطة بين كل الـ agents.

للأسئلة والاقتراحات: ابعت لـ MEGA مباشرة.
للقرارات الكبيرة: لازم موافقة MEGA أولاً.
```

---

**آخر تحديث: 2026-04-08 | بواسطة: Agent 09**
