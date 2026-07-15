# SAANJH KI ROTI API — IMPLEMENTATION PLAN

# PHASE 1 IMPLEMENTATION PLAN

Version: 1.0

---

# PURPOSE

Phase 1 establishes the technical foundation of the Saanjh Ki Roti API.

This phase focuses on building the infrastructure required for the rest of the application while delivering a working backend that supports authentication and subscription plan management.

At the end of this phase, administrators should be able to:

- Start the FastAPI server
- Authenticate using JWT
- Access protected APIs
- Create and manage subscription plans
- Verify application health through API endpoints

This phase intentionally avoids customer onboarding and business operations so that the project starts with a stable and secure foundation.

---

# INCLUDED IN PHASE 1

## Project Setup

- FastAPI application
- SQLModel configuration
- SQLite database
- Environment configuration
- Automatic database initialization
- Swagger documentation

## Authentication

- User model
- Password hashing
- JWT token generation
- Login API
- Role-based authorization

## Plan Management

- Plan model
- Create Plan
- Update Plan
- Delete Plan
- View Plans

## Infrastructure

- Exception handling
- Configuration management
- Dependency Injection
- Database session management
- Health Check API

---

# NOT INCLUDED IN PHASE 1

The following modules will be implemented in later phases.

- Customer Management
- Customer Document Upload
- Route Management
- Delivery Boy Management
- Subscription Management
- Pause Management
- Daily Meal Planning
- Add-On Orders
- Delivery Tracking
- Billing
- Payments
- Referral System
- Complaint Management
- Notifications
- Dashboard
- Reports

---

# TECHNOLOGY STACK

Backend Framework

- FastAPI

Programming Language

- Python 3.12+

Database

- SQLite

ORM

- SQLModel

Authentication

- JWT

Password Hashing

- Passlib (bcrypt)

Validation

- Pydantic (via SQLModel)

API Documentation

- Swagger UI
- OpenAPI

Dependency Management

- pip
- virtualenv

---

# PROJECT STRUCTURE

saanjh_ki_roti_api/

├── app/
│
├── api/
│   ├── auth_api.py
│   ├── plan_api.py
│   └── health_api.py
│
├── core/
│   ├── config.py
│   ├── security.py
│   ├── dependencies.py
│   └── exceptions.py
│
├── database/
│   └── db.py
│
├── models/
│   ├── user.py
│   └── plan.py
│
├── schemas/
│   ├── auth_schema.py
│   ├── user_schema.py
│   └── plan_schema.py
│
├── repositories/
│   ├── user_repository.py
│   └── plan_repository.py
│
├── services/
│   ├── auth_service.py
│   └── plan_service.py
│
├── utils/
│
├── middleware/
│
├── tests/
│
├── uploads/
│
├── requirements.txt
│
├── .env.example
│
└── main.py

---

# PROJECT SETUP

Step 1

Clone the repository

git clone <repository-url>

cd saanjh_ki_roti_api

---

Step 2

Create a virtual environment

Windows

python -m venv venv

venv\Scripts\activate

Linux / macOS

python3 -m venv venv

source venv/bin/activate

---

Step 3

Install dependencies

pip install -r requirements.txt

---

Step 4

Create the environment file

Copy

.env.example

to

.env

---

Step 5

Run the application

uvicorn main:app --reload

---

Step 6

Open Swagger

http://127.0.0.1:8000/docs

---

# ENVIRONMENT VARIABLES

DATABASE_URL=sqlite:///./saanjh.db

JWT_SECRET_KEY=your_secret_key

JWT_ALGORITHM=HS256

ACCESS_TOKEN_EXPIRE_MINUTES=60

---

# DATABASE INITIALIZATION

The application automatically creates all database tables during startup.

New developers only need to:

1. Clone the repository

2. Install dependencies

3. Configure the .env file

4. Start the FastAPI application

No manual SQL scripts are required.

---

# DATABASE MODELS

Phase 1 introduces only two database models.

- User
- Plan

All remaining models are introduced in later phases.

------------------------------------------------------------
DATABASE MODELS (PHASE 1)
------------------------------------------------------------

Phase 1 introduces only the foundational models required for authentication and subscription plan management.

The remaining business models will be introduced incrementally in later phases.

Database Models:

- User
- Plan

------------------------------------------------------------
USER MODEL
------------------------------------------------------------

Purpose

Stores authentication credentials and role information used throughout the application.

Every authenticated user must have exactly one User record.

Business users such as Customers and Delivery Boys will be linked to the User table in later phases using the user_id field.

Fields

- id
- username
- password_hash
- role
- active
- created_at

Role Values

- ADMIN
- CUSTOMER
- DELIVERY_BOY

Business Rules

- username must be unique.
- password_hash is never returned in API responses.
- Passwords are always stored as bcrypt hashes.
- Only active users can log in.
- JWT tokens reference the User ID and Role.
- User records are the single source of truth for authentication.

Future Relationships

Customer
    user_id ────> User.id

DeliveryBoy
    user_id ────> User.id

------------------------------------------------------------
PLAN MODEL
------------------------------------------------------------

Purpose

Stores subscription plans offered by Saanjh Ki Roti.

Customers will subscribe to these plans beginning in Phase 2.

Fields

- id
- name
- price
- diet_type
- meal_slots
- duration_days

Diet Types

- VEG
- NON_VEG
- JAIN
- DIABETIC

Meal Slots

- LUNCH
- DINNER
- BOTH

Business Rules

- Plan names should be unique.
- Price must be greater than zero.
- Duration must be greater than zero.
- A plan cannot be deleted while active subscriptions exist (implemented in Phase 2).
- Plan prices may change in the future, but active subscriptions will retain their original price using price_snapshot (implemented in Phase 2).

Default Plans

The following plans should be created during initial setup.

1. Monthly Veg Plan
2. Monthly Premium Plan
3. Weekly Saver Plan
4. Diabetic Special Plan

------------------------------------------------------------
DATABASE RELATIONSHIPS
------------------------------------------------------------

Phase 1

User

Plan

There are currently no relationships between these two models.

Relationships introduced in later phases include:

Customer
    │
    └──── user_id ─────> User

Subscription
    │
    ├──── customer_id ─> Customer
    └──── plan_id ─────> Plan

------------------------------------------------------------
SCHEMAS
------------------------------------------------------------

The API uses separate request and response schemas to ensure validation and prevent exposing sensitive information.

Authentication Schemas

LoginRequest

Fields

- username
- password

TokenResponse

Fields

- access_token
- token_type

UserResponse

Fields

- id
- username
- role
- active

password_hash is intentionally excluded.

------------------------------------------------------------

Plan Schemas

PlanCreate

Fields

- name
- price
- diet_type
- meal_slots
- duration_days

------------------------------------------------------------

PlanUpdate

Fields

- name (optional)
- price (optional)
- diet_type (optional)
- meal_slots (optional)
- duration_days (optional)

------------------------------------------------------------

PlanResponse

Fields

- id
- name
- price
- diet_type
- meal_slots
- duration_days

------------------------------------------------------------
REPOSITORIES
------------------------------------------------------------

Repositories isolate database operations from business logic.

User Repository

Responsibilities

- Create user
- Find user by username
- Find user by ID
- Update user
- Disable user

------------------------------------------------------------

Plan Repository

Responsibilities

- Create plan
- Update plan
- Delete plan
- Get plan by ID
- List all plans

------------------------------------------------------------
SERVICES
------------------------------------------------------------

Business logic is implemented inside the service layer.

------------------------------------------------------------

Auth Service

Responsibilities

- Register user
- Authenticate user
- Verify password
- Generate JWT token
- Decode JWT token

Main Functions

register_user()

authenticate_user()

create_access_token()

decode_access_token()

------------------------------------------------------------

Plan Service

Responsibilities

- Validate plan data
- Create plan
- Update plan
- Delete plan
- Retrieve plans

Main Functions

create_plan()

update_plan()

delete_plan()

get_plan()

list_plans()

------------------------------------------------------------
AUTHENTICATION FLOW
------------------------------------------------------------

Step 1

User submits login credentials.

↓

POST /auth/login

↓

System searches for username.

↓

Password is verified using bcrypt.

↓

JWT access token is generated.

↓

Token is returned to the client.

↓

Client includes token in every protected request.

Authorization Header

Authorization: Bearer <JWT_TOKEN>

------------------------------------------------------------
JWT TOKEN CONTENTS
------------------------------------------------------------

Each token contains

- User ID
- Username
- User Role
- Expiration Time

The token is digitally signed using the secret key stored in the .env file.

------------------------------------------------------------
AUTHORIZATION FLOW
------------------------------------------------------------

Every protected endpoint performs the following checks.

Step 1

Read Authorization header.

↓

Step 2

Decode JWT.

↓

Step 3

Validate token signature.

↓

Step 4

Verify token expiration.

↓

Step 5

Retrieve User from database.

↓

Step 6

Verify user is active.

↓

Step 7

Verify required role.

↓

Grant or deny access.

------------------------------------------------------------
ROLE-BASED ACCESS CONTROL
------------------------------------------------------------

ADMIN

Permissions

- Login
- Manage Plans
- Access Admin APIs
- View Protected APIs

------------------------------------------------------------

CUSTOMER

Permissions

- Login only

(Customer functionality begins in Phase 2.)

------------------------------------------------------------

DELIVERY_BOY

Permissions

- Login only

(Delivery functionality begins in Phase 3.)

------------------------------------------------------------
DEPENDENCIES
------------------------------------------------------------

get_db()

Purpose

Provides a SQLModel database session for every request.

------------------------------------------------------------

get_current_user()

Responsibilities

- Read JWT token
- Validate token
- Retrieve authenticated user
- Reject invalid tokens

------------------------------------------------------------

get_current_admin()

Responsibilities

- Validate JWT
- Verify role == ADMIN
- Deny access to non-admin users

------------------------------------------------------------
SECURITY ARCHITECTURE
------------------------------------------------------------

Authentication

JWT

Password Storage

bcrypt hashing

Protected APIs

JWT Authorization

Role Validation

Dependency Injection

Secret Storage

Environment Variables

------------------------------------------------------------

------------------------------------------------------------
API MODULES
------------------------------------------------------------

Phase 1 introduces three API modules.

- auth_api.py
- plan_api.py
- health_api.py

Each API module is responsible only for request handling.

Business logic is delegated to the service layer.

Database operations are delegated to the repository layer.

------------------------------------------------------------
AUTH API
------------------------------------------------------------

Base Path

/auth

Purpose

Provides authentication services for all users.

------------------------------------------------------------

POST /auth/register

Purpose

Create a new user account.

Authorization

Public

Request Body

- username
- password
- role

Response

201 Created

Returns

- User ID
- Username
- Role
- Active Status

Business Rules

- Username must be unique.
- Password is stored only as a bcrypt hash.
- Password hash is never returned in the response.
- Only valid roles are accepted.

------------------------------------------------------------

POST /auth/login

Purpose

Authenticate a user and generate a JWT access token.

Authorization

Public

Request Body

- username
- password

Response

200 OK

Returns

- access_token
- token_type

Business Rules

- Username must exist.
- Password must match the stored hash.
- Inactive users cannot log in.
- Invalid credentials return Unauthorized.

------------------------------------------------------------

GET /auth/me

Purpose

Return information about the currently authenticated user.

Authorization

Authenticated User

Response

200 OK

Returns

- id
- username
- role
- active

------------------------------------------------------------
PLAN API
------------------------------------------------------------

Base Path

/plans

Purpose

Manage subscription plans.

Only administrators are allowed to modify plans.

------------------------------------------------------------

POST /plans

Purpose

Create a subscription plan.

Authorization

Admin Only

Request Body

- name
- price
- diet_type
- meal_slots
- duration_days

Response

201 Created

Business Rules

- Plan name must be unique.
- Price must be greater than zero.
- Duration must be greater than zero.
- Diet type must be valid.
- Meal slot must be valid.

------------------------------------------------------------

GET /plans

Purpose

Retrieve all available plans.

Authorization

Authenticated User

Response

200 OK

Returns

List of plans.

------------------------------------------------------------

GET /plans/{plan_id}

Purpose

Retrieve a specific plan.

Authorization

Authenticated User

Response

200 OK

Returns

Plan details.

------------------------------------------------------------

PUT /plans/{plan_id}

Purpose

Update an existing plan.

Authorization

Admin Only

Business Rules

- Updated price affects only future subscriptions.
- Existing subscriptions retain their original price_snapshot (implemented in Phase 2).

------------------------------------------------------------

DELETE /plans/{plan_id}

Purpose

Delete a subscription plan.

Authorization

Admin Only

Business Rules

- Phase 1 allows deletion if the plan exists.
- Phase 2 will prevent deletion when active subscriptions exist.

------------------------------------------------------------
HEALTH API
------------------------------------------------------------

Base Path

/health

------------------------------------------------------------

GET /health

Purpose

Verify that the application is running.

Authorization

Public

Response

200 OK

Example Response

{
    "status": "ok",
    "application": "Saanjh Ki Roti API",
    "version": "1.0"
}

------------------------------------------------------------
BUSINESS RULES
------------------------------------------------------------

Authentication

- Only active users can log in.
- Passwords are never stored in plain text.
- JWT tokens must contain User ID and Role.
- Invalid or expired tokens are rejected.

------------------------------------------------------------

Users

- Username must be unique.
- Password hash is never returned.
- Every authenticated request requires a valid JWT.

------------------------------------------------------------

Plans

- Price must be greater than zero.
- Duration must be greater than zero.
- Diet type must be one of:

  - VEG
  - NON_VEG
  - JAIN
  - DIABETIC

- Meal slot must be one of:

  - LUNCH
  - DINNER
  - BOTH

------------------------------------------------------------
ERROR HANDLING
------------------------------------------------------------

400 Bad Request

Returned when request data is invalid.

Examples

- Missing required field
- Invalid enum value
- Invalid request body

------------------------------------------------------------

401 Unauthorized

Returned when authentication fails.

Examples

- Invalid username
- Incorrect password
- Invalid JWT
- Expired JWT

------------------------------------------------------------

403 Forbidden

Returned when the authenticated user does not have permission.

Examples

- Customer accessing Admin API
- Delivery Boy creating plans

------------------------------------------------------------

404 Not Found

Returned when a requested resource does not exist.

Examples

- User not found
- Plan not found

------------------------------------------------------------

409 Conflict

Returned when attempting to create duplicate data.

Examples

- Duplicate username
- Duplicate plan name

------------------------------------------------------------

422 Unprocessable Entity

Returned automatically by FastAPI validation.

Examples

- Invalid request schema
- Missing required attributes
- Incorrect data types

------------------------------------------------------------
SECURITY RULES
------------------------------------------------------------

- Passwords are stored only as bcrypt hashes.
- JWT secret key is stored in the .env file.
- Sensitive configuration is never committed to Git.
- API responses never expose password_hash.
- Authentication is required for all protected endpoints.
- Authorization is enforced using role-based access control.
- Database sessions are managed per request.
- SQLModel parameterized queries protect against SQL injection.
- Input validation is performed using Pydantic/SQLModel schemas.

------------------------------------------------------------
DEFINITION OF DONE (PHASE 1)
------------------------------------------------------------

Phase 1 is considered complete when all of the following are achieved.

Project Setup

- FastAPI application starts successfully.
- SQLite database initializes automatically.
- SQLModel creates required tables on startup.
- Swagger documentation is accessible.

Authentication

- User registration works.
- User login works.
- JWT token generation works.
- JWT validation works.
- Protected endpoints reject invalid tokens.
- Role-based authorization works correctly.

Plan Management

- Admin can create plans.
- Admin can update plans.
- Admin can delete plans.
- Authenticated users can view plans.

Infrastructure

- Health endpoint returns success.
- Database sessions work correctly.
- Exception handling is implemented.
- Environment variables are loaded successfully.

Security

- Password hashes are stored securely.
- Sensitive information is never exposed.
- Admin-only routes are protected.

------------------------------------------------------------
BASIC TEST CASES
------------------------------------------------------------

Authentication

✓ test_register_user_success

✓ test_register_duplicate_username

✓ test_login_success

✓ test_login_invalid_password

✓ test_login_inactive_user

✓ test_invalid_jwt_token

✓ test_expired_jwt_token

------------------------------------------------------------

Authorization

✓ test_admin_access_allowed

✓ test_customer_access_denied

✓ test_delivery_boy_access_denied

------------------------------------------------------------

Plan Management

✓ test_create_plan_success

✓ test_duplicate_plan_name

✓ test_update_plan

✓ test_delete_plan

✓ test_get_all_plans

✓ test_get_plan_by_id

------------------------------------------------------------

Infrastructure

✓ test_database_connection

✓ test_health_endpoint

✓ test_swagger_documentation

------------------------------------------------------------
PHASE 1 DEMO SCENARIO
------------------------------------------------------------

The Phase 1 demonstration should include the following workflow.

1. Start the FastAPI application.

2. Verify database tables are created automatically.

3. Open Swagger UI.

4. Register an Admin user.

5. Log in using Admin credentials.

6. Obtain a JWT access token.

7. Authorize requests using the JWT token.

8. Create the default subscription plans.

9. Retrieve the list of plans.

10. Update an existing plan.

11. Delete a plan.

12. Verify the Health API responds successfully.

This demonstration confirms that the application's core infrastructure, authentication system, and plan management are fully functional.

------------------------------------------------------------
PHASE 1 DELIVERABLES
------------------------------------------------------------

At the end of Phase 1, the project includes:

✓ FastAPI project structure

✓ SQLModel database configuration

✓ SQLite database

✓ JWT authentication

✓ Password hashing

✓ User model

✓ Plan model

✓ Authentication APIs

✓ Plan APIs

✓ Health API

✓ Repository layer

✓ Service layer

✓ Dependency injection

✓ Environment configuration

✓ Swagger documentation

✓ Basic automated tests

------------------------------------------------------------
NEXT PHASE
------------------------------------------------------------

Phase 2 introduces the core business entities and customer lifecycle management.

Modules to be implemented:

- Customer Management
- Route Management
- Customer Document Upload
- Subscription Management
- Pause History
- Pause & Resume APIs

Phase 2 builds directly on the authentication and plan management foundation established in Phase 1.