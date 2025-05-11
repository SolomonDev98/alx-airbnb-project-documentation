🔐 1. User Authentication
1.1 Endpoints
POST /auth/register/ – Register a new user (guest or host)

POST /auth/login/ – Log in with email and password

POST /auth/oauth/ – Login/register via OAuth (Google, Facebook)

GET /users/{user_id}/ – Retrieve user profile

PUT /users/{user_id}/ – Update profile details

1.2 Input Specifications
POST /auth/register/
{
  "email": "user@example.com",
  "password": "StrongPass123!",
  "full_name": "Jane Doe",
  "role": "host" // or "guest"
}
Validation Rules:

Email must be unique and valid.

Password must be at least 8 characters with a number and special character.

Role must be either "guest" or "host".

POST /auth/login/
{
  "email": "user@example.com",
  "password": "StrongPass123!"
}
1.3 Output Specifications
Successful Registration/Login:
{
  "user_id": 101,
  "email": "user@example.com",
  "role": "guest",
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOi..."
}
1.4 Performance & Security
Passwords hashed using bcrypt.

JWTs expire after 1 hour; refresh tokens supported.

Rate-limiting for login attempts.

OAuth token verification with Google/Facebook APIs.

🏘️ 2. Property Management
2.1 Endpoints
POST /properties/ – Create a new property

GET /properties/ – List all properties with filters

GET /properties/{property_id}/ – Get single property details

PUT /properties/{property_id}/ – Update property

DELETE /properties/{property_id}/ – Remove property

2.2 Input Specifications
POST /properties/
{
  "title": "Cozy Apartment in Paris",
  "description": "Great view of the Eiffel Tower",
  "location": "Paris, France",
  "price": 120.00,
  "amenities": ["Wi-Fi", "Kitchen", "Pet Friendly"],
  "max_guests": 4,
  "availability": [
    {"start_date": "2025-06-01", "end_date": "2025-06-30"}
  ]
}
Validation Rules:

Title and description required.

Price must be a positive float.

Max guests: integer > 0.

Availability dates must be in the future and non-overlapping.

2.3 Output Specifications
{
  "property_id": 501,
  "host_id": 101,
  "title": "Cozy Apartment in Paris",
  "created_at": "2025-05-10T15:00:00Z"
}
2.4 Filtering Support
Query params: ?location=Paris&min_price=50&max_price=150&amenities=Wi-Fi,Kitchen

2.5 Performance Criteria
Indexed on location, price, and availability.start_date.

Caching frequent queries using Redis.

📅 3. Booking System
3.1 Endpoints
POST /bookings/ – Create a booking

GET /bookings/{booking_id}/ – View booking details

PUT /bookings/{booking_id}/ – Modify booking (if allowed)

DELETE /bookings/{booking_id}/ – Cancel booking

3.2 Input Specifications
POST /bookings/
`{
  "property_id": 501,
  "guest_id": 303,
  "start_date": "2025-06-05",
  "end_date": "2025-06-10"
}`

Validation Rules:

Dates must fall within the property’s available range.

Check for overlapping bookings.

Prevent same user from booking the same property twice for overlapping dates.

3.3 Output Specifications
{
  "booking_id": 901,
  "status": "pending",
  "total_price": 600.00,
  "created_at": "2025-05-11T12:00:00Z"
}
3.4 Booking Status Lifecycle
pending → confirmed (after payment) → completed or cancelled

3.5 Cancellation Rules
Allowed within a defined time window (e.g., 48 hours before start).

Automatically trigger refund logic if eligible.

3.6 Performance Criteria
Conflict-checking optimized via database-level constraints or indexed queries.

Background tasks (via Celery) to update expired pending bookings.

