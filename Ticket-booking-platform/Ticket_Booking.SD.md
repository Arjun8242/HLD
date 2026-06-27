# Ticket Booking Platform (like BookMyShow, Paytm, District)

-   Online platform that allows users to purchase tickets for movies,
    events, etc.

## 1. Functional Requirements

-   User should be able to search an event based on:
    -   Title
    -   Description
    -   Date
-   User should be able to view details of the event:
    -   Seats
    -   Description
    -   Metadata
-   User should be able to book tickets for that event.

## 2. Non-Functional Requirements

-   Scale: **100M DAU**
-   **CAP Theorem**
    -   Highly available with respect to searching and viewing an event.
    -   Highly consistent with respect to booking a ticket.

## 3. Core Entities

-   User
-   Event (Movie / Concert)
-   Venue (Hall / Location)
-   Ticket

## 4. API Design

### Search Events

``` http
GET /v1/search?q={searchTerm}&location={location}&date={date}
```

**Response**

-   List`<EventId>` (with pagination)

### Get Event Details

``` http
GET /v1/event/{eventId}
```

**Response**

-   Event details
-   Location
-   Seats

> To make it secure, pass the **userId** in the request headers.

### Reserve Seats (First Reserve)

``` http
POST /v1/booking/reserve
```

Request Body

``` text
{
  List<Seat> seats;
}
```

### Confirm Booking

``` http
POST /v1/booking/confirm
```

Request Body

``` text
{
  bookingId,
  paymentDetails
}
```

------------------------------------------------------------------------

# High Level Design (HLD)

``` text
Client
   |
API Gateway
(Authentication, Routing)
   |--------> Search Service ----> Search DB
   |--------> Event Service  ----> Cassandra
   |--------> Booking Service ---> MySQL
   |--------> Payment Service
```

## HLD Notes

-   Search operations prioritize **availability**.
-   Booking operations prioritize **consistency**.
-   Booking data is stored in **MySQL/PostgreSQL**.
-   Search data is stored in **Elasticsearch**.
-   New events are pushed to Kafka so the Search Service can index them
    into Elasticsearch.

## Low Level Design (LLD)

## LLD Block Diagram

flowchart LR
    Client --> APIGateway

    APIGateway --> SearchService
    APIGateway --> BookingService
    APIGateway --> EventService

    SearchService --> RedisCache
    SearchService --> Elasticsearch

    EventService --> Cassandra

    BookingService --> MySQL
    BookingService --> PaymentService

    PaymentService --> PaymentGateway

    EventProducer --> Kafka
    Kafka --> SearchIndexer
    SearchIndexer --> Elasticsearch

### Seat Reservation Flow

``` text
Client
   │
   ▼
API Gateway
   │
   ▼
Booking Service
   │
   ├── Reserve seats in Redis (TTL ≈ 10 min)
   │
   ├── Payment Service
   │      │
   │      ├── Success → Persist booking in MySQL → Release Redis lock
   │      └── Failure → Release seat lock / Wait for TTL expiry
   │
   ▼
Response to Client
```

### Search Flow

-   Client → API Gateway → Search Service → Search Cache → Elasticsearch

### Booking Flow

-   Client → API Gateway → Booking Service → MySQL

### Payment Flow

-   Booking Service → Payment Gateway

## Cache

-   Search cache uses **Redis**.
-   Cache entries have a **TTL**.

## Seat Locking

-   When a user selects seats:
    -   Reserve them temporarily.
    -   Store the reservation in Redis.
    -   Apply a TTL (around 10 minutes).

### Booking Confirmation

-   If payment succeeds:
    -   Persist booking in MySQL.
    -   Remove reservation from Redis.

### Payment Failure

-   If payment fails:
    -   Release seat lock.
    -   Reservation expires after TTL.

## Notes

-   Movie:
    -   Title
    -   Actors
-   Event:
    -   Movie
    -   Match
    -   Concert
    -   etc.
-   When booking:
    -   If payment service crashes after payment but before
        confirmation, recovery should happen through asynchronous
        processing.
