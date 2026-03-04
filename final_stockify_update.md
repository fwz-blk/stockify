

# 📦 Stockify

### Smart Kirana Shop Management & Ordering Platform

Stockify is a small-scale full-stack cloud-based shop management system built for local kirana stores.

It enables:

- 🏪 Shop owners to manage inventory and process orders
    
- 🛍 Customers to browse products and place orders
    
- ☁️ Persistent cloud database using Supabase (PostgreSQL)
    
- 🔐 Secure authentication with session management
    
- 📱 Future-ready mobile deployment (Capacitor compatible)
    

This project evolved from a UI prototype into a fully functional relational backend system.

---

# 🚀 Current System Capabilities

## 🏪 Owner Features

- Secure registration & login (Supabase Auth)
    
- Automatic session restoration on refresh
    
- One-time shop setup (persistent in database)
    
- Add new products (linked via `shop_id`)
    
- Update stock and price (database-persistent)
    
- View orders filtered by shop
    
- Mark orders as completed
    
- Low-stock alerts
    
- Dashboard statistics
    
- Secure logout (state fully cleared)
    

---

## 🛍 Customer Features

- Secure registration & login
    
- Enter shop ID manually
    
- Browse shop inventory (filtered by `shop_id`)
    
- Add items to cart
    
- Place orders
    
- Stock auto-updates after purchase
    
- Continue shopping
    
- Logout and re-enter any shop ID
    

---

## 🔐 Authentication

Implemented using Supabase Auth:

- `signUp()`
    
- `signInWithPassword()`
    
- `getSession()` (auto-login)
    
- `signOut()` (secure logout)
    

Session is restored automatically on refresh using controlled lifecycle logic.

Role (`owner` / `customer`) stored in `localStorage`.

---

# 🧱 Database Architecture

Stockify uses a relational PostgreSQL schema:

### Tables

**shops**

- id (uuid, primary key)
    
- owner_id (uuid → Supabase user.id)
    
- name (text)
    
- phone (text)
    
- created_at (timestamp)
    

**products**

- id (uuid)
    
- shop_id (foreign key → shops.id)
    
- name
    
- category
    
- price
    
- stock
    
- unit
    

**orders**

- id (uuid)
    
- shop_id (foreign key)
    
- customer_name
    
- total
    
- status
    
- created_at
    

**order_items**

- id (uuid)
    
- order_id (foreign key → orders.id)
    
- product_name
    
- qty
    
- price
    

Supabase manages:

- auth.users (authentication layer)
    

---

# 🔄 Order Workflow

When a customer places an order:

1. Insert into `orders`
    
2. Insert items into `order_items`
    
3. Update product stock
    
4. Reload products
    
5. Update UI state
    
6. Owner sees order in dashboard
    

Stock updates are safeguarded using:

Math.max(0, stock - qty)

Parallel stock updates handled via `Promise.all`.

All data persists across refresh and across devices.

---

# 🧠 Architecture Decisions

This project intentionally prioritizes:

- Stability over complexity
    
- Relational integrity
    
- Clean async state management
    
- Deterministic logout behavior
    
- Simple, production-ready logic
    

Not included intentionally:

- No real-time subscriptions
    
- No enterprise RLS complexity
    
- No analytics module
    
- No transaction wrapper
    
- No lastShop persistence
    
- No routing libraries
    
- No Firebase
    

This keeps the system stable and focused.

---

# 🛠 Technical Stack

|Technology|Purpose|
|---|---|
|React (CDN)|UI & state management|
|Supabase|Backend as a Service|
|PostgreSQL|Relational database|
|Babel|JSX compilation|
|HTML/CSS|Layout & styling|

Single-file React architecture (no build step required).

---

# 👥 Team Contributions

## 🎨 Frontend Architecture & UI

**Omer Siddiqui**

- Designed and implemented complete UI system
    
- Built component structure
    
- Designed layout & styling
    
- Implemented visual dashboard
    
- Designed landing, auth, product, and order interfaces
    
- Built responsive layout
    
- Created full React CDN architecture
    
- Developed base state structure
    

Omer built the visual and structural foundation of the application.

---

## 🧠 Backend Engineering & System Integration

**Fawaz Mohammed**

- Replaced demo logic with real Supabase backend
    
- Integrated full Supabase authentication system
    
- Implemented session restoration logic
    
- Built persistent shop onboarding flow
    
- Designed relational database schema
    
- Connected products to `shop_id`
    
- Implemented full order workflow (orders + order_items)
    
- Implemented stock update logic
    
- Ensured multi-role routing (owner/customer)
    
- Fixed schema mismatches between UI and database
    
- Stabilized async state lifecycle
    
- Implemented secure logout with full state reset
    
- Removed Firebase remnants
    
- Eliminated DEMO_SHOP fallback logic
    
- Fixed race conditions and refresh flickering
    
- Validated end-to-end owner ↔ customer data flow
    

Fawaz transformed the UI prototype into a fully functional full-stack system.

---

# ✅ Current System Status

Stockify is now:

- A relational multi-table backend system
    
- Authenticated and session-stable
    
- Role-based (Owner / Customer)
    
- Refresh-safe
    
- Cloud-persistent
    
- Production-ready at small scale
    
- Free from runtime crashes or blank-screen errors
    

This is no longer a mock UI project.  
It is a working full-stack cloud application.