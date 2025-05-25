# Welcome to the Bazaar E-commerce Platform!

Bazaar is a comprehensive e-commerce platform designed to enable dynamic interaction between buyers, sellers, and system administrators. Our architecture consists of a centralized backend system that serves three client applications: a mobile app for buyers, a mobile app for sellers (both built with React Native for Android and iOS), and a web-based administrative control panel (React).

The goal of the Bazaar platform is to provide a modern, efficient, and user-oriented online shopping experience.

## Key System Components

Our platform is divided into the following main components, each with its own set of features:

1. **Buyer App (Mobile)**
2. **Seller App (Mobile)**
3. **Admin Panel (Web)**
4. **Central Backend System (.NET 8)**

## Platform Features

### For Buyers

Buyers are provided with an intuitive experience for browsing and purchasing products:

* **Registration & Login:** Simple registration and login, including options to sign in using Google and Facebook accounts.
* **Store and Product Browsing:** Search and filter stores and products by category and other criteria.
* **Product Details:** View detailed information about products including images, descriptions, prices, and availability.
* **Shopping Cart:** Add products to the cart, view and update cart contents.
* **Order Process:** Easy checkout and delivery address selection.
* **Reviews & Ratings:** Leave reviews and ratings for purchased products and stores. View reviews by other customers.
* **Loyalty Program:**
    * Earn loyalty points when purchasing eligible products.
    * View current point balance.
    * Redeem points for discounts on future purchases.
* **Customer Support (Ticketing & Chat):**
    * Create support tickets for technical issues or order-related inquiries.
    * Direct chat with sellers (regarding specific orders, products, or general store questions).
    * Direct chat with administrators (for support tickets).
* **Address Management:** Add and manage multiple delivery addresses.

### For Sellers

Sellers are provided with tools to efficiently manage their online store and interact with customers:

* **Registration & Approval:** Registration process with account approval by administrators.
* **Store Management:** Create and update store information (name, description, address, category).
* **Product Management:**
    * Add, edit, and delete products.
    * Set retail and wholesale prices with quantity thresholds.
    * Upload images and detailed descriptions.
* **Inventory Management:**
    * Inventory records are automatically created when a product is added.
    * Track available stock.
    * Update product quantities.
    * Automatically mark items as "Out of Stock" when quantity reaches zero.
    * Receive notifications for out-of-stock items.
* **Order Management:**
    * View incoming orders.
    * Update order status (e.g., confirmed, ready to ship, shipped, delivered, rejected).
* **Loyalty Program:**
    * Set point rates for products to define how many loyalty points customers earn per purchase.
    * View loyalty program statistics (points issued, points redeemed, financial impact).
* **Customer Communication (Chat):**
    * Receive and respond to buyer messages regarding the store, products, or orders.
* **Support (Ticketing & Chat):**
    * Create support tickets for technical issues to administrators.
    * Direct chat with administrators regarding tickets.
* **Review Management:**
    * View reviews left by buyers for products/store.
    * Respond to buyer reviews.
    * Receive notifications for new reviews.
* **Delivery Logistics:**
    * Option to schedule deliveries via a third party (admin), or mark deliveries as self-managed (admin handles route details and optimization).
    * The most efficient delivery route is generated for the chosen orders. 

### For Administrators

The admin control panel provides centralized management of the entire platform:

* **User Management:** View all users (Buyers, Sellers, Admins), approve Seller registrations, activate/deactivate accounts.
* **Store Management:** View and (if necessary) edit store details.
* **Product Management:** View and modify product details for any store. Deactivate/delete products if needed.
* **Order Management:** View all orders in the system, track statuses, modify or cancel orders for support purposes.
* **Support (Ticketing & Chat):**
    * View all tickets created by Buyers and Sellers.
    * Assign tickets (if multiple admins are present - currently single admin).
    * Respond to tickets and update their status (Open, Resolved, Closed).
    * Direct chat with users through ticket-related conversations.
    * View Buyer-Seller chat conversations (while respecting privacy for `IsPrivate` messages).
* **Loyalty Program (LPO - Loyalty Program Operator Role):**
    * Set global constants and rules (e.g., point value, conversion rates, commissions).
    * Monitor total seller revenue for issued points.
    * Track overall profit from the loyalty program.
    * Handle financial settlements with participating sellers.
* **Seller Charging:**
    * Set standard and seller-specific platform fees.
    * Track total revenue from sellers and collected fees.
* **Analytics:** Access various analytics dashboards for monitoring sales performance, user activity, and marketing efficiency.
* **Delivery Logistics (Centralized Management):**
    * Schedule deliveries via third parties for orders forwarded by Sellers.
    * Create and optimize delivery routes (with map integration and routing tools).
    * Provide visibility into delivery routes for Buyers (and Sellers if self-delivering).
* **Advertisement Management:**
    * Link ads to specific entities (products, stores, users).
    * Add ads and track ad metrics (impressions, clicks, conversions, cost).

## Technology Stack (Overview)

* **Backend:** .NET 8, ASP.NET Core, Entity Framework Core, PostgreSQL, SignalR, JWT
* **Frontend (Mobile Apps):** React Native, Expo, TypeScript
* **Frontend (Admin Panel):** React, Vite, JavaScript
* **Infrastructure:** AWS (EC2, S3), Docker, Docker Compose
* **CI/CD:** GitHub Actions
* **Push Notifications:** Firebase Cloud Messaging (FCM) for Android, Apple Push Notification service (APNs) for iOS (via backend service).

## Organization Repositories

This GitHub organization includes the following main repositories that make up the Bazaar platform:

1. **`bazaar-backend`**: Contains the code for the central backend system.
2. **`bazaar-mobile-react-native`**: Monorepo containing both the Buyer and Seller mobile app code.
3. **`bazaar-admin-web`**: Contains the code for the web-based admin control panel.

Each repository contains its own detailed `README.md` file with setup and usage instructions.

## System Documentation

[System Documentation for the entire System](/../Bazaar%20-%20System%20Documentation.md)

![Component Diagram](/Documentation/Component.png)