# Chapter 5: Project Outcome

This chapter describes the developed Online Shipping System as an implemented multi-vendor fashion marketplace. It focuses on the major non-trivial features of the delivered application, covering customer shopping, vendor catalogue management, order processing, promotional engagement, and analytics at a higher level than a step-by-step walkthrough. The demonstration video complements this chapter by illustrating each interaction in detail; this written account concentrates on the workflows and screens that are most relevant for assessing the breadth and depth of the implementation.

The delivered system addresses the three compulsory specification blocks — Block A (Core Functions), Block B (Multiple Photos and Order Processing), and Block F (Multiple Vendors) — together with three further blocks — Block T (User Generated Content), Block U (Wishlist and Promotional Pricing Strategy), and Block X (User Experience Design). Screenshot placement is indicated explicitly at points where visual evidence is most useful.

## 5.1 Outcome Overview

The delivered application comprises a React single-page storefront and vendor interface connected to a Django REST backend. Customers and vendors operate through separate role-specific views while relying on a shared product, order, and notification infrastructure.

On the customer side, the system supports account registration (A1), product browsing and search before login (A2), server-side cart management (A7–A10), and order placement with multi-vendor split checkout (A11, F4). On the vendor side, it provides shop registration with contact and store information (F1), product catalogue maintenance (A14–A18), and order fulfilment scoped to each vendor's own shop (F2). Products can be presented with multiple images and optional video media (B1), and orders progress through a four-state lifecycle covering Pending, Holding, Shipped, and Cancelled states (B2), with a refund-request workflow connecting customers and vendors. Beyond the core transactional features, the system offers wishlist management with promotional pricing and automated sale notifications (Block U), verified-purchase reviews with optional media attachments (Block T), a vendor analytics dashboard with visual charts and data-driven discount recommendations (Block U), and responsive layout patterns adapted for desktop and mobile screens (Block X).

The sections that follow are organised around the most significant completed workflows.

## 5.2 Customer Storefront and Purchase Flow

The customer-facing interface covers the entire purchase journey from account creation through to order placement. Registration collects the customer's name, email, password, and shipping address as specified by A1. Unauthenticated visitors can browse the product catalogue and view product details freely (A2), while logged-in customers additionally access the cart, wishlist, order history, notifications, and profile settings, with all data persisted server-side across sessions (A2).

Figure 5.1 should be inserted here to show the storefront home page and product-list view.

Suggested caption: Figure 5.1. Customer storefront with searchable product catalogue and paginated product listings.

The product catalogue offers several discovery mechanisms. Customers can browse products displayed in a responsive grid showing name, price, and thumbnail image (A3), and search for products by name (A4). Pagination controls allow navigation through lengthy product lists (A5), while filter options narrow results by brand, category, store, stock availability, and sale status. Opening an individual product-detail page presents additional information well beyond the minimum two extra attributes required by A6, including a multi-image gallery (B1), description, stock level, brand, store information, and customer reviews (Block T).

Figure 5.2 should be inserted here to show a product-detail page with multiple images and product information.

Suggested caption: Figure 5.2. Product-detail screen displaying image gallery, pricing, description, and review section.

The shopping-cart and checkout process is one of the more involved outcomes of the project. Because cart data is persisted on the server (A2), it survives across browser sessions once the customer is logged in. Products are added to the cart with a default quantity of one (A7), and the cart displays each item with its name, price, quantity, and the total order amount (A8). Customers can subsequently adjust quantities (A9), remove items (A10), and click any cart entry to review the corresponding product detail (A8).

Checkout handles the multi-vendor scenario transparently. When the selected cart items belong to more than one shop, the backend groups them by vendor and creates a separate purchase order for each group (F4). The customer can therefore add products from across the marketplace to a single cart and have them split automatically into per-shop orders at checkout. Rather than clearing the entire cart as in the single-vendor case described by A11, items that are not selected for a given checkout remain in the cart for a future purchase (F5).

Figure 5.3 should be inserted here to show the cart and checkout screen with selected items and shipping details.

Suggested caption: Figure 5.3. Shopping-cart and checkout interface producing one or more vendor-specific purchase orders.

This flow ties together product search, cart persistence, promotional pricing, shipping-address capture, stock deduction, order creation, and vendor notification in a single end-to-end transaction, making it a useful measure of the project's overall integration across Blocks A, B, and F.

## 5.3 Order Tracking, Status Changes, and After-Sales Features

Once an order has been placed, customers can view their complete order history. The order list is sorted in reverse chronological order and shows the purchase-order number, purchase date, and current status (A12). Expanding an entry reveals the line items at a glance, and opening the full detail view presents the paid unit price and quantity for each item, the order total, the shipping address, the recipient name, the vendor's store details, and the date of the most recent status update (A13, B4).

Figure 5.4 should be inserted here to show the customer order-history screen and a single order-detail screen.

Suggested caption: Figure 5.4. Customer order history and detailed purchase-order view.

Order processing follows a four-state lifecycle as required by B2. An order begins in the Pending state when it is first created. The vendor can move it to Holding when fulfilment must be temporarily paused, or advance it to Shipped once the items have been dispatched. Either the vendor or the customer can set the order to Cancelled. Both parties can see the current status at all times, and customers may filter their order list by status (B3), which is particularly helpful as the number of orders grows.

The system supports a refund-request workflow as part of its after-sales handling. While an order remains in Pending or Holding, the customer can submit a refund request accompanied by a written reason. The request is recorded on the order and surfaced to the vendor, who can then approve or reject it. This extends the order-processing workflow defined by B2 with a realistic dispute-resolution step.

A second after-sales feature is the verified-purchase review system, which satisfies Block T. Before a customer can submit a review, the backend confirms that the customer has received the product in a shipped order and has not already reviewed it. Each customer is limited to one review per product regardless of how many times the product was ordered, and the review may include optional image or video attachments. These constraints ensure that published reviews originate exclusively from verified buyers and that no duplicate entries appear, reinforcing the consumer trust objective described in the Block T specification.

Figure 5.5 should be inserted here to show the review submission area and the refund-request interface on an order.

Suggested caption: Figure 5.5. After-sales features: refund-request submission and verified-purchase review form.

## 5.4 Vendor Shop Management and Order Processing

Each vendor operates within a dedicated management workspace tied to their own shop, satisfying the Block F requirement that each vendor manages their own product catalogue and purchase orders (F2). After registering an account with contact information and shop details (F1), the vendor creates a shop with descriptive information and photographs, and from that point onward manages only the products and orders associated with that shop.

Figure 5.6 should be inserted here to show the vendor profile dashboard or main vendor navigation area.

Suggested caption: Figure 5.6. Vendor management hub providing access to catalogue, orders, promotions, and analytics.

The vendor catalogue workspace allows browsing and searching products within the vendor's own inventory (A14), with each product displaying its system-generated identifier (A15). Vendors can create new products with fields for price, stock quantity, description, brand, category, and media (A16), and edit any of these details after initial creation (A17). Products can be hidden to remove them temporarily from the storefront and later re-enabled when appropriate (A18). Each product supports multiple photographs and optional video entries (B1), and the vendor can also update shop profile information and store photographs through the same management interface (F1).

Figure 5.7 should be inserted here to show the product create/edit screen with brand, category, and media management.

Suggested caption: Figure 5.7. Vendor catalogue maintenance screen for product editing and media management.

Order management on the vendor side is equally central to the project outcome. Vendors can list incoming orders sorted by newest first with purchase-order numbers, dates, customer names, totals, and status (A19), open individual order details and line items (A20), update the order status through the four-state workflow (B2), inspect shipping information, and handle refund requests. The interface separates active orders from shipped orders so that day-to-day fulfilment tasks remain clearly organised.

Figure 5.8 should be inserted here to show the vendor orders list and vendor order-detail view.

Suggested caption: Figure 5.8. Vendor order-processing screens with status actions and refund management.

Together these features cover the complete commercial cycle from initial order receipt through to dispatch. Status changes made by the vendor are communicated back to the customer automatically through the notification system (Block X).

## 5.5 Wishlist, Promotions, Notifications, and Shop Discovery

In addition to the core transactional features covered by Blocks A, B, and F, the project delivers the engagement-oriented capabilities defined by Blocks U and X.

Customers can navigate directly to a vendor's shop page, view the shop's profile and photographs, browse the shop's products, and search within that shop (F3). This gives the multi-vendor structure a visible presence on the storefront rather than confining it to backend logic.

Customers can add products to a personal wishlist for future consideration, as described by Block U. Each wishlist entry records price-related information at the time of addition, enabling later comparison when promotional discounts are applied. Vendors can create promotions for their products by specifying a discount percentage together with start and end dates. When a newly active promotion applies to a product that one or more customers have wishlisted, the system automatically generates sale notifications for those customers, directly implementing the Block U objective of informing customers that wish-listed items have gone on sale to pull them back to complete the purchase.

The notification system covers a wider set of events as well, including new orders, order shipment, order holding, vendor-initiated order cancellation, refund requests, and refund outcomes. Block X describes notifications as powerful tools to inform users of relevant messages and promote engagement while warning against user annoyance. The implementation addresses this by sending notifications only for specific meaningful events and displaying them in a non-intrusive notification panel accessible through a badge icon, rather than using disruptive pop-ups.

Figure 5.9 should be inserted here to show the wishlist screen, a promotion-management screen, and the notification panel.

Suggested caption: Figure 5.9. Engagement features: wishlist, promotion management, and event-driven notification panel.

Collectively, these features add customer retention and re-engagement mechanisms to the project alongside its core transactional processing, satisfying both the wishlist-driven sales objectives of Block U and the notification-management guidance of Block X.

## 5.6 Analytics and Sample Report Layouts

The vendor analytics dashboard serves as the main report-style deliverable of the project. Rather than producing static downloadable files, the application renders live dashboard views derived from accumulated transaction and behavioural data. This feature extends the Block U specification, which states that the system can monitor search queries and search result browsing to anticipate potential sales.

The dashboard opens with summary cards for total views, total orders, revenue, and active product count, giving the vendor an immediate snapshot of shop performance. Below the summary, line charts track product views over time and revenue over time, while ranked lists surface the top search queries and most-viewed products. A dedicated chart highlights the most-wishlisted products, and a pie chart shows the breakdown of views by product category. A conversion-funnel bar chart illustrates progression from product views through cart additions to completed orders, and a set of recommended discount suggestions targets products with high customer interest but low conversion.

Figure 5.10 should be inserted here to show the analytics dashboard overview.

Suggested caption: Figure 5.10. Vendor analytics dashboard with summary metrics and chart-based report layouts.

The dashboard fulfils two roles in the context of the project outcome. It demonstrates that the application captures behavioural and sales data that goes beyond basic data entry, and it provides structured report layouts that an assessor can examine as evidence of the system's analytical capabilities.

Figure 5.11 should be inserted here to show the recommended-discount section and one or two supporting charts.

Suggested caption: Figure 5.11. Analytics-derived discount recommendations and supporting behavioural charts.

The discount-recommendation section translates recorded views, wishlist additions, cart events, and completed orders into actionable suggestions for the vendor, directly supporting Block U's objective of planning discounts and special offers around products in customer wishlists to encourage purchase.

## 5.7 Summary of the Delivered Outcome

The delivered project is a working multi-vendor e-commerce system that satisfies all twenty core requirements of Block A, implements multi-photo support and a four-state order workflow from Block B (with B4 partially met through a general last-updated timestamp), delivers the full multi-vendor shop structure of Block F, and fulfils the further blocks for user-generated content (Block T), wishlist and promotional pricing (Block U), and user experience design (Block X). Its most significant outcomes are the end-to-end purchase workflow with automatic order splitting by shop (A11, F4, F5), the role-specific vendor management workspace (F1, F2, A14–A20), the multi-step order lifecycle with refund handling (B2, B3), the verified-purchase review system (Block T), and the integrated wishlist, notification, and analytics features (Block U, Block X).

Overall, the system supports realistic marketplace interactions for both customer and vendor roles and offers sufficient breadth and depth for an assessor to evaluate the completeness of the implementation through the screenshots, interface layouts, and workflow descriptions presented in this chapter.
