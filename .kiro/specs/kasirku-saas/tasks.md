# Implementation Plan: KasirKu SaaS POS System

## Overview

This implementation plan breaks down the KasirKu multi-tenant SaaS POS system into discrete coding tasks. The system will be built using Next.js 14 App Router with TypeScript, Tailwind CSS, shadcn/ui, and Supabase for authentication and database. Each task builds incrementally on previous work, with property-based tests validating universal correctness properties from the design document.

## Tasks

- [x] 1. Initialize Next.js project and install dependencies
  - Create Next.js 14 project with App Router and TypeScript
  - Install dependencies: @supabase/ssr, @supabase/supabase-js, tailwindcss, luxon, shadcn/ui CLI
  - Configure Tailwind CSS and shadcn/ui
  - Set up environment variables for Supabase URL and anon key
  - _Requirements: 1.1, 9.1, 9.3_

- [x] 2. Set up Supabase database schema and Row Level Security
  - [x] 2.1 Create database schema
    - Write and execute SQL migration for tenants, user_tenants, menu_items, and orders tables
    - Add UUID extension and indexes for performance
    - Add updated_at trigger for menu_items
    - _Requirements: 10.1, 10.6_
  - [x] 2.2 Implement Row Level Security policies
    - Create helper function get_user_tenant_id()
    - Create RLS policies for menu_items (SELECT, INSERT, UPDATE, DELETE)
    - Create RLS policies for orders (SELECT, INSERT)
    - Create RLS policies for user_tenants and tenants (SELECT)
    - Enable RLS on all tables
    - _Requirements: 3.3, 3.5, 10.5_

- [x] 3. Create Supabase client utilities and middleware
  - [x] 3.1 Implement Supabase client factories
    - Create lib/supabase/server.ts for Server Components and Server Actions
    - Create lib/supabase/client.ts for Client Components
    - _Requirements: 2.1, 2.2, 2.3_
  - [x] 3.2 Implement tenant context utilities
    - Create lib/tenant.ts with getTenantId() function
    - Create createTenantForUser() function for user registration
    - _Requirements: 3.1, 3.2, 3.6_
  - [x] 3.3 Implement date/time utilities
    - Create lib/datetime.ts with timezone-aware functions
    - Implement getStartOfDay(), getEndOfDay(), getStartOfWeek(), getEndOfWeek(), getMonthBounds()
    - Use Asia/Jakarta timezone (UTC+7)
    - _Requirements: 5.4, 7.3, 7.4, 7.5_
  - [x] 3.4 Implement authentication middleware
    - Create middleware.ts with session validation
    - Protect /dashboard/\* routes
    - Redirect authenticated users away from /auth
    - Set up cookie handling with Supabase SSR
    - _Requirements: 2.10, 4.3, 4.5, 8.1, 8.2, 8.3, 8.4_
  - [x] 3.5 Write property test for Protected Route Access Control
    - **Property 11: Protected Route Access Control**
    - **Validates: Requirements 4.3, 5.9, 6.10, 7.10, 8.1, 8.2, 8.3**
    - Test that /dashboard\* routes redirect to /auth without active session
    - Test that /dashboard\* routes allow access with valid session

- [x] 4. Implement TypeScript type definitions
  - Create types/database.ts with Supabase-generated database types
  - Create types/models.ts with application types (MenuItem, OrderItem, Order, RevenueReport)
  - _Requirements: 6.3, 6.4, 5.5, 7.3_

- [x] 5. Build authentication system
  - [x] 5.1 Create authentication Server Actions
    - Create app/auth/actions.ts with signUpAction() and signInAction()
    - Implement email and password validation
    - Handle Supabase authentication errors with user-friendly messages
    - Create tenant on successful registration
    - Set session cookie with 7-day expiration
    - _Requirements: 2.2, 2.3, 2.4, 2.5, 2.6, 2.7, 2.8, 2.9, 3.1_
  - [x] 5.2 Write property test for User Registration Creates Tenant
    - **Property 1: User Registration Creates Tenant**
    - **Validates: Requirements 2.2, 3.1, 3.2**
    - Test that valid registration creates exactly one tenant
    - Test tenant association with user
  - [x] 5.3 Write property test for Authentication Session Properties
    - **Property 2: Authentication Session Properties**
    - **Validates: Requirements 2.3, 2.4, 2.5**
    - Test session expiration is exactly 7 days
    - Test successful authentication redirects to /dashboard
  - [x] 5.4 Write property test for Email Validation
    - **Property 3: Email Validation Rejects Invalid Formats**
    - **Validates: Requirements 2.9**
    - Test that non-RFC 5322 emails are rejected
    - Test error messages for invalid email formats
  - [x] 5.5 Create authentication page UI
    - Create app/auth/page.tsx as Client Component
    - Implement login/register mode toggle
    - Create form with email and password inputs
    - Add HTML5 validation attributes
    - Display error messages from Server Actions
    - Handle loading states during submission
    - _Requirements: 2.1, 2.8, 2.9_

- [x] 6. Build landing page
  - Create app/page.tsx with public marketing content
  - Describe at least 3 KasirKu features
  - Add "Get Started" link to /auth
  - Implement mobile-first responsive layout with Tailwind
  - _Requirements: 1.1, 1.2, 1.3, 1.4, 1.5, 9.2, 9.4, 9.5_

- [x] 7. Create dashboard layout and navigation
  - Create app/dashboard/layout.tsx with navigation
  - Add navigation links to /dashboard/order, /dashboard/menu, /dashboard/laporan
  - Implement responsive navigation (collapsed menu below 768px, expanded above)
  - Create app/dashboard/page.tsx with overview content
  - _Requirements: 4.1, 4.2, 4.4, 9.4, 9.5_

- [x] 8. Checkpoint - Verify authentication and navigation
  - Ensure all tests pass, ask the user if questions arise.

- [x] 9. Implement menu management system
  - [x] 9.1 Create menu management Server Actions
    - Create app/dashboard/menu/actions.ts
    - Implement createMenuItemAction() with validation (name 1-200 chars, price 0.00-999999999.99)
    - Implement updateMenuItemAction() with validation
    - Implement deleteMenuItemAction()
    - Associate all operations with authenticated user's tenant_id
    - Add revalidatePath() calls after mutations
    - _Requirements: 6.3, 6.4, 6.5, 6.6, 6.8, 6.9_
  - [x] 9.2 Write property test for Menu Item CRUD with Tenant Association
    - **Property 7: Menu Item CRUD Operations with Tenant Association**
    - **Validates: Requirements 6.3, 6.4, 6.5, 6.6**
    - Test create, update, delete operations associate with correct tenant
    - Test operations persist successfully
  - [x] 9.3 Write property test for Menu Item Validation Boundaries
    - **Property 8: Menu Item Validation Boundaries**
    - **Validates: Requirements 6.8**
    - Test name > 200 characters is rejected
    - Test price < 0.00 or > 999999999.99 is rejected
    - Test validation error messages are displayed
  - [x] 9.4 Create menu management page UI
    - Create app/dashboard/menu/page.tsx as hybrid component
    - Fetch menu items in Server Component (filtered by tenant_id)
    - Render menu items in table or card grid
    - Create Client Component for create/edit form
    - Add delete confirmation dialog
    - Display validation errors from Server Actions
    - Implement responsive layout
    - _Requirements: 6.1, 6.2, 6.7, 9.2, 9.6_
  - [x] 9.5 Write property test for Tenant Data Isolation (Menu)
    - **Property 4: Tenant Data Isolation (Menu component)**
    - **Validates: Requirements 3.3, 3.4, 6.2, 6.7**
    - Test queries return only tenant's menu items
    - Test cross-tenant modification attempts are rejected

- [x] 10. Implement order management system
  - [x] 10.1 Create queue number generation utility
    - Create lib/queue.ts with getNextQueueNumber() function
    - Query last order for tenant on current day (UTC+7)
    - Increment by 1, or return 1 if no orders today
    - Handle race conditions with database constraints
    - _Requirements: 5.3, 5.4_
  - [x] 10.2 Create order Server Action
    - Create app/dashboard/order/actions.ts with createOrderAction()
    - Generate queue number using getNextQueueNumber()
    - Calculate total_amount from order items
    - Associate order with authenticated user's tenant_id
    - Insert order into database with status 'completed'
    - Return queue number on success
    - _Requirements: 5.5, 5.6, 5.7, 5.8_
  - [x] 10.3 Write property test for Queue Number Sequential Increment and Daily Reset
    - **Property 5: Queue Number Sequential Increment and Daily Reset**
    - **Validates: Requirements 5.3, 5.4**
    - Test queue numbers increment sequentially within same day
    - Test queue numbers reset to 1 at 00:00:00 UTC+7
    - Test queue numbers are unique within tenant and day
  - [x] 10.4 Write property test for Order Persistence with Tenant Association
    - **Property 6: Order Persistence with Tenant Association**
    - **Validates: Requirements 5.5, 5.6**
    - Test orders persist with correct queue number
    - Test orders persist with correct total_amount
    - Test orders associate with correct tenant_id
  - [x] 10.5 Create order management page UI
    - Create app/dashboard/order/page.tsx as hybrid component
    - Fetch menu items for current tenant in Server Component
    - Create Client Component for order form
    - Implement menu item selector (dropdown or search)
    - Add quantity inputs with min value 1
    - Display line items with subtotals
    - Show total amount
    - Display queue number after successful submission
    - Implement responsive layout
    - _Requirements: 5.1, 5.2, 5.7, 9.2, 9.6_
  - [x] 10.6 Write property test for Tenant Data Isolation (Orders)
    - **Property 4: Tenant Data Isolation (Orders component)**
    - **Validates: Requirements 3.3, 5.6, 7.8**
    - Test queries return only tenant's orders
    - Test cross-tenant order creation is rejected

- [x] 11. Checkpoint - Verify menu and order functionality
  - Ensure all tests pass, ask the user if questions arise.

- [x] 12. Implement financial reporting system
  - [x] 12.1 Create currency formatting utility
    - Create lib/currency.ts with formatIDR() function
    - Format with "Rp" prefix, period as thousands separator, 0 decimal places
    - Use id-ID locale with Intl.NumberFormat
    - _Requirements: 7.7_
  - [x] 12.2 Write property test for Indonesian Rupiah Currency Formatting
    - **Property 10: Indonesian Rupiah Currency Formatting**
    - **Validates: Requirements 7.7**
    - Test formatting with "Rp" prefix
    - Test period as thousands separator
    - Test zero decimal places
  - [x] 12.3 Create revenue calculation utility
    - Create lib/revenue.ts with calculateRevenue() function
    - Query orders within date range (adjusted to UTC+7)
    - Filter by authenticated user's tenant_id
    - Sum total_amount field
    - Return 0 if no orders in period
    - _Requirements: 7.3, 7.4, 7.5, 7.6, 7.8, 7.9_
  - [x] 12.4 Write property test for Revenue Calculation Correctness
    - **Property 9: Revenue Calculation Correctness**
    - **Validates: Requirements 7.3, 7.4, 7.5, 7.6, 7.8**
    - Test revenue is sum of total_amount for orders in period
    - Test date boundaries are correctly adjusted to UTC+7
    - Test only tenant's orders are included
    - Test zero revenue for periods with no orders
  - [x] 12.5 Create financial reports page UI
    - Create app/dashboard/laporan/page.tsx as Server Component
    - Parse URL search params for view type (daily/weekly/monthly) and date
    - Calculate date range using lib/datetime.ts utilities
    - Query and calculate revenue using calculateRevenue()
    - Format revenue using formatIDR()
    - Display view selector (tabs or dropdown)
    - Display date picker appropriate to view type
    - Display revenue with large formatted amount
    - Implement responsive layout
    - _Requirements: 7.1, 7.2, 7.3, 7.4, 7.5, 7.7, 7.9, 7.10, 9.2, 9.6_
  - [x] 12.6 Write property test for Tenant Data Isolation (Reports)
    - **Property 4: Tenant Data Isolation (Reports component)**
    - **Validates: Requirements 7.8**
    - Test reports only include tenant's orders
    - Test cross-tenant data access is prevented

- [x] 13. Implement responsive design polish
  - [x] 13.1 Write property test for Responsive Viewport Rendering
    - **Property 12: Responsive Viewport Rendering**
    - **Validates: Requirements 9.2, 9.4, 9.5, 9.6**
    - Test all pages render without horizontal scroll from 320px to 1920px
    - Test touch targets are minimum 44x44 pixels
    - Test navigation collapses below 768px, expands above 768px
  - [x] 13.2 Review and refine responsive layouts
    - Test all pages on various screen widths (320px, 375px, 768px, 1024px, 1920px)
    - Verify touch target sizes on touch devices
    - Ensure no horizontal scrolling
    - Verify navigation collapse/expand behavior
    - Fix any layout issues discovered
    - _Requirements: 9.2, 9.4, 9.5, 9.6_

- [x] 14. Final integration and testing
  - [x] 14.1 Write integration tests for end-to-end flows
    - Test complete user journey: register → login → create menu item → create order → view report
    - Test multi-tenant isolation: two users cannot see each other's data
    - Test session expiration and redirect flows
    - _Requirements: 2.2, 2.3, 3.3, 4.5, 8.4_
  - [x] 14.2 Final checkpoint - Verify all functionality
    - Run all tests and ensure they pass
    - Test complete user flows manually
    - Verify all requirements are implemented
    - Ask the user if questions arise

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- Each task references specific requirements for traceability
- Property tests validate universal correctness properties from the design document
- Integration with Supabase requires environment variables: NEXT_PUBLIC_SUPABASE_URL and NEXT_PUBLIC_SUPABASE_ANON_KEY
- Database migrations should be run against Supabase instance before running the application
- All Server Actions use @supabase/ssr for session management
- Row Level Security provides defense-in-depth for tenant isolation
- All date/time operations use Asia/Jakarta timezone (UTC+7)

## Task Dependency Graph

```json
{
  "waves": [
    { "id": 0, "tasks": ["1"] },
    { "id": 1, "tasks": ["2.1", "4"] },
    { "id": 2, "tasks": ["2.2", "3.1"] },
    { "id": 3, "tasks": ["3.2", "3.3", "3.4", "5.1"] },
    { "id": 4, "tasks": ["3.5", "5.2", "5.3", "5.4", "5.5", "6"] },
    { "id": 5, "tasks": ["7"] },
    { "id": 6, "tasks": ["9.1", "10.1"] },
    { "id": 7, "tasks": ["9.2", "9.3", "9.4", "10.2"] },
    { "id": 8, "tasks": ["9.5", "10.3", "10.4", "10.5"] },
    { "id": 9, "tasks": ["10.6", "12.1", "12.3"] },
    { "id": 10, "tasks": ["12.2", "12.4", "12.5"] },
    { "id": 11, "tasks": ["12.6", "13.1", "13.2"] },
    { "id": 12, "tasks": ["14.1", "14.2"] }
  ]
}
```
