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

6. Billing and Payments
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

7. Complaint Management

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


8. Dashboard
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

9. Reporting

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

## Implementation Plan

| Phase   | Module                | Tasks                                                                             | Deliverable                       |
| ------- | --------------------- | --------------------------------------------------------------------------------- | --------------------------------- |
| Phase 1 | Project Setup         | Create repository, Configure FastAPI, Configure database, Create folder structure | Project skeleton ready            |
| Phase 2 | Authentication Module | Login API, JWT Authentication, Role permissions                                   | Secure access system              |
| Phase 3 | Customer Module       | Customer CRUD APIs, Document upload                                               | Customer management completed     |
| Phase 4 | Subscription Module   | Plan APIs, Subscription APIs, Pause & Resume APIs                                 | Subscription management completed |
| Phase 5 | Meal Planning Module  | Daily meal calculation, Diet wise summary                                         | Kitchen planning ready            |
| Phase 6 | Delivery Module       | Route management, Delivery tracking, Retry handling                               | Delivery workflow completed       |
| Phase 7 | Billing Module        | Bill generation, Payment tracking, Discounts, Auto pause                          | Billing workflow completed        |
| Phase 8 | Complaint Module      | Complaint creation, Resolution workflow, Compensation tracking                    | Complaint management completed    |
| Phase 9 | Dashboard & Reports   | Dashboard APIs, PDF report generation                                             | Analytics completed               |


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
Purpose:
Stores customer information.

* plan.py
### Class

```python
Plan
```
Fields

| Field         | Type  |
| ------------- | ----- |
| id            | int   |
| name          | str   |
| price         | float |
| meal_type     | str   |
| duration_days | int   |

Purpose:
Stores plan details.

* subscription.py
### Class

```python
Subscription
```
Fields

| Field       | Type |
| ----------- | ---- |
| id          | int  |
| customer_id | int  |
| plan_id     | int  |
| start_date  | date |
| end_date    | date |
| status      | str  |
| paused_days | int  |

Purpose:
Stores subscription information.

* delivery.py
### Class

```python
Delivery
```
Fields

| Field           | Type |
| --------------- | ---- |
| id              | int  |
| customer_id     | int  |
| route           | str  |
| status          | str  |
| delivery_boy_id | int  |

Purpose:
Stores delivery records.

* payment.py
### Class

```python
Payment
```
Fields

| Field          | Type     |
| -------------- | -------- |
| id             | int      |
| customer_id    | int      |
| amount         | float    |
| payment_method | str      |
| paid_at        | datetime |

Purpose:
Stores payment records

* complaint.py
### Class

```python
Complaint
```
Fields

| Field       | Type |
| ----------- | ---- |
| id          | int  |
| customer_id | int  |
| type        | str  |
| severity    | str  |
| description | str  |
| status      | str  |

Purpose:
Stores complaints


## SERVICES
** customer_service.py
* create_customer()
| Attribute | Details         |
| --------- | --------------- |
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
| Report         |
| Route          |
| DeliveryBoy    |
| Referral       |
| PauseHistory   |
| Bill           |




