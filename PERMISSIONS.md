# Permission Matrix (Draft)

## Roles
- Super Admin (Ali Aref, Ali Hasan) = ALL
- Accountant
- Project Manager
- Social Media Manager
- Photographer
- Designer
- Video Editor
- Sales
- Viewer

## Rules
- المالية (Invoices/Payments/Expenses/Cashbox/Profit Allocation) مخفية عن الجميع ما عدا Super Admin + Accountant (حسب التفويض).
- تعديل/إلغاء الفواتير: Super Admin فقط.
- Period Close: Super Admin فقط.

## High-level Permissions
- CRM: PM/Sales/Accountant/SuperAdmin (CRUD) — باقي الأدوار Read محدود.
- Projects/Tasks: PM (CRUD+Assign+Approve Deliverable), باقي الأدوار My Tasks.
- Deliverables: حسب التخصص (Designer/Editor/Photographer) Create+Update لمهامه فقط، Approve للـ PM أو SuperAdmin حسب الإعداد.
- Quotes: Sales/PM/SuperAdmin (CRUD), Accountant (Read).
- Invoices/Payments: Accountant/SuperAdmin (CRUD+Approve+Export), Sales (Read فواتيره فقط بدون تفاصيل مالية حساسة).
- HR/Payroll/Loans: Accountant/SuperAdmin فقط.
- Telegram Channels: SuperAdmin فقط.
- Reports Export Excel: SuperAdmin + Accountant (وباقي الأدوار حسب التفويض).
