# Chapter 5: Project Outcome

This chapter describes the developed Online Shipping System as an implemented multi-vendor fashion marketplace. It focuses on the major non-trivial features of the delivered application, covering customer shopping, vendor catalogue management, order processing, promotional engagement, and analytics at a higher level than a step-by-step walkthrough. The demonstration video complements this chapter by illustrating each interaction in detail; this written account concentrates on the workflows and screens that are most relevant for assessing the breadth and depth of the implementation.

The delivered system addresses the core customer and vendor requirements alongside extensions for multi-vendor order splitting, multiple product media, order-status processing, wishlist-driven promotions, verified-purchase reviews, and responsive design. Screenshot placement is indicated explicitly at points where visual evidence is most useful.

## 5.1 Outcome Overview

The delivered application comprises a React single-page storefront and vendor interface connected to a Django REST backend. Customers and vendors operate through separate role-specific views while relying on a shared product, order, and notification infrastructure.

The system provides the following principal capability groups:

- customer registration, login, server-side cart management, product discovery, and order placement;
- vendor registration, shop setup, product catalogue maintenance, and order fulfilment;
- split checkout that separates items from different shops into individual purchase orders;
- support for multiple product images and optional video media;
- order lifecycle tracking through Pending, Holding, Shipped, and Cancelled states;
- refund-request workflow between customers and vendors;
- wishlist management with promotional pricing and automated sale notifications;
- verified-purchase reviews with optional media attachments;
- vendor analytics dashboard with visual charts and data-driven discount recommendations;
- responsive layout patterns adapted for desktop and mobile screens.

The sections that follow are organised around the most significant completed workflows.

## 5.2 Customer Storefront and Purchase Flow

The customer-facing interface covers the entire purchase journey from account creation through to order placement. Unauthenticated visitors can browse the product catalogue freely, while logged-in customers additionally access the cart, wishlist, order history, notifications, and profile settings.

Figure 5.1 should be inserted here to show the storefront home page and product-list view.

Suggested caption: Figure 5.1. Customer storefront with searchable product catalogue and paginated product listings.

The product catalogue offers several discovery mechanisms. Customers can:

- browse products displayed in a grid showing name, price, and thumbnail image;
- search for products by name;
- navigate lengthy product lists through pagination controls;
- narrow results by brand, category, store, stock availability, and sale status;
- open individual product-detail pages that present additional information, a multi-image gallery, and customer reviews.

Figure 5.2 should be inserted here to show a product-detail page with multiple images and product information.

Suggested caption: Figure 5.2. Product-detail screen displaying image gallery, pricing, description, and review section.

The shopping-cart and checkout process is one of the more involved outcomes of the project. Because cart data is persisted on the server, it survives across browser sessions once the customer is logged in. Products are added to the cart with a default quantity of one; customers can subsequently adjust quantities, remove items, and review the total order value before proceeding to checkout.

Checkout handles the multi-vendor scenario transparently. When the selected cart items belong to more than one shop, the backend groups them by vendor and creates a separate purchase order for each group. The customer can therefore add products from across the marketplace to a single cart and have them split automatically into per-shop orders at checkout. Any items that are not selected for a given checkout remain in the cart for a future purchase.

Figure 5.3 should be inserted here to show the cart and checkout screen with selected items and shipping details.

Suggested caption: Figure 5.3. Shopping-cart and checkout interface producing one or more vendor-specific purchase orders.

This flow ties together product search, cart persistence, promotional pricing, shipping-address capture, stock deduction, order creation, and vendor notification in a single end-to-end transaction, making it a useful measure of the project's overall integration.

## 5.3 Order Tracking, Status Changes, and After-Sales Features

Once an order has been placed, customers can view their complete order history. The order list is sorted in reverse chronological order and shows the purchase-order number, purchase date, and current status. Expanding an entry reveals the line items at a glance, and opening the full detail view presents the paid unit price and quantity for each item, the order total, the shipping address, the recipient name, the vendor's store details, and the date of the most recent status update.

Figure 5.4 should be inserted here to show the customer order-history screen and a single order-detail screen.

Suggested caption: Figure 5.4. Customer order history and detailed purchase-order view.

Order processing follows a four-state lifecycle:

- `Pending` — assigned when the order is first created;
- `Holding` — set by the vendor when fulfilment must be temporarily paused;
- `Shipped` — set by the vendor once the items have been dispatched;
- `Cancelled` — set by either the vendor or the customer to cancel the order.

Both customers and vendors can see the current status at all times. Customers may also filter their order list by status, which is particularly helpful as the number of orders grows.

The system supports a refund-request workflow as part of its after-sales handling. While an order remains in `Pending` or `Holding`, the customer can submit a refund request accompanied by a written reason. The request is recorded on the order and surfaced to the vendor, who can then approve or reject it. This adds a realistic dispute-resolution step to the order lifecycle.

A second after-sales feature is the verified-purchase review system. Before a customer can submit a review, the backend confirms that the customer has received the product in a shipped order and has not already reviewed it. Each customer is limited to one review per product regardless of how many times the product was ordered, and the review may include optional image or video attachments. These constraints ensure that published reviews originate exclusively from verified buyers and that no duplicate entries appear.

Figure 5.5 should be inserted here to show the review submission area and the refund-request interface on an order.

Suggested caption: Figure 5.5. After-sales features: refund-request submission and verified-purchase review form.

## 5.4 Vendor Shop Management and Order Processing

Each vendor operates within a dedicated management workspace tied to their own shop. After registering an account, the vendor creates a shop with descriptive information and photographs, and from that point onward manages only the products and orders associated with that shop.

Figure 5.6 should be inserted here to show the vendor profile dashboard or main vendor navigation area.

Suggested caption: Figure 5.6. Vendor management hub providing access to catalogue, orders, promotions, and analytics.

The key vendor-side capabilities delivered by the project are:

- browsing and searching products within the vendor's own catalogue;
- viewing the system-generated identifier assigned to each product;
- creating new products with fields for price, stock quantity, description, brand, category, and media;
- editing product details after initial creation;
- hiding a product to remove it temporarily from the storefront, and later re-enabling it;
- attaching multiple photographs and optional video entries to each product;
- updating shop profile information and store photographs.

Figure 5.7 should be inserted here to show the product create/edit screen with brand, category, and media management.

Suggested caption: Figure 5.7. Vendor catalogue maintenance screen for product editing and media management.

Order management on the vendor side is equally central to the project outcome. Vendors can browse incoming orders, open order details, update the order status, inspect shipping information, and handle refund requests. The interface separates active orders from shipped orders so that day-to-day fulfilment tasks remain clearly organised.

Figure 5.8 should be inserted here to show the vendor orders list and vendor order-detail view.

Suggested caption: Figure 5.8. Vendor order-processing screens with status actions and refund management.

Together these features cover the complete commercial cycle from initial order receipt through to dispatch. Status changes made by the vendor are communicated back to the customer automatically through the notification system.

## 5.5 Wishlist, Promotions, Notifications, and Shop Discovery

In addition to the core transactional features, the project delivers several engagement-oriented capabilities.

Customers can navigate directly to a vendor's shop page, view the shop's profile and photographs, browse the shop's products, and search within that shop. This gives the multi-vendor structure a visible presence on the storefront rather than confining it to backend logic.

Customers can add products to a personal wishlist for future consideration. Each wishlist entry records price-related information at the time of addition, enabling later comparison when promotional discounts are applied.

Vendors can create promotions for their products by specifying a discount percentage together with start and end dates. When a newly active promotion applies to a product that one or more customers have wishlisted, the system automatically generates sale notifications for those customers. This creates a direct connection between wishlist data and promotional activity.

The notification system covers a wider set of events as well, including new orders, order shipment, order holding, vendor-initiated order cancellation, refund requests, and refund outcomes. These event-driven notifications keep both customers and vendors informed of important changes without requiring either party to check manually.

Figure 5.9 should be inserted here to show the wishlist screen, a promotion-management screen, and the notification panel.

Suggested caption: Figure 5.9. Engagement features: wishlist, promotion management, and event-driven notification panel.

Collectively, these features add customer retention and re-engagement mechanisms to the project alongside its core transactional processing.

## 5.6 Analytics and Sample Report Layouts

The vendor analytics dashboard serves as the main report-style deliverable of the project. Rather than producing static downloadable files, the application renders live dashboard views derived from accumulated transaction and behavioural data.

The dashboard presents:

- summary cards for total views, total orders, revenue, and active product count;
- line charts tracking product views over time and revenue over time;
- ranked lists of the top search queries and most-viewed products;
- a chart of the most-wishlisted products;
- a pie chart showing the breakdown of views by product category;
- a bar chart illustrating the conversion funnel from product views through cart additions to completed orders;
- a set of recommended discount suggestions targeting products with high customer interest but low conversion.

Figure 5.10 should be inserted here to show the analytics dashboard overview.

Suggested caption: Figure 5.10. Vendor analytics dashboard with summary metrics and chart-based report layouts.

The dashboard fulfils two roles in the context of the project outcome. It demonstrates that the application captures behavioural and sales data that goes beyond basic data entry, and it provides structured report layouts that an assessor can examine as evidence of the system's analytical capabilities.

Figure 5.11 should be inserted here to show the recommended-discount section and one or two supporting charts.

Suggested caption: Figure 5.11. Analytics-derived discount recommendations and supporting behavioural charts.

The discount-recommendation section translates recorded views, wishlist additions, cart events, and completed orders into actionable suggestions for the vendor, giving the application a business-intelligence capability alongside its transactional functions.

## 5.7 Summary of the Delivered Outcome

The delivered project is a working multi-vendor e-commerce system encompassing customer shopping, vendor-operated shops, order processing, promotional engagement, user-generated reviews, and analytics-driven decision support. Its most significant outcomes are the end-to-end purchase workflow with automatic order splitting by shop, the role-specific vendor management workspace, the multi-step order lifecycle with refund handling, and the integrated wishlist, notification, and analytics features.

Overall, the system supports realistic marketplace interactions for both customer and vendor roles and offers sufficient breadth and depth for an assessor to evaluate the completeness of the implementation through the screenshots, interface layouts, and workflow descriptions presented in this chapter.
