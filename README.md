# Airbnb Clone Project

## Overview

The Airbnb Clone Project is a comprehensive, real-world application designed to simulate the development of a robust booking platform like Airbnb. It involves a deep dive into full-stack development, focusing on backend systems, database design, API development, and application security. This project enables learners to understand complex architectures, workflows, and collaborative team dynamics while building a scalable web application.

## ⚙️ Technology Stack

- Django: A high-level Python web framework used for building the RESTful API.
- Django REST Framework: Provides tools for creating and managing RESTful APIs.
- PostgreSQL: A powerful relational database used for data storage.
- GraphQL: Allows for flexible and efficient querying of data.
- Celery: For handling asynchronous tasks such as sending notifications or processing payments.
- Redis: Used for caching and session management.
- Docker: Containerization tool for consistent development and deployment environments.
- CI/CD Pipelines: Automated pipelines for testing and deploying code changes.

## 👥 Team Roles

- Backend Developer: Responsible for implementing API endpoints, database schemas, and business logic.
- Database Administrator: Manages database design, indexing, and optimizations.
- DevOps Engineer: Handles deployment, monitoring, and scaling of the backend services.
- QA Engineer: Ensures the backend functionalities are thoroughly tested and meet quality standards.

## Database Design

### Overview

The Airbnb Clone database is designed using a relational database model with PostgreSQL as the primary database management system. The schema follows normalization principles to ensure data integrity, minimize redundancy, and optimize query performance. The database supports the core functionalities of the platform including user management, property listings, booking systems, payment processing, and review management.

### Key Entities

#### 1. Users

The Users entity stores information about all platform users, including both guests and hosts.

**Important Fields:**

- `user_id` (Primary Key): Unique identifier for each user
- `email`: User's email address (unique, used for authentication)
- `password_hash`: Securely hashed password for authentication
- `first_name`: User's first name
- `last_name`: User's last name
- `phone_number`: Contact phone number
- `profile_picture_url`: URL to user's profile image
- `is_host`: Boolean flag indicating if user can list properties
- `created_at`: Timestamp of account creation
- `updated_at`: Timestamp of last profile update

#### 2. Properties

The Properties entity contains all information about rental properties listed on the platform.

**Important Fields:**

- `property_id` (Primary Key): Unique identifier for each property
- `host_id` (Foreign Key): References Users table - owner of the property
- `title`: Property listing title
- `description`: Detailed description of the property
- `price_per_night`: Rental price per night
- `location`: Property address/location details
- `bedrooms`: Number of bedrooms
- `bathrooms`: Number of bathrooms
- `max_guests`: Maximum number of guests allowed
- `amenities`: JSON field storing available amenities
- `is_available`: Boolean flag for property availability
- `created_at`: Timestamp when property was listed
- `updated_at`: Timestamp of last property update

#### 3. Bookings

The Bookings entity manages all reservation information and booking details.

**Important Fields:**

- `booking_id` (Primary Key): Unique identifier for each booking
- `guest_id` (Foreign Key): References Users table - guest making the booking
- `property_id` (Foreign Key): References Properties table - booked property
- `check_in_date`: Start date of the booking
- `check_out_date`: End date of the booking
- `number_of_guests`: Number of guests for this booking
- `total_amount`: Total cost of the booking
- `booking_status`: Status (confirmed, pending, cancelled, completed)
- `created_at`: Timestamp when booking was made
- `updated_at`: Timestamp of last booking modification

#### 4. Reviews

The Reviews entity stores user feedback and ratings for properties.

**Important Fields:**

- `review_id` (Primary Key): Unique identifier for each review
- `reviewer_id` (Foreign Key): References Users table - user writing the review
- `property_id` (Foreign Key): References Properties table - property being reviewed
- `booking_id` (Foreign Key): References Bookings table - associated booking
- `rating`: Numerical rating (1-5 stars)
- `comment`: Text review content
- `created_at`: Timestamp when review was posted
- `updated_at`: Timestamp of last review update

#### 5. Payments

The Payments entity tracks all financial transactions within the platform.

**Important Fields:**

- `payment_id` (Primary Key): Unique identifier for each payment
- `booking_id` (Foreign Key): References Bookings table - associated booking
- `payer_id` (Foreign Key): References Users table - user making the payment
- `amount`: Payment amount
- `payment_method`: Payment method used (credit card, PayPal, etc.)
- `payment_status`: Status (pending, completed, failed, refunded)
- `transaction_id`: External payment processor transaction ID
- `created_at`: Timestamp when payment was initiated
- `processed_at`: Timestamp when payment was completed

### Entity Relationships

#### One-to-Many Relationships

1. **Users → Properties**

   - One user (host) can have multiple properties
   - Each property belongs to exactly one host
   - Relationship: `Users.user_id` ← `Properties.host_id`

2. **Users → Bookings (as Guest)**

   - One user can make multiple bookings
   - Each booking is made by exactly one guest
   - Relationship: `Users.user_id` ← `Bookings.guest_id`

3. **Properties → Bookings**

   - One property can have multiple bookings
   - Each booking is for exactly one property
   - Relationship: `Properties.property_id` ← `Bookings.property_id`

4. **Users → Reviews**

   - One user can write multiple reviews
   - Each review is written by exactly one user
   - Relationship: `Users.user_id` ← `Reviews.reviewer_id`

5. **Properties → Reviews**

   - One property can have multiple reviews
   - Each review is for exactly one property
   - Relationship: `Properties.property_id` ← `Reviews.property_id`

6. **Bookings → Payments**
   - One booking can have multiple payment attempts
   - Each payment is associated with exactly one booking
   - Relationship: `Bookings.booking_id` ← `Payments.booking_id`

#### One-to-One Relationships

1. **Bookings → Reviews**
   - Each booking can have at most one review
   - Each review is associated with exactly one booking
   - Relationship: `Bookings.booking_id` ← `Reviews.booking_id`

### Database Constraints and Indexes

#### Primary Keys

- All entities have auto-incrementing primary keys for optimal performance
- Primary keys ensure entity uniqueness and serve as the main reference point

#### Foreign Key Constraints

- All foreign keys include referential integrity constraints
- Cascade rules defined for data consistency during deletions
- ON DELETE CASCADE for dependent records
- ON UPDATE CASCADE for primary key updates

#### Unique Constraints

- `Users.email` - Ensures unique user identification
- `Properties.property_id` - Prevents duplicate property listings
- `Reviews.booking_id` - Ensures one review per booking

#### Indexes for Performance Optimization

- **Users**: Index on `email` for authentication queries
- **Properties**: Index on `host_id`, `location`, `price_per_night` for search functionality
- **Bookings**: Index on `guest_id`, `property_id`, `check_in_date`, `check_out_date` for booking queries
- **Reviews**: Index on `property_id` for review retrieval
- **Payments**: Index on `booking_id` and `payment_status` for transaction tracking

### Data Integrity Rules

1. **Business Logic Constraints**

   - Check-in date must be before check-out date
   - Number of guests cannot exceed property's max_guests
   - Rating must be between 1 and 5
   - Price per night must be positive

2. **Referential Integrity**

   - All foreign keys must reference existing records
   - Orphaned records are prevented through cascade rules
   - Booking dates cannot overlap for the same property

3. **Data Validation**
   - Email format validation
   - Phone number format validation
   - Required fields enforcement
   - Enum constraints for status fields

This database design ensures scalability, data integrity, and optimal performance for the Airbnb Clone application while supporting all core functionalities and business requirements.

## Feature Breakdown

### 1. User Management

Handles secure registration, authentication, and profile management for guests and hosts. Implements role-based access control and serves as the foundation for all platform interactions. Ensures secure access and maintains user data integrity across the application.

### 2. Property Management

Enables hosts to create, update, and manage rental listings with details like pricing, amenities, and availability. Provides CRUD operations through RESTful APIs for property maintenance. Integrates with search and booking systems for real-time updates.

### 3. Booking System

Facilitates reservation functionality, allowing guests to search and book properties for specific dates. Manages availability checking, booking status tracking, and coordinates with payment processing. Ensures smooth booking experience while maintaining data consistency.

### 4. Payment Processing

Handles secure financial transactions with multiple payment methods and PCI compliance. Integrates with external payment gateways for processing transactions and managing refunds. Works with booking system to verify payments before confirming reservations.

### 5. Review System

Allows guests to provide feedback and ratings after completing stays. Implements one-review-per-booking policy for authentic feedback. Builds trust within the platform community and maintains service quality standards.

### 6. API Documentation

Provides comprehensive OpenAPI documentation for clear frontend-backend communication. Offers interactive documentation for testing endpoints and understanding request/response formats. Supports both REST and GraphQL APIs for flexible integration.

### 7. Database Optimization

Implements strategic indexing, Redis caching, and query optimization for high performance. Includes connection pooling and optimized queries to handle increased loads efficiently. Provides the foundation for fast response times and scalability.
