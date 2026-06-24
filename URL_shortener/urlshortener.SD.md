# URL Shortener System Design (Bitly-like)

## 1. Problem Statement

Design a URL shortening service similar to Bitly that:

* Converts a long URL into a short URL
* Redirects users from short URL to original URL
* Supports custom aliases
* Supports URL expiration
* Handles millions of redirects with low latency

---

# 2. Requirements

## Functional Requirements

1. Create a short URL from a long URL
2. Redirect short URL to original URL
3. Support custom aliases (optional)
4. Support expiration dates (optional)

## Non-Functional Requirements

### Latency

* URL creation should be fast
* Redirect should happen within a few milliseconds

### Availability

* Redirect service should remain available even during failures

### Scalability

Example assumptions:

* 100M Daily Active Users
* 1B URLs stored
* 100M+ redirects/day

### CAP Theorem

Prefer:

```text
Availability > Consistency
```

# 3. Scale Estimation

100M Daily Users
1B urls

Observation:

```text
Read Traffic >> Write Traffic
```

Redirect requests are much higher than URL creation requests.

---

# 4. Core Entities

## URL

```sql
URL
----
id
short_code
long_url
user_id
created_at
expires_at
click_count
```

## User

```sql
USER
-----
id
name
email
```

---

# 5. API Design

## Create Short URL

### Request

```http
POST /v1/url
```

Body:

```json
{
  "longUrl": "https://google.com/some/very/long/path",
  "customAlias": "google",
  "expiresAt": "2026-12-31"
}
```

### Response

```json
{
  "shortUrl": "https://bit.ly/15FTGh"
}
```

---

## Redirect URL

### Request

```http
GET /{shortCode}
```

Example:

```http
GET /15FTGh
```

### Response

```http
301 Moved Permanently
Location: https://google.com
```

---

# 6. High Level Design

```text
                    +------------+
                    |   Client   |
                    +------------+
                           |
                           v
                  +----------------+
                  | Load Balancer  |
                  +----------------+
                     /          \
                    /            \
                   v              v

       +----------------+   +----------------+
       | URL            |   | URL            |
       | Shortening     |   | Redirection    |
       | Service        |   | Service        |
       +----------------+   +----------------+
               |                     |
               |                     |
               +----------+----------+
                          |
                          v

                 +----------------+
                 | Redis Cache    |
                 +----------------+
                          |
                          v

                 +----------------+
                 | Database       |
                 +----------------+
```

---

# 7. URL Shortening Flow

The URL Shortening Service handles URL creation.

## Flow

```text
Long URL
    |
    v
Generate Unique Counter
    |
    v
Counter Value
(1000000001)
    |
    v
Convert Counter to Base62
    |
    v
15FTGh
    |
    v
Store Mapping
(short -> long)
    |
    v
Redis + Database
```

---

# 8. Why Base62?


Capacity:

| Length | Capacity     |
| ------ | ------------ |
| 4      | 14.7 Million |
| 5      | 916 Million  |
| 6      | 56.8 Billion |
| 7      | 3.5 Trillion |

Example:

```text
Counter = 1000000001

Base62 = 15FTGh
```

This becomes the short code.

---

# 9. URL Redirection Flow

The URL Redirection Service handles incoming redirect requests.

## Cache Hit

```text
Client
   |
GET /15FTGh
   |
   v
URL Redirection Service
   |
   v
Redis
   |
   | Cache Hit
   v
Long URL
   |
   v
301/302 Redirect
```

---

## Cache Miss

```text
Client
   |
GET /15FTGh
   |
   v
URL Redirection Service
   |
   v
Redis
   |
   | Miss
   v
Database
   |
   v
Long URL
   |
   v
Populate Redis
   |
   v
301/302 Redirect
```

---

# 10. Database Schema

```sql
CREATE TABLE urls (
    id BIGINT PRIMARY KEY,
    short_code VARCHAR(10) UNIQUE,
    long_url TEXT,
    user_id BIGINT,
    created_at TIMESTAMP,
    expires_at TIMESTAMP,
    click_count BIGINT DEFAULT 0
);
```

---

# 11. Redis Usage

Purpose:

```text
short_code -> long_url
```

Example:

```text
15FTGh -> https://google.com/very-long-url
```

Benefits:

* Sub-millisecond lookups
* Reduces database load
* Handles massive redirect traffic

---

# 12. Why Separate Services?

Instead of one service doing everything:

Use:

## URL Shortening Service

Responsible for:

* Generating IDs
* Base62 Conversion
* Creating URLs
* Writing to Database

Traffic:

```text
Write Heavy
```

---

## URL Redirection Service

Responsible for:

* Reading short URL
* Fetching long URL
* Redirecting user

Traffic:

```text
Read Heavy
```

Reason:

```text
Redirect Traffic >> Create Traffic
```

Example:

```text
Create URLs:
1 Million/day

Redirect URLs:
100 Million/day
```

Therefore:

```text
URL Shortening Service
Few Instances

URL Redirection Service
Many Instances
```

Allows independent scaling.

---



## Expiration Cleanup

Periodically remove:

```text
Expired URLs
```

Using background workers(cron jobs to delete daily).

---

# 13. Final Architecture

```text
Client
   |
   v
Load Balancer
   |
   +-----------------------------+
   |                             |
   v                             v

URL Shortening Service      URL Redirection Service
       |                            |
       +------------+---------------+
                    |
                    v

               Redis Cache
                    |
                    v

                Database
```

---

# Key Takeaways

1. Use Counter → Base62 for short code generation
2. Store mappings in Database
3. Use Redis as cache
4. Separate URL Shortening and URL Redirection services
5. Read traffic is much higher than write traffic
6. Scale Redirect Service independently

