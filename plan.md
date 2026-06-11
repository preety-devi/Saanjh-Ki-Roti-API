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

## Functional Requirements
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
*Subscribe
*Renew
*Pause
*Resume
*Cancel subscriptions

* Business rules:
Maximum 7 pause days per billing cycle,
Unused pause days do not carry forward,
Paused days should not be counted in meal preparation

3. Daily Meal Planning

* Every morning the system should generate:
Total tiffins required,
Diet-wise breakdown,
Plan-wise breakdown

* Categories include:
*Veg
*Non-Veg
*Jain
*Diabetic

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
*Monthly subscribers -> 1st of every month
*Weekly subscribers -> Every Monday

* Supported payment methods:
-Cash
-UPI
-Khaata

* Business Rules:

-10% discount for early payment
-5% referral reward after referred customer completes first paid month
-Payment reminders sent 5 days before due date
-Subscription auto-pauses after 10 days of non-payment

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

## Non-Functional Requirements
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