# SAANJH KI ROTI API — IMPLEMENTATION PLAN


# PHASE 4 IMPLEMENTATION PLAN

Version: 1.0


------------------------------------------------------------
PURPOSE
------------------------------------------------------------

Phase 4 completes the Saanjh Ki Roti backend system by
introducing the financial, customer support, analytics,
and reporting layer.

Building on previous phases:

Phase 1:
- Authentication
- Users
- Plans

Phase 2:
- Customers
- Routes
- Subscriptions
- Pause Management

Phase 3:
- Meal Planning
- Delivery Management
- Add-ons


Phase 4 adds:

- Billing
- Payments
- Notifications
- Referral Rewards
- Complaint Management
- Dashboard Analytics
- Monthly Reports


At the end of Phase 4, the complete business workflow
will be automated.


------------------------------------------------------------
COMPLETE BUSINESS FLOW
------------------------------------------------------------


Customer Registration

        ↓

Subscription Creation

        ↓

Daily Meal Preparation

        ↓

Delivery Completion

        ↓

Bill Generation

        ↓

Payment Collection

        ↓

Customer Support

        ↓

Business Analytics

        ↓

Monthly Reports



------------------------------------------------------------
PREREQUISITES
------------------------------------------------------------

Phase 1, Phase 2, and Phase 3 must be completed.


Required existing modules:


- User Authentication
- Customer Management
- Subscription Management
- Pause Tracking
- Meal Planning
- Delivery Management
- Add-On Management



------------------------------------------------------------
INCLUDED IN PHASE 4
------------------------------------------------------------


Billing Module

- Generate bills
- Billing cycles
- Price snapshot handling
- Discounts
- Payment settlement


------------------------------------------------------------

Payment Module

- Record payments
- Track payment status
- Early payment discount
- Payment methods


------------------------------------------------------------

Notification Module

- Payment reminders
- Auto pause notifications
- Delivery failure notifications


------------------------------------------------------------

Referral Module

- Track referrals
- Reward customers


------------------------------------------------------------

Complaint Module

- Customer complaints
- SLA tracking
- Resolution workflow
- Compensation handling


------------------------------------------------------------

Dashboard Module

- Operational statistics
- Financial statistics
- Customer service analytics


------------------------------------------------------------

Reporting Module

- Monthly PDF reports
- Report storage
- Download access


------------------------------------------------------------
NOT INCLUDED
------------------------------------------------------------

Future enhancements:


- Online payment gateway integration
- SMS provider integration
- WhatsApp automation
- Mobile applications
- Advanced AI analytics



------------------------------------------------------------
PROJECT STRUCTURE UPDATES
------------------------------------------------------------


New files introduced in Phase 4:


app/


├── api/

│
├── billing_api.py
├── payment_api.py
├── complaint_api.py
├── dashboard_api.py
├── report_api.py
├── notification_api.py
├── referral_api.py


├── models/

│
├── bill.py
├── payment.py
├── notification.py
├── complaint.py
├── referral.py
├── report.py


├── schemas/

│
├── billing_schema.py
├── payment_schema.py
├── complaint_schema.py
├── dashboard_schema.py
├── report_schema.py
├── notification_schema.py
├── referral_schema.py


├── repositories/

│
├── bill_repository.py
├── payment_repository.py
├── complaint_repository.py
├── notification_repository.py
├── report_repository.py
├── referral_repository.py


├── services/

│
├── billing_service.py
├── payment_service.py
├── complaint_service.py
├── notification_service.py
├── referral_service.py
├── dashboard_service.py
├── report_service.py


├── reports/

│
├── templates/
├── generated/



------------------------------------------------------------
DATABASE MODELS (PHASE 4)
------------------------------------------------------------


Phase 4 introduces six major models.


- Bill
- Payment
- Notification
- Complaint
- Referral
- Report



------------------------------------------------------------
BILL MODEL
------------------------------------------------------------


Purpose


Stores customer billing records.



Fields:


- id
- customer_id
- billing_period
- total_amount
- discount_amount
- final_amount
- due_date
- status
- generated_at
- paid_at



Relationships:


Customer


    |

    |

Bill


    |

    |

Payment



------------------------------------------------------------

Bill Status:


- PENDING
- PAID
- OVERDUE
- PARTIALLY_PAID
- CANCELLED



------------------------------------------------------------

Business Rules:


- Monthly bills generated on 1st day.
- Weekly bills generated every Monday.
- Bill uses subscription price_snapshot.
- Due date controls:
    - Reminder generation
    - Early payment discount
    - Overdue calculation
    - Auto pause



------------------------------------------------------------
PAYMENT MODEL
------------------------------------------------------------


Purpose


Stores customer payment transactions.



Fields:


- id
- customer_id
- bill_id
- amount
- payment_method
- paid_at



Payment Methods:


- CASH
- UPI
- KHAATA



------------------------------------------------------------

Business Rules:


- Payment must reference a valid bill.
- Paid amount cannot exceed outstanding amount.
- Multiple partial payments are allowed.
- Successful payment updates bill status.



------------------------------------------------------------
NOTIFICATION MODEL
------------------------------------------------------------


Purpose


Stores system generated notifications.



Fields:


- id
- customer_id
- bill_id
- subscription_id
- type
- sent_at
- status



Notification Types:


- PAYMENT_REMINDER
- AUTO_PAUSE
- DELIVERY_FAILED
- COMPLAINT_RESOLVED



------------------------------------------------------------

Business Rules:


- Duplicate reminders are not created.
- One notification type per bill per day.
- Notification history is stored.



------------------------------------------------------------
COMPLAINT MODEL
------------------------------------------------------------


Purpose


Stores customer complaints and resolution tracking.



Fields:


- id
- customer_id
- type
- description
- severity
- status
- created_at
- due_at
- resolved_at
- compensation_type
- compensation_detail



Complaint Types:


- LATE_DELIVERY
- COLD_FOOD
- WRONG_ORDER
- MISSING_ITEMS
- TASTE_ISSUES



------------------------------------------------------------

Severity:


LOW

Resolution:
48 Hours



MEDIUM

Resolution:
24 Hours



HIGH

Resolution:
6 Hours



------------------------------------------------------------
REFERRAL MODEL
------------------------------------------------------------


Purpose


Stores referral reward information.



Fields:


- id
- referrer_customer_id
- referred_customer_id
- reward_amount
- reward_status



Reward Status:


- PENDING
- AWARDED



------------------------------------------------------------

Business Rules:


Reward is generated only when:


Referred customer completes first paid month.



------------------------------------------------------------
REPORT MODEL
------------------------------------------------------------


Purpose


Stores generated report information.



Fields:


- id
- report_month
- file_path
- generated_at



------------------------------------------------------------
PHASE 4 WORKFLOW
------------------------------------------------------------


Billing Day


        ↓


Generate Bills


        ↓


Customer Makes Payment


        ↓


Apply Discount


        ↓


Update Payment Status


        ↓


Generate Notifications


        ↓


Track Complaints


        ↓


Generate Dashboard Data


        ↓


Create Monthly Report



------------------------------------------------------------

------------------------------------------------------------
SCHEMAS
------------------------------------------------------------

Phase 4 introduces request and response schemas for:

- Billing
- Payments
- Notifications
- Referrals
- Complaints
- Dashboard
- Reports


Separate schemas are used to:

- Validate incoming requests
- Prevent exposing internal fields
- Maintain clean API contracts


------------------------------------------------------------
BILLING SCHEMAS
------------------------------------------------------------


BillCreate


Fields:


- customer_id
- billing_period



------------------------------------------------------------


BillResponse


Fields:


- id
- customer_id
- billing_period
- total_amount
- discount_amount
- final_amount
- due_date
- status
- generated_at
- paid_at



------------------------------------------------------------
PAYMENT SCHEMAS
------------------------------------------------------------


PaymentCreate


Fields:


- bill_id
- amount
- payment_method



------------------------------------------------------------


PaymentResponse


Fields:


- id
- customer_id
- bill_id
- amount
- payment_method
- paid_at



------------------------------------------------------------
NOTIFICATION SCHEMAS
------------------------------------------------------------


NotificationCreate


Fields:


- customer_id
- bill_id
- subscription_id
- type



------------------------------------------------------------


NotificationResponse


Fields:


- id
- customer_id
- type
- status
- sent_at



------------------------------------------------------------
COMPLAINT SCHEMAS
------------------------------------------------------------


ComplaintCreate


Fields:


- type
- description
- severity



------------------------------------------------------------


ComplaintUpdate


Fields:


- status
- resolution_details
- compensation_type
- compensation_detail



------------------------------------------------------------


ComplaintResponse


Fields:


- id
- customer_id
- type
- description
- severity
- status
- created_at
- due_at
- resolved_at
- compensation_type
- compensation_detail



------------------------------------------------------------
REFERRAL SCHEMAS
------------------------------------------------------------


ReferralCreate


Fields:


- referred_customer_id



------------------------------------------------------------


ReferralResponse


Fields:


- id
- referrer_customer_id
- referred_customer_id
- reward_amount
- reward_status



------------------------------------------------------------
DASHBOARD SCHEMAS
------------------------------------------------------------


DashboardResponse


Fields:


daily_operations:

- total_tiffins
- delivered_count
- failed_count
- missed_count


financial_overview:

- revenue_collected
- outstanding_amount
- active_subscriptions


customer_service:

- open_complaints
- resolved_complaints
- overdue_complaints



------------------------------------------------------------
REPORT SCHEMAS
------------------------------------------------------------


ReportResponse


Fields:


- id
- report_month
- file_path
- generated_at



------------------------------------------------------------
REPOSITORIES
------------------------------------------------------------

Repositories handle only database interaction.

Business rules remain inside services.



------------------------------------------------------------
BILL REPOSITORY
------------------------------------------------------------


Responsibilities:


- Create bill
- Update bill status
- Find bill by ID
- Find customer bills
- Find pending bills
- Find overdue bills



------------------------------------------------------------
PAYMENT REPOSITORY
------------------------------------------------------------


Responsibilities:


- Create payment
- Get bill payments
- Calculate paid amount
- Update payment status



------------------------------------------------------------
NOTIFICATION REPOSITORY
------------------------------------------------------------


Responsibilities:


- Create notification
- Check duplicate notification
- Get customer notifications
- Get bill notifications



------------------------------------------------------------
COMPLAINT REPOSITORY
------------------------------------------------------------


Responsibilities:


- Create complaint
- Update complaint
- Find complaint
- List complaints
- Find overdue complaints



------------------------------------------------------------
REFERRAL REPOSITORY
------------------------------------------------------------


Responsibilities:


- Create referral
- Update reward status
- Find referral records



------------------------------------------------------------
REPORT REPOSITORY
------------------------------------------------------------


Responsibilities:


- Save report metadata
- Find reports
- Retrieve report file information



------------------------------------------------------------
SERVICES
------------------------------------------------------------


All business logic is implemented inside service classes.



------------------------------------------------------------
BILLING SERVICE
------------------------------------------------------------


Purpose:


Generate and manage customer bills.



Functions:


generate_bill()


apply_discount()


calculate_due_date()


get_outstanding_amount()



------------------------------------------------------------

generate_bill()


Input:


Customer ID



Process:


1. Fetch active subscription.

2. Read price_snapshot.

3. Calculate billing amount.

4. Create Bill record.

5. Set due_date.


Output:


Bill object



------------------------------------------------------------

Billing Rules:


Monthly Plans:

Billing Date:

1st of every month



Weekly Plans:

Billing Date:

Every Monday



------------------------------------------------------------

Price Handling:


Billing NEVER uses current Plan price.


Billing uses:


Subscription.price_snapshot



This ensures old customers maintain their existing pricing.



------------------------------------------------------------
PAYMENT SERVICE
------------------------------------------------------------


Purpose:


Manage payment collection.



Functions:


settle_payment()


apply_early_payment_discount()


calculate_remaining_amount()



------------------------------------------------------------

Payment Flow:


Customer Payment


        ↓


Retrieve Bill


        ↓


Check Payment Amount


        ↓


Check Early Payment Eligibility


        ↓


Apply Discount


        ↓


Create Payment Record


        ↓


Update Bill Status



------------------------------------------------------------

Early Payment Rule:


If:


payment_date < bill.due_date



Then:


Apply 10% discount.



------------------------------------------------------------

Payment Status Rules:


FULL PAYMENT:


Bill Status = PAID



PARTIAL PAYMENT:


Bill Status = PARTIALLY_PAID



NO PAYMENT AFTER DUE DATE:


Bill Status = OVERDUE



------------------------------------------------------------
NOTIFICATION SERVICE
------------------------------------------------------------


Purpose:


Create system notifications.



Functions:


create_notification()


prevent_duplicate_notification()


generate_payment_reminder()



------------------------------------------------------------

Business Rules:


Payment Reminder:


Generated:


5 days before bill due date.



Duplicate Prevention:


Only one notification of same type
can exist for same bill on same day.



------------------------------------------------------------
COMPLAINT SERVICE
------------------------------------------------------------


Purpose:


Manage customer complaints.



Functions:


create_complaint()


resolve_complaint()


assign_compensation()


is_overdue()



------------------------------------------------------------

Complaint SLA Calculation:


When complaint is created:


LOW:


due_at = created_at + 48 hours



MEDIUM:


due_at = created_at + 24 hours



HIGH:


due_at = created_at + 6 hours



------------------------------------------------------------

Overdue Logic:


Complaint is overdue when:


current_time > due_at


AND


status != RESOLVED



------------------------------------------------------------

Resolution Flow:


Customer Creates Complaint


        ↓


Admin Reviews


        ↓


Complaint Status:

OPEN


        ↓


IN_PROGRESS


        ↓


RESOLVED



------------------------------------------------------------
REFERRAL SERVICE
------------------------------------------------------------


Purpose:


Manage referral rewards.



Functions:


create_referral()


check_reward_eligibility()


award_reward()



------------------------------------------------------------

Business Rule:


Referral reward is awarded only when:


Referred customer completes first paid month.



Before completion:


reward_status = PENDING



After completion:


reward_status = AWARDED



------------------------------------------------------------
DASHBOARD SERVICE
------------------------------------------------------------


Purpose:


Generate business analytics.



Functions:


get_daily_operations()


get_financial_summary()


get_customer_service_summary()



------------------------------------------------------------

Dashboard Calculations:


Daily Operations:


From Delivery table:


- Total deliveries
- Delivered meals
- Failed deliveries
- Missed deliveries



------------------------------------------------------------

Financial Overview:


From Bill + Payment tables:


- Revenue collected
- Pending payments
- Active subscriptions



------------------------------------------------------------

Customer Service:


From Complaint table:


- Open complaints
- Resolved complaints
- Overdue complaints



------------------------------------------------------------
REPORT SERVICE
------------------------------------------------------------


Purpose:


Generate monthly PDF reports.



Function:


generate_monthly_report()



------------------------------------------------------------

Report Includes:


Customer Statistics


- Total customers served


------------------------------------------------------------

Meal Statistics


- Total meals delivered


------------------------------------------------------------

Financial Statistics


- Revenue summary
- Outstanding payments


------------------------------------------------------------

Complaint Statistics


- Complaint count
- Resolution summary


------------------------------------------------------------

Pause Statistics


- Total paused days


------------------------------------------------------------

Delivery Statistics


- Delivery success rate
- Failed deliveries
- Best route performance


------------------------------------------------------------

Report Generation Flow:


Collect Data


        ↓


Generate PDF


        ↓


Store File


        ↓


Save Report Metadata


        ↓


Make Available For Download



------------------------------------------------------------
AUTO PAUSE SERVICE
------------------------------------------------------------


Purpose:


Handle long pending payments.



Function:


check_overdue_subscriptions()



------------------------------------------------------------

Business Rule:


If payment remains unpaid for:


10 days after due date



Then:


Subscription status = PAUSED


auto_paused = True



Notification created:


AUTO_PAUSE



------------------------------------------------------------
------------------------------------------------------------
API MODULES
------------------------------------------------------------

Phase 4 introduces the final API modules.

- billing_api.py
- payment_api.py
- notification_api.py
- complaint_api.py
- referral_api.py
- dashboard_api.py
- report_api.py


Each API module handles HTTP communication only.

Business logic is implemented inside services.

Database operations are handled through repositories.


------------------------------------------------------------
BILLING API
------------------------------------------------------------


Base Path


/billing



Purpose


Manage bill generation and billing records.



------------------------------------------------------------

POST /billing/generate


Purpose


Generate customer bill.


Authorization


ADMIN Only



Request Body


BillCreate



Process:


1. Fetch active subscription.

2. Read price_snapshot.

3. Calculate bill amount.

4. Apply billing rules.

5. Create bill.



Response:


BillResponse



------------------------------------------------------------

GET /billing/{bill_id}


Purpose


Retrieve bill details.



Authorization


ADMIN / CUSTOMER



Business Rule:


Customer can access only own bills.



------------------------------------------------------------

GET /billing/customer/{customer_id}


Purpose


Retrieve customer billing history.



Authorization


ADMIN Only



------------------------------------------------------------

PUT /billing/{bill_id}/cancel


Purpose


Cancel pending bill.



Authorization


ADMIN Only



------------------------------------------------------------
PAYMENT API
------------------------------------------------------------


Base Path


/payments



Purpose


Manage payment transactions.



------------------------------------------------------------

POST /payments


Purpose


Record customer payment.



Authorization


ADMIN Only



Request:


PaymentCreate



Process:


Retrieve Bill


        ↓


Validate Amount


        ↓


Apply Early Payment Discount


        ↓


Create Payment


        ↓


Update Bill Status



------------------------------------------------------------

GET /payments/customer/{customer_id}


Purpose


Retrieve payment history.



Authorization


ADMIN / CUSTOMER



------------------------------------------------------------

GET /payments/bill/{bill_id}


Purpose


Retrieve bill payment details.



Authorization


ADMIN Only



------------------------------------------------------------
NOTIFICATION API
------------------------------------------------------------


Base Path


/notifications



Purpose


Manage system notifications.



------------------------------------------------------------

GET /notifications/customer/{customer_id}


Purpose


Retrieve customer notifications.



Authorization


ADMIN / CUSTOMER



------------------------------------------------------------

POST /notifications/payment-reminder


Purpose


Generate payment reminder.



Authorization


ADMIN Only



Business Rules:


- Reminder generated 5 days before due date.
- Duplicate reminders are prevented.



------------------------------------------------------------
COMPLAINT API
------------------------------------------------------------


Base Path


/complaints



Purpose


Manage customer complaints.



------------------------------------------------------------

POST /complaints


Purpose


Create complaint.



Authorization


CUSTOMER



Request:


ComplaintCreate



Process:


Create Complaint


        ↓


Calculate SLA deadline


        ↓


Store complaint



------------------------------------------------------------

GET /complaints


Purpose


Retrieve complaints.



Authorization


ADMIN Only



Filters:


- Status
- Severity
- Type
- Date



------------------------------------------------------------

GET /complaints/{complaint_id}


Purpose


Retrieve complaint details.



Authorization


ADMIN / CUSTOMER



------------------------------------------------------------

PUT /complaints/{complaint_id}/resolve


Purpose


Resolve complaint.



Authorization


ADMIN Only



Request:


ComplaintUpdate



Process:


Update Status


        ↓


Store Resolution


        ↓


Store Compensation Details



------------------------------------------------------------
REFERRAL API
------------------------------------------------------------


Base Path


/referrals



Purpose


Manage referral rewards.



------------------------------------------------------------

POST /referrals


Purpose


Create referral entry.



Authorization


CUSTOMER



Request:


ReferralCreate



Business Rule:


Referral starts with:


reward_status = PENDING



------------------------------------------------------------

GET /referrals


Purpose


Retrieve referral history.



Authorization


ADMIN



------------------------------------------------------------

POST /referrals/{id}/award


Purpose


Award referral reward.



Authorization


ADMIN Only



Business Rule:


Reward allowed only after referred customer
completes first paid month.



------------------------------------------------------------
DASHBOARD API
------------------------------------------------------------


Base Path


/dashboard



Purpose


Provide business analytics.



------------------------------------------------------------

GET /dashboard/summary


Authorization


ADMIN Only



Response:


DashboardResponse



Includes:


Daily Operations


- Total tiffins
- Delivery status summary
- Route performance



Financial Overview


- Revenue collected
- Outstanding payments
- Active subscriptions



Customer Service


- Open complaints
- Resolved complaints
- Complaint trends



------------------------------------------------------------
REPORT API
------------------------------------------------------------


Base Path


/reports



Purpose


Generate and access monthly reports.



------------------------------------------------------------

POST /reports/generate


Purpose


Generate monthly PDF report.



Authorization


ADMIN Only



Request:


Month



Process:


Collect business data


        ↓


Generate PDF


        ↓


Store file


        ↓


Save Report metadata



------------------------------------------------------------

GET /reports


Purpose


List generated reports.



Authorization


ADMIN Only



------------------------------------------------------------

GET /reports/{report_id}/download


Purpose


Download report PDF.



Authorization


ADMIN Only



------------------------------------------------------------
ERROR HANDLING
------------------------------------------------------------


400 BAD REQUEST


Examples:


- Invalid payment amount.
- Invalid complaint update.
- Invalid referral reward request.
- Invalid report month.



------------------------------------------------------------

401 UNAUTHORIZED


Examples:


- Missing JWT token.
- Invalid token.
- Expired token.



------------------------------------------------------------

403 FORBIDDEN


Examples:


- Customer accessing admin dashboard.
- Delivery boy accessing financial information.
- Unauthorized complaint modification.



------------------------------------------------------------

404 NOT FOUND


Examples:


- Bill not found.
- Payment not found.
- Complaint not found.
- Report not found.



------------------------------------------------------------

409 CONFLICT


Examples:


- Duplicate notification.
- Reward already awarded.
- Bill already cancelled.



------------------------------------------------------------

422 VALIDATION ERROR


Examples:


- Invalid schema.
- Missing fields.
- Invalid enum values.



------------------------------------------------------------
SECURITY RULES
------------------------------------------------------------


Authentication


- All protected APIs require JWT authentication.



------------------------------------------------------------

Authorization


ADMIN:


Can access:


- Customers
- Billing
- Payments
- Reports
- Dashboard
- Complaints


------------------------------------------------------------

CUSTOMER:


Can access:


- Own bills
- Own payments
- Create complaints
- Own notifications
- Create referrals



------------------------------------------------------------

DELIVERY BOY:


Cannot access Phase 4 financial modules.


------------------------------------------------------------

Data Protection


- Password hashes are never returned.
- Customers cannot view other customer data.
- Financial data is restricted.
- Reports are admin-only.
- Uploaded files require validation.



------------------------------------------------------------
FINAL SYSTEM DEFINITION OF DONE
------------------------------------------------------------


Authentication


✓ JWT login working

✓ Role-based access implemented

✓ Protected APIs working



------------------------------------------------------------

Customer Management


✓ Customer CRUD

✓ Duplicate phone prevention

✓ Customer deactivation



------------------------------------------------------------

Subscription Management


✓ Subscription lifecycle

✓ Pause tracking

✓ Price snapshot handling



------------------------------------------------------------

Operations


✓ Meal planning

✓ Delivery tracking

✓ Delivery retry workflow

✓ Add-on management



------------------------------------------------------------

Billing


✓ Automatic bill generation

✓ Payment tracking

✓ Discount handling

✓ Overdue calculation



------------------------------------------------------------

Notifications


✓ Payment reminders

✓ Auto pause notifications

✓ Duplicate prevention



------------------------------------------------------------

Complaints


✓ Complaint creation

✓ SLA calculation

✓ Resolution workflow

✓ Compensation tracking



------------------------------------------------------------

Analytics


✓ Dashboard APIs

✓ Financial overview

✓ Operational statistics



------------------------------------------------------------

Reports


✓ Monthly PDF generation

✓ Report storage

✓ Download support



------------------------------------------------------------
COMPLETE SYSTEM TEST CASES
------------------------------------------------------------


AUTH TESTS


✓ test_login_success

✓ test_invalid_password

✓ test_role_permission



------------------------------------------------------------

CUSTOMER TESTS


✓ test_create_customer

✓ test_duplicate_phone

✓ test_customer_deactivation



------------------------------------------------------------

SUBSCRIPTION TESTS


✓ test_active_subscription_limit

✓ test_pause_limit

✓ test_price_snapshot



------------------------------------------------------------

MEAL TESTS


✓ test_daily_meal_generation

✓ test_pause_exclusion



------------------------------------------------------------

DELIVERY TESTS


✓ test_delivery_flow

✓ test_retry_delivery

✓ test_second_failure_missed



------------------------------------------------------------

BILLING TESTS


✓ test_generate_monthly_bill

✓ test_payment_success

✓ test_early_payment_discount

✓ test_overdue_bill



------------------------------------------------------------

COMPLAINT TESTS


✓ test_create_complaint

✓ test_sla_calculation

✓ test_overdue_detection



------------------------------------------------------------

REPORT TESTS


✓ test_generate_pdf_report

✓ test_download_report



------------------------------------------------------------
FINAL PROJECT DEMO FLOW
------------------------------------------------------------


1. Admin Login


        ↓


2. Create Plans


        ↓


3. Create Routes


        ↓


4. Create Customers


        ↓


5. Create Subscriptions


        ↓


6. Generate Daily Meal Plan


        ↓


7. Create Deliveries


        ↓


8. Assign Delivery Boys


        ↓


9. Complete Delivery


        ↓


10. Generate Monthly Bills


        ↓


11. Receive Payment


        ↓


12. Apply Discount


        ↓


13. Create Customer Complaint


        ↓


14. Resolve Complaint


        ↓


15. View Dashboard


        ↓


16. Generate Monthly PDF Report



------------------------------------------------------------
PHASE 4 FINAL DELIVERABLES
------------------------------------------------------------


The Saanjh Ki Roti API V1 is complete with:


✓ Authentication System

✓ Customer Management

✓ Subscription Management

✓ Pause Management

✓ Meal Planning

✓ Delivery Management

✓ Add-On Management

✓ Billing System

✓ Payment Tracking

✓ Notification System

✓ Referral Rewards

✓ Complaint Management

✓ Dashboard Analytics

✓ Monthly Reports

✓ Automated Testing

✓ Swagger Documentation



------------------------------------------------------------
PROJECT STATUS AFTER PHASE 4
------------------------------------------------------------


Saanjh Ki Roti backend is now ready for:


- Production deployment
- Frontend dashboard integration
- Mobile app integration
- Future payment gateway integration
- Business scaling


------------------------------------------------------------
END OF IMPLEMENTATION PLAN
------------------------------------------------------------

