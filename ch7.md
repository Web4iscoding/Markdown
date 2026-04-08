# Chapter 7: Conclusion and Further Work

## 7.1 Project Summary

This project set out to develop a multi-vendor fashion e-commerce platform that supports the full marketplace workflow for both customers and vendors. The scope was defined by three compulsory specification blocks — Block A (Core Functions), Block B (Multiple Photos and Order Processing), and Block F (Multiple Vendors) — together with three optional blocks — Block T (User Generated Content), Block U (Wishlist and Promotional Pricing Strategy), and Block X (User Experience Design). The system was implemented as a React single-page application connected to a Django REST Framework backend with an SQLite database.

The customer-facing storefront delivers product browsing and search without authentication, a persistent server-side shopping cart, multi-vendor split checkout, order history with status filtering, and a product-detail experience that includes multi-image galleries and verified-purchase reviews. The vendor-facing management workspace provides shop creation and profile maintenance, product catalogue operations (create, edit, hide, multi-image upload), a four-state order-processing workflow (Pending, Holding, Shipped, Cancelled), refund-request handling, promotional pricing with configurable discount periods, and an analytics dashboard driven by recorded product views, search queries, wishlist data, and sales figures.

Engagement features connect the two sides of the marketplace: wishlist sale notifications inform customers when a saved product enters a promotion, event-driven in-app notifications cover order status changes and refund outcomes, and the analytics dashboard helps vendors identify high-interest products and recommend discount candidates. The interface adapts to desktop and mobile screen sizes through responsive layouts, a mobile drawer menu, swipe-enabled image galleries, and persistent dark-mode support.

## 7.2 Requirements Satisfaction

The following table summarises how each chosen specification block has been addressed by the delivered system. Detailed per-item evidence is provided in Chapter 3, Section 3.3.

| Block | Type | Outcome |
| --- | --- | --- |
| A – Core Functions | Basic (compulsory) | All 20 requirements implemented. Customer storefront functions (A1–A13) and vendor administration functions (A14–A20) are fully operational, including registration, product browsing, cart management, checkout, and order detail views for both roles. |
| B – Multiple Photos and Order Processing | Basic (compulsory) | 3 of 4 requirements fully implemented. B1 (multiple photos), B2 (four-state order workflow), and B3 (order status filtering) are complete. B4 (per-transition date recording) is partially implemented; the system records a general last-updated timestamp rather than individual dates for each status change. |
| F – Multiple Vendors | Basic (compulsory) | All 5 requirements implemented. Vendors operate independent shops (F1–F2), customers can visit and search within individual stores (F3), the cart accepts products from multiple shops with automatic order splitting at checkout (F4), and unselected items remain in the cart (F5). F1 is adapted slightly in that shop photos are uploaded through store management after registration rather than on the registration form. |
| T – User Generated Content | Further (optional) | Implemented. Customers can rate products on a five-point scale, write text reviews, and attach image media. The system enforces one review per customer per product and restricts reviews to verified purchasers who have received the item in a shipped order. |
| U – Wishlist and Promotional Pricing | Further (optional) | Implemented. Customers maintain wishlists, vendors create time-limited promotions, and the system generates sale notifications for customers who have wishlisted a newly promoted product. Product views and search queries are logged and surfaced through the vendor analytics dashboard alongside wishlist popularity and discount recommendations. |
| X – User Experience Design | Further (optional) | Implemented. The interface provides responsive layouts for desktop and mobile, a mobile navigation drawer, swipe-enabled product galleries, dark-mode persistence, pagination controls, notification badges, and feedback messages for user actions. |

All three compulsory blocks are satisfied, with only B4 partially met. All three optional blocks are fully implemented. The delivered system therefore meets the requirements identified at the start of the project.

## 7.3 Limitations

Despite satisfying the chosen specification blocks, the project has several limitations that would need to be addressed before the system could operate in a production environment.

**Database.** The application uses SQLite as its sole data store. SQLite is file-based and does not support concurrent write access efficiently, which limits the system to single-user or low-traffic scenarios. A production deployment would require migration to a client–server database such as PostgreSQL.

**Payment processing.** The system does not integrate with any external payment gateway. Orders are created and tracked, but no monetary transaction is processed. Checkout currently assumes payment has been completed without verification.

**Authentication.** User authentication relies on a custom device-token scheme. The system does not provide email verification on registration, password-reset functionality, or third-party OAuth login (e.g. Google or Facebook). These omissions reduce account security and user convenience.

**Search.** Product search is implemented through Django ORM substring matching (`icontains`). There is no full-text search engine, relevance ranking, stemming, or fuzzy matching. For larger catalogues this approach would degrade in both performance and result quality.

**Status date recording.** As noted under B4, the order model records a single `statusUpdatedDate` timestamp rather than separate dates for each status transition. This means that, for example, the date an order moved from Pending to Holding is overwritten when it later moves to Shipped.

**Notifications.** All notifications are stored in the database and displayed within the application interface. The system does not send email notifications, push notifications, or SMS alerts, which limits its ability to reach users who are not actively using the application.

**Image handling.** Uploaded images are stored on the local filesystem at their original resolution without compression, resizing, or thumbnail generation. There is no content delivery network integration and no conversion to optimised formats such as WebP.

**Testing.** The backend test file is empty. No unit tests, integration tests, or API tests have been written, which means the system has no automated regression-testing coverage.

**Video support.** The `ProductMedia` model defines a video media type, but the upload field uses `ImageField` and the frontend does not include a video player component. In practice, video attachments are not fully functional.

**Internationalisation.** The application is available in English only. Although Django's internationalisation setting is enabled, no translation files or frontend localisation libraries are present.

**Administration.** System-level administration relies entirely on Django's built-in admin interface. There is no custom admin dashboard with platform-wide analytics, user management tools, or moderation workflows.

## 7.4 Further Work

The limitations described above suggest several directions for future development.

**Payment gateway integration.** Connecting the checkout flow to a payment provider such as Stripe or PayPal would allow the system to process real transactions. This would involve creating a payment intent before order confirmation, handling success and failure callbacks, and recording payment status alongside order status.

**Database migration.** Replacing SQLite with PostgreSQL would improve concurrency, support full-text search through PostgreSQL's built-in `tsvector` capabilities, and prepare the system for multi-instance deployment behind a load balancer.

**Enhanced authentication.** Adding email verification at registration, a password-reset flow via email tokens, and OAuth sign-in through providers such as Google would improve both security and the registration experience. API rate limiting through Django REST Framework's throttling classes would protect authentication endpoints from brute-force attacks.

**Full-text search.** Integrating a dedicated search engine such as Elasticsearch, or using PostgreSQL full-text search, would enable relevance-ranked results, stemming, and fuzzy matching. This would improve the shopping experience as the product catalogue grows.

**Multi-channel notifications.** Extending the notification system to include email (via Django's `send_mail`) and mobile push notifications (via Firebase Cloud Messaging) would ensure that customers and vendors receive timely updates even when they are not actively browsing the application.

**Image optimisation.** Introducing server-side image resizing and thumbnail generation — for example through the `django-imagekit` library — together with WebP conversion and integration with a CDN, would reduce page-load times and bandwidth consumption.

**Per-transition status dates.** Extending the order model to record a separate timestamp for each status transition would fully satisfy requirement B4. This could be implemented with a related `OrderStatusHistory` model that logs every state change with its date.

**Automated testing.** Building a test suite covering model validation, serializer logic, API endpoint behaviour, and permission checks would provide regression safety for future development. Frontend component tests using a framework such as React Testing Library would complement the backend coverage.

**Video playback.** Replacing the `ImageField` on `ProductMedia` with a `FileField` for video entries and adding a frontend video player component would make the existing video media type functional. Server-side validation of video formats and file-size limits would also be necessary.

**Internationalisation.** Adding Django translation files and a frontend localisation library such as `react-i18next` would allow the interface to be offered in multiple languages, broadening the potential user base.

**Live messaging.** Implementing a real-time chat channel between customers and vendors — for example through Django Channels with WebSocket support — would allow pre-sale enquiries and post-sale communication without leaving the platform.

**Shipment tracking.** Integrating with courier APIs to provide real-time tracking information within the order-detail view would give customers visibility into the delivery status of shipped orders, which is a standard expectation of modern e-commerce platforms.

These extensions would collectively move the system from a project-scope prototype toward a production-ready multi-vendor marketplace.
