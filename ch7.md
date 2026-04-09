# Chapter 7: Conclusion and Further Work

## 7.1 Summary

This project developed a multi-vendor fashion e-commerce platform implemented as a React single-page application connected to a Django REST Framework backend with an SQLite database. The platform serves two primary user roles — customers who browse, purchase, and review products across multiple vendor shops, and vendors who manage their own catalogues, process orders, run promotions, and monitor sales analytics.

The project followed a structured development process. Chapter 3 analysed the requirements by studying established e-commerce platforms and mapping common marketplace patterns to a set of functional and non-functional requirements. Chapter 4 translated these requirements into an architectural plan covering the three-tier system structure, data modelling, and dynamic workflows. Chapter 5 presented the project outcome by describing the delivered features and their supporting interfaces.

The delivered system covers the full marketplace workflow for both user roles. Customers can register, browse and search products, manage a persistent server-side cart, check out with automatic order splitting by vendor, track orders through a four-state lifecycle, submit refund requests, leave verified-purchase reviews with ratings and media, maintain a wishlist, and receive in-app notifications for order updates and promotional sales. Vendors can register a shop, maintain a product catalogue with multi-image support, process orders, create time-limited promotions, and use an analytics dashboard that surfaces search trends, product views, wishlist popularity, and discount recommendations. The interface supports responsive layouts, swipe-enabled image galleries, persistent dark mode, and pagination.

Implementation correctness was verified through manual testing against each specification requirement. The per-requirement status is documented in Chapter 3, Section 3.3. The only requirement not fully satisfied is per-transition date recording for order status changes; the system records a general last-updated timestamp rather than separate dates for each transition. All remaining requirements are met, and the delivered system satisfies the scope defined at the start of the project.

## 7.2 Limitations

Despite meeting the specification requirements, the project has several limitations that would need to be addressed for production use.

The application uses SQLite, which is file-based and does not support concurrent write access efficiently, restricting the system to low-traffic scenarios. No external payment gateway is integrated; checkout assumes payment has been completed without verification. User authentication relies on a custom device-token scheme without email verification, password-reset functionality, or third-party OAuth login. Product search uses Django ORM substring matching with no full-text search engine, relevance ranking, or fuzzy matching.

The notification system is limited to in-app delivery, with no email or push notification support. Uploaded images are stored at their original resolution without compression, resizing, or CDN integration. Although the data model defines a video media type, the upload field and frontend do not fully support video playback. The application is available in English only, and system-level administration relies entirely on Django's built-in admin interface without a custom dashboard.

## 7.3 Future Work

The limitations above suggest several directions for future development.

The most impactful infrastructure change would be migrating from SQLite to PostgreSQL, which would improve write concurrency, enable multi-instance deployment, and support full-text search with relevance ranking and fuzzy matching. Alternatively, Elasticsearch could be integrated for advanced catalogue search.

Connecting the checkout flow to a payment provider such as Stripe or PayPal would enable real transaction processing. On the authentication side, adding email verification, password-reset via email tokens, OAuth sign-in, and API rate limiting would improve both security and user convenience.

Extending notifications to include email and mobile push delivery via Firebase Cloud Messaging would reach users who are not actively browsing. Introducing server-side image resizing and thumbnail generation, together with WebP conversion and CDN integration, would reduce page-load times. Making the video media type functional would require a general file upload field and a frontend video player component.

Recording a separate timestamp for each order status transition through a related history model would provide a complete audit trail. Building an automated test suite for backend and frontend would provide regression safety. Adding internationalisation support, real-time customer–vendor chat via WebSocket, and courier API integration for shipment tracking would further extend the platform toward a production-ready marketplace.
