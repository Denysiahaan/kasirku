# Requirements Document

## Introduction

KasirKu adalah platform SaaS kasir digital untuk UMKM Indonesia yang menyediakan sistem Point of Sale (POS) lengkap dengan manajemen menu, antrian otomatis, dan pelaporan keuangan. Platform ini menggunakan arsitektur multi-tenant dimana setiap UMKM yang mendaftar memiliki data terisolasi.

Platform dibangun menggunakan Next.js 14 App Router dengan TypeScript, Tailwind CSS, shadcn/ui untuk UI components, dan Supabase untuk authentication dan database.

## Glossary

- **KasirKu_System**: Platform SaaS kasir digital untuk UMKM
- **UMKM**: Usaha Mikro Kecil dan Menengah (small business)
- **Tenant**: Satu UMKM yang terdaftar dalam sistem
- **Order_System**: Subsistem yang mengelola pemesanan
- **Menu_System**: Subsistem yang mengelola daftar menu dan harga
- **Report_System**: Subsistem yang mengelola laporan keuangan
- **Auth_System**: Subsistem yang mengelola autentikasi dan otorisasi
- **Queue_Number**: Nomor antrian otomatis yang diberikan kepada setiap pesanan
- **Landing_Page**: Halaman publik yang dapat diakses tanpa autentikasi
- **Dashboard**: Halaman utama setelah pengguna berhasil login
- **Valid_Email**: Alamat email dengan format yang sesuai standar RFC 5322
- **Active_Session**: Sesi pengguna yang telah terautentikasi dan belum kadaluarsa
- **Tenant_Data**: Seluruh data yang dimiliki oleh satu tenant termasuk menu, pesanan, dan laporan
- **Daily_Report**: Laporan pendapatan untuk satu hari
- **Weekly_Report**: Laporan pendapatan untuk satu minggu
- **Monthly_Report**: Laporan pendapatan untuk satu bulan

## Requirements

### Requirement 1: Public Landing Page

**User Story:** As a potential user, I want to view a public landing page, so that I can learn about KasirKu features before registering.

#### Acceptance Criteria

1. WHEN a user sends an HTTP GET request to the root path "/", THE KasirKu_System SHALL return an HTTP 200 response with the Landing_Page content
2. THE Landing_Page SHALL include text describing at least 3 distinct KasirKu features
3. THE Landing_Page SHALL include a clickable link element with href attribute set to "/auth"
4. WHEN a user without Active_Session requests the root path "/", THE KasirKu_System SHALL return the Landing_Page content
5. WHEN a user with Active_Session requests the root path "/", THE KasirKu_System SHALL return the Landing_Page content
6. IF the KasirKu_System fails to load the Landing_Page, THEN THE KasirKu_System SHALL return an HTTP 500 response with an error message

### Requirement 2: User Authentication

**User Story:** As an UMKM owner, I want to register and login with email, so that I can access the platform securely.

#### Acceptance Criteria

1. THE Auth_System SHALL provide an authentication page at "/auth"
2. WHEN a user submits registration with Valid_Email and password containing 8 to 128 characters, THE Auth_System SHALL create a new account via Supabase
3. WHEN a user submits login credentials with Valid_Email and password, THE Auth_System SHALL authenticate via Supabase
4. WHEN authentication succeeds, THE Auth_System SHALL create an Active_Session with expiration of 7 days from creation time
5. WHEN authentication succeeds, THE Auth_System SHALL redirect the user to "/dashboard"
6. IF registration fails due to duplicate Valid_Email, THEN THE Auth_System SHALL display an error message indicating that the email is already registered
7. IF authentication fails due to incorrect credentials, THEN THE Auth_System SHALL display an error message indicating invalid email or password
8. IF a user submits registration or login with empty email field or empty password field, THEN THE Auth_System SHALL display an error message indicating that all fields are required
9. IF a user submits registration or login with email that does not match Valid_Email format, THEN THE Auth_System SHALL display an error message indicating invalid email format
10. WHEN a user with Active_Session accesses "/auth", THE Auth_System SHALL redirect to "/dashboard"

### Requirement 3: Multi-Tenant Data Isolation

**User Story:** As an UMKM owner, I want my business data to be separate from other businesses, so that my data remains private and secure.

#### Acceptance Criteria

1. WHEN a user registers, THE KasirKu_System SHALL create a new Tenant for that user
2. THE KasirKu_System SHALL associate each user with exactly one Tenant
3. WHEN a user with Active_Session queries Tenant_Data, THE KasirKu_System SHALL return only data records where the tenant_id matches the authenticated user's Tenant identifier
4. WHEN a user attempts to modify or delete Tenant_Data not belonging to their Tenant, THE KasirKu_System SHALL reject the operation and return an error indicating insufficient permissions
5. FOR ALL database queries involving Tenant_Data, THE KasirKu_System SHALL filter by the authenticated user's Tenant identifier
6. IF the KasirKu_System fails to create a new Tenant during registration, THEN THE KasirKu_System SHALL reject the registration and display an error message indicating the account could not be created

### Requirement 4: Dashboard Access

**User Story:** As a logged-in user, I want to access a dashboard, so that I can see an overview of my business.

#### Acceptance Criteria

1. THE KasirKu_System SHALL provide a Dashboard page at "/dashboard"
2. WHEN a user with Active_Session accesses "/dashboard", THE KasirKu_System SHALL display the Dashboard within 3 seconds of request
3. IF a user without Active_Session attempts to access "/dashboard", THEN THE Auth_System SHALL redirect to "/auth"
4. THE Dashboard SHALL display interactive navigation elements linking to "/dashboard/order", "/dashboard/menu", and "/dashboard/laporan"
5. WHEN a user's Active_Session expires while on the Dashboard, THE Auth_System SHALL redirect to "/auth" on the next user action
6. WHEN a user authenticates at "/auth" after being redirected from "/dashboard", THE Auth_System SHALL redirect back to "/dashboard" after successful authentication

### Requirement 5: Order Management with Automatic Queue Numbers

**User Story:** As a cashier, I want to create orders with automatic queue numbers, so that I can manage customer orders efficiently.

#### Acceptance Criteria

1. THE Order_System SHALL provide an order page at "/dashboard/order"
2. WHEN a user with Active_Session accesses "/dashboard/order", THE Order_System SHALL display the order input form
3. WHEN a user creates a new order containing at least one menu item with quantity and price, THE Order_System SHALL generate a unique Queue_Number automatically
4. THE Order_System SHALL increment Queue_Number sequentially for each new order within the same Tenant, resetting to 1 at 00:00:00 UTC+7 each day
5. WHEN a user submits an order, THE Order_System SHALL save the order with the Queue_Number, menu items, quantities, and total amount to the database
6. THE Order_System SHALL associate each order with the authenticated user's Tenant
7. WHEN an order is saved, THE Order_System SHALL display the Queue_Number in numeric format to the user
8. IF the Order_System fails to save an order to the database, THEN THE Order_System SHALL display an error message indicating the order could not be saved
9. IF a user without Active_Session attempts to access "/dashboard/order", THEN THE Auth_System SHALL redirect to "/auth"

### Requirement 6: Menu Management

**User Story:** As a business owner, I want to manage my menu items and prices, so that I can keep my product offerings up to date.

#### Acceptance Criteria

1. THE Menu_System SHALL provide a menu management page at "/dashboard/menu"
2. WHEN a user with Active_Session accesses "/dashboard/menu", THE Menu_System SHALL display all menu items for that user's Tenant
3. WHEN a user creates a new menu item with name containing 1 to 200 characters and price between 0.00 and 999999999.99, THE Menu_System SHALL save the item to the database
4. WHEN a user updates a menu item identified by its unique identifier, THE Menu_System SHALL update the name and price in the database
5. WHEN a user deletes a menu item identified by its unique identifier, THE Menu_System SHALL remove the item from the database
6. THE Menu_System SHALL associate each menu item with the authenticated user's Tenant
7. WHEN a user attempts to update or delete a menu item belonging to a different Tenant, THE Menu_System SHALL reject the operation and display an error message indicating insufficient permissions
8. IF a user submits a menu item with name exceeding 200 characters or price outside the range 0.00 to 999999999.99, THEN THE Menu_System SHALL reject the operation and display an error message indicating validation failure
9. IF the Menu_System fails to save, update, or delete a menu item due to database error, THEN THE Menu_System SHALL display an error message indicating the operation could not be completed
10. IF a user without Active_Session attempts to access "/dashboard/menu", THEN THE Auth_System SHALL redirect to "/auth"

### Requirement 7: Financial Reporting

**User Story:** As a business owner, I want to view daily, weekly, and monthly revenue reports, so that I can track my business performance.

#### Acceptance Criteria

1. THE Report_System SHALL provide a report page at "/dashboard/laporan"
2. WHEN a user with Active_Session accesses "/dashboard/laporan", THE Report_System SHALL display reporting options for daily, weekly, and monthly views
3. WHEN a user selects daily view with a specific date, THE Report_System SHALL calculate and display Daily_Report showing revenue from orders with created_at timestamp between 00:00:00 and 23:59:59 UTC+7 on the selected date for the authenticated user's Tenant
4. WHEN a user selects weekly view with a specific week start date, THE Report_System SHALL calculate and display Weekly_Report showing revenue from orders with created_at timestamp between the week start date at 00:00:00 UTC+7 and 6 days later at 23:59:59 UTC+7 for the authenticated user's Tenant
5. WHEN a user selects monthly view with a specific month, THE Report_System SHALL calculate and display Monthly_Report showing revenue from orders with created_at timestamp between the first day of the month at 00:00:00 UTC+7 and the last day of the month at 23:59:59 UTC+7 for the authenticated user's Tenant
6. THE Report_System SHALL calculate revenue by summing the total_amount field of all orders within the selected time period
7. THE Report_System SHALL display revenue amounts in Indonesian Rupiah format with "Rp" prefix, period as thousands separator, and 0 decimal places
8. THE Report_System SHALL only include orders belonging to the authenticated user's Tenant
9. WHEN the Report_System calculates revenue for a time period with no orders, THE Report_System SHALL display "Rp 0"
10. IF a user without Active_Session attempts to access "/dashboard/laporan", THEN THE Auth_System SHALL redirect to "/auth"

### Requirement 8: Protected Routes

**User Story:** As a system administrator, I want all dashboard routes to be protected, so that only authenticated users can access them.

#### Acceptance Criteria

1. WHEN a user requests any route matching the pattern "/dashboard*", THE Auth_System SHALL validate the presence of Active_Session before rendering the route
2. WHEN a user without Active_Session attempts to access any route under "/dashboard", THE Auth_System SHALL redirect to "/auth" with the attempted URL preserved
3. WHEN a user with Active_Session accesses any route under "/dashboard", THE KasirKu_System SHALL allow access
4. WHEN a user's Active_Session exceeds 7 days from creation time, THE Auth_System SHALL treat the session as expired and redirect to "/auth" on the next protected route access

### Requirement 9: Responsive User Interface

**User Story:** As a user, I want the interface to work on mobile and desktop devices, so that I can use the system on any device.

#### Acceptance Criteria

1. THE KasirKu_System SHALL render all pages using responsive design with Tailwind CSS
2. THE KasirKu_System SHALL display all interactive elements and text content within the viewport bounds without requiring horizontal scrolling on screen widths from 320px to 1920px
3. THE KasirKu_System SHALL use shadcn/ui components for consistent UI elements
4. WHEN a user accesses the system on screen width below 768px, THE KasirKu_System SHALL display navigation in collapsed menu format and stack content in single column layout
5. WHEN a user accesses the system on screen width 768px or above, THE KasirKu_System SHALL display navigation in expanded horizontal format and arrange content in multi-column layout where applicable
6. WHEN a user accesses the system on a touch-enabled device, THE KasirKu_System SHALL render all interactive elements with minimum touch target size of 44x44 pixels

### Requirement 10: Database Schema and Relationships

**User Story:** As a system administrator, I want a well-structured database schema, so that data is organized and relationships are maintained.

#### Acceptance Criteria

1. THE KasirKu_System SHALL implement a Supabase database schema with tables: tenants (with id, name, created_at), menu_items (with id, tenant_id, name, price, created_at, updated_at), orders (with id, tenant_id, queue_number, total_amount, status, created_at), and user_tenants (with user_id, tenant_id, role)
2. WHEN an operation attempts to insert or update a record with tenant_id that does not exist in tenants table, THE KasirKu_System SHALL reject the operation with foreign key constraint violation
3. THE KasirKu_System SHALL enforce foreign key relationships between user_tenants.user_id and auth.users.id, user_tenants.tenant_id and tenants.id, menu_items.tenant_id and tenants.id, and orders.tenant_id and tenants.id
4. WHEN a tenant record is deleted, THE KasirKu_System SHALL cascade delete all associated records in menu_items, orders, and user_tenants tables
5. WHEN a user with Active_Session queries menu_items or orders tables, THE KasirKu_System SHALL apply Row Level Security policies that filter results to only include records where tenant_id matches the authenticated user's Tenant identifier
6. THE KasirKu_System SHALL create B-tree indexes on the tenant_id column in menu_items, orders, and user_tenants tables
