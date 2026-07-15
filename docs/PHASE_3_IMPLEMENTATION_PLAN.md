# SAANJH KI ROTI API — IMPLEMENTATION PLAN


# PHASE 3 IMPLEMENTATION PLAN

Version: 1.0


------------------------------------------------------------
PURPOSE
------------------------------------------------------------

Phase 3 introduces the daily operational workflow of the
Saanjh Ki Roti business.

Building on Phase 2 customer and subscription management,
this phase enables the system to calculate daily meal
requirements, manage add-on orders, assign deliveries,
and track delivery execution.

At the end of this phase, the business owner will be able
to manage the complete daily kitchen-to-customer workflow.

The operational flow becomes:

Customer Subscription

        ↓

Daily Meal Calculation

        ↓

Kitchen Preparation

        ↓

Delivery Assignment

        ↓

Delivery Tracking

        ↓

Delivery Completion / Retry Handling


------------------------------------------------------------
PREREQUISITES
------------------------------------------------------------

Phase 1 and Phase 2 must be completed before starting
Phase 3.

Required existing modules:

- Authentication
- User Management
- Customer Management
- Route Management
- Plan Management
- Subscription Management
- Pause Management
- PauseHistory


------------------------------------------------------------
INCLUDED IN PHASE 3
------------------------------------------------------------


Daily Meal Planning

- Generate daily meal count
- Diet-wise meal summary
- Plan-wise meal summary
- Exclude inactive subscriptions


------------------------------------------------------------

Delivery Management

- DeliveryBoy management
- Delivery assignment
- Route-based delivery access
- Delivery status tracking
- Failed delivery retry handling


------------------------------------------------------------

Add-On Management

- Add-on catalog
- Customer add-on orders
- Add-on cutoff validation


------------------------------------------------------------

Operational Business Rules

- Paused customers excluded from meal count
- Expired subscriptions excluded
- Failed deliveries retry once
- Retry scheduled at 8 PM
- Second failure marked as missed
- Add-on cutoff at 9 AM


------------------------------------------------------------
NOT INCLUDED IN PHASE 3
------------------------------------------------------------

The following modules will be implemented in Phase 4.

- Billing
- Payments
- Payment reminders
- Auto pause due to payment
- Referral rewards
- Complaint management
- Dashboard
- Monthly reports
- PDF generation
- Email delivery


------------------------------------------------------------
PROJECT STRUCTURE UPDATES
------------------------------------------------------------


New files introduced in Phase 3:


app/

├── api/
│
├── meal_api.py
├── addon_api.py
├── delivery_api.py
├── delivery_boy_api.py
│
├── models/
│
├── delivery.py
├── delivery_boy.py
├── addon.py
├── addon_order.py
│
├── schemas/
│
├── meal_schema.py
├── addon_schema.py
├── delivery_schema.py
├── delivery_boy_schema.py
│
├── repositories/
│
├── delivery_repository.py
├── delivery_boy_repository.py
├── addon_repository.py
├── meal_repository.py
│
├── services/
│
├── meal_service.py
├── delivery_service.py
├── delivery_boy_service.py
├── addon_service.py


------------------------------------------------------------
DATABASE MODELS (PHASE 3)
------------------------------------------------------------


Phase 3 introduces four new database models.


- DeliveryBoy
- Delivery
- AddOn
- AddOnOrder


------------------------------------------------------------
DELIVERY BOY MODEL
------------------------------------------------------------


Purpose

Stores delivery staff information and route assignments.


Fields


- id
- name
- phone
- route_id
- user_id
- active


Relationships


DeliveryBoy

    user_id
        |
        ↓
      User


    route_id
        |
        ↓
      Route



Business Rules


- Delivery boy must have a linked User account.
- Delivery boy can access only assigned route deliveries.
- Inactive delivery boys cannot receive new assignments.
- Route reassignment can be performed by Admin.


------------------------------------------------------------
DELIVERY MODEL
------------------------------------------------------------


Purpose

Stores individual customer meal delivery records.


Fields


- id
- customer_id
- route_id
- meal_slot
- status
- delivery_boy_id
- retry_count
- retry_scheduled_at


Relationships


Delivery


customer_id

        ↓

Customer



route_id

        ↓

Route



delivery_boy_id

        ↓

DeliveryBoy



------------------------------------------------------------

Delivery Status Values


- PREPARED
- OUT_FOR_DELIVERY
- DELIVERED
- FAILED
- MISSED



Meal Slot Values


- LUNCH
- DINNER



------------------------------------------------------------

Business Rules


- Every delivery belongs to one customer.
- Delivery is generated from active subscriptions.
- Paused subscriptions do not generate deliveries.
- Expired subscriptions do not generate deliveries.
- Delivery status changes must follow workflow rules.
- Failed deliveries are retried only once.
- Retry does not create another Delivery record.


------------------------------------------------------------
ADDON MODEL
------------------------------------------------------------


Purpose

Stores available additional food items.


Fields


- id
- name
- price


Examples


- Extra Paneer
- Raita
- Salad
- Kheer



Business Rules


- Add-on price is stored separately.
- Admin manages available add-ons.
- Inactive add-ons cannot be ordered.


------------------------------------------------------------
ADDON ORDER MODEL
------------------------------------------------------------


Purpose

Stores customer requests for additional items.


Fields


- id
- customer_id
- addon_id
- order_date
- requested_at
- quantity



Relationships


AddOnOrder


customer_id

        ↓

Customer



addon_id

        ↓

AddOn



------------------------------------------------------------

Business Rules


- Add-on request time must be before 9:00 AM.
- Late requests are rejected.
- Customer must have an active subscription.
- Quantity must be greater than zero.


------------------------------------------------------------
DATABASE RELATIONSHIPS
------------------------------------------------------------


Customer

    |

    |


Subscription

    |

    |


Delivery


--------------------------------


Route

    |

    |

DeliveryBoy

    |

    |

Delivery



--------------------------------


Customer

    |

    |

AddOnOrder

    |

    |

AddOn



------------------------------------------------------------
PHASE 3 OPERATIONAL FLOW
------------------------------------------------------------


Every Morning


Admin triggers meal generation


        ↓


System checks active subscriptions


        ↓


Exclude:

- Paused subscriptions
- Expired subscriptions
- Auto-paused subscriptions


        ↓


Generate meal count


        ↓


Create delivery records


        ↓


Assign delivery boys


        ↓


Track delivery status


        ↓


Complete deliveries


        ↓


Handle failures and retries



------------------------------------------------------------

------------------------------------------------------------
SCHEMAS
------------------------------------------------------------

Phase 3 introduces request and response schemas for:

- Delivery Boy Management
- Delivery Management
- Meal Planning
- Add-On Management


Separate request and response schemas are used to:

- Validate incoming data
- Control API responses
- Prevent exposing internal database fields


------------------------------------------------------------
DELIVERY BOY SCHEMAS
------------------------------------------------------------


DeliveryBoyCreate


Fields


- name
- phone
- route_id
- user_id



------------------------------------------------------------


DeliveryBoyUpdate


Fields


- name (optional)
- phone (optional)
- route_id (optional)
- active (optional)



------------------------------------------------------------


DeliveryBoyResponse


Fields


- id
- name
- phone
- route_id
- user_id
- active



------------------------------------------------------------
DELIVERY SCHEMAS
------------------------------------------------------------


DeliveryCreate


Fields


- customer_id
- route_id
- meal_slot
- delivery_boy_id



------------------------------------------------------------


DeliveryUpdate


Fields


- status
- delivery_boy_id



------------------------------------------------------------


DeliveryResponse


Fields


- id
- customer_id
- route_id
- meal_slot
- status
- delivery_boy_id
- retry_count
- retry_scheduled_at



------------------------------------------------------------
MEAL PLANNING SCHEMAS
------------------------------------------------------------


DailyMealRequest


Fields


- date



------------------------------------------------------------


MealSummaryResponse


Fields


- date
- total_tiffins
- diet_breakdown
- plan_breakdown



Example:


{
    "date": "2026-07-15",
    "total_tiffins": 67,
    "diet_breakdown": {
        "VEG": 50,
        "JAIN": 10,
        "DIABETIC": 7
    }
}


------------------------------------------------------------
ADD-ON SCHEMAS
------------------------------------------------------------


AddOnCreate


Fields


- name
- price



------------------------------------------------------------


AddOnResponse


Fields


- id
- name
- price



------------------------------------------------------------


AddOnOrderCreate


Fields


- addon_id
- quantity



------------------------------------------------------------


AddOnOrderResponse


Fields


- id
- customer_id
- addon_id
- quantity
- order_date
- requested_at



------------------------------------------------------------
REPOSITORIES
------------------------------------------------------------

Repositories handle only database operations.

Business decisions remain inside services.


------------------------------------------------------------
DELIVERY BOY REPOSITORY
------------------------------------------------------------


Responsibilities:


- Create delivery boy
- Update delivery boy
- Find delivery boy
- Assign route
- List active delivery boys


------------------------------------------------------------
DELIVERY REPOSITORY
------------------------------------------------------------


Responsibilities:


- Create delivery
- Update delivery status
- Find delivery by ID
- Get route deliveries
- Get delivery boy deliveries
- Count deliveries by status


------------------------------------------------------------
MEAL REPOSITORY
------------------------------------------------------------


Responsibilities:


- Fetch active subscriptions
- Fetch customer diet details
- Fetch plan information
- Generate meal statistics



------------------------------------------------------------
ADD-ON REPOSITORY
------------------------------------------------------------


Responsibilities:


- Create add-on
- Update add-on
- Delete add-on
- Create add-on order
- List customer add-on orders



------------------------------------------------------------
SERVICES
------------------------------------------------------------


Business logic for Phase 3 is implemented inside services.


------------------------------------------------------------
MEAL SERVICE
------------------------------------------------------------


Purpose


Generates daily kitchen requirements.


Functions


generate_daily_meal_count()


calculate_diet_summary()


calculate_plan_summary()



------------------------------------------------------------


Business Logic


The system checks all subscriptions.


Include:


- ACTIVE subscriptions


Exclude:


- PAUSED subscriptions
- EXPIRED subscriptions
- CANCELLED subscriptions
- AUTO_PAUSED subscriptions



Calculation:


Total Tiffins

=

Number of eligible subscriptions


Diet Summary:

Group customers by:


- VEG
- NON_VEG
- JAIN
- DIABETIC



Plan Summary:

Group customers by:


- Monthly Veg
- Monthly Premium
- Weekly Saver
- Diabetic Special



------------------------------------------------------------
DELIVERY BOY SERVICE
------------------------------------------------------------


Purpose


Manage delivery staff.


Functions


create_delivery_boy()


assign_route()


update_delivery_boy()


deactivate_delivery_boy()



------------------------------------------------------------


Business Rules


- Delivery boy must have a valid User account.
- Route must exist.
- Inactive delivery boys cannot receive deliveries.
- Delivery boys can access only their assigned route.


------------------------------------------------------------
DELIVERY SERVICE
------------------------------------------------------------


Purpose


Handles delivery workflow.


Functions


create_delivery()


assign_delivery()


update_status()


retry_failed_delivery()



------------------------------------------------------------


DELIVERY STATUS FLOW


Valid workflow:


PREPARED

        ↓

OUT_FOR_DELIVERY

        ↓

DELIVERED



Failure workflow:


OUT_FOR_DELIVERY

        ↓

FAILED

        ↓

Retry Scheduled

        ↓

OUT_FOR_DELIVERY

        ↓

DELIVERED / MISSED



------------------------------------------------------------


Retry Handling Logic


When delivery status becomes FAILED:


Step 1:

Increase retry_count.


retry_count = 1


Step 2:

Set retry_scheduled_at:


8:00 PM same day



Step 3:

Keep same Delivery record.



If retry succeeds:


status = DELIVERED



If retry fails again:


status = MISSED



Business Rules:


- Only one retry allowed.
- No additional Delivery rows are created.
- Second failure permanently closes delivery.



------------------------------------------------------------
ADD-ON SERVICE
------------------------------------------------------------


Purpose


Handles additional food item ordering.


Functions


create_addon_order()


validate_cutoff_time()


list_customer_addons()



------------------------------------------------------------


Business Rules


Before creating an order:


Check current time.


Allowed:


Before 09:00 AM



Rejected:


After 09:00 AM



Additional Rules:


- Customer must exist.
- Customer must have active subscription.
- Add-on must exist.
- Quantity must be greater than zero.



------------------------------------------------------------
VALIDATION RULES
------------------------------------------------------------


Meal Generation Validation


Before generating meals:


✓ Date is valid

✓ Subscription status checked

✓ Paused customers removed

✓ Expired subscriptions removed



------------------------------------------------------------


Delivery Validation


Before assigning delivery:


✓ Customer exists

✓ Route exists

✓ Delivery boy exists

✓ Delivery boy belongs to route



------------------------------------------------------------


Delivery Status Validation


PREPARED

can move to:

OUT_FOR_DELIVERY



OUT_FOR_DELIVERY

can move to:

DELIVERED

or

FAILED



FAILED

can move to:

OUT_FOR_DELIVERY

or

MISSED



------------------------------------------------------------


Add-On Validation


Before accepting add-on:


✓ Customer exists

✓ Subscription active

✓ Add-on exists

✓ Request time before 9 AM



------------------------------------------------------------
PHASE 3 INTERNAL DATA FLOW
------------------------------------------------------------


Subscription Data

        ↓


Meal Service

        ↓


Daily Meal Count


        ↓


Delivery Creation


        ↓


Route Assignment


        ↓


Delivery Boy Assignment


        ↓


Delivery Status Updates


        ↓


Delivery Completion


        ↓


Operational Reports (Phase 4)



------------------------------------------------------------
DEPENDENCIES
------------------------------------------------------------


get_current_user()


Purpose


Authenticate logged-in users.



------------------------------------------------------------


get_current_admin()


Purpose


Restrict operational management APIs.



------------------------------------------------------------


get_current_delivery_boy()


Purpose


Allows delivery boys to access only assigned deliveries.



Rules:


- Delivery boy cannot access another route.
- Delivery boy cannot modify customer data.
- Delivery boy cannot assign deliveries.



------------------------------------------------------------
------------------------------------------------------------
API MODULES
------------------------------------------------------------

Phase 3 introduces four API modules.

- meal_api.py
- delivery_boy_api.py
- delivery_api.py
- addon_api.py


Each API module is responsible only for handling HTTP requests.

Business logic is delegated to the service layer.

Database operations are delegated to repositories.


------------------------------------------------------------
MEAL PLANNING API
------------------------------------------------------------


Base Path


/meals



Purpose


Generate daily kitchen preparation requirements.


------------------------------------------------------------

POST /meals/generate


Purpose


Generate daily meal count.


Authorization


ADMIN Only



Request Body


DailyMealRequest


Fields:


- date



Response


200 OK


Returns:


MealSummaryResponse



Business Rules


- Only active subscriptions are counted.
- Paused subscriptions are excluded.
- Expired subscriptions are excluded.
- Cancelled subscriptions are excluded.
- Auto-paused subscriptions are excluded.



------------------------------------------------------------

GET /meals/{date}


Purpose


Retrieve generated meal summary.


Authorization


ADMIN Only



Response


MealSummaryResponse



------------------------------------------------------------
DELIVERY BOY API
------------------------------------------------------------


Base Path


/delivery-boys



Purpose


Manage delivery staff.



------------------------------------------------------------

POST /delivery-boys


Purpose


Create delivery boy profile.


Authorization


ADMIN Only



Request Body


DeliveryBoyCreate



Business Rules


- User account must exist.
- Route must exist.
- Phone number must be unique.
- Delivery boy is active by default.



------------------------------------------------------------

GET /delivery-boys


Purpose


List delivery boys.


Authorization


ADMIN Only



------------------------------------------------------------

PUT /delivery-boys/{id}


Purpose


Update delivery boy information.


Authorization


ADMIN Only



Allowed Updates:


- Name
- Phone
- Route
- Active status



------------------------------------------------------------

DELETE /delivery-boys/{id}


Purpose


Deactivate delivery boy.


Authorization


ADMIN Only



Business Rule


Delivery boy is not permanently deleted.



------------------------------------------------------------
DELIVERY API
------------------------------------------------------------


Base Path


/deliveries



Purpose


Manage delivery lifecycle.



------------------------------------------------------------

POST /deliveries


Purpose


Create delivery record.


Authorization


ADMIN Only



Request Body


DeliveryCreate



Business Rules


- Customer must exist.
- Route must exist.
- Delivery boy must belong to route.
- Delivery status starts as PREPARED.



------------------------------------------------------------

GET /deliveries


Purpose


Retrieve deliveries.



Authorization


ADMIN Only



Filters:


- Date
- Route
- Status
- Delivery Boy



------------------------------------------------------------

GET /deliveries/my-route


Purpose


Retrieve deliveries assigned to current delivery boy.



Authorization


DELIVERY_BOY



Business Rules


- Delivery boy can only view assigned route deliveries.
- Other route data is hidden.



------------------------------------------------------------

PUT /deliveries/{delivery_id}/status


Purpose


Update delivery status.



Authorization


ADMIN / DELIVERY_BOY



Request:


{
    "status": "DELIVERED"
}



Business Rules


Allowed transitions:



PREPARED

↓

OUT_FOR_DELIVERY



OUT_FOR_DELIVERY

↓

DELIVERED



OUT_FOR_DELIVERY

↓

FAILED



FAILED

↓

OUT_FOR_DELIVERY



FAILED

↓

MISSED



------------------------------------------------------------

POST /deliveries/{delivery_id}/retry


Purpose


Schedule retry for failed delivery.



Authorization


ADMIN Only



Business Rules


- Delivery status must be FAILED.
- retry_count must be less than 1.
- retry_scheduled_at is set to 8 PM.
- Same delivery record is reused.



------------------------------------------------------------
ADD-ON API
------------------------------------------------------------


Base Path


/addons



Purpose


Manage additional food items.



------------------------------------------------------------

POST /addons


Purpose


Create add-on item.


Authorization


ADMIN Only



Request:


- name
- price



------------------------------------------------------------

GET /addons


Purpose


List available add-ons.


Authorization


Authenticated Users



------------------------------------------------------------

POST /addons/orders


Purpose


Create customer add-on order.


Authorization


CUSTOMER / ADMIN



Request Body


AddOnOrderCreate



Business Rules


- Customer must have active subscription.
- Add-on must exist.
- Quantity must be greater than zero.
- Request must be before 9:00 AM.



------------------------------------------------------------

GET /addons/orders


Purpose


View add-on orders.


Authorization


ADMIN Only



------------------------------------------------------------
ERROR HANDLING
------------------------------------------------------------


400 Bad Request


Examples:


- Invalid delivery status transition.
- Invalid retry request.
- Add-on requested after cutoff time.
- Invalid meal date.



------------------------------------------------------------

401 Unauthorized


Examples:


- Missing JWT.
- Invalid JWT.
- Expired token.



------------------------------------------------------------

403 Forbidden


Examples:


- Customer accessing admin APIs.
- Delivery boy accessing another route.
- Unauthorized status update.



------------------------------------------------------------

404 Not Found


Examples:


- Delivery not found.
- Customer not found.
- Delivery boy not found.
- Add-on not found.



------------------------------------------------------------

409 Conflict


Examples:


- Duplicate delivery boy phone.
- Delivery already assigned.
- Retry already scheduled.



------------------------------------------------------------

422 Validation Error


Examples:


- Invalid request body.
- Missing required fields.
- Invalid enum values.



------------------------------------------------------------
SECURITY RULES
------------------------------------------------------------


Authentication


- JWT required for protected APIs.


Authorization


- Admin manages operational data.
- Delivery boys access only assigned deliveries.
- Customers can create only their own add-on requests.


Data Protection


- Delivery boys cannot view customer identity documents.
- Internal database IDs are validated before access.
- Sensitive data is not exposed in responses.


Validation


- All input data passes through Pydantic schemas.
- Business validation occurs inside services.


------------------------------------------------------------
DEFINITION OF DONE (PHASE 3)
------------------------------------------------------------


Daily Meal Planning


✓ Generate daily meal count.

✓ Calculate diet-wise summary.

✓ Calculate plan-wise summary.

✓ Exclude paused subscriptions.

✓ Exclude expired subscriptions.



------------------------------------------------------------

Delivery Management


✓ Create delivery records.

✓ Assign delivery boys.

✓ Track delivery status.

✓ Implement delivery workflow.

✓ Handle failed deliveries.

✓ Schedule one retry.

✓ Mark second failure as missed.



------------------------------------------------------------

Delivery Boy Management


✓ Create delivery boys.

✓ Assign routes.

✓ Restrict route access.

✓ Deactivate delivery boys.



------------------------------------------------------------

Add-On Management


✓ Create add-on items.

✓ Accept add-on orders.

✓ Validate 9 AM cutoff.

✓ Reject invalid orders.



------------------------------------------------------------

Infrastructure


✓ Swagger documentation updated.

✓ Service layer completed.

✓ Repository layer completed.

✓ Business rules tested.



------------------------------------------------------------
BASIC TEST CASES
------------------------------------------------------------


Meal Planning Tests


✓ test_generate_daily_meal_count

✓ test_paused_subscription_excluded

✓ test_expired_subscription_excluded

✓ test_diet_summary_generation

✓ test_plan_summary_generation



------------------------------------------------------------

Delivery Boy Tests


✓ test_create_delivery_boy

✓ test_assign_route

✓ test_inactive_delivery_boy

✓ test_route_access_restriction



------------------------------------------------------------

Delivery Tests


✓ test_create_delivery

✓ test_status_prepared_to_out_for_delivery

✓ test_delivery_success

✓ test_failed_delivery_retry

✓ test_second_failure_marks_missed

✓ test_invalid_status_transition



------------------------------------------------------------

Add-On Tests


✓ test_create_addon

✓ test_create_addon_order

✓ test_addon_after_cutoff_rejected

✓ test_addon_without_subscription_rejected



------------------------------------------------------------
PHASE 3 DEMO SCENARIO
------------------------------------------------------------


The Phase 3 demonstration should include:


1. Login as Admin.


2. Create delivery boys.


3. Assign delivery boys to routes.


4. Generate today's meal plan.


5. Verify diet-wise and plan-wise counts.


6. Create delivery records.


7. Assign deliveries to delivery boys.


8. Login as delivery boy.


9. View assigned route deliveries.


10. Update delivery status:

PREPARED

↓

OUT_FOR_DELIVERY

↓

DELIVERED



11. Simulate failed delivery.


12. Verify retry scheduling at 8 PM.


13. Retry delivery.


14. Mark successful delivery.


15. Create add-on item.


16. Place customer add-on order before cutoff.


17. Test rejection after 9 AM.



This confirms that the complete daily kitchen and delivery
workflow is functional.


------------------------------------------------------------
PHASE 3 DELIVERABLES
------------------------------------------------------------


At the end of Phase 3, the system includes:


✓ Daily Meal Planning


✓ Diet-wise meal calculation


✓ Plan-wise meal calculation


✓ Delivery Boy Management


✓ Route-based Delivery Assignment


✓ Delivery Tracking


✓ Delivery Retry Handling


✓ Add-On Management


✓ Add-On Order Validation


✓ Operational Business Workflow


✓ Automated Tests


✓ Updated Swagger Documentation



------------------------------------------------------------
NEXT PHASE
------------------------------------------------------------


Phase 4 introduces the financial, customer support,
analytics, and reporting layer.


Modules to be implemented:


- Billing System
- Payment Management
- Payment Reminders
- Auto Pause Due To Payment
- Referral Rewards
- Complaint Management
- Dashboard APIs
- Monthly PDF Reports
- Production Readiness



Phase 4 builds directly on the operational data generated
from customers, subscriptions, meals, and deliveries.
