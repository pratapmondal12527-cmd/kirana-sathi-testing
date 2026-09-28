# Kirana Sathi (কিরানা সাথী) 🛒
### *“সব কিছু এক ছাদের নিচে” (Everything Under One Roof)*

Kirana Sathi is a production-oriented, full-stack Indian grocery marketplace inspired by Amazon, Flipkart, and modern quick-commerce platforms (Blinkit/Zepto), tailored for local neighborhood fulfillment across Kolkata, West Bengal.

---

## 🌟 Key Features

### 🛍️ Customer Experience
- **Grocery Aisles & Categories**: Fresh Vegetables & Fruits, Staples (Atta, Rice, Dal), Edible Oils & Ghee, Dairy & Amul, Spices, Salt & Sugar, Snacks & Tea, Cleaning & Household, and **Kirana Sathi Select** (proprietary own-brand).
- **Kolkata Pincode Serviceability Check**: Real-time delivery checker for Salt Lake (700064, 700091), New Town (700156, 700157, 700160), Gariahat/Rashbehari (700029), Ballygunge (700019), and Kolkata metropolitan zones.
- **Pack Size & Weight Variant Selector**: Dynamic pricing for 500g, 1kg, 2kg, 5kg, 1L, and 5L packs directly on product cards and detail pages.
- **Smart Cart with Free Delivery Meter**: Real-time progress bar towards the ₹499 free delivery threshold.
- **Coupons & Discount Engine**: Support for percentage coupons (with max caps) and flat vouchers (e.g. `WELCOME100`, `FREEDEL`, `KIRANA20`).
- **Flexible Payments**: Cash on Delivery (COD) and Razorpay UPI/Cards/NetBanking integration with test sandbox mode.
- **Order Tracking & Live Status**: Step-by-step order journey: *Placed → Confirmed → Packed → Out for Delivery → Delivered*.
- **Customer Account**: Address book management, past order history with instant reordering, and wishlist.

### 🛡️ Administration Panel (`/admin`)
- **Dashboard & KPIs**: Total gross and net revenue, order volume, average order value (AOV), and real-time low-stock alerts.
- **Product Management**: Create, edit, and archive grocery SKUs, variants, prices, and images.
- **Inventory Management**: Live warehouse stock tracking with auditable `InventoryMovement` logs (Purchase receipts, count adjustments, damage write-offs).
- **Order Dispatch Workflow**: Pack bin allocation, assign orders to delivery riders, and transition order states.
- **Reports & Valuation**: Product-wise sales aggregation, financial audit, and 1-click CSV export.

### 🚚 Delivery Staff Portal (`/delivery`)
- **Rider-Assigned View**: Delivery agents see only orders assigned to them.
- **Customer Contact & Navigation**: Direct 1-tap phone dialer, full address snapshot with landmarks.
- **Fulfillment Status**: Update orders to *Out for Delivery*, *Delivered* (automatically recording payment for COD), or record failure reasons.

---

## 🛠️ Technology Stack

- **Framework**: Next.js 16 (App Router, Turbopack, Server & Client Components)
- **Frontend**: React 19, TypeScript, Tailwind CSS v4, Lucide Icons
- **Database & ORM**: PostgreSQL / SQLite with Prisma ORM 6.4
- **Authentication**: Secure JWT sessions stored in HTTP-only cookies with bcrypt password hashing and Role-Based Access Control (RBAC)
- **Payments**: Razorpay Node integration with automatic test mode fallback and signature verification
- **Testing**: Vitest with unit and integration test suites

---

## 🚀 Getting Started

### 1. Installation
Clone the repository and install dependencies:
```bash
npm install
```

### 2. Environment Variables
Copy `.env.example` to `.env`:
```bash
cp .env.example .env
```
Default local variables are configured for instant out-of-the-box execution with SQLite.

### 3. Database Initialization & Seed
Generate Prisma client, synchronize schema, and seed realistic demo grocery products:
```bash
npx prisma db push
npm run seed
```

### 4. Run Development Server
```bash
npm run dev
```
Open [http://localhost:3000](http://localhost:3000) in your browser.

---

## 👥 Demo Accounts (1-Click Login on `/login`)

| Role | Email | Password | Access Area |
| :--- | :--- | :--- | :--- |
| **Customer** | `customer@kiranasathi.in` | `Password@123` | Store, Account, Cart, Checkout |
| **Admin** | `admin@kiranasathi.in` | `Admin@12345` | `/admin` Panel & Full Control |
| **Inventory Staff**| `inventory@kiranasathi.in` | `Staff@12345` | `/admin/inventory` & Products |
| **Delivery Rider** | `delivery@kiranasathi.in` | `Delivery@12345` | `/delivery` Staff Portal |

---

## 🧪 Running Automated Tests

Run the test suite covering cart calculations, free delivery thresholds, coupon discounts, JWT auth, and payment signature verification:
```bash
npm test
```

---

## 🗄️ Database Architecture & Production PostgreSQL

For local development, SQLite (`dev.db`) is used for zero-friction setup.

For production deployment (e.g. **Neon**, **Supabase**, **Railway**, **AWS RDS**, or **Vercel Postgres**):
1. Use `prisma/schema.postgresql.prisma` (or change `provider = "postgresql"` in `prisma/schema.prisma`).
2. Provide your PostgreSQL connection string in `.env`:
   ```env
   DATABASE_URL="postgresql://user:password@ep-sample-pooler.us-east-1.aws.neon.tech/kirana_sathi?sslmode=require"
   ```
3. Run `npx prisma db push` or `npx prisma migrate deploy`.
4. Run `npm run seed` to populate demo data.

---

## 💳 Payment Gateway (Razorpay)

- In development mode (or if demo keys are present in `.env`), the application operates in safe **Test Sandbox Mode**. A realistic payment simulator allows testing successful UPI/Card authorizations or failed payments without real financial transactions.
- In production, set your live Razorpay credentials:
  ```env
  RAZORPAY_KEY_ID="rzp_live_..."
  RAZORPAY_KEY_SECRET="your_live_secret"
  NEXT_PUBLIC_RAZORPAY_KEY_ID="rzp_live_..."
  RAZORPAY_WEBHOOK_SECRET="your_webhook_secret"
  ```

---

## 📄 License
© 2026 Kirana Sathi Retail Private Limited. All rights reserved.
