# MVC (Model-View-Controller)

## Core Idea
An architectural pattern that separates an application into three responsibilities.

```
User
 ↓
Controller
 ↓
Model
 ↓
Controller
 ↓
View
 ↓
User
```

## 1. Model
Responsible for **data and business logic**.
- Entities: User, Product, Order
- Database operations
- Validation / business rules

## 2. View
Responsible for **what the user sees** — e.g. the HTML/UI in a web app.

## 3. Controller
Acts as the **coordinator** — receives the request, decides what needs to happen, interacts with the Model, and chooses the View/response.

```
GET /users/123
       ↓
   Controller
       ↓
      Model
       ↓
   User data
       ↓
     View
       ↓
 HTML response
```

## Key Insight
> Separation of concerns: Model handles data/business logic, View handles presentation, Controller handles request coordination.

## Revision Questions
1. What does the Model do? → Data and business logic (validation, DB operations).
2. What does the View do? → What the user sees (presentation/UI).
3. What does the Controller do? → Coordinates: receives the request, uses the Model, picks the View.
4. GET /products/123 — which component first receives the request? → The Controller.