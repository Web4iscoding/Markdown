# Chapter 4: System Design

This chapter describes the design of the Online Shipping System and shows how the implemented system satisfies the requirements established in Chapter 3. Section 4.1 presents the high-level architecture, explains the separation between the browser-based frontend and the server-side backend, and justifies the choice of development tools. Section 4.2 defines the data model, covering entity relationships, integrity constraints, and media storage. Section 4.3 addresses dynamic modelling, focusing on the checkout workflow, the order-status lifecycle, and the notification mechanism. The remaining sections discuss mobile-oriented UX design (Section 4.4), security and data management (Section 4.5), and vendor analytics with product-discount recommendations (Section 4.6). Taken together, these sections form a single coherent design in which a React single-page application communicates with a Django REST API backed by a relational database to deliver the multi-vendor fashion marketplace required by the project specification.

## 4.1 Architectural Design

### 4.1.1 Overall System Architecture

The system follows a three-tier web application architecture comprising a presentation layer, an application layer, and a data layer.

- **Presentation layer.** A React single-page application (SPA), built and served with Vite, provides the user interface for both customers and vendors within the same browser-based client.
- **Application layer.** A Django server together with Django REST Framework (DRF) exposes RESTful API endpoints that handle authentication, product catalogues, shopping carts, orders, wishlists, promotions, reviews, analytics, and notifications.
- **Data layer.** SQLite stores all structured application data, while the server's local media directory holds uploaded files such as profile images, store photos, product images, and review images.

This separation is well suited to the project because the customer storefront and the vendor management interface share the same business logic yet present different views to the user. Exposing all business functions through a Web API keeps the frontend flexible while centralising validation, security, and data access on the server.

Figure 4.1 should be inserted here to show the overall architecture of the system.

Suggested caption: Figure 4.1. High-level architecture of the Online Shipping System Project.

At runtime the layers interact as follows:

1. A customer or vendor opens the React client in a web browser.
2. The client issues HTTP requests carrying JSON payloads (or multipart form data for file uploads) to the Django API.
3. The backend authenticates the request, validates the input, executes the relevant business rules, and reads from or writes to the database.
4. For media uploads, Django saves the file to the server-side media directory and returns the resulting URL.
5. The client renders the JSON response into product catalogues, order screens, vendor dashboards, notification panels, and other interface views.

All business logic resides in the Django application layer—specifically in serializers and view sets—rather than in database stored procedures. This keeps the logic in a single codebase and avoids coupling it to a particular database engine.

### 4.1.2 Client-Side Design

The frontend is built with React 19 and uses React Router 7 for client-side routing. Because navigation between pages—such as moving from a product list to product detail, opening the cart, or switching to the vendor dashboard—is handled entirely in the browser, the application behaves as a single-page application with no full-page reloads.

Routes are divided into two groups. Public routes allow unauthenticated visitors to browse the product catalogue, view store pages, explore categories and brands, read reviews, and access the contact page. Authenticated routes give customers access to cart, checkout, wishlist, order history, profile management, and review submission, while vendors can manage their product catalogue, store information, orders, promotions, and analytics.

This client-side architecture offers several advantages:

- smoother navigation during catalogue browsing, where users frequently move between list and detail views;
- a single API backend serving both the customer and vendor interfaces without duplication;
- easier implementation of responsive and mobile-specific behaviours compared with traditional server-rendered pages;
- clear separation between user-interface concerns and server-side business logic.

### 4.1.3 Server-Side Design

The backend runs on Django 5.2 with Django REST Framework 3.16. Its API is organised around resource-oriented ViewSets and APIViews, grouped into the following functional areas:

- **Authentication** — customer and vendor registration, login, logout, profile updates, and password changes.
- **Catalogue** — product listing, product detail, brand, category, and store endpoints.
- **Shopping** — cart management and order creation for customers.
- **Vendor operations** — order processing, product management, promotion management, and analytics.
- **Engagement** — review submission, wishlist management, and notification delivery.

Critical business rules are enforced entirely on the server. Key examples are:

- grouping cart items by vendor and creating a separate order for each vendor during checkout;
- applying active promotions to compute the paid price of every order item;
- decrementing product stock quantities when an order is confirmed;
- restricting each vendor's access to only the products and orders that belong to that vendor's store;
- generating notifications automatically on events such as new orders, status changes, refund requests, and wishlist-item sales.

Centralising these rules on the backend is essential because order correctness and data integrity must not depend on the behaviour of the client.

### 4.1.4 Development Tools and Environment

Table 4.1 summarises the principal technologies used across the three tiers. Each entry includes the role the technology plays and the rationale behind its selection.

Table 4.1 should be inserted here to summarise the development tools and environment.

Suggested caption: Table 4.1. Main technologies used in the Online Shipping System Project.

| Layer | Technology | Role | Rationale |
| --- | --- | --- | --- |
| Frontend framework | React 19 | Component-based user interface | Supports reusable components, client-side routing, and responsive interactive pages |
| Build tool | Vite 7 | Development server and production bundler | Provides fast cold starts and a simple configuration-light workflow |
| UI libraries | Material UI 7, MUI X Charts 8, custom CSS | Accordion menus, layout helpers, analytics charts, and project-specific styling | Balances ready-made components with the freedom to apply custom visual design |
| Backend framework | Django 5.2 | Web framework, ORM, and application backbone | Mature ecosystem with built-in authentication, media handling, and an admin interface |
| API framework | Django REST Framework 3.16 | JSON-based RESTful API layer | Serializer and ViewSet abstractions map cleanly to SPA-to-server communication |
| Authentication | Custom DeviceToken mechanism | Per-device token management | Allows multiple simultaneous sessions so that logging out on one device does not affect others |
| Database | SQLite | Persistent relational data store | Zero-configuration setup suited to coursework-scale development and testing |
| Image processing | Pillow 12 | Server-side image handling for uploaded media | Standard library required by Django's `ImageField` |

SQLite was chosen because the application is a development and demonstration system rather than a high-concurrency production marketplace. It eliminates the need for a separate database server and simplifies local setup. Because the data model is fully relational, migration to a production-grade engine such as PostgreSQL would require only a configuration change rather than a schema redesign.

Uploaded media files (profile pictures, store banners, product images, and review photos) are stored on the server's local file system via Django's `ImageField`. This approach avoids the complexity of an external object-storage service during development; however, in a production deployment the media directory should be replaced by durable cloud storage to ensure scalability and redundancy.

### 4.1.5 Architectural Rationale

The main architectural decisions and their justifications are summarised below.

**Decoupled frontend and backend.** Server-rendered templates were rejected in favour of a standalone SPA communicating through a REST API. This decision is justified by the fact that the system serves two distinct user experiences—a customer-oriented storefront and a vendor-oriented management interface—that share the same backend logic. A shared API ensures consistency across both interfaces while keeping each free to evolve its own navigation and layout.

**Per-device token authentication.** A custom `DeviceToken` model was chosen over traditional session-based authentication. Each login creates an independent token, so signing out on one browser does not invalidate sessions on other devices. This is a practical benefit for users who access the marketplace from multiple devices.

**Server-side pagination.** Product listings are returned in pages of eight items. Pagination reduces the volume of data transferred per request and directly supports the specification requirement that customers should have suitable controls for navigating large product lists.

**Centralised analytics and notification logic.** Behavioural data—product views, search queries, wishlist additions, and cart events—are recorded and processed on the server. Keeping these computations server-side ensures that analytics reports and discount recommendations are derived from a complete and trustworthy event history rather than from client-reported data alone.

## 4.2 Data Modelling

### 4.2.1 Overview of the Data Model

The database design is centred on a multi-vendor marketplace. The main entities are users, customers, vendors, stores, products, carts, orders, wishlists, promotions, reviews, notifications, and analytics logs. The design separates business identities cleanly: customers buy products, vendors own stores, stores own products, and orders are created per store during checkout.

Figure 4.2 should be inserted here to show the entity relationship diagram of the database.

Suggested caption: Figure 4.2. Entity relationship diagram of the Online Shipping System Project database.

The main entities and relationships are as follows:

- `User` is the authentication root provided by Django.
- `Customer` extends `User` with shipping address, phone number, and profile image.
- `Vendor` extends `User` with contact information and profile image.
- `Store` is in a one-to-one relationship with `Vendor`, meaning each vendor manages one store in the current design.
- `StorePhoto` is in a one-to-many relationship with `Store` so each store can have multiple photos.
- `Product` belongs to one `Store`, one optional `Brand`, and one optional `Category`.
- `ProductMedia` is in a one-to-many relationship with `Product`, allowing multiple product images.
- `CartItem` links `Customer` and `Product` and stores quantity.
- `Order` belongs to one `Customer` and stores shipping and status information.
- `OrderItem` belongs to one `Order` and one product reference, and stores a snapshot of product name, quantity, and paid price.
- `WishlistItem` links `Customer` and `Product` and stores price-related information at the time of addition.
- `Promotion` belongs to one `Product` and stores discount percentage and active dates.
- `Review` belongs to one `OrderItem`, while `ReviewMedia` stores optional media for the review.
- `Notification` belongs to one `User` and may also reference an order or product.
- `SearchQuery` and `ProductView` record behavioural events used for analytics.

### 4.2.2 Important Modelling Decisions

The data model includes several deliberate design decisions to preserve history, enforce useful constraints, and support future analysis.

#### Snapshotting order details

The `OrderItem` entity stores `productName` and `paidPrice` directly instead of depending only on the current `Product` record. This is an important design decision because product names, prices, and promotions may change after purchase. By snapshotting the purchased name and price inside the order item, the system preserves the commercial meaning of a completed transaction.

#### Splitting orders by vendor

The specification requires that a purchase order contains products from one shop only. This is supported by the data model and the checkout logic together. The cart can contain items from many stores, but the server groups the cart items by vendor and creates separate `Order` records. Therefore, the database remains consistent with the marketplace rule that each order belongs to a single vendor context.

#### Representing many-to-many relationships

Several many-to-many business relationships are implemented through associative entities rather than direct many-to-many fields.

- Customer-to-product in cart is modelled through `CartItem`.
- Customer-to-product in wishlist is modelled through `WishlistItem`.
- Order-to-product is modelled through `OrderItem`.

This approach is necessary because each relationship stores extra attributes. For example, cart items and order items need quantity, while order items also need the paid price and product snapshot.

#### Media storage design

The project stores image file paths in the database and the actual image files in the media directory on the server. This design is used for:

- customer profile images;
- vendor profile images;
- store photos;
- product media;
- review media.

The database therefore stores references to files rather than binary blobs. This keeps the relational data model simpler and allows the frontend to load media through URLs.

### 4.2.3 Integrity Constraints and Business Rules

The model enforces several useful constraints.

- `CartItem` uses a unique constraint on customer and product, so the same product cannot appear multiple times as separate cart rows for one customer.
- `WishlistItem` uses a unique constraint on customer and product, so a customer can only wishlist a product once.
- `Store` uses a unique combination of vendor and store name.
- `Review` uses a one-to-one relationship with `OrderItem`, which prevents multiple reviews on the same purchased line item.

In addition to the database-level constraints, some rules are enforced in application logic.

- A customer is prevented from reviewing the same product more than once, even if they bought it multiple times, because the review creation logic checks for an existing review by that customer on that product.
- A refund request requires a reason and is handled as a separate flag (`refundRequest`) and explanation (`refundReason`) on the order.
- Promotions are treated as active only when both their status and date range are valid.

This mixture of database constraints and server-side validation is appropriate because some rules are structural while others are workflow-dependent.

### 4.2.4 Order Status and Transaction Tracking

The `Order` table tracks the progress of fulfilment through four main statuses:

- `Pending`
- `Holding`
- `Shipped`
- `Cancelled`

The order record also stores:

- total amount;
- shipping name, phone number, and address snapshot;
- cancellation reason;
- refund request flag and refund reason;
- a general `statusUpdatedDate` timestamp.

This design is sufficient for the required order processing workflow. It is simple and easy to query for customer and vendor interfaces. The main limitation is that the system records a general status update timestamp rather than dedicated timestamps such as shipped date, cancelled date, or refund date. For the scale of this project, the simpler model is acceptable, but a production design would likely use a separate order-status history table.

### 4.2.5 Imported and Exported Data Formats

The system uses two main data exchange formats.

- JSON is used for most API requests and responses between the React frontend and the Django backend.
- `multipart/form-data` is used when uploading files such as profile images, product images, store photos, and review images.

The exported data displayed to users is generated dynamically through API responses rather than through downloadable reports. Vendor analytics are returned as structured JSON objects that the frontend converts into visual charts and summary panels.

## 4.3 Dynamic Modelling

### 4.3.1 Customer Checkout Activity

The most important dynamic process in the system is the checkout flow. It begins when a customer has added products to the cart and ends when one or more vendor-specific orders have been created.

Figure 4.3 should be inserted here to show the activity diagram for the customer checkout workflow.

Suggested caption: Figure 4.3. Activity diagram of the customer checkout and split-order workflow.

The typical sequence is as follows:

1. The customer browses products and adds selected items to the shopping cart.
2. The customer opens the cart and chooses which items to check out.
3. The frontend sends the selected product IDs and shipping details to the backend.
4. The backend validates the customer identity and shipping data.
5. The backend groups the chosen cart items by vendor.
6. For each vendor group, the backend creates one order and multiple order items.
7. For each order item, the backend calculates the effective paid price, applying any active promotion.
8. The backend reduces the available product quantity.
9. The backend calculates the total order amount and saves the order.
10. The checked-out cart items are removed from the cart.
11. A notification is created for each vendor who received a new order.
12. The frontend shows the successful purchase result to the customer.

This process is a good example of why checkout logic belongs on the server side. If grouping by vendor, price calculation, stock deduction, and notification creation were performed only in the client, data consistency would be much harder to guarantee.

The auxiliary path occurs when the customer checks out only part of the cart. In that case, only the selected items are converted into orders, while the rest remain in the cart. This directly implements the split-order requirement.

### 4.3.2 Order State Changes

The order lifecycle is controlled through explicit status changes.

Figure 4.4 should be inserted here to show the order state diagram.

Suggested caption: Figure 4.4. State diagram for purchase order processing.

The main permissible transitions are:

- checkout creates a new order in `Pending`;
- the vendor may change `Pending` to `Holding`;
- the vendor may change `Pending` to `Shipped`;
- the vendor may change `Pending` to `Cancelled`;
- the vendor may change `Holding` to `Shipped` after resolving issues;
- the vendor may change `Holding` to `Cancelled`;
- the customer may cancel an order before shipment, which also moves the order to `Cancelled`.

Whenever the vendor changes the order state to `Holding`, `Shipped`, or `Cancelled`, the system creates a notification for the customer. This ensures that the dynamic model is not limited to database state alone; the system also reacts by informing the user.

The refund process is modelled as a side process rather than a separate top-level state. A customer may request a refund for a pending or held order by setting the refund request flag and storing a reason. The vendor can then approve or reject that request. This design is simpler than adding more main order states, although a more advanced implementation could model refund handling as a dedicated state machine.

### 4.3.3 Review and Promotion Interaction

Another important dynamic aspect of the system involves post-purchase feedback and promotional engagement.

After a product has been purchased and delivered, the product detail endpoint checks whether the authenticated customer has bought the product in a shipped order and whether that customer has already reviewed it. If the product is eligible for review, the frontend allows the customer to submit one review with optional media. This creates a controlled feedback loop that supports trust without allowing unlimited duplicate reviews.

Promotions trigger another dynamic flow. When a vendor creates an active promotion for a product, the server checks whether that product appears in any customers' wishlists. If it does, the backend creates notifications for those customers to inform them that the item is on sale. This interaction links together product pricing, wishlists, and notifications in one event-driven behaviour.

Figure 4.5 should be inserted here to show a sequence diagram for promotion-triggered wishlist notifications.

Suggested caption: Figure 4.5. Sequence diagram for creating a promotion and notifying interested customers.

### 4.3.4 Notification Behaviour

Notifications are generated when specific events occur, rather than by continuous background messaging. This helps avoid unnecessary noise while still supporting user awareness.

The current notification events include:

- new order received by a vendor;
- refund request sent to a vendor;
- wishlist item placed on sale for a customer;
- order shipped for a customer;
- order put on hold for a customer;
- order cancelled for a customer;
- refund approved or rejected for a customer.

The frontend periodically polls the unread notification count and displays it as a badge in the navigation bar. When the user opens the notification panel, notifications can be marked as read and linked directly to the related product or order screen. This design balances simplicity and usability without introducing the complexity of WebSocket-based real-time messaging.

## 4.4 Mobile UX Design and Client-Server Interaction

### 4.4.1 Mobile-Friendly User Interface

The project was designed to work well on both desktop and mobile screens. This is especially important because browsing a fashion catalogue is a visually intensive task that often happens on mobile devices.

The interface includes the following mobile-oriented design decisions:

- a mobile drawer menu for navigation when horizontal space is limited;
- inline mobile search in the drawer to reduce navigation steps;
- swipe-enabled image galleries for product and store images;
- responsive product grids and cards;
- compact notification and cart badges;
- persistent dark mode stored in local storage.

These decisions are suitable because customers typically browse products, compare images, and react to promotions on smaller screens. A mobile-friendly layout therefore directly supports the business goals of browsing, discovery, and conversion.

Figure 4.6 should be inserted here to show the desktop and mobile navigation layouts.

Suggested caption: Figure 4.6. Responsive navigation design for desktop and mobile views.

### 4.4.2 Accessibility and Web Standards

The frontend uses standard browser technologies such as semantic links, buttons, image tags, and RESTful HTTP communication. Several interface elements also include explicit accessibility labels, for example the dark mode toggle and navigation buttons. These choices help screen readers and improve clarity of interactive controls.

The frontend also avoids overloading the user with hidden state. Badges clearly indicate unread notifications and cart item counts, while modal overlays are used for focused tasks such as search and notifications. This gives the user stronger visual feedback and reduces confusion.

### 4.4.3 Client-Server Protocol Considerations

The communication protocol between the frontend and backend is based on HTTP requests with JSON payloads for ordinary data and `multipart/form-data` for image uploads. Authentication tokens are sent in the `Authorization` header using the `Token <key>` format.

This protocol design is appropriate because:

- JSON is lightweight and easy for React and DRF to exchange;
- `multipart/form-data` is a standard solution for mixed text-and-file submissions;
- token headers allow the same API to be used from multiple devices or sessions;
- the same protocol supports both desktop and mobile browsers without needing a separate backend.

The application also uses paginated API responses for product listing. This reduces payload size and prevents long catalogue pages from becoming slow or unwieldy on smaller devices and slower networks.

## 4.5 Web Application Security and Data Management

### 4.5.1 Authentication and Authorisation Design

The system uses Django's user model for identity management and password hashing, while application-level profile data is stored in the `Customer` and `Vendor` models. After successful login, the backend creates a device token and returns it to the frontend. The token is then attached to future API requests.

Authorisation is enforced primarily in the backend. Examples include:

- only authenticated customers can access cart, wishlist, order, and review creation features;
- only the owner of a customer account can view that customer's orders;
- only the owner of a vendor account can manage that vendor's store, products, orders, and promotions.

This design is important because client-side route hiding alone is not sufficient protection. The backend must check ownership before reading or changing sensitive records.

### 4.5.2 Input Validation and Business Safety

The backend applies validation in serializers and model fields. Examples include:

- validating unique usernames and email addresses;
- requiring current password confirmation before changing email;
- enforcing minimum lengths for passwords;
- validating numeric bounds for price, quantity, rating, and discount values;
- requiring reasons for cancellation and refund requests in the relevant workflows.

These checks reduce the likelihood of inconsistent data and accidental misuse. They also help mitigate common problems such as invalid form submissions and inappropriate state changes.

### 4.5.3 Data Security and Backup Considerations

The project stores structured data in SQLite and uploaded media in the local media folder. For development and coursework purposes this is sufficient, but the design has clear operational implications.

- database backup should include the SQLite database file;
- media backup should include the entire media directory;
- both should be versioned or archived together to preserve referential consistency between media paths and database records.

In a production deployment, the design should be strengthened by:

- moving the secret key out of source code into environment variables;
- disabling debug mode;
- using HTTPS only;
- narrowing CORS configuration to trusted origins;
- moving from SQLite to a production database engine;
- moving media files to durable external storage.

These are not changes to the logical design of the system, but they are important deployment considerations for a real marketplace environment.

## 4.6 Analytics and Product Recommendation Design

### 4.6.1 Analytics Data Collection

The system includes an analytics design intended for vendor decision support. Two event entities are particularly important:

- `SearchQuery`, which records search text and result count;
- `ProductView`, which records product detail views, the user if available, the source of the visit, and the originating search query when relevant.

These event logs allow the system to generate behavioural summaries beyond completed purchases alone. This is important because customer interest is often visible before conversion.

### 4.6.2 Vendor Analytics Dashboard

The vendor analytics endpoint computes a structured summary that includes:

- total views;
- total orders;
- total revenue;
- total products;
- top search queries;
- top viewed products;
- view trends over time;
- revenue by day;
- category breakdown of views;
- conversion funnel from views to cart additions to orders;
- top wishlisted products.

This design is useful because it combines sales data with behavioural signals. A vendor can therefore see not only what sold, but also what attracted interest without converting.

Figure 4.7 should be inserted here to show the analytics dashboard layout.

Suggested caption: Figure 4.7. Vendor analytics dashboard showing sales and behavioural metrics.

### 4.6.3 Recommended Discount Algorithm

The system also includes a simple recommendation algorithm for suggesting which products may benefit from a discount. The logic scores products using the following factors:

$$
\text{score} = 1(\text{views}) + 3(\text{wishlists}) + 2(\text{cart adds}) - 5(\text{orders})
$$

Products with high interest but relatively low completed orders receive higher scores. Products that already have active promotions are excluded from recommendation.

This is a reasonable design for the project because it is transparent, lightweight, and easy to explain. It does not require machine learning infrastructure, but it still turns user behaviour into a practical vendor recommendation. The weighting also reflects a sensible business intuition: wishlist additions and cart additions are stronger purchase signals than simple page views, while completed orders reduce the need for further discount encouragement.

## 4.7 Summary

The system design of this project is built around a clear separation between interface, application logic, and persistence. The architectural design supports both customers and vendors through a shared API and role-specific interfaces. The data model captures the main marketplace entities while preserving important commercial history such as paid prices and order contents. The dynamic design supports split checkout, order state changes, notifications, and promotional engagement. Additional design work on responsive UX, analytics, and lightweight recommendation logic extends the system beyond basic e-commerce functionality and makes the implementation suitable for a multi-vendor fashion marketplace.

## References

1. Django Software Foundation. Django documentation. Available at: https://docs.djangoproject.com/
2. Encode OSS Ltd. Django REST framework documentation. Available at: https://www.django-rest-framework.org/
3. React. React documentation. Available at: https://react.dev/
4. Vite. Vite documentation. Available at: https://vite.dev/
5. Material UI. Material UI documentation. Available at: https://mui.com/
6. SQLite. SQLite documentation. Available at: https://www.sqlite.org/docs.html
