# ERD (Draft) — Vision Production

> ملاحظة: هذا مسودة أولية لتوجيه Codex. سيتم توليد Prisma Schema/Migrations منها لاحقاً.

## Core Tables
- users (id, username, password_hash, is_active, role_id)
- roles (id, name)
- permissions (id, code, module, action)
- role_permissions (role_id, permission_id)

## Security & Audit
- login_attempts (id, username, ip, success, created_at)
- refresh_tokens (id, user_id, token_hash, expires_at, revoked_at)
- audit_logs (id, actor_user_id, action, entity, entity_id, payload_json, created_at)

## Currency & Cashbox
- exchange_rates (id, date, rate_usd_to_iqd, created_by)
- cashbox (id=1, balance_iqd, balance_usd)
- cash_transactions (id, type, currency, amount, exchange_rate_used, amount_converted_iqd, reference_type, reference_id, created_at)

## CRM
- clients (id, name, category, notes, created_at)
- client_contacts (id, client_id, name, phone, whatsapp, title, notes)

## Projects / Tasks / Deliverables
- projects (id, project_code, client_id, title, stage, status, created_at)
- tasks (id, project_id, title, assignee_user_id, due_date, status, priority)
- task_comments (id, task_id, user_id, body, created_at)
- deliverables (id, project_id, task_id, type, drive_link, telegram_link, notes, status, approved_by, approved_at)

## Quotes / Invoices / Payments
- quotes (id, client_id, project_id, currency, valid_until, status, discount_amount, total_after_discount)
- quote_items (id, quote_id, description, qty, unit_price, line_total)
- invoices (id, invoice_no, client_id, project_id, currency, status, discount_amount, total_after_discount, issued_at, voided_at, sales_rep_user_id)
- invoice_items (id, invoice_id, description, qty, unit_price, line_total)
- payments (id, invoice_id, currency, amount, exchange_rate_used, amount_converted_iqd, paid_at, method)

## Expenses (including recurring)
- expense_categories (id, name)
- expense_rules (id, category_id, title, amount, currency, day_of_month, due_date, is_active)
- expenses (id, category_id, title, amount, currency, due_date, paid_at, status, generated_from_rule_id)

## HR / Payroll / Loans
- employees (id, user_id, base_salary, salary_currency, status)
- loans (id, employee_id, principal, currency, monthly_deduction, start_date, status)
- payroll_runs (id, month, year, status, approved_by, approved_at, closed_at)
- payroll_items (id, payroll_run_id, employee_id, base_salary, bonuses, deductions, loan_deduction, commissions_total, shares_total, net_pay)

## Commissions / Shares
- commission_records (id, invoice_id, sales_rep_user_id, base_amount_after_discount, rate, amount, month, year, status)
- share_records (id, invoice_id, employee_user_id, base_amount_after_discount, percent, amount, month, year, status)

## Profit Allocation
- profit_allocations (id, month, year, revenue_iqd, expenses_iqd, payroll_iqd, net_profit_iqd, dev_fund_percent, dev_fund_iqd, partner_a_percent, partner_a_iqd, partner_b_percent, partner_b_iqd, approved_by, approved_at)

## Telegram
- telegram_channels (id, name, purpose, channel_link, channel_id, is_default)
- telegram_messages (id, channel_id, message_type, payload_json, sent_at, status, error)
