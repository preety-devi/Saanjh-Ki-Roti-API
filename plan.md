# Project: Saanjh Ki Roti API

## Objective

Build a FastAPI-based backend system for Saanjh Ki Roti, a home-cooked tiffin service, to manage customers, subscriptions, meal preparation, deliveries, billing, complaints, and reporting.

The goal is to replace the current notebook-based process with a centralized system that reduces food wastage, improves operational efficiency, and provides better visibility into business performance.

## Business Understanding

Saanjh Ki Roti currently serves approximately 67 tiffins per day across multiple routes and diet categories.

The major operational challenges are:

Inaccurate daily cooking counts
Manual tracking of subscription pauses
Difficulty managing deliveries
Manual billing and payment follow-up
Complaint tracking through WhatsApp and phone calls
Lack of business analytics and reporting

The system should automate these processes while remaining simple enough for a small family-run business.

## Technology Stack

- Backend Framework: FastAPI
- Database: PostgreSQL
- ORM: SQLAlchemy
- Database Migration Tool: Alembic
- Authentication: JWT

### Database Initialization

When a new developer clones the repository:

1. Install dependencies from requirements.txt
2. Configure environment variables
3. Run Alembic migrations
4. Start FastAPI server

All database tables will be created through Alembic migration scripts.

## Key Stakeholders

* Business Owner (Saanjh)
Responsible for:
Customer management,
Meal preparation,
Billing oversight,
Complaint resolution,
Business reporting

* Delivery Boys
Responsible for:
Delivering assigned tiffins,
Updating delivery status,
Reporting failed deliveries

* Customers
Responsible for:
Managing subscriptions,
Requesting pauses,
Ordering add-ons,
Making payments,
Raising complaints

## Project Requirements

### Functional Requirements
1. Customer Management

* The system must allow:
Customer registration,
Customer profile updates,
Customer search,
Customer deactivation

* Customer information should include:
*Name
*Phone number
*Address
*Diet preference
*Route assignment
*Identity document image
The system should prevent duplicate customer records based on phone number.

2. Subscription Management

* The system must support:
Monthly Veg Plan,
Monthly Premium Plan,
Weekly Saver Plan,
Diabetic Special Plan

* Customers should be able to:
Subscribe,
Renew,
Pause,
Resume,
Cancel subscriptions

* Business rules:
Maximum 7 pause days per billing cycle,
Unused pause days do not carry forward,
Paused days should not be counted in meal preparation,
A customer cannot have more than one active subscription simultaneously.

3. Daily Meal Planning

* Every morning the system should generate:
Total tiffins required,
Diet-wise breakdown,
Plan-wise breakdown

* Categories include:
Veg,
Non-Veg,
Jain,
Diabetic

* The count should automatically exclude:
Paused subscriptions,
Expired subscriptions,
Auto-paused subscriptions

4. Add-On Management

* Customers may order additional items such as:
Extra Paneer,
Raita,
Salad,
Kheer

* Business Rule:
Add-ons must be requested before 9:00 AM,
Requests after cutoff should be rejected


5. Delivery Management
The system must support route-based deliveries.

* Current routes:
East Vijaynagar,
West Vijaynagar,
Indra Vihar

* Each delivery should move through statuses:
*Prepared
*Out For Delivery
*Delivered
*Failed
*Missed

* Business Rules:
Failed deliveries receive one retry,
Retry occurs at 8 PM,
Second failure marks the meal as missed

Delivery boys should only access their assigned route.

6. Delivery Retry Handling


Failed deliveries remain in the same Delivery record.

When status changes to Failed:

- retry_count becomes 1
- retry_scheduled_at is set to 8:00 PM

If retry succeeds:
status = Delivered

If retry fails:
status = Missed

No additional Delivery rows are created.

7. Billing and Payments
The system should generate bills automatically.

* Billing schedules:
Monthly subscribers -> 1st of every month,
Weekly subscribers -> Every Monday

* Supported payment methods:
-Cash
-UPI
-Khaata

* Business Rules:

10% discount for early payment,
5% referral reward after referred customer completes first paid month,
Payment reminders sent 5 days before due date,
Subscription auto-pauses after 10 days of non-payment

8. Complaint Management

* Customers should be able to raise complaints.
Supported complaint categories:
Late Delivery,
Cold Food,
Wrong Order,
Missing Items,
Taste Issues

* Each complaint should include:
Description,
Severity,
Status,
Resolution details,
Compensation details


* Resolution deadlines:
| Severity | Resolution Time |
| -------- | --------------- |
| Low      | 48 Hours        |
| Medium   | 24 Hours        |
| High     | 6 Hours         |


SLA deadlines are calculated when a complaint is created.

Low severity    -> due_at = created_at + 48 hours
Medium severity -> due_at = created_at + 24 hours
High severity   -> due_at = created_at + 6 hours

A complaint is considered overdue when:
current_time > due_at
and status != Resolved

9. Dashboard
The owner should have access to a dashboard displaying:

* Daily Operations
Total tiffins,
Plan-wise count,
Route-wise count,
Delivery status summary

* Financial Overview
Outstanding payments,
Revenue collected,
Active subscriptions

* Customer Service Overview
Open complaints,
Resolved complaints,
Complaint trends

10. Reporting

The system should generate a monthly PDF report containing:
- Total customers served
- Total meals delivered
- Revenue summary
- Complaint summary
- Pause statistics
- Delivery performance statistics
- Best performing delivery route
- Delivery boy performance summary

Reports should be accessible for download and email delivery.


### Non-Functional Requirements
* Security
Role-based access control
Secure authentication
Protected document uploads

* Reliability
Accurate billing calculations
Consistent delivery tracking
Data validation

* Scalability
System should support:
Additional routes
More delivery staff
New subscription plans

* Usability
Mobile-friendly APIs
Simple workflows
Easy dashboard navigation

## Assumptions
* Sunday is a non-operational day.
* One customer can have only one active subscription at a time.
* Phone number acts as the primary unique identifier.
* Existing subscribers keep their plan price until the next billing cycle if prices change.
* Delivery retries happen only once.

## Pause Tracking

- PauseHistory is the authoritative source of pause information.

- Subscription.paused_days is a derived value calculated from PauseHistory records.

- Monthly reports and pause analytics will always use PauseHistory records.


## Things That Can Go Wrong
- Duplicate customer registration.
- Plan price changes during active subscriptions.
- Delivery boy leaves while deliveries are in progress.
- Customer requests pause after meals are prepared.
- Add-on requested after cutoff time.
- Payment remains overdue beyond allowed limit.
- Uploaded identity documents are missing or corrupted.
- Route reassignment becomes necessary.
- Customer cancels during an active billing period.
- Delivery status not updated by delivery staff.


## Edge Case Handling Decisions

### Duplicate Phone Number

The system will reject customer creation if the phone number already exists.

### Plan Price Change During Active Subscription

Each subscription stores a price_snapshot value.

Billing uses the stored price_snapshot instead of the current Plan price.

Existing customers keep their current price until renewal.

### Delivery Boy Leaves Mid-Day

The owner can manually reassign pending deliveries to another delivery boy.

Until reassignment, deliveries remain pending under the current assignment.

## Implementation Plan

| Phase   | Module                | Tasks                                                                             | Deliverable                       |
| ------- | --------------------- | --------------------------------------------------------------------------------- | --------------------------------- |
| Phase 1 | Project Setup         | Create repository, Configure FastAPI, Configure database, Create folder structure | Project skeleton ready            |
| Phase 2 | Authentication Module | User management, Login API, JWT Authentication, Role permissions                  | Secure access system              |
| Phase 3 | Customer Module       | Customer CRUD APIs, Document upload                                               | Customer management completed     |
| Phase 4 | Subscription Module   | Plan APIs, Subscription APIs, Pause & Resume APIs                                 | Subscription management completed |
| Phase 5 | Meal Planning Module  | Daily meal calculation, Diet wise summary                                         | Kitchen planning ready            |
| Phase 6 | Delivery Module       | Route management, Delivery tracking, Retry handling                               | Delivery workflow completed       |
| Phase 7 | Billing Module        | Bill generation, Payment tracking, Discounts, Auto pause                          | Billing workflow completed        |
| Phase 8 | Complaint Module      | Complaint creation, Resolution workflow, Compensation tracking                    | Complaint management completed    |
| Phase 9 | Dashboard & Reports   | Dashboard APIs, PDF report generation                                             | Analytics completed               |

## Definition of Done (V1)

The project will be considered complete when:

1. Authentication and role-based access control are implemented.
2. Customer CRUD APIs are functional.
3. Subscription create, pause, resume, and cancel workflows work correctly.
4. Daily meal planning excludes paused and expired subscriptions.
5. Delivery tracking supports all defined statuses.
6. Billing generation and payment tracking are functional.
7. Complaint management and SLA tracking are implemented.
8. Dashboard APIs provide operational and financial summaries.
9. Monthly PDF reports can be generated and downloaded.
10. Core business rules are covered by automated tests.
11. API documentation is available through FastAPI Swagger.

## Open Technical Question

The requirements mention monthly report delivery through email.

Clarification is required regarding whether Version 1 should support automatic email delivery through an email service provider or only downloadable PDF reports.

This decision affects infrastructure planning, deployment configuration, and implementation effort.


## Folder Structure

saanjh_ki_roti_api/

│
├── app/
│
│   ├── api/
│   │
│   ├── models/
│   │
│   ├── schemas/
│   │
│   ├── services/
│   │
│   ├── repositories/
│   │
│   ├── core/
│   │
│   ├── database/
│   │
│   ├── utils/
│   │
│   ├── reports/
|   |
|   ├── middleware/
│
├── uploads/
│
├── tests/
│
├── requirements.txt
│
└── main.py


## Files, Classes, Fields & Functions

* main.py

Purpose:
Application entry point.

Function: create_app()

| Attribute | Details                     |
| --------- | --------------------------- |
| Input     | None                        |
| Output    | FastAPI app                 |
| Purpose   | Create application instance |


* database/db.py

Purpose:
Database connection.

Function: get_db()

| Attribute | Details            |
| --------- | ------------------ |
| Input     | None               |
| Output    | Database session   |
| Purpose   | Provide DB session |


## MODELS
* customer.py
### Class

```python
Customer
```
Fields

| Field         | Type     |
| ------------- | -------- |
| id            | int      |
| name          | str      |
| phone         | str      |
| address       | str      |
| diet_type     | str      |
| document_path | str      |
| created_at    | datetime |
| route_id      | int      |
| user_id       | int      |

Purpose:
Stores customer information.

* User.py

### Class

```python
User
```
| Field         | Type     |
| ------------- | -------- |
| id            | int      |
| username      | str      |
| password_hash | str      |
| role          | str      |
| active        | bool     |
| created_at    | datetime |

Purpose:

Stores authentication credentials and role information used by JWT authentication.
JWT tokens reference the User table.
Customer and DeliveryBoy profiles are linked through user_id.
Admin users authenticate directly through User records.

Role Values:
- Admin
- DeliveryBoy
- Customer

* plan.py
### Class

```python
Plan
```
Fields

| Field         | Type    |
| ------------- | -----   |
| id            | int     |
| name          | str     |
| price         | Decimal |
| meal_type     | str     |
| duration_days | int     |

Purpose:
Stores plan details.

* subscription.py
### Class

```python
Subscription
```
Fields

| id             | int      |
| customer_id    | int      |
| plan_id        | int      |
| start_date     | date     |
| end_date       | date     |
| status         | str      |
| paused_days    | int      |
| price_snapshot | Decimal  |

Purpose:
Stores subscription information.

* delivery.py
### Class

```python
Delivery
```
Fields

| id                 | int      |
| customer_id        | int      |
| route              | str      |
| status             | str      |
| delivery_boy_id    | int      |
| retry_count        | int      |
| retry_scheduled_at | datetime |

Purpose:
Stores delivery records.

* payment.py
### Class

```python
Payment
```
Fields

| Field          | Type      |
| -------------- | --------  |
| id             | int       |
| customer_id    | int       |
| amount         | Decimal   |
| payment_method | str       |
| paid_at        | datetime  |

Purpose:
Stores payment records

* complaint.py
### Class

```python
Complaint
```
Fields
Complaint

| Field       | Type     |
| ----------- | -------- |
| id          | int      |
| customer_id | int      |
| type        | str      |
| severity    | str      |
| description | str      |
| status      | str      |
| created_at  | datetime |
| due_at      | datetime |
| resolved_at | datetime |

Purpose:
Stores complaints

* PauseHistory.py
### Class

```python
PauseHistory
```
Fields

| Field           | Type     |
| --------------- | -------- |
| id              | int      |
| subscription_id | int      |
| pause_start     | date     |
| pause_end       | date     |
| pause_days      | int      |
| created_at      | datetime |

Purpose:

Stores every pause request for auditing and reporting.

* AddOn.py
### Class

```python
AddOn
```
Fields

| Field | Type    |
| ----- | ------- |
| id    | int     |
| name  | str     |
| price | Decimal |

Purpose:

Stores available add-on items that customers can order.


* AddOnOrder.py

### Class

```python
AddOnOrder
```
Fields

| Field       | Type |
| ----------- | ---- |
| id          | int  |
| customer_id | int  |
| addon_id    | int  |
| order_date  | date |
| quantity    | int  |

Purpose:

Stores customer add-on requests.


* Route.py

### Class

```python
Route
```
Fields

| Field | Type |
| ----- | ---- |
| id    | int  |
| name  | str  |

Purpose:

Stores delivery route information.


* DeliveryBoy.py

### Class

```python
DeliveryBoy
```

Fields

| Field    | Type |
| -------- | ---- |
| id       | int  |
| name     | str  |
| phone    | str  |
| route_id | int  |
| active   | bool |
| user_id  | int  |

Purpose:

Stores delivery staff information



* Referral.py

### Class

```python
Referral
```

Fields

| Field                | Type    |
| -------------------- | ------- |
| id                   | int     |
| referrer_customer_id | int     |
| referred_customer_id | int     |
| reward_amount        | Decimal |
| reward_status        | str     |

Purpose:

Stores referral reward records.



* Bill.py

### Class

```python
Bill
```

Fields
| Field           | Type     |
| --------------- | -------- |
| id              | int      |
| customer_id     | int      |
| billing_period  | str      |
| total_amount    | Decimal  |
| discount_amount | Decimal  |
| final_amount    | Decimal  |
| status          | str      |
| generated_at    | datetime |

Purpose:

Stores generated billing records.



* Report.py

### Class

```python
Report
```

Fields

| Field        | Type     |
| ------------ | -------- |
| id           | int      |
| report_month | str      |
| file_path    | str      |
| generated_at | datetime |

Purpose:

Stores generated monthly report metadata






## SERVICES
** customer_service.py
* create_customer()
| Attribute | Details         |
| --------- | ----------------|
| Input     | Customer data   |
| Output    | Customer object |
| Purpose   | Create customer |

* get_customer()
| Attribute | Details        |
| --------- | -------------- |
| Input     | Customer ID    |
| Output    | Customer       |
| Purpose   | Fetch customer |

* update_customer()
| Attribute | Details            |
| --------- | ------------------ |
| Input     | Customer ID + data |
| Output    | Updated customer   |
| Purpose   | Update customer    |

* delete_customer()
| Attribute | Details             |
| --------- | ------------------- |
| Input     | Customer ID         |
| Output    | Success message     |
| Purpose   | Deactivate customer |

** subscription_service.py
* create_subscription()
| Attribute | Details               |
| --------- | --------------------- |
| Input     | Customer ID + Plan ID |
| Output    | Subscription          |
| Purpose   | Create subscription   |

* pause_subscription()
| Attribute | Details             |
| --------- | ------------------- |
| Input     | Subscription ID     |
| Output    | Paused subscription |
| Purpose   | Pause subscription  |

* resume_subscription()
| Attribute | Details             |
| --------- | ------------------- |
| Input     | Subscription ID     |
| Output    | Active subscription |
| Purpose   | Resume subscription |

* validate_pause_limit()
| Attribute | Details           |
| --------- | ----------------- |
| Input     | Subscription ID   |
| Output    | Boolean           |
| Purpose   | Check pause limit |

** meal_service.py
* generate_daily_meal_count()
| Attribute | Details               |
| --------- | --------------------- |
| Input     | Date                  |
| Output    | Meal summary          |
| Purpose   | Calculate daily meals |

** addon_service.py
* create_addon_order()
| Attribute | Details               |
| --------- | --------------------- |
| Input     | Customer ID + Addon   |
| Output    | Addon order           |
| Purpose   | Create add-on request |

* validate_cutoff_time()
| Attribute | Details           |
| --------- | ----------------- |
| Input     | Current time      |
| Output    | Boolean           |
| Purpose   | Check 9 AM cutoff |

** delivery_service.py

* assign_delivery()
| Attribute | Details             |
| --------- | ------------------- |
| Input     | Delivery ID         |
| Output    | Assigned delivery   |
| Purpose   | Assign delivery boy |


* update_status()
| Attribute | Details                |
| --------- | ---------------------- |
| Input     | Delivery ID + Status   |
| Output    | Updated delivery       |
| Purpose   | Update delivery status |


* retry_failed_delivery()
| Attribute | Details          |
| --------- | ---------------- |
| Input     | Delivery ID      |
| Output    | Updated delivery |
| Purpose   | Schedule retry   |


** billing_service.py
* generate_bill()
| Attribute | Details       |
| --------- | ------------- |
| Input     | Customer ID   |
| Output    | Bill          |
| Purpose   | Generate bill |

* apply_discount()
| Attribute | Details         |
| --------- | --------------- |
| Input     | Bill            |
| Output    | Updated bill    |
| Purpose   | Apply discounts |

* send_payment_reminder()
| Attribute | Details       |
| --------- | ------------- |
| Input     | Customer ID   |
| Output    | Notification  |
| Purpose   | Send reminder |

** complaint_service.py
* create_complaint()
| Attribute | Details          |
| --------- | ---------------- |
| Input     | Complaint data   |
| Output    | Complaint        |
| Purpose   | Create complaint |

* resolve_complaint()
| Attribute | Details            |
| --------- | ------------------ |
| Input     | Complaint ID       |
| Output    | Resolved complaint |
| Purpose   | Resolve complaint  |

* is_overdue()

| Attribute | Details                |
| --------- | ---------------------- |
| Input     | Complaint ID           |
| Output    | Boolean                |
| Purpose   | Check SLA violation    |


* assign_compensation()
| Attribute | Details            |
| --------- | ------------------ |
| Input     | Complaint ID       |
| Output    | Compensation       |
| Purpose   | Store compensation |

** report_service.py
* generate_monthly_report()

| Attribute | Details         |
| --------- | --------------- |
| Input     | Month           |
| Output    | PDF             |
| Purpose   | Generate report |


## API Modules

The following API modules will contain route handlers for their respective functionalities:

| API Module          |
| ------------------- |
| customer_api.py     |
| subscription_api.py |
| meal_api.py         |
| addon_api.py        |
| delivery_api.py     |
| billing_api.py      |
| complaint_api.py    |
| report_api.py       |

Purpose:
Each file contains route handlers for its respective module.

## Database Models Summary

| Database Model |
| -------------- |
| Customer       |
| Plan           |
| Subscription   |
| Delivery       |
| Payment        |
| Complaint      |
| AddOn          |
| AddOnOrder     |
| Report         |
| Route          |
| DeliveryBoy    |
| Referral       |
| PauseHistory   |
| Bill           |
| User           |




