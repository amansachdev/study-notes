# Backend for Frontend (BFF)

## Core Idea
A backend layer **specifically designed for a particular frontend or client**.

> BFF is a backend tailored to the needs of a specific frontend.

```
                 Frontend
                    │
                    ▼
             ┌─────────────┐
             │     BFF     │
             └──────┬──────┘
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
       User API   Product API  Orders API
```

Instead of the frontend calling many backend services directly, it calls **one BFF**, which coordinates those calls.

## Why Use It?
Imagine a homepage needs: user profile, recommendations, cart, notifications.

**Without BFF** (frontend fans out to each service):
```
Frontend → User Service
         → Recommendation Service
         → Cart Service
         → Notification Service
```

**With BFF** (frontend calls one endpoint, BFF fans out):
```
Frontend → BFF → User Service
                Recommendation Service
                Cart Service
                Notification Service
```

## Aggregation & Reshaping
The BFF can aggregate and reshape responses into exactly what that frontend needs.

Instead of four separate responses, it returns one:
```json
{
  "user": {...},
  "recommendations": [...],
  "cart": {...},
  "notifications": [...]
}
```

## Why a Separate BFF per Client?
Companies often build a separate BFF for **mobile** vs **web** — different clients need different response shapes, payload sizes, and performance characteristics. A single shared backend API can't tailor to both well.

## Key Insight
- Frontend talks to **one BFF**, not N services
- BFF coordinates, aggregates, and reshapes responses
- BFF is client-specific — mobile and web may each get their own

## Revision Questions
1. What is a BFF? → A backend layer tailored to a specific frontend/client.
2. What does it do? → Coordinates calls to multiple services, aggregates and reshapes responses.
3. Why one per client? → Different clients need different shapes, payloads, and performance.
4. BFF vs direct service calls? → Frontend calls one BFF instead of fanning out to many services.