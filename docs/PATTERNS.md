# 🎨 الأنماط المعتمدة — Approved Patterns

---

## 📋 نظرة عامة

هذا الملف يحتوي على الأنماط والحلول التي تم اختبارها واعتمادها من قبل الفريق.

---

## 🗂️ فهرس الأنماط

### 1. Development Patterns

#### PATTERN-001: Arabic Text Encoding in Oracle APEX

**Category:** Development  
**Status:** ✅ Approved  
**Author:** Agent 01  
**Date:** 2026-04-08

**Problem:**
عند توليد صفحات APEX، النصوص العربية تظهر مكسورة/مشوهة (Ø¹Ø±ÙˆØ¶ بدلاً من عروض).

**Root Cause:**
Oracle لا يحفظ النصوص العربية بشكل صحيح عند استخدام plain text في SQL scripts.

**Solution:**
استخدام `UNISTR()` لكل نص عربي في APEX SQL:

```sql
-- ❌ WRONG
,p_step_title=>'عروض أسعار التأمين'

-- ✅ CORRECT
,p_step_title=>UNISTR('\0639\0631\0648\0636 \0623\0633\0639\0627\0631 \0627\0644\062A\0623\0645\064A\0646')
```

**Implementation:**
1. في SQL Scripts المولدة:
   - Page titles: `p_step_title=>UNISTR('...')`
   - Region names: `p_plug_name=>UNISTR('...')`
   - Item labels: `p_prompt=>UNISTR('...')`
   - Button labels: `p_button_image_alt=>UNISTR('...')`

2. في UPDATE statements على Database:
   ```sql
   UPDATE APEX_240200.WWV_FLOW_STEP_ITEMS
   SET prompt = UNISTR('\0627\0644\0639\0645\064A\0644')
   WHERE flow_id = 110 AND name = 'P2002_CLIENT_ID';
   ```

3. Python converter:
   ```python
   def to_unistr(arabic_text):
       result = []
       for char in arabic_text:
           if ord(char) > 127:
               result.append(f'\\{ord(char):04X}')
           else:
               result.append(char)
       return ''.join(result)
   ```

**Example:**
```sql
-- Converting "العميل" to UNISTR
Text: العميل
UNISTR: '\0627\0644\0639\0645\064A\0644'

Full SQL:
wwv_flow_imp_page.create_page_item(
 p_id=>wwv_flow_imp.id(200200003)
,p_name=>'P2002_CLIENT_ID'
,p_prompt=>UNISTR('\0627\0644\0639\0645\064A\0644')
,p_display_as=>'NATIVE_TEXT_FIELD'
);
```

**Checklist:**
- [ ] All `p_step_title` with UNISTR()
- [ ] All `p_plug_name` with UNISTR()
- [ ] All `p_prompt` with UNISTR()
- [ ] All `p_button_image_alt` with UNISTR()
- [ ] UPDATE statements use UNISTR()
- [ ] Test in APEX after execution
- [ ] Clear browser cache (Ctrl+Shift+R)

**Related:**
- SKILLS/lessons_learned/2026-04-08_ARABIC_ENCODING_UNISTR.md
- CLAUDE.md Rule 8

### 2. Architecture Patterns
*سيتم إضافة الأنماط هنا*

### 3. UI/UX Patterns
*سيتم إضافة الأنماط هنا*

### 4. Automation Patterns
*سيتم إضافة الأنماط هنا*

---

## 📝 نموذج Pattern

```markdown
## PATTERN-XXX: Pattern Name

**Category:** Development / Architecture / UI / Automation
**Status:** Draft / Approved / Deprecated
**Author:** Agent XX
**Date:** YYYY-MM-DD

### Problem
[وصف المشكلة]

### Solution
[وصف الحل]

### Implementation
[خطوات التنفيذ]

### Example
[مثال عملي]

### Related
[أنماط ذات صلة]
```

---

**Last Updated:** 2026-04-08
