# Kirana Sathi — API Documentation

This document outlines the RESTful API endpoints for **Kirana Sathi**. All monetary values are represented in **Integer Paise** (1 INR = 100 paise) to prevent floating-point rounding errors.

---

## 1. Authentication Endpoints

### `POST /api/auth/register`
Register a new customer account. Public registrations are strictly granted the `CUSTOMER` role.
- **Request Body**:
  ```json
  {
    "name": "Ananya Banerjee",
    "email": "ananya@example.com",
    "phone": "+91 98300 12345",
    "password": "Password@123"
  }
  ```
- **Response `200 OK`**:
  ```json
  {
    "success": true,
    "message": "Account created successfully!",
    "user": { "id": "cuid...", "name": "...", "email": "...", "role": "CUSTOMER" }
  }
  ```
- **Sets Cookie**: `kirana_session` (HTTP-only, Lax, 7 days).

### `POST /api/auth/login`
Authenticate using email and password.
- **Request Body**:
  ```json
  {
    "email": "customer@kiranasathi.in",
    "password": "Password@123"
  }
  ```
- **Response `200 OK`**:
  ```json
  {
    "success": true,
    "message": "Logged in successfully!",
    "user": { "id": "...", "name": "...", "role": "CUSTOMER" }
  }
  ```

### `POST /api/auth/logout`
Clears session cookie.

### `GET /api/auth/session`
Returns currently active session or `{ success: false, user: null }`.

---

## 2. Products & Catalogue

### `GET /api/products`
Query catalogue with server-side filters, search, and pagination.
- **Query Parameters**:
  - `search`: string (e.g. `atta`, `mustard oil`)
  - `category`: category slug or ID
  - `brand`: brand slug or ID
  - `dietaryType`: `VEG`, `NON_VEG`, `EGG`, `VEGAN`
  - `sort`: `newest`, `popular`, `price_asc`, `price_desc`, `discount`
  - `page`: integer (default: 1)
  - `limit`: integer (default: 12)
- **Response `200 OK`**:
  ```json
  {
    "success": true,
    "products": [...],
    "pagination": { "page": 1, "limit": 12, "totalCount": 25, "totalPages": 3 }
  }
  ```

### `GET /api/products/[id]`
Retrieve product details by ID, slug, or base SKU. Includes all variants, stock, images, and reviews.

### `POST /api/products` *(Admin / Inventory Staff Only)*
Create a new product with multiple pack size variants and initial stock.

### `DELETE /api/products/[id]` *(Admin Only)*
Safely archives product from store without breaking historic order references.

---

## 3. Categories

### `GET /api/categories`
Retrieve active category hierarchy with product counts.
- Query Parameter: `includeProducts=true` (includes top 6 products per category).

### `POST /api/categories` *(Admin Only)*
Create a new category with Bengali title and slug.

---

## 4. Coupons & Marketing

### `POST /api/coupons/validate`
Validates coupon against current basket subtotal and usage limits.
- **Request Body**:
  ```json
  {
    "code": "WELCOME100",
    "subtotalInPaise": 55000
  }
  ```
- **Response `200 OK`**:
  ```json
  {
    "success": true,
    "message": "Coupon WELCOME100 applied successfully!",
    "coupon": {
      "id": "...",
      "code": "WELCOME100",
      "discountType": "FIXED",
      "discountValue": 10000,
      "freeDelivery": false
    }
  }
  ```

---

## 5. Orders & Checkout

### `POST /api/orders`
Place a grocery order in an ACID database transaction. Atomically checks stock, reserves/deducts inventory, records inventory movement log, and snapshots prices and SKUs.
- **Request Body**:
  ```json
  {
    "address": {
      "recipientName": "Ananya Banerjee",
      "phone": "+91 98300 44556",
      "addressLine1": "Flat 4B, Silver Oak, Sector 1",
      "city": "Kolkata",
      "postalCode": "700064"
    },
    "items": [
      { "variantId": "var_123", "quantity": 1 }
    ],
    "paymentMethod": "COD", // or "RAZORPAY"
    "couponCode": "WELCOME100",
    "deliverySlot": "Express 45-60 Mins"
  }
  ```
- **Response `200 OK`**:
  ```json
  {
    "success": true,
    "message": "Order placed successfully!",
    "order": { "id": "...", "orderNumber": "KS-2026-...", "totalInPaise": 41745 }
  }
  ```

### `GET /api/orders`
List orders. Customers view their own orders; delivery staff view their assigned orders; Admins view all orders.

### `PATCH /api/orders/[id]/status`
Update order status enforcing state machine rules:
- `PENDING` → `CONFIRMED` or `CANCELLED`
- `CONFIRMED` → `PACKED` or `CANCELLED`
- `PACKED` → `OUT_FOR_DELIVERY` or `CANCELLED`
- `OUT_FOR_DELIVERY` → `DELIVERED` or `FAILED`
*When an order is transitioned to `CANCELLED`, all reserved stock is restored atomically to the warehouse inventory.*

---

## 6. Payments (Razorpay)

### `POST /api/payments/create-order`
Creates a gateway order with Razorpay (or generates sandbox test order if demo keys).

### `POST /api/payments/verify`
Server-side cryptographic signature verification (`HMAC_SHA256`). Confirms payment and order idempotently.

### `POST /api/payments/webhook`
Webhook handler for asynchronous status synchronization from Razorpay servers.

---

## 7. Inventory Management

### `GET /api/inventory` *(Admin / Inventory Staff)*
Lists live warehouse inventory. Query param `lowStock=true` filters SKUs at or below threshold.

### `POST /api/inventory/adjustments` *(Admin / Inventory Staff)*
Records manual stock receipts, damage deductions, or physical inventory audit adjustments with auditable logs.

---

## 8. Admin & Reporting

### `GET /api/admin/dashboard`
Live KPI metrics: total sales, order volume, average order value, pending orders count, low-stock alerts.

### `GET /api/admin/reports`
Financial aggregates: gross revenue, total discounts, delivery fees, net revenue, inventory valuation, and top-selling SKUs.
