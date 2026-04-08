# ⚖️ سجل القرارات — Decision Log

---

## 📋 نظرة عامة

سجل القرارات الهندسية والاستراتيجية المتخذة في المختبر.

**Format:** Architecture Decision Records (ADR)

---

## 🗂️ القرارات

### DEC-001: إنشاء المختبر
**Date:** 2026-04-08  
**Status:** Accepted  
**Decided by:** Agent 09 + Agent 01

**Context:**
حاجة لمساحة تعاونية مستقلة لتطوير الأنماط والحلول خارج الـ projects الرئيسية.

**Decision:**
إنشاء ريبو مستقل `babybot-lab` على GitHub.

**Consequences:**
- ✅ مساحة نظيفة للتجريب
- ✅ سهولة المشاركة بين الـ Agents
- ✅ فصل التجارب عن الـ production code

---

### DEC-002: Mandatory UNISTR() for Arabic Text in APEX
**Date:** 2026-04-08  
**Status:** ✅ Accepted  
**Decided by:** Agent 01 (after troubleshooting Pages 2001+2002)

**Context:**
عند توليد صفحات APEX عبر BB_BUILDER، النصوص العربية كانت تظهر مكسورة في الـ UI:
- "عروض أسعار" → ظهرت كـ "Ø¹Ø±ÙˆØ¶ Ø£Ø³Ø¹Ø§Ø±"
- "العميل" → ظهرت كـ "Ø§Ù„Ø¹Ù…ÙŠÙ„"

السبب: Oracle لا يحفظ plain Arabic text بشكل صحيح في SQL scripts.

**Decision:**
**MANDATORY:** استخدام `UNISTR()` لكل نص عربي في APEX SQL - بدون استثناءات.

**Alternatives Considered:**
1. ❌ **UTF-8 BOM in SQL files:** لا يعمل - Oracle يتجاهله
2. ❌ **NLS_LANG environment variable:** غير موثوق - يعتمد على الـ session
3. ❌ **Plain Arabic with ALTER SESSION:** معقد ويحتاج setup في كل مرة
4. ✅ **UNISTR() encoding:** الحل الوحيد الموثوق والمحمول

**Implementation:**
```sql
-- All Arabic text in APEX SQL must use UNISTR()
,p_step_title=>UNISTR('\0639\0631\0648\0636 \0623\0633\0639\0627\0631')
,p_prompt=>UNISTR('\0627\0644\0639\0645\064A\0644')
,p_button_image_alt=>UNISTR('\062D\0641\0638')
```

**Consequences:**
- ✅ **Arabic text displays correctly** - 100% reliable
- ✅ **Portable** - Works regardless of NLS_LANG settings
- ✅ **Maintainable** - Clear encoding visible in SQL
- ⚠️ **Verbose** - SQL files larger (acceptable tradeoff)
- ⚠️ **Manual conversion needed** - Requires converter tool
- ✅ **Action Item:** Update BB_BUILDER to auto-generate UNISTR()

**Related:**
- PATTERN-001: Arabic Text Encoding in Oracle APEX
- SKILLS/lessons_learned/2026-04-08_ARABIC_ENCODING_UNISTR.md
- CLAUDE.md Rule 8

---

## 📝 نموذج Decision Record

```markdown
### DEC-XXX: Decision Title
**Date:** YYYY-MM-DD  
**Status:** Proposed / Accepted / Rejected / Deprecated  
**Decided by:** Agent XX

**Context:**
[السياق والمشكلة]

**Decision:**
[القرار المتخذ]

**Alternatives Considered:**
[البدائل التي تم النظر فيها]

**Consequences:**
- ✅ إيجابيات
- ⚠️ مخاطر
- ❌ سلبيات

**Related:**
[قرارات ذات صلة]
```

---

**Last Updated:** 2026-04-08
