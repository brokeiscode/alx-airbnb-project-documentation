# Backend Feature Requirements: Airbnb Clone

This document specifies the detailed technical and functional requirements for critical backend features of the Airbnb Clone application. Each section outlines the purpose, API endpoints, input/output specifications, validation rules, and performance criteria.

---

## 1. Feature: User Authentication & Authorization

### Purpose

To securely manage user registration, login, and access control, ensuring that only authenticated and authorized users can perform specific actions within the system.

### Functional Requirements

- **1. User Registration:** The system SHALL allow new users to register as either a "Guest" or a "Host" by providing a unique email address, password, and chosen role.
- **2. User Login:** The system SHALL allow registered users to log in using their email and password, issuing a JWT upon successful authentication.
- **3. Password Hashing:** The system SHALL securely hash and salt all user passwords before storing them in the database.
- **4. Token Validation:** The system SHALL validate JWTs for every authenticated request to ensure the user's identity and session validity.
- **5. Role-Based Authorization:** The system SHALL enforce role-based access control (RBAC), ensuring users can only access resources and perform actions permitted by their assigned role (Guest, Host, Admin).
- **6. Profile Update:** The system SHALL allow authenticated users to update their profile information (e.g., first name, last name, phone number).

### Technical Requirements

#### API Endpoints:

- **User Registration:**
  - `POST /users/`
- **User Login:**
  - `GET /users/{user_id}/`
- **Profile Update:**
  - `PUT /users/{user_id}/`

#### Input Specifications:

- **`POST /users/`:**

  ```json
  {
    "first_name": "John",
    "last_name": "Doe",
    "email": "johndoe@example.com",
    "password": "StrongPassword123!",
    "phone_number": "+234812345678",
    "role": "guest" // or "host" or "admin"
  }
  ```

  - `first_name`: String
  - `last_name`: String
  - `email`: String (email format)
  - `password`: String
  - `role`: String (enum: "guest", "host")

- **`GET /users/{user_id}/`:**

  ```json
  {
    "email": "johndoe@example.com",
    "password": "StrongPassword123!"
  }
  ```

  - `email`: String (email format)
  - `password`: String

- **`PUT /users/{user_id}/`:** (Example for updating phone number)
  ```json
  {
    "phone_number": "+234812345678"
  }
  ```
  - `phone_number`: String (optional, E.164 format recommended)

#### Output Specifications:

- **Successful Registration/Login (200 OK):**

  ```json
  {
    "message": "User registered/logged in successfully",
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "user_id": "uuid-1234",
      "email": "johndoe@example.com",
      "role": "guest",
      "created_at": "2025-06-28T10:00:00Z"
    }
  }
  ```

  - `message`: String
  - `token`: String (JWT)
  - `user`: Object (user ID, email, role)

- **Successful Profile Update (200 OK):**
  ```json
  {
    "message": "Profile updated successfully",
    "user": {
      "user_id": "uuid-1234",
      "first_name": "John",
      "last_name": "Doe",
      "email": "johndoe@example.com",
      "phone_number": "+234812345678",
      "role": "guest", // or "host" or "admin"
      "created_at": "2025-06-28T10:00:00Z"
    }
  }
  ```
- **Error Responses (400 Bad Request, 401 Unauthorized, 409 Conflict):**
  ```json
  {
    "error": "Error message describing the issue."
  }
  ```

#### Validation Rules:

- **Email:** Must be a valid email format, unique for registration.
- **Password:** Minimum 8 characters, at least one uppercase, one lowercase, one number, one special character (for registration).
- **Role:** Must be "guest" or "host" for registration.
- **Profile Update:** Fields must conform to expected data types.

#### Performance Criteria:

- **Latency:** Login and registration requests SHALL respond within 100ms for 95% of requests under normal load.
- **Throughput:** The system SHALL support 500 concurrent authentication requests per second.

---

## 2. Feature: Property Listing Management

### Purpose

To enable hosts to create, read, update, and delete property listings, including details, pricing, and availability.

### Functional Requirements

- **1. Add Listing:** Hosts SHALL be able to create new property listings with comprehensive details (title, description, location, price per night).
- **2. View Listings:** Users (guests and hosts) SHALL be able to view individual property listings and a list of all available properties.
- **3. Edit Listing:** Hosts SHALL be able to modify details of their existing property listings. Only the owning host can edit their listing.
- **4. Delete Listing:** Hosts SHALL be able to remove their property listings. Only the owning host can delete their listing.
- **5. Manage Availability:** Hosts SHALL be able to specify available and unavailable dates for their properties.

### Technical Requirements

#### API Endpoints:

- **Add Property:**
  - `POST /properties/` (Requires Host Authorization)
- **Get All Properties (for guests/search):**
  - `GET /properties/`
- **Get Single Property:**
  - `GET /properties/{property_id}/`
- **Update Property:**
  - `PUT /properties/{property_id}/` (Requires Host Authorization)
- **Delete Property:**
  - `DELETE /properties/{property_id}/` (Requires Host Authorization)

#### Input Specifications:

- **`POST /properties/` / `PUT /properties/{property_id}/`:**

  ```json
  {
    "host_id": "user-uuid-1234",
    "name": "Cozy Beachfront Villa",
    "description": "A beautiful villa with ocean views...",
    "street_name": "123 Adeniyi Jones",
    "city": "Ikeja",
    "state": "Lagos",
    "country": "Nigeria",
    "zip_code": "101200",
    "pricepernight": 250.0
  }
  ```

  - `name`: String (max 255 chars)
  - `description`: String (max 1000 chars)
  - `street_name`: String
  - `city`: String
  - `state`: String
  - `country`: String
  - `zip_code`: String
  - `price_per_night`: Decimal (Integer, two decimal places)

- **`DELETE /properties/{property_id}/`:**

  ```json
  {
    "id": "prop-uuid-5678",
    "host_id": "host-uuid-1234"
  }
  ```

- **`GET /properties/{property_id}/`:**

#### Output Specifications:

- **Successful Property Creation/Update (201 Created / 200 OK):**

  ```json
  {
    "message": "Property created/updated successfully",
    "property": {
      "property_id": "prop-uuid-5678",
      "host_id": "user-uuid-1234",
      "name": "Cozy Beachfront Villa",
      "description": "A beautiful villa with ocean views...",
      "street_name": "123 Adeniyi Jones",
      "city": "Ikeja",
      "state": "Lagos",
      "country": "Nigeria",
      "zip_code": "101200",
      "pricepernight": 250.0,
      "created_at": "2025-06-28T10:00:00Z",
      "updated_at": "2025-06-28T10:00:00Z"
    }
  }
  ```

- **Successful Property Deletion (204 No Content):**

  ```json
  {
    "response": "Listing was successfully deleted."
  }
  ```

- **Get All Properties (200 OK):**

  ```json
  {
      "properties": [
          { "property_id": "prop-uuid-5678", "title": "...", "price_per_night": 250.00, ... },
          { "property_id": "prop-uuid-9101", "title": "...", "price_per_night": 150.00, ... }
      ],
      "pagination": { "total_pages": 5, "current_page": 1, "total_items": 50 }
  }
  ```

- **Error Responses (400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found):**

  ```json
  {
    "error": "Error message describing the issue."
  }
  ```

#### Validation Rules:

- **Host Authorization:** Only authenticated hosts can add, edit, or delete listings.
- **Ownership Check:** A host can only modify/delete listings they own.
- **Required Fields:** Name, description, street name, city, state, country, zip code, price per night.

#### Performance Criteria:

- **Latency:** Add/Edit/Delete listing requests SHALL respond within 200ms for 95% of requests. Get all properties requests SHALL respond within 300ms for 95% of requests.
- **Throughput:** The system SHALL support 100 concurrent listing management requests per second.

---

## 3. Feature: Booking Management

### Purpose

To facilitate guests in booking properties and enable hosts to manage incoming booking requests and confirmations.

### Functional Requirements

- **1. Book Property:** Guests SHALL be able to book an available property for a specified check-in and check-out date range. The system MUST ensure the property is available for the entire duration.
- **2. View Guest Bookings:** Guests SHALL be able to view a list of their past, current, and upcoming bookings.
- **3. View Host Bookings:** Hosts SHALL be able to view a list of bookings made for their properties.
- **4. Cancel Booking - Guest:** Guests SHALL be able to cancel their bookings, subject to the property's cancellation policy.
- **5. Cancel Booking - Host:** Hosts SHALL be able to cancel bookings for their properties under specific, justified circumstances (e.g., emergency), subject to platform policies.
- **6. Booking Status Tracking:** The system SHALL maintain and update the status of each booking (e.g., `pending`, `confirmed`, `canceled`, `completed`).

### Technical Requirements

#### API Endpoints:

- **Create Booking:**
  - `POST /bookings/` (Requires Guest Authorization)
- **Get Guest's Bookings:**
  - `GET /bookings/{booking_id}/` (Requires Guest Authorization, or Admin for any `userId`)
- **Get Host's Property Bookings:**
  - `GET /bookings/{booking_id}/` (Requires Host Authorization for own `hostId`, or Admin)
- **Cancel Booking:**
  - `PUT /bookings/{booking_id}/` (Requires Guest/Host Authorization for own booking/property)

#### Input Specifications:

- **`POST /bookings/`:**

  ```json
  {
    "property_id": "prop-uuid-5678",
    "user_id": "user-uuid-1234"
    "start_date": "2025-08-01",
    "end_date": "2025-08-05",
    "total_price": 1238.50,
  }
  ```

  - `property_id`: String (UUID)
  - `user_id`: String (UUID)
  - `start_date`: String (YYYY-MM-DD)
  - `end_date`: String (YYYY-MM-DD)
  - `total_price`: Float (Integer, decimal digits)

- **`PUT /bookings/{booking_id}/`:**

  ```json
  {
    "reason": "Changed travel plans" // Optional
  }
  ```

  - `reason`: String (optional)

- **`GET /bookings/{booking_id}/`:** (Host)

  ```json
  {
    "booking_id": "book-uuid-abc"
  }
  ```

  - `reason`: String (optional)

#### Output Specifications:

- **Successful Booking Creation (201 Created):**

  ```json
  {
    "message": "Booking created successfully",
    "booking": {
      "booking_id": "book-uuid-abc",
      "property_id": "prop-uuid-5678",
      "user_id": "user-uuid-123",
      "start_date": "2025-08-01",
      "end_date": "2025-08-05",
      "total_price": 1238.5,
      "status": "pending",
      "created_at": "2025-06-28T10:05:00Z"
    }
  }
  ```

- **Successful Booking Cancellation (200 OK):**

  ```json
  {
    "message": "Booking cancelled successfully",
    "booking": {
      "booking_id": "book-uuid-abc",
      "status": "canceled",
      "cancellation_reason": "Changed travel plans"
    }
  }
  ```

- **Get Bookings (200 OK):** (Host)

  ```json
  {
      "bookings": [
          { "booking_id": "book-uuid-abc", "property_id": "...", "status": "...", ... },
          { "booking_id": "book-uuid-def", "property_id": "...", "status": "...", ... }
      ],
      "pagination": { "total_pages": 3, "current_page": 1, "total_items": 30 }
  }
  ```

- **Error Responses (400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 409 Conflict):**

  ```json
  {
    "error": "Error message describing the issue."
  }
  ```

  (e.g., "Property not available for selected dates", "Invalid number of guests")

#### Validation Rules:

- **Date Range:** `end_date` must be after `start_date`.
- **Availability:** Requested dates must be available in the property's calendar.
- **Authorization:** Guests can only book properties and cancel their own bookings. Hosts can view bookings for their properties and cancel bookings on their properties.
- **Cancellation Policy:** Apply business logic for allowed cancellation periods and refunds.

#### Performance Criteria:

- **Latency:** Booking creation requests SHALL respond within 250ms for 95% of requests, including availability check and database write.
- **Throughput:** The system SHALL support 150 concurrent booking requests per second.
