# 📦 Stockify - Project Documentation

## 1. Project Overview
**Stockify** is a specialized, cloud-based Kirana Shop Management and Ordering Platform. It is designed to bridge the gap between local shop owners and their customers by providing a digital interface for inventory management and seamless ordering. Built as a full-stack application, it leverages a modern "Serverless" architecture to ensure scalability and ease of deployment.

---

## 2. Technical Stack
The project follows a "Single-File Architecture" for the frontend to minimize build complexity while maintaining a robust backend integration.

| Layer | Technology |
|---|---|
| **Frontend Framework** | React 18 (via CDN) |
| **State Management** | React Context API |
| **Styling** | Vanilla CSS3 (Custom Design System) |
| **Backend / BaaS** | Supabase |
| **Database** | PostgreSQL (Relational) |
| **Authentication** | Supabase Auth (Email/Password) |
| **Compiler** | Babel (for JSX in-browser) |

---

## 3. System Architecture & Workflow

### A. Frontend Workflow
The frontend is a Single Page Application (SPA) that manages multiple "Views" based on the user's role and authentication state.

#### 1. Entry & Authentication
- **Landing Page:** Users choose between two primary roles: **Shop Owner** or **Customer**.
- **Role-Based Auth:** 
    - Owners and Customers have separate authentication flows.
    - **Sign Up/In:** Handled via Supabase Auth. Upon successful login, the user's role is persisted in `localStorage`.
    - **Session Recovery:** The app checks for an active Supabase session on load and automatically routes the user to their respective dashboard.

#### 2. Shop Owner Workflow
- **Onboarding:** If a new owner logs in, they are prompted to set up their shop (Name, Phone). This creates a record in the `shops` table linked to their unique `owner_id`.
- **Dashboard:** A central hub showing key metrics (Total Orders, Total Products, Revenue, Low Stock items).
- **Inventory Management:** Owners can add new products, update prices, and adjust stock levels.
- **Order Management:** Real-time view of incoming orders. Owners can mark orders as "Completed," which updates the status in the database.
- **QR Code Generation:** Owners can generate a unique shop link/QR for customers to access their specific storefront.

#### 3. Customer Workflow
- **Shop Entry:** Customers enter a specific "Shop ID" (or follow a link) to access a particular store's inventory.
- **Product Browsing:** A categorized view of products available in the selected shop.
- **Cart System:** Interactive cart where customers can add/remove items and adjust quantities.
- **Checkout:** Placing an order triggers a multi-step database transaction (see Backend Workflow).

---

### B. Backend Workflow & Data Flow
The backend is powered by Supabase, providing a relational PostgreSQL database and secure authentication.

#### 1. Database Schema (PostgreSQL)
- **`shops` Table:** Stores shop metadata. Primary Key: `id`, Foreign Key: `owner_id` (linked to Auth).
- **`products` Table:** Inventory items. Linked to `shops` via `shop_id`. Includes `name`, `price`, `stock`, `category`, and `unit`.
- **`orders` Table:** Records customer purchases. Linked to `shops` via `shop_id`. Stores `total`, `status`, and `customer_name`.
- **`order_items` Table:** Line items for each order. Linked to `orders` via `order_id`.

#### 2. The Order Lifecycle (Atomic Operations)
When a customer clicks "Place Order", the following sequence occurs:
1. **Order Creation:** A new row is inserted into the `orders` table.
2. **Item Mapping:** Individual items from the cart are inserted into the `order_items` table, mapped to the new `order_id`.
3. **Stock Reduction:** The application performs a parallel update to the `products` table, decrementing the `stock` count for each purchased item using `Math.max(0, stock - qty)` to prevent negative inventory.
4. **State Refresh:** The frontend fetches the updated product list and order history to ensure UI consistency.

---

## 4. Key UI/UX Features
- **Responsive Design:** Optimized for both mobile devices (for customers scanning in-store) and desktops (for owners managing inventory).
- **Notification System:** Custom-built toast notifications for feedback on actions like "Order Placed" or "Stock Updated."
- **Low Stock Alerts:** Visual indicators for owners when product stock falls below a threshold.
- **Theme:** A modern "Dark Mode" aesthetic using a custom CSS variable-based design system (`--accent`, `--bg-primary`, etc.).

---

## 5. Security & Persistence
- **State Management:** All critical data (Shop ID, Products, Orders) is fetched from the cloud, ensuring that the user sees the same data regardless of the device.
- **Logout Logic:** A secure logout process clears the Supabase session, removes the role from `localStorage`, and resets the global React state to prevent data leakage between users.
