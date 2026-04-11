# APEX Page Templates — App 110
**These are the verified skeletons for generating APEX pages.**
**Always start from here — never from scratch.**

---

## MANDATORY SCRIPT HEADER (Every Script)

```sql
SET DEFINE OFF
SET VERIFY OFF

prompt --application/pages/page_XXXXX
begin
wwv_flow_imp.component_begin (
 p_version_yyyy_mm_dd=>'2024.11.30'
,p_release=>'24.2.0'
,p_default_workspace_id=>1802032451027025
,p_default_application_id=>110
,p_default_id_offset=>0
,p_default_owner=>'ERP2026'
);
```

---

## TYPE 1 — Interactive Report Page

**Use for:** Read-only list pages, audit views, reports

```sql
SET DEFINE OFF
SET VERIFY OFF

prompt --application/pages/page_XXXXX
begin
wwv_flow_imp.component_begin (
 p_version_yyyy_mm_dd=>'2024.11.30'
,p_release=>'24.2.0'
,p_default_workspace_id=>1802032451027025
,p_default_application_id=>110
,p_default_id_offset=>0
,p_default_owner=>'ERP2026'
);

-- Create Page
wwv_flow_imp_page.create_page(
 p_id=>XXXX                                    -- Page ID (number)
,p_name=>UNISTR('\XXXX')                        -- Page name in Arabic via UNISTR
,p_page_mode=>'NORMAL'
,p_step_title=>UNISTR('\XXXX')
,p_autocomplete_on_off=>'OFF'
,p_page_template_options=>'#DEFAULT#'
,p_protection_level=>'C'
,p_page_component_map=>'18'
,p_last_updated_by=>'ERP2026'
);

-- Authorization
wwv_flow_imp_page.create_page_authorization(
 p_page_id=>XXXX
,p_authorization_scheme=>'BB_HAS_ACCESS'       -- Always use this
);

-- Breadcrumb Region
wwv_flow_imp_page.create_page_plug(
 p_id=>wwv_flow_imp.id(UNIQUE_ID)
,p_plug_name=>UNISTR('\XXXX')
,p_region_template_options=>'#DEFAULT#:t-BreadcrumbRegion--useBreadcrumbTitle'
,p_component_template_options=>'#DEFAULT#'
,p_plug_template=>4072362960822175091             -- Breadcrumb template
,p_plug_display_sequence=>10
,p_plug_display_point=>'REGION_POSITION_01'
);

-- IR Region
wwv_flow_imp_page.create_page_plug(
 p_id=>wwv_flow_imp.id(UNIQUE_ID)
,p_plug_name=>UNISTR('\XXXX')
,p_region_template_options=>'#DEFAULT#'
,p_component_template_options=>'#DEFAULT#'
,p_plug_template=>2100526641005906379             -- Region IR template ID
,p_plug_display_sequence=>20
,p_attribute_01=>'N'
,p_attribute_02=>'HTML'
,p_plug_source=>q'[
    SELECT
        COL1,
        COL2,
        COL3
    FROM TABLE_NAME
    WHERE TENANT_ID = :P0_TENANT_ID
    AND IS_DELETED = 0
    ORDER BY CREATED_DATE DESC
]'
,p_plug_source_type=>'NATIVE_IR'
);

-- IR Attributes
wwv_flow_imp_page.create_worksheet(
 p_id=>wwv_flow_imp.id(UNIQUE_ID)
,p_max_row_count=>'1000000'
,p_show_nulls_as=>'-'
,p_pagination_type=>'ROWS_X_TO_Y'
,p_pagination_display_pos=>'BOTTOM_RIGHT'
,p_report_list_mode=>'TABS'
,p_lazy_loading=>false
,p_show_detail_link=>'N'
,p_show_notify=>'Y'
,p_download_formats=>'CSV:HTML:XLSX:PDF'
,p_enable_mail_download=>'Y'
,p_owner=>'ERP2026'
,p_internal_uid=>UNIQUE_ID
);

-- IR Columns (repeat for each column)
wwv_flow_imp_page.create_worksheet_column(
 p_id=>wwv_flow_imp.id(UNIQUE_ID)
,p_db_column_name=>'COL1'
,p_display_order=>10
,p_column_identifier=>'A'
,p_column_label=>UNISTR('\XXXX')
,p_column_type=>'STRING'
,p_heading_alignment=>'CENTER'
,p_column_alignment=>'RIGHT'                      -- RIGHT for Arabic, LEFT for English
);

wwv_flow_imp.component_end;
end;
/
```

---

## TYPE 2 — Interactive Report + Modal Form

**Use for:** Full CRUD pages (List + Create/Edit)
**Requires TWO scripts:** one for IR page, one for Modal page

### Script A — IR Page (same as TYPE 1 + Add Button)

Add this after the IR region:

```sql
-- Create Button (opens Modal)
wwv_flow_imp_page.create_page_button(
 p_id=>wwv_flow_imp.id(UNIQUE_ID)
,p_button_sequence=>10
,p_button_plug_id=>wwv_flow_imp.id(IR_REGION_ID)
,p_button_name=>'CREATE'
,p_button_action=>'REDIRECT_PAGE'
,p_button_template_options=>'#DEFAULT#'
,p_button_template_id=>4072362960822175091         -- Button template ID
,p_button_image_alt=>UNISTR('\XXXX')               -- Button label
,p_button_position=>'RIGHT_OF_IR_SEARCH_BAR'
,p_button_redirect_url=>'f?p=&APP_ID.:MODAL_PAGE_ID:&SESSION.::&DEBUG.:MODAL_PAGE_ID:P_ID:'
);
```

### Script B — Modal Form Page

```sql
SET DEFINE OFF
SET VERIFY OFF

prompt --application/pages/page_XXXXX
begin
wwv_flow_imp.component_begin (
 p_version_yyyy_mm_dd=>'2024.11.30'
,p_release=>'24.2.0'
,p_default_workspace_id=>1802032451027025
,p_default_application_id=>110
,p_default_id_offset=>0
,p_default_owner=>'ERP2026'
);

-- Create Modal Page
wwv_flow_imp_page.create_page(
 p_id=>XXXX
,p_name=>UNISTR('\XXXX')
,p_page_mode=>'MODAL'                              -- MODAL not NORMAL
,p_step_title=>UNISTR('\XXXX')
,p_autocomplete_on_off=>'OFF'
,p_page_template_options=>'#DEFAULT#'
,p_dialog_height=>'600'
,p_dialog_width=>'800'
,p_protection_level=>'C'
);

-- Hidden PK Item
wwv_flow_imp_page.create_page_item(
 p_id=>wwv_flow_imp.id(UNIQUE_ID)
,p_name=>'P_ID'
,p_item_sequence=>10
,p_item_plug_id=>wwv_flow_imp.id(REGION_ID)
,p_display_as=>'NATIVE_HIDDEN'
,p_is_primary_key=>true                           -- Mark PK
);

-- Form Region
wwv_flow_imp_page.create_page_plug(
 p_id=>wwv_flow_imp.id(UNIQUE_ID)
,p_plug_name=>UNISTR('\XXXX')
,p_region_template_options=>'#DEFAULT#'
,p_plug_template=>4072358936313175081              -- Region Form template ID
,p_plug_display_sequence=>20
,p_attribute_01=>'N'
,p_attribute_02=>'TEXT'
,p_attribute_03=>'Y'
);

-- Text Item (repeat pattern for each field)
wwv_flow_imp_page.create_page_item(
 p_id=>wwv_flow_imp.id(UNIQUE_ID)
,p_name=>'P_FIELD_NAME'
,p_item_sequence=>20
,p_item_plug_id=>wwv_flow_imp.id(REGION_ID)
,p_prompt=>UNISTR('\XXXX')
,p_display_as=>'NATIVE_TEXT_FIELD'
,p_cSize=>40
,p_field_template=>2526760615038828570             -- Required field template
,p_item_template_options=>'#DEFAULT#'
,p_attribute_01=>'N'
,p_attribute_02=>'N'
,p_attribute_03=>'N'
,p_attribute_04=>'TEXT'
,p_attribute_05=>'NONE'
);

-- Save Button
wwv_flow_imp_page.create_page_button(
 p_id=>wwv_flow_imp.id(UNIQUE_ID)
,p_button_sequence=>10
,p_button_plug_id=>wwv_flow_imp.id(REGION_ID)
,p_button_name=>'SAVE'
,p_button_action=>'SUBMIT'
,p_button_template_id=>4072362960822175091
,p_button_image_alt=>UNISTR('\062D\0641\0638')     -- حفظ
,p_button_position=>'NEXT'
,p_button_condition_type=>'ITEM_IS_NOT_NULL'
,p_button_condition=>'P_ID'
);

-- Create Button
wwv_flow_imp_page.create_page_button(
 p_id=>wwv_flow_imp.id(UNIQUE_ID)
,p_button_sequence=>20
,p_button_plug_id=>wwv_flow_imp.id(REGION_ID)
,p_button_name=>'CREATE_BTN'
,p_button_action=>'SUBMIT'
,p_button_template_id=>4072362960822175091
,p_button_image_alt=>UNISTR('\0625\0636\0627\0641\0629')  -- إضافة
,p_button_position=>'NEXT'
,p_button_condition_type=>'ITEM_IS_NULL'
,p_button_condition=>'P_ID'
);

-- Process: Fetch Row (on load)
wwv_flow_imp_page.create_page_process(
 p_id=>wwv_flow_imp.id(UNIQUE_ID)
,p_process_sequence=>10
,p_process_point=>'BEFORE_HEADER'
,p_process_type=>'NATIVE_FORM_FETCH'
,p_process_name=>'Fetch Row'
,p_attribute_02=>'TABLE_NAME'
,p_attribute_03=>'P_ID'
,p_attribute_04=>'TABLE_ID'
,p_process_error_message=>UNISTR('\062D\062F\062B \062E\0637\0623 \0641\064A \062C\0644\0628 \0627\0644\0628\064A\0627\0646\0627\062A')
,p_process_condition_type=>'ITEM_IS_NOT_NULL'
,p_process_condition=>'P_ID'
);

-- Process: Insert/Update (on submit)
wwv_flow_imp_page.create_page_process(
 p_id=>wwv_flow_imp.id(UNIQUE_ID)
,p_process_sequence=>20
,p_process_point=>'AFTER_SUBMIT'
,p_process_type=>'NATIVE_FORM_DML'
,p_process_name=>'Save Record'
,p_attribute_01=>'TABLE_NAME'
,p_attribute_02=>'TABLE_ID'
,p_attribute_03=>'P_ID'
,p_attribute_05=>'Y'                               -- Insert allowed
,p_attribute_06=>'Y'                               -- Update allowed
,p_attribute_07=>'N'                               -- Delete not from here
,p_process_error_message=>UNISTR('\062D\062F\062B \062E\0637\0623 \0641\064A \0627\0644\062D\0641\0638')
,p_process_success_message=>UNISTR('\062A\0645 \0627\0644\062D\0641\0638 \0628\0646\062C\0627\062D')
);

-- Close Dialog on Success
wwv_flow_imp_page.create_page_process(
 p_id=>wwv_flow_imp.id(UNIQUE_ID)
,p_process_sequence=>30
,p_process_point=>'AFTER_SUBMIT'
,p_process_type=>'NATIVE_CLOSE_WINDOW'
,p_process_name=>'Close Dialog'
,p_error_display_location=>'INLINE_IN_NOTIFICATION'
);

wwv_flow_imp.component_end;
end;
/
```

---

## UNISTR Quick Reference

| Arabic | UNISTR |
|--------|--------|
| حفظ | `UNISTR('\062D\0641\0638')` |
| إضافة | `UNISTR('\0625\0636\0627\0641\0629')` |
| تعديل | `UNISTR('\062A\0639\062F\064A\0644')` |
| حذف | `UNISTR('\062D\0630\0641')` |
| إلغاء | `UNISTR('\0625\0644\063A\0627\0621')` |
| بحث | `UNISTR('\0628\062D\062B')` |
| الاسم | `UNISTR('\0627\0644\0627\0633\0645')` |
| الوصف | `UNISTR('\0627\0644\0648\0635\0641')` |
| نشط | `UNISTR('\0646\0634\0637')` |
| تاريخ الإنشاء | `UNISTR('\062A\0627\0631\064A\062E \0627\0644\0625\0646\0634\0627\0621')` |
