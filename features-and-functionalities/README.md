# Airbnb Clone Backend - Features & Functionalities

This project is a backend system replicating key features of a rental marketplace similar to Airbnb.

## Key Features & Functionalities

- **User Authentication**

  - User Registration (Guest or Host)
  - Login with JWT and OAuth (Google/Facebook)
  - Profile Management

- **Property Listings Management**

  - Add Listings (Details, Price, Availability)
  - Edit/Delete Listings

- **Searching and Filtering**

  - Enables users to search and filter properties
  - Pagination of large dataset

- **Booking Management**

  - Booking Creation with Date Validation
  - Booking Cancellation per Policy
  - Booking Status (Pending, Confirmed, Canceled, Completed)

- **Payments Integration**

  - Secure Payment Processing with Stripe or PayPal
  - Automatic Host Payouts
  - Multi-currency Support

- **Reviews and Ratings**

  - Guest Reviews Linked to Bookings
  - Host Responses

- **Notifications System**

  - Email Notifications
  - In-app Notifications

- **Admin Dashboard**
  - Manage Users, Listings, Bookings, Payments

## Technical Requirements (Summary)

- Relational Database (PostgreSQL)
- RESTful APIs with optional GraphQL support
- JWT Authentication with Role-Based Access Control
- Image Storage via AWS S3
- Email Notifications through SendGrid or Mailgun
- Centralized Error Handling and Logging

## Non-Functional Requirements (Summary)

- Scalable, Modular Architecture with Load Balancers
- Data Encryption, Firewalls, and Rate Limiting for Security
- Redis Caching and Database Optimization for Performance
- Unit, Integration, and Automated API Testing

## Project Diagram

Below is a visual representation of the system's structure:

![Features and Functionalities](/features_and_functionality.png)
