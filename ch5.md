# Chapter 5: Project Outcome

This chapter presents the outcome of the Online Shipping System Project as an implemented multi-vendor fashion marketplace. Its purpose is to show the main non-trivial features of the completed system at a higher level, with emphasis on how the delivered application supports customer shopping, vendor catalogue management, order processing, promotional engagement, and analytics. Rather than repeating every user interaction step, this chapter highlights the major flows and screens that best demonstrate the completed functionality.

The implemented system covers the core customer and vendor requirements, together with the selected extensions for multi-vendor order splitting, multiple product media, order-status processing, wishlist-driven promotions, review-based user-generated content, and responsive user experience. Where screenshots are most useful for assessment, their placement is indicated explicitly.

## 5.1 Outcome Overview

The finished system consists of a React-based storefront and vendor interface connected to a Django REST backend. Customers and vendors use separate role-specific features while sharing the same underlying product, order, and notification infrastructure.

At a project-outcome level, the system delivers the following major capability groups:

- customer registration, login, persistent cart management, product discovery, and order placement;
- vendor registration, shop creation, product maintenance, and order fulfilment;
- split checkout for items originating from different shops;
- multiple product images and optional video media;
- order tracking through Pending, Holding, Shipped, and Cancelled states;
- refund-request handling between customers and vendors;
- wishlist management, promotional pricing, and sale notifications;
- verified-purchase reviews with optional media attachments;
- vendor analytics with charts and discount recommendations;
- responsive interaction patterns for desktop and mobile screens.

The remainder of the chapter is organised around the most significant completed workflows.

## 5.2 Customer Storefront and Purchase Flow

The customer-facing side of the application supports the full purchase journey from account creation to order placement. Public users can browse the product catalogue before login, while authenticated customers gain access to cart, wishlist, orders, notifications, and profile functions.

Figure 5.1 should be inserted here to show the storefront home page and product-list view.

Suggested caption: Figure 5.1. Customer storefront showing searchable product catalogue and navigable product listings.

The implemented catalogue goes beyond a basic product listing. Customers can:

- browse products in a grid with name, price, and thumbnail image;
- search products by name;
- navigate long product lists with pagination;
- filter product listings by brand, category, store, stock state, and sale status;
- open a product-detail page containing richer information, multiple media items, and review content.

Figure 5.2 should be inserted here to show a product-detail page with multiple images and product information.

Suggested caption: Figure 5.2. Product-detail screen with gallery, pricing, description, and review area.

The shopping-cart and checkout process demonstrates one of the main non-trivial project outcomes. Cart data is stored on the server, so it persists across sessions after login. Customers can add products with a default quantity of one, adjust quantities later, remove items, and inspect total order value before checkout.

The checkout implementation goes beyond a single-shop cart. If a customer selects items belonging to different vendors, the backend groups the selected cart items by shop and creates a separate purchase order for each vendor. This means the system satisfies the multi-vendor constraint while still allowing the customer to shop across the whole marketplace in one session. Items not selected for checkout remain in the cart, which also satisfies the partial-checkout requirement.

Figure 5.3 should be inserted here to show the cart and checkout screen with selected items and shipping details.

Suggested caption: Figure 5.3. Shopping-cart and checkout interface used to create one or more vendor-specific purchase orders.

This purchase flow is a representative example of the project's depth because it integrates search, cart persistence, product pricing, shipping information capture, stock deduction, order creation, and vendor notification within a single end-to-end transaction.

## 5.3 Order Tracking, Status Changes, and After-Sales Features

After checkout, customers can open their order history and inspect detailed order information. Orders are listed in reverse chronological order and display the purchase-order number, purchase date, and current status. Expanding an order reveals its line items, and clicking through to the detail view shows the paid unit price and quantity for each item, the order total, the shipping address, recipient name, vendor store details, and the latest status-update date.

Figure 5.4 should be inserted here to show the customer order-history screen and a single order-detail screen.

Suggested caption: Figure 5.4. Customer order history and detailed purchase-order view.

The order-processing outcome includes a four-state workflow:

- `Pending` when the order is first created;
- `Holding` when a vendor temporarily pauses fulfilment;
- `Shipped` when the vendor dispatches the order;
- `Cancelled` when the order is cancelled by the vendor or customer.

This status model is visible to both customers and vendors and satisfies the project requirement for basic order processing. Customers can filter their order list by status, which simplifies navigation when the order history grows large.

The implemented after-sales workflow includes refund requests. When an order is still in `Pending` or `Holding`, the customer can submit a refund request with a reason. The request is recorded on the order, surfaced to the vendor, and resolved by vendor action. This gives the project a more realistic transactional workflow than a simple order list.

Another completed after-sales feature is verified-purchase review submission. The system checks whether the customer has previously received the product in a shipped order and whether a review has already been submitted. Only valid purchasers can create one review per product, with optional image or video attachments. This prevents anonymous or duplicate feedback and turns completed orders into useful user-generated content.

Figure 5.5 should be inserted here to show the review submission area and the refund-request interface on an order.

Suggested caption: Figure 5.5. Post-purchase features including refund request and verified-purchase review submission.

## 5.4 Vendor Shop Management and Order Processing

The vendor side of the application provides a dedicated management workspace scoped to each shop. Vendors register their own accounts, create a shop with profile information and photos, and subsequently manage only the products and orders that belong to that shop.

Figure 5.6 should be inserted here to show the vendor profile dashboard or main vendor navigation area.

Suggested caption: Figure 5.6. Vendor management area for catalogue, orders, promotions, and analytics.

From the project-outcome perspective, the most important vendor features are:

- browsing and searching products within the vendor catalogue;
- viewing system-generated product identifiers;
- creating products with price, stock quantity, description, brand, category, and media;
- editing product details after creation;
- hiding or re-enabling products in the storefront;
- managing multiple photos and optional video entries for each product;
- maintaining shop information and store photos.

Figure 5.7 should be inserted here to show the product create/edit screen with brand, category, and media management.

Suggested caption: Figure 5.7. Vendor catalogue maintenance screen for product editing and media management.

The vendor order-management outcome is equally important. Vendors can list incoming orders, inspect order details, change order status, review shipping information, and process refund requests. The order screen distinguishes active orders from shipped orders, making it easier to support daily fulfilment work.

Figure 5.8 should be inserted here to show the vendor orders list and vendor order-detail view.

Suggested caption: Figure 5.8. Vendor order-processing screens showing status actions and refund management.

This part of the system covers the full commercial cycle from order receipt to fulfilment. Vendors can progress customer orders through to shipment and communicate status changes back to the customer through the notification mechanism.

## 5.5 Wishlist, Promotions, Notifications, and Shop Discovery

Beyond the core purchase flow, the project includes engagement features intended to improve realism and commercial value.

Customers can visit vendor shops directly, browse products within a shop, and search within that shop context. This makes the multi-vendor structure visible on the storefront rather than hidden purely in the backend.

Customers can also add products to a wishlist for future purchase. Wishlist entries preserve price-related information at the time they are added, which supports promotional comparison and later conversion.

On the vendor side, promotions can be created for products using a discount percentage and start/end dates. When a promotion becomes active for a product that appears in customer wishlists, the system generates sale notifications to the affected users. This links wishlist behaviour with promotional pricing rather than treating them as unrelated features.

The notification system also informs users of important order and refund events, including new orders, order shipment, order holding, vendor-initiated order cancellation, refund requests, and refund outcomes. Together these notifications keep both parties informed after significant actions without requiring manual checking.

Figure 5.9 should be inserted here to show the wishlist screen, a promotion-management screen, and the notification panel.

Suggested caption: Figure 5.9. Engagement features: wishlist, promotions, and event-driven notifications.

These features extend the project outcome beyond the minimum store-and-cart workflow by incorporating customer retention and re-engagement mechanisms alongside transactional processing.

## 5.6 Analytics and Sample Report Layouts

The system includes a vendor analytics dashboard that acts as the main report-style outcome of the project. Instead of producing static downloadable reports, the application generates live dashboard views from collected transaction and behaviour data.

The analytics dashboard includes:

- summary cards for total views, total orders, revenue, and product count;
- charts of product views over time and revenue over time;
- top search queries and top viewed products;
- top wishlisted products;
- category-view breakdown;
- a conversion funnel from product views to cart additions to completed orders;
- recommended discount suggestions for products with strong interest but weak conversion.

Figure 5.10 should be inserted here to show the analytics dashboard overview.

Suggested caption: Figure 5.10. Vendor analytics dashboard with summary metrics and chart-based report layouts.

The dashboard serves two purposes in the context of the project outcome. First, it shows that the application records behavioural and sales data beyond simple CRUD operations. Second, it provides sample report layouts in the form of structured business information that an assessor can evaluate alongside the transactional features.

Figure 5.11 should be inserted here to show the recommended-discount section and one or two supporting charts.

Suggested caption: Figure 5.11. Analytics-derived discount recommendations and supporting behavioural charts.

The recommendation section turns recorded views, wishlists, cart additions, and completed orders into a practical vendor decision aid, adding a business-intelligence dimension to the otherwise transactional application.

## 5.7 Summary of the Delivered Outcome

The completed project outcome is a functioning multi-vendor e-commerce system that supports customer shopping, vendor-operated shops, order processing, promotional engagement, user-generated reviews, and analytics-driven decision support. The most significant implemented outcomes are the end-to-end purchase workflow with split ordering by shop, role-specific vendor management, multi-step order handling with refund support, and the integration of wishlist, notification, and analytics features.

The developed system supports realistic marketplace behaviour across both customer and vendor roles and provides sufficient visible functionality for an assessor to evaluate the depth and completeness of the implementation through the screenshots, interface layouts, and workflow descriptions presented in this chapter.
