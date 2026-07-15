# SAANJH KI ROTI API — IMPLEMENTATION PLAN

# PHASE 2 IMPLEMENTATION PLAN

Version: 1.0

------------------------------------------------------------
PURPOSE
------------------------------------------------------------

Phase 2 introduces the core business entities of the Saanjh Ki Roti API.

Building on the authentication and plan management implemented in Phase 1, this phase enables administrators to onboard customers, manage subscription lifecycles, assign delivery routes, and handle subscription pause and resume requests.

At the end of this phase, the application will support complete customer onboarding and subscription management while enforcing all defined business rules.

This phase provides the foundation required for daily meal planning, delivery operations, and billing in subsequent phases.

------------------------------------------------------------
PREREQUISITES
------------------------------------------------------------

Phase 1 must be completed before starting Phase 2.

The following components from Phase 1 are required:

- FastAPI project structure
- SQLModel configuration
- SQLite database
- JWT Authentication
- User model
- Plan model
- Role-based authorization
- Repository layer
- Service layer
- Dependency Injection
- Swagger documentation

------------------------------------------------------------
INCLUDED IN PHASE 2
------------------------------------------------------------

Customer Management

- Customer model
- Customer CRUD APIs
- Customer search
- Customer deactivation
- Customer document upload

------------------------------------------------------------

Route Management

- Route model
- Route CRUD APIs
- Route assignment

------------------------------------------------------------

Subscription Management

- Subscription model
- Create subscription
- View subscription
- Cancel subscription
- Active subscription validation
- Price snapshot

------------------------------------------------------------

Pause Management

- PauseHistory model
- Pause subscription
- Resume subscription
- Pause validation
- Pause history

------------------------------------------------------------

Business Rule Validation

- Duplicate customer prevention
- One active subscription per customer
- Maximum 7 pause days per billing cycle
- PauseHistory as source of truth
- Subscription status validation

------------------------------------------------------------
NOT INCLUDED IN PHASE 2
------------------------------------------------------------

The following modules will be implemented in later phases.

- Daily Meal Planning
- Add-On Management
- Delivery Management
- Delivery Retry
- Billing
- Payments
- Notifications
- Referral Management
- Complaint Management
- Dashboard
- Reports

------------------------------------------------------------
PROJECT STRUCTURE UPDATES
------------------------------------------------------------

New files introduced in Phase 2

app/

├── api/
│
├── customer_api.py
├── subscription_api.py
├── route_api.py
│
├── models/
│
├── customer.py
├── subscription.py
├── pause_history.py
├── route.py
│
├── schemas/
│
├── customer_schema.py
├── subscription_schema.py
├── pause_schema.py
├── route_schema.py
│
├── repositories/
│
├── customer_repository.py
├── subscription_repository.py
├── pause_repository.py
├── route_repository.py
│
├── services/
│
├── customer_service.py
├── subscription_service.py
├── pause_service.py
├── route_service.py

------------------------------------------------------------
DATABASE MODELS (PHASE 2)
------------------------------------------------------------

Phase 2 introduces four new models.

- Customer
- Route
- Subscription
- PauseHistory

------------------------------------------------------------
CUSTOMER MODEL
------------------------------------------------------------

Purpose

Stores customer profile information used throughout the business workflow.

Authentication information remains in the User model.

Customer records are linked to User records using user_id.

Fields

- id
- name
- phone
- address
- diet_type
- document_path
- created_at
- updated_at
- route_id
- user_id
- active

Relationships

Customer
    │
    ├── user_id ─────────► User.id
    │
    └── route_id ────────► Route.id

Business Rules

- Phone number must be unique.
- Every customer must be linked to a User account.
- Every customer belongs to one delivery route.
- Customer can be deactivated but never permanently deleted.
- Identity document path stores uploaded file location.
- Duplicate customers are rejected.

------------------------------------------------------------
ROUTE MODEL
------------------------------------------------------------

Purpose

Represents delivery routes used for customer assignment.

Fields

- id
- name

Default Routes

- East Vijaynagar
- West Vijaynagar
- Indra Vihar

Business Rules

- Route names must be unique.
- Customers are assigned to exactly one route.
- Routes can be expanded in future versions without schema changes.

------------------------------------------------------------
SUBSCRIPTION MODEL
------------------------------------------------------------

Purpose

Stores the customer's active subscription details.

Fields

- id
- customer_id
- plan_id
- start_date
- end_date
- status
- auto_paused
- price_snapshot
- created_at

Relationships

Subscription

customer_id ─────► Customer.id

plan_id ─────────► Plan.id

Status Values

- ACTIVE
- PAUSED
- CANCELLED
- EXPIRED

Business Rules

- Customer can have only one ACTIVE subscription.
- price_snapshot stores the plan price at the time of subscription.
- Future plan price changes must not affect existing subscriptions.
- Subscription status determines customer eligibility for meals.
- Auto pause functionality will be activated in Phase 4.

------------------------------------------------------------
PAUSE HISTORY MODEL
------------------------------------------------------------

Purpose

Stores every subscription pause request for auditing, reporting, and business rule validation.

PauseHistory is the authoritative source of pause information.

Subscription does not store pause usage.

Fields

- id
- subscription_id
- pause_start
- pause_end
- pause_days
- created_at

Relationship

PauseHistory

subscription_id ─────► Subscription.id

Business Rules

- Every approved pause creates one PauseHistory record.
- Maximum pause usage is seven days per billing cycle.
- Unused pause days do not carry forward.
- Reports and analytics always use PauseHistory.
- Pause history records are never modified after creation.

------------------------------------------------------------
DATABASE RELATIONSHIPS
------------------------------------------------------------

User

▲

│

Customer

▲

│

Subscription

▲

│

PauseHistory

Plan

▲

│

Subscription

Route

▲

│

Customer

------------------------------------------------------------
PHASE 2 DATA FLOW
------------------------------------------------------------

Admin Login

↓

Create Customer

↓

Assign Route

↓

Create Subscription

↓

Store Price Snapshot

↓

Customer Requests Pause

↓

Validate Pause Limit

↓

Create PauseHistory Record

↓

Resume Subscription

This workflow forms the complete customer onboarding lifecycle that later phases will build upon.

------------------------------------------------------------

------------------------------------------------------------
SCHEMAS
------------------------------------------------------------

Phase 2 introduces request and response schemas for customer, route,
subscription, and pause management.

Separate request and response schemas ensure proper validation and
prevent internal fields from being exposed through the API.

------------------------------------------------------------
CUSTOMER SCHEMAS
------------------------------------------------------------

CustomerCreate

Fields

- name
- phone
- address
- diet_type
- route_id
- user_id
- document_path

------------------------------------------------------------

CustomerUpdate

Fields

- name (optional)
- phone (optional)
- address (optional)
- diet_type (optional)
- route_id (optional)
- document_path (optional)

------------------------------------------------------------

CustomerResponse

Fields

- id
- name
- phone
- address
- diet_type
- route_id
- active
- created_at
- updated_at

------------------------------------------------------------
ROUTE SCHEMAS
------------------------------------------------------------

RouteCreate

Fields

- name

------------------------------------------------------------

RouteUpdate

Fields

- name

------------------------------------------------------------

RouteResponse

Fields

- id
- name

------------------------------------------------------------
SUBSCRIPTION SCHEMAS
------------------------------------------------------------

SubscriptionCreate

Fields

- customer_id
- plan_id
- start_date

------------------------------------------------------------

SubscriptionResponse

Fields

- id
- customer_id
- plan_id
- start_date
- end_date
- status
- auto_paused
- price_snapshot

------------------------------------------------------------
PAUSE SCHEMAS
------------------------------------------------------------

PauseRequest

Fields

- pause_start
- pause_end

------------------------------------------------------------

PauseResponse

Fields

- subscription_id
- pause_start
- pause_end
- pause_days

------------------------------------------------------------
REPOSITORIES
------------------------------------------------------------

Repositories are responsible only for database operations.

Business logic is implemented inside the service layer.

------------------------------------------------------------
Customer Repository
------------------------------------------------------------

Responsibilities

- Create customer
- Update customer
- Find customer by ID
- Find customer by phone
- Search customers
- Deactivate customer
- List customers

------------------------------------------------------------
Route Repository
------------------------------------------------------------

Responsibilities

- Create route
- Update route
- Delete route
- Find route
- List routes

------------------------------------------------------------
Subscription Repository
------------------------------------------------------------

Responsibilities

- Create subscription
- Update subscription
- Find subscription
- Get active subscription
- Cancel subscription
- List subscriptions

------------------------------------------------------------
Pause Repository
------------------------------------------------------------

Responsibilities

- Create pause history
- Get pause history
- Calculate pause usage
- List pause history

------------------------------------------------------------
SERVICES
------------------------------------------------------------

Business rules are implemented in the service layer.

------------------------------------------------------------
Customer Service
------------------------------------------------------------

Responsibilities

- Register customer
- Validate duplicate phone numbers
- Update customer
- Search customers
- Deactivate customer

Functions

create_customer()

get_customer()

update_customer()

search_customers()

deactivate_customer()

------------------------------------------------------------

Business Rules

- Phone number must be unique.
- Every customer must be linked to an existing User.
- Route must exist.
- Customer profile cannot be permanently deleted.
- Inactive customers cannot create subscriptions.

------------------------------------------------------------
Route Service
------------------------------------------------------------

Responsibilities

- Create routes
- Update routes
- List routes

Functions

create_route()

update_route()

get_routes()

------------------------------------------------------------

Business Rules

- Route names must be unique.
- Routes can be reassigned to customers.
- Default routes should exist before onboarding customers.

------------------------------------------------------------
Subscription Service
------------------------------------------------------------

Responsibilities

- Create subscription
- Cancel subscription
- Validate active subscription
- Store price snapshot

Functions

create_subscription()

cancel_subscription()

get_subscription()

validate_active_subscription()

------------------------------------------------------------

Business Rules

- Customer cannot have more than one ACTIVE subscription.
- Customer must exist.
- Plan must exist.
- Subscription end_date is calculated from plan.duration_days.
- price_snapshot is copied from Plan.price during subscription creation.
- Cancelled subscriptions cannot be resumed.

------------------------------------------------------------
Pause Service
------------------------------------------------------------

Responsibilities

- Pause subscription
- Resume subscription
- Validate pause limit
- Calculate pause usage

Functions

pause_subscription()

resume_subscription()

validate_pause_limit()

calculate_pause_days()

------------------------------------------------------------

Business Rules

- PauseHistory is the only source of pause records.
- Maximum pause allowance is 7 days per billing cycle.
- Pause requests cannot overlap existing pauses.
- Pause start date must not be after pause end date.
- Pause duration must be greater than zero.
- Resume changes subscription status back to ACTIVE.
- Unused pause days expire at the end of the billing cycle.
- Pause requests are rejected if they exceed the remaining allowance.

------------------------------------------------------------
BUSINESS RULES
------------------------------------------------------------

Customer Rules

- Duplicate phone numbers are not allowed.
- Customer must belong to exactly one route.
- Customer must be linked to one User account.
- Deactivated customers remain in the database.

------------------------------------------------------------

Route Rules

- Route names are unique.
- Routes are reusable.
- Customers can be reassigned to another route.

------------------------------------------------------------

Subscription Rules

- One ACTIVE subscription per customer.
- Every subscription belongs to one customer.
- Every subscription belongs to one plan.
- Price snapshot never changes after creation.
- Expired subscriptions cannot become ACTIVE again.

------------------------------------------------------------

Pause Rules

- Maximum seven pause days per billing cycle.
- PauseHistory is the authoritative source.
- Overlapping pauses are not allowed.
- Pause requests must fall within the subscription period.
- Resume is allowed only for PAUSED subscriptions.

------------------------------------------------------------
PHASE 2 WORKFLOW
------------------------------------------------------------

Admin Login

↓

Create Customer

↓

Validate Unique Phone Number

↓

Assign Route

↓

Create Subscription

↓

Copy Plan Price to price_snapshot

↓

Subscription ACTIVE

↓

Customer Requests Pause

↓

Validate Remaining Pause Days

↓

Create PauseHistory Record

↓

Subscription Status = PAUSED

↓

Resume Request

↓

Subscription Status = ACTIVE

------------------------------------------------------------
VALIDATION CHECKLIST
------------------------------------------------------------

Before creating a Customer

✓ User exists

✓ Route exists

✓ Phone number is unique

------------------------------------------------------------

Before creating a Subscription

✓ Customer exists

✓ Plan exists

✓ Customer is active

✓ No existing ACTIVE subscription

------------------------------------------------------------

Before approving a Pause

✓ Subscription exists

✓ Subscription is ACTIVE

✓ Pause dates are valid

✓ Pause limit not exceeded

✓ No overlapping pause

------------------------------------------------------------
DEPENDENCIES
------------------------------------------------------------

get_db()

Provides SQLModel session.

------------------------------------------------------------

get_current_user()

Authenticates the current user.

------------------------------------------------------------

get_current_admin()

Restricts customer, route, and subscription management
to administrators.

------------------------------------------------------------

Future phases will introduce additional authorization
rules for Customers and Delivery Boys.

------------------------------------------------------------
------------------------------------------------------------
API MODULES
------------------------------------------------------------

Phase 2 introduces four API modules.

- customer_api.py
- subscription_api.py
- pause_api.py
- route_api.py

Each API module is responsible only for request handling.

Business logic is delegated to the service layer.

Database operations are delegated to the repository layer.

------------------------------------------------------------
CUSTOMER API
------------------------------------------------------------

Base Path

/customers

Purpose

Manage customer profiles.

Only administrators can create, update, search and deactivate
customers in Version 1.

------------------------------------------------------------

POST /customers

Purpose

Create a new customer.

Authorization

Admin Only

Request Body

CustomerCreate

Response

201 Created

Returns

CustomerResponse

Business Rules

- Phone number must be unique.
- Route must exist.
- User must exist.
- Customer is active by default.

------------------------------------------------------------

GET /customers

Purpose

Retrieve all customers.

Authorization

Admin Only

Response

List<CustomerResponse>

------------------------------------------------------------

GET /customers/{customer_id}

Purpose

Retrieve customer details.

Authorization

Admin Only

Response

CustomerResponse

------------------------------------------------------------

PUT /customers/{customer_id}

Purpose

Update customer details.

Authorization

Admin Only

Business Rules

- Updated phone number must remain unique.
- Route must exist.

------------------------------------------------------------

DELETE /customers/{customer_id}

Purpose

Deactivate customer.

Authorization

Admin Only

Business Rules

- Customer is marked inactive.
- Record is never permanently deleted.

------------------------------------------------------------

GET /customers/search

Purpose

Search customers.

Authorization

Admin Only

Supported Filters

- Name
- Phone Number
- Route
- Active Status

------------------------------------------------------------
ROUTE API
------------------------------------------------------------

Base Path

/routes

Purpose

Manage delivery routes.

------------------------------------------------------------

POST /routes

Authorization

Admin Only

Purpose

Create a delivery route.

------------------------------------------------------------

GET /routes

Authorization

Authenticated User

Purpose

Retrieve all routes.

------------------------------------------------------------

PUT /routes/{route_id}

Authorization

Admin Only

Purpose

Update route information.

------------------------------------------------------------

DELETE /routes/{route_id}

Authorization

Admin Only

Purpose

Delete unused routes.

Business Rule

Routes assigned to customers cannot be deleted.

------------------------------------------------------------
SUBSCRIPTION API
------------------------------------------------------------

Base Path

/subscriptions

Purpose

Manage customer subscriptions.

------------------------------------------------------------

POST /subscriptions

Authorization

Admin Only

Purpose

Create a subscription.

Business Rules

- Customer must exist.
- Plan must exist.
- Customer must not already have an ACTIVE subscription.
- price_snapshot is copied automatically.

------------------------------------------------------------

GET /subscriptions/{subscription_id}

Authorization

Admin Only

Purpose

Retrieve subscription details.

------------------------------------------------------------

GET /subscriptions/customer/{customer_id}

Authorization

Admin Only

Purpose

Retrieve customer's active subscription.

------------------------------------------------------------

PUT /subscriptions/{subscription_id}/cancel

Authorization

Admin Only

Purpose

Cancel subscription.

Business Rules

- Status becomes CANCELLED.
- Cancelled subscriptions cannot be resumed.

------------------------------------------------------------
PAUSE API
------------------------------------------------------------

Base Path

/subscriptions

------------------------------------------------------------

POST /subscriptions/{subscription_id}/pause

Authorization

Admin Only

Purpose

Pause a subscription.

Request

PauseRequest

Response

PauseResponse

Business Rules

- Maximum seven pause days.
- Pause dates cannot overlap.
- Subscription must be ACTIVE.

------------------------------------------------------------

PUT /subscriptions/{subscription_id}/resume

Authorization

Admin Only

Purpose

Resume a paused subscription.

Business Rules

- Subscription must currently be PAUSED.

------------------------------------------------------------

GET /subscriptions/{subscription_id}/pause-history

Authorization

Admin Only

Purpose

Retrieve pause history.

------------------------------------------------------------
BUSINESS RULES
------------------------------------------------------------

Customer Rules

- Phone numbers are unique.
- Customers are never permanently deleted.
- Every customer belongs to exactly one route.
- Every customer has one linked User account.

------------------------------------------------------------

Subscription Rules

- One ACTIVE subscription per customer.
- Every subscription references one Plan.
- price_snapshot is immutable.
- Cancelled subscriptions cannot become ACTIVE again.

------------------------------------------------------------

Pause Rules

- Maximum seven pause days per billing cycle.
- PauseHistory is the single source of truth.
- Pause requests cannot overlap.
- Pause must fall within the subscription period.
- Resume is allowed only from PAUSED status.

------------------------------------------------------------

Route Rules

- Route names are unique.
- Routes with assigned customers cannot be deleted.
- Customers can be reassigned to another route.

------------------------------------------------------------
ERROR HANDLING
------------------------------------------------------------

400 Bad Request

Examples

- Invalid pause dates
- Invalid billing period
- Invalid route assignment

------------------------------------------------------------

401 Unauthorized

Examples

- Missing JWT
- Invalid JWT
- Expired JWT

------------------------------------------------------------

403 Forbidden

Examples

- Customer accessing Admin API
- Delivery Boy accessing customer management

------------------------------------------------------------

404 Not Found

Examples

- Customer not found
- Route not found
- Subscription not found

------------------------------------------------------------

409 Conflict

Examples

- Duplicate phone number
- Customer already has an ACTIVE subscription
- Duplicate route name

------------------------------------------------------------

422 Validation Error

Examples

- Invalid request schema
- Missing required fields
- Invalid enum values

------------------------------------------------------------
SECURITY RULES
------------------------------------------------------------

- JWT authentication required for protected APIs.
- Only ADMIN users manage customers and subscriptions.
- Password hashes are never exposed.
- SQLModel validation prevents invalid data.
- Uploaded document paths are stored, not file contents.
- Database access is performed only through repositories.

------------------------------------------------------------
DEFINITION OF DONE (PHASE 2)
------------------------------------------------------------

Customer Management

✓ Create customer

✓ Update customer

✓ Search customer

✓ Deactivate customer

✓ Prevent duplicate phone numbers

------------------------------------------------------------

Route Management

✓ Create routes

✓ Update routes

✓ View routes

✓ Assign routes to customers

------------------------------------------------------------

Subscription Management

✓ Create subscription

✓ View subscription

✓ Cancel subscription

✓ Prevent multiple ACTIVE subscriptions

✓ Store price_snapshot

------------------------------------------------------------

Pause Management

✓ Pause subscription

✓ Resume subscription

✓ Maintain PauseHistory

✓ Enforce seven-day pause limit

✓ Prevent overlapping pauses

------------------------------------------------------------

Infrastructure

✓ Swagger documentation updated

✓ Repository layer complete

✓ Service layer complete

✓ API validation implemented

------------------------------------------------------------
BASIC TEST CASES
------------------------------------------------------------

Customer

✓ test_create_customer_success

✓ test_duplicate_phone_number

✓ test_update_customer

✓ test_deactivate_customer

✓ test_search_customer

------------------------------------------------------------

Route

✓ test_create_route

✓ test_duplicate_route

✓ test_update_route

✓ test_delete_unused_route

✓ test_delete_assigned_route

------------------------------------------------------------

Subscription

✓ test_create_subscription

✓ test_second_active_subscription_rejected

✓ test_cancel_subscription

✓ test_price_snapshot_saved

------------------------------------------------------------

Pause

✓ test_pause_subscription

✓ test_resume_subscription

✓ test_pause_limit_exceeded

✓ test_overlapping_pause

✓ test_pause_outside_subscription_period

------------------------------------------------------------
PHASE 2 DEMO SCENARIO
------------------------------------------------------------

The Phase 2 demonstration should include the following workflow.

1. Log in as Administrator.

2. Create delivery routes.

3. Create a customer.

4. Upload customer identity document.

5. Assign the customer to a route.

6. Create a subscription.

7. Verify that price_snapshot is stored.

8. Attempt to create another ACTIVE subscription.

9. Verify the request is rejected.

10. Pause the subscription.

11. View PauseHistory.

12. Resume the subscription.

13. Search for the customer.

14. Deactivate the customer.

This demonstration confirms that customer onboarding,
subscription lifecycle management, and pause handling are
working correctly.

------------------------------------------------------------
PHASE 2 DELIVERABLES
------------------------------------------------------------

At the end of Phase 2, the project includes:

✓ Customer Management

✓ Route Management

✓ Customer Search

✓ Customer Deactivation

✓ Customer Document Upload

✓ Subscription Management

✓ Pause Management

✓ Pause History

✓ Repository Layer

✓ Service Layer

✓ Business Rule Validation

✓ Swagger Documentation

✓ Automated Tests

------------------------------------------------------------
NEXT PHASE
------------------------------------------------------------

Phase 3 introduces the operational workflow of the business.

Modules to be implemented:

- Daily Meal Planning
- Add-On Management
- Delivery Boy Management
- Delivery Assignment
- Delivery Tracking
- Delivery Retry Handling

Phase 3 builds directly on the customer and subscription
management completed in Phase 2.