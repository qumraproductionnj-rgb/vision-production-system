# Vision Production (رؤية للإنتاج الفني) — System Specification (SPEC)

## 1) Overview
نظام إدارة لشركة تسويق/تصوير/إنتاج باسم "رؤية للإنتاج الفني" يعمل كـ Web App Responsive (PWA) على الموبايل والكمبيوتر بواجهة عربية RTL بالكامل.

### Goals
- إدارة العملاء والمشاريع والمهام والتسليمات بروابط فقط (Drive/Telegram).
- إدارة مالية دقيقة بعملتين IQD/USD مع دعم دفع مختلط داخل نفس الفاتورة.
- رواتب + قروض/سلف + عمولات/نسب تُحسب تلقائياً.
- توزيع أرباح شهري: 10% صندوق تطوير + 45% علي عارف + 45% علي حسن (قابل للتعديل من Super Admin).
- صلاحيات دقيقة RBAC مع منع رؤية المالية لأي موظف إلا بتفويض من المالك.
- Audit Log إلزامي لكل العمليات الحساسة.
- تقارير + Excel Export + نسخ احتياطي يومي/أسبوعي.

## 2) Users & Security
### Authentication
- Username + Password فقط.
- Password Policy قوية (min length, complexity).
- Lockout بعد محاولات فاشلة + سجل محاولات.
- JWT + Refresh Tokens.

### Super Admin (صلاحيات مطلقة)
- علي عارف
- علي حسن
فقط السوبر أدمن يستطيع:
- تعديل/إلغاء الفواتير.
- إقفال الشهر Period Close.
- عرض كل المالية.
- إدارة الصلاحيات.
- تصدير Excel/PDF (حسب السياسة).

### Audit Log (إلزامي)
يسجل: Create/Update/Delete/Approve/Export + الوقت + المستخدم + نوع الكيان + ID + التغييرات الأساسية.

## 3) Currency & Cashbox
- العملات: IQD و USD.
- خزنة واحدة Cashbox لكن تحتفظ برصيدين منفصلين:
  - BalanceIQD
  - BalanceUSD
- ExchangeRates: سجل يومي/بالتاريخ.
- كل حركة مالية تحفظ:
  - currency, amount, exchange_rate_used (يدوي لكل عملية), amount_converted (للتقارير).

## 4) Files Policy
- لا يوجد رفع ملفات داخل النظام.
- كل مهمة/تسليم: روابط فقط:
  - Google Drive link
  - Telegram post link (اختياري)

## 5) Telegram Integration
### Channels
عدة قنوات تلگرام:
1) قناة التصميم
2) قناة المونتاج
3) قناة الخطة الشهرية للزبون
4) قناة الكابشن

### Telegram Channels Settings Screen
- Add/Edit/Delete Channel:
  - name
  - channel_username_or_link
  - purpose: design | editing | plan | caption | reports
  - is_default_for_role (اختياري)
- Bot اختياري:
  - bot_token + channel_id
  - تخزين token بشكل مشفر في DB/ENV
- عند اعتماد تسليم Deliverable:
  - اختيار القناة تلقائياً حسب نوع المهمة:
    - design -> design channel
    - editing -> editing channel
    - plan -> plan channel
    - caption -> caption channel
  - يرسل البوت رسالة تحتوي:
    Client + ProjectCode + DeliverableType + DriveLink + Notes
- إذا ماكو Bot:
  - فقط يخزن رابط منشور تلگرام الذي يدخله المستخدم

## 6) Modules

### 6.1 Dashboard
- مؤشرات: دخل/صرف/صافي، رصيد الخزنة IQD/USD، ذمم العملاء، مشاريع قيد التنفيذ، فواتير غير مدفوعة، رواتب مستحقة، عمولات/نسب مستحقة، مصاريف تشغيل.
- فلاتر: فترة + عميل + مشروع + عملة + موظف.
- تنبيهات: تسليم متأخر / فاتورة متأخرة / إقفال شهر.

### 6.2 CRM العملاء
- ملف عميل مفصل:
  - الاسم
  - أرقام متعددة
  - واتساب
  - عنوان
  - جهات اتصال
  - ملاحظات
  - تصنيف: VIP/دائم/جديد/متعثر
- حساب العميل:
  - فواتير + دفعات + رصيد IQD/USD + كشف حساب.

### 6.3 Quotes (عروض أسعار)
- Quote مرتبط بعميل + مشروع + بنود + عملة + مدة صلاحية.
- حالات: Draft / Sent / Approved / Rejected / Expired.
- تحويل Quote إلى Invoice بضغطة بعد الاعتماد.

### 6.4 Projects & Tasks
- Project مرتبط بعميل + مراحل:
  تخطيط/تصوير/مونتاج/تصميم/تسليم/مغلق
- Project ID تلقائي:
  VP-PROJ-XXXXXX
- Tasks:
  - assignee
  - due date
  - status
  - priority
  - comments
  - links (Drive/Telegram)
- Deliverables:
  - type (design/edit/plan/caption/other)
  - drive link
  - notes
  - approval workflow
  - optional telegram link
- محضر تسليم PDF اختياري.

### 6.5 Accounting (Invoices / Payments / Expenses)
#### Invoices
- الفاتورة بعملة أساس invoice_currency (IQD أو USD).
- بنود + خصم (Discount Box) مدعوم.
- Invoice Status: Draft / Issued / PartiallyPaid / Paid / Voided
- تعديل/إلغاء الفاتورة:
  - مسموح فقط Super Admin
  - عند التعديل: إعادة احتساب النسب تلقائياً
  - عند الإلغاء (Void): تصفير النسب تلقائياً

#### Mixed Payments (داخل نفس الفاتورة)
- Payments تدعم دفع مختلط:
  - يمكن إضافة دفعة IQD ودفعة USD لنفس الفاتورة
  - لكل دفعة exchange_rate_used يدوي
  - النظام يحسب أثر الدفعات على الرصيدين Cashbox وعلى تقارير التحويل

#### Expenses
- مصروفات بتصنيفات:
  إعلانات، إيجار، كهرباء، إنترنت، نقل، معدات، اشتراكات، أخرى...
- سندات قبض/صرف.
- Period Close:
  لا يُفتح إلا Super Admin.

#### Reports
- P&L, Cashflow, تقرير عميل, تقرير مشروع, مصروفات حسب التصنيف, كشف خزنة.
- Export PDF + Excel.

### 6.6 HR (Employees / Payroll / Loans)
- Employee:
  بيانات + راتب شهري ثابت + عملة + حالة.
- Loans:
  سلفة/قرض + جدول خصم تلقائي من الرواتب.
- Payroll شهري:
  - راتب أساسي
  - سلف/قروض
  - خصومات
  - مكافآت
  - عمولات/نِسب
  - اعتماد الرواتب قبل ترحيلها للمالية
- بعد إقفال الشهر: لا تعديل.

### 6.7 Commissions & Shares Rules
- تُحتسب عند إصدار الفاتورة (Issued) وليس عند التحصيل.
- تُصرف شهرياً ضمن Payroll لنفس الشهر قبل الإقفال.
- تُحتسب من إجمالي الفاتورة بعد الخصم (Gross After Discount).

A) Sales Commission
- لكل موظف مبيعات:
  - Target شهري
  - نسب/شرائح (configurable)
- ربط الفاتورة بـ SalesRep
- عند إصدار الفاتورة: توليد CommissionRecord تلقائياً

B) Project Shares
- توزيع نسب متغيرة على الموظفين المشاركين (على مستوى الفاتورة افتراضياً)
- عند إصدار الفاتورة: توليد EmployeeShareRecord تلقائياً
- يمكن تعديلها قبل الإقفال حسب صلاحية المحاسب/السوبر أدمن

### 6.8 Profit Allocation (توزيع أرباح الشركاء)
- Net Profit الشهري:
  Revenue - Operating Expenses - Payroll(+Commissions/Shares) - Other
- Development Fund:
  10% من صافي الربح (قابل للتعديل)
- Partner Split:
  - علي عارف: 45%
  - علي حسن: 45%
  (قابل للتعديل)
- يولد تقرير شهري + قيود مالية تلقائياً (Journal Entries) عند الاعتماد.

### 6.9 RBAC Permissions
- واجهة إدارة صلاحيات للسوبر أدمن فقط.
- Roles:
  Accountant, Project Manager, Social Media Manager, Photographer, Designer, Video Editor, Sales, Viewer
- Permissions granular:
  Create/Read/Update/Delete/Approve/Export
- Data Scopes:
  My Items Only / All
- إخفاء كل البيانات المالية عن غير المخولين.

### 6.10 Role-Based Screens (واجهة خاصة لكل تخصص)
- Social Media Manager:
  تقويم محتوى + مهام نشر/ردود + خطة شهرية + روابط قنوات
- Photographer:
  جدول تصوير + مهام تصوير + تسليم روابط
- Designer:
  مهام تصميم + مراجعات + تسليم روابط + قناة التصميم
- Video Editor:
  مهام مونتاج + نسخ V1/V2 + تسليم روابط + قناة المونتاج
- Sales:
  Leads + Quotes + Invoices الخاصة به + Target + عمولات
- Accountant:
  المالية + الرواتب + اعتماد + إقفال
- Project Manager:
  توزيع مهام + متابعة + اعتماد تسليمات ضمن صلاحياته

## 7) Scheduled Reports + Backups
- تقرير يومي 9 مساءً:
  فواتير اليوم + مصروفات اليوم + مهام متأخرة + تسليمات معتمدة + روابطها
- تقرير أسبوعي (الخميس):
  ملخص مالي + تقدم مشاريع + أفضل العملاء + تاركيت المبيعات + مستحقات
- التقارير تُرسل:
  - داخل النظام Notifications Center
  - عبر Telegram Bot للقناة المناسبة (أو قناة الإدارة/التقارير)
- Backups:
  - Daily + Weekly
- Excel Export:
  - Export on demand للتقارير الأساسية

## 8) Tech Stack (إلزامي)
- Frontend: Next.js + TypeScript + PWA + RTL
- Backend: NestJS + TypeScript
- DB: PostgreSQL + Prisma
- Docker + docker-compose للتطوير
- Security:
  rate limit, hashing (bcrypt/argon2), validation, XSS/CSRF protection, audit
