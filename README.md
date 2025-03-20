# HBnB Evolution: Technical Documentation

## Overview

This document outlines the architecture and design of **HBnB Evolution**, a simplified AirBnB-like application. It covers the high-level package structure, detailed class design for the business logic layer, and sequence diagrams for key API operations. This serves as the foundation for development.

---

## Architecture

HBnB Evolution uses a **layered architecture** with three primary layers:

- **Presentation Layer**: Exposes RESTful APIs for user interaction.
- **Business Logic Layer**: Manages core entities and business rules.
- **Persistence Layer**: Handles database storage and retrieval.

### High-Level Package Diagram

The system is organized into three packages, communicating via the **Facade pattern**:

| Package         | Components                          | Responsibilities                          |
|-----------------|-------------------------------------|-------------------------------------------|
| `presentation`  | `UserAPI`, `PlaceAPI`, `ReviewAPI`, `AmenityAPI` | Handles API requests and responses.       |
| `business`      | `ApplicationFacade`, `UserService`, `PlaceService`, `ReviewService`, `AmenityService` | Processes logic and delegates to persistence. |
| `persistence`   | `UserRepository`, `PlaceRepository`, `ReviewRepository`, `AmenityRepository` | Manages data CRUD operations.            |

**Flow**: `presentation` → `business` (via `ApplicationFacade`) → `persistence`.

---

## Business Logic Layer

### Class Diagram

The core entities include attributes, methods, and relationships:

#### User
- **Attributes**: `id: UUID`, `firstName: String`, `lastName: String`, `email: String`, `password: String`, `isAdmin: Boolean`, `createdAt: DateTime`, `updatedAt: DateTime`
- **Methods**: `register()`, `updateProfile()`, `delete()`

#### Place
- **Attributes**: `id: UUID`, `title: String`, `description: String`, `price: Float`, `latitude: Float`, `longitude: Float`, `owner: User`, `amenities: List<Amenity>`, `createdAt: DateTime`, `updatedAt: DateTime`
- **Methods**: `create()`, `update()`, `delete()`, `addAmenity()`, `removeAmenity()`

#### Review
- **Attributes**: `id: UUID`, `place: Place`, `user: User`, `rating: Integer`, `comment: String`, `createdAt: DateTime`, `updatedAt: DateTime`
- **Methods**: `create()`, `update()`, `delete()`

#### Amenity
- **Attributes**: `id: UUID`, `name: String`, `description: String`, `createdAt: DateTime`, `updatedAt: DateTime`
- **Methods**: `create()`, `update()`, `delete()`

#### Relationships
- `User` → `Place`: One-to-Many
- `Place` ↔ `Amenity`: Many-to-Many
- `Place` → `Review`: One-to-Many
- `User` → `Review`: One-to-Many

---

## API Sequence Diagrams

### 1. User Registration (`POST /users/register`)
**Participants**: Client, `UserAPI`, `ApplicationFacade`, `UserService`, `UserRepository`  
**Steps**:  
1. Client sends `{firstName, lastName, email, password}`.  
2. `UserAPI` → `ApplicationFacade.registerUser()`.  
3. `ApplicationFacade` → `UserService.register()`.  
4. `UserService` checks email uniqueness via `UserRepository`.  
5. If valid, hashes password and saves user via `UserRepository`.  
6. Response: `201 Created` with user details (no password).

### 2. Place Creation (`POST /places/create`)
**Participants**: Client, `PlaceAPI`, `ApplicationFacade`, `PlaceService`, `PlaceRepository`, `UserRepository`  
**Steps**:  
1. Client sends `{title, description, price, latitude, longitude, ownerId}`.  
2. `PlaceAPI` → `ApplicationFacade.createPlace()`.  
3. `PlaceService` verifies owner via `UserRepository`.  
4. Saves place via `PlaceRepository`.  
5. Response: `201 Created` with place details.

### 3. Review Submission (`POST /reviews/create`)
**Participants**: Client, `ReviewAPI`, `ApplicationFacade`, `ReviewService`, `ReviewRepository`, `PlaceRepository`, `UserRepository`  
**Steps**:  
1. Client sends `{placeId, userId, rating, comment}`.  
2. `ReviewAPI` → `ApplicationFacade.createReview()`.  
3. `ReviewService` validates `placeId` and `userId`.  
4. Saves review via `ReviewRepository`.  
5. Response: `201 Created` with review details.

### 4. Fetch Places (`GET /places`)
**Participants**: Client, `PlaceAPI`, `ApplicationFacade`, `PlaceService`, `PlaceRepository`  
**Steps**:  
1. Client requests list of places.  
2. `PlaceAPI` → `ApplicationFacade.getAllPlaces()`.  
3. `PlaceService` → `PlaceRepository.findAll()`.  
4. Response: `200 OK` with list of places.
