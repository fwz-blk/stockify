# 🧠 Stockify: Technical Deep Dive & Presentation Guide

This document provides an exhaustive explanation of the **Stockify** architecture, designed to help you present the project. It is divided into two distinct sections: the **Frontend** (built by your friend) and the **Backend** (the Supabase/Vibe-coded logic).

---

## 🎨 Part 1: Frontend Architecture (UI & Client-Side)

The frontend is built using a "Lean React" approach. Instead of a complex build system like Vite or Webpack, it uses a single-file CDN architecture for maximum portability and simplicity.

### 1. The "Single File" Philosophy
- **Component Structure:** The entire UI is written inside `<script type="text/babel">`. This means the browser compiles the React code on the fly.
- **Styling:** Uses a robust custom CSS design system located in the `<style>` block. It uses CSS Variables (e.g., `--accent: #6c63ff`) for a consistent dark-mode aesthetic.
- **Responsiveness:** Uses CSS Media Queries to ensure the shop works on a shop owner's laptop and a customer's smartphone.

### 2. State Management (The App's "Brain")
The frontend uses the **React Context API** (`AppCtx`). 
- **Centralized Logic:** Instead of spreading code everywhere, all the "intelligence" (like adding to cart, logging in, or placing orders) lives in the `AppProvider` component.
- **Dynamic Views:** The `view` state variable controls what the user sees (e.g., `landing`, `ownerDash`, `customerShop`). This creates a Single Page Application (SPA) experience without needing a router library.

---

## ⚙️ Part 2: Backend Architecture (Supabase & Logic)

This is the "engine" of the application. Even though it's "Serverless," there is a lot of relational logic happening behind the scenes.

### 1. What is Supabase?
Supabase is used as the **Backend-as-a-Service (BaaS)**. It provides three critical things:
1. **PostgreSQL Database:** A professional-grade relational database to store shops, products, and orders.
2. **Authentication:** Handles secure user sign-up and login sessions.
3. **Auto-Generated API:** The `supabase-js` library allows the frontend to "talk" to the database using simple JavaScript commands.

### 2. The Relational Data Model (Schema)
The backend is organized into four tables. Their relationships are what make the app work:
- **`shops`:** The anchor table. Every product and order is "owned" by a `shop_id`.
- **`products`:** Contains inventory. Critical fields: `stock` (numeric) and `price`.
- **`orders`:** Stores the "Header" of a sale (Who bought it? How much total?).
- **`order_items`:** The "Details" of a sale. It links back to an `order_id`. This is a classic "One-to-Many" relationship (One order has many items).

### 3. Deep Dive: The Order Workflow (The most important logic)
When a customer clicks "Place Order," the code executes a complex sequence to ensure data integrity:

1. **Step 1: Order Insertion**
   ```javascript
   const { data: orderInsert } = await supabase.from("orders").insert([orderData]);
   ```
   This creates the main receipt in the database.

2. **Step 2: Relational Mapping**
   The app takes every item in the `cart` and attaches the `order_id` from Step 1 to them, then saves them to `order_items`. This ensures we know exactly what was bought in which order.

3. **Step 3: Atomic Stock Update**
   To prevent "Ghost Stock" (selling things you don't have), the app runs:
   ```javascript
   supabase.from("products").update({ stock: Math.max(0, item.stock - item.qty) });
   ```
   This subtracts the purchased quantity from the database immediately.

### 4. Authentication & Security
- **Session Persistence:** When you refresh the page, the backend uses `supabase.auth.getSession()`. If a user was logged in, it retrieves their data instantly so they don't have to log in again.
- **Role Separation:** The code checks `localStorage.getItem("userRole")` to decide if the user should see the "Owner Dashboard" or the "Customer Shop."

### 5. Why this Backend is Professional
- **Asynchronous Operations:** Uses `async/await` and `Promise.all` to handle database calls without freezing the UI.
- **Error Handling:** Every database call is wrapped in checks to notify the user if something goes wrong (e.g., "Could not save order").
- **Scalability:** Because it uses PostgreSQL, this system could theoretically handle thousands of shops and products without changing the core code.
