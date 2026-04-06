# Chapter 4: System Design

This chapter presents the system design of the Online Shipping System Project and explains how the implementation satisfies the requirements analysed in Chapter 3. The discussion starts with the high-level architecture of the complete system, then moves into the underlying data model and the dynamic behaviour of key business processes such as checkout, order processing, and notifications. The later sections focus on design areas that are especially important for this project, namely mobile-friendly user experience, client-server communication, security and data management, and vendor-facing analytics and recommendations. Together, these sections describe one coherent marketplace system in which a React storefront and administration portal interact with a Django REST backend and a relational database to support customers, vendors, products, orders, promotions, and user engagement features.

## 4.1 Architectural Design

### 4.1.1 Overall System Architecture

The project follows a three-tier web application architecture consisting of a presentation layer, an application layer, and a data layer.

- The presentation layer is implemented as a React single-page application (SPA) built with Vite.
- The application layer is implemented with Django and Django REST Framework (DRF), which expose REST-style API endpoints for authentication, products, carts, orders, wishlists, promotions, reviews, analytics, and notifications.
- The data layer is implemented with SQLite for structured application data and the local media directory for uploaded images such as customer profile images, vendor profile images, store photos, product media, and review media.

The architectural separation is suitable for this project because the customer storefront and vendor administration portal share the same backend business logic but require different user interfaces. By exposing business functions through Web APIs, the system keeps the frontend flexible while centralising validation, authentication, order processing, and data access on the server side.

Figure 4.1 should be inserted here to show the overall architecture of the system.

Suggested caption: Figure 4.1. High-level architecture of the Online Shipping System Project.

At runtime, the interaction is as follows:

1. A customer or vendor uses the browser-based React client.
2. The React client sends HTTP requests to the Django backend through the API layer.
3. The backend authenticates the request, validates the data, executes business rules, and reads or updates the database.
4. When media files are involved, Django stores them in the server-side media folder and returns the media URLs to the frontend.
5. The frontend renders the returned JSON data into catalogue pages, product pages, carts, order screens, vendor dashboards, and notification interfaces.

No stored procedures are used in the database. All business logic is implemented in the Django application layer, especially in serializers and API views. This design keeps the logic in one place and reduces coupling between the database engine and the application behaviour.

### 4.1.2 Client-Side Design

The frontend is implemented in React 19 with React Router for route-based navigation. Although the application is delivered through a browser, it behaves like a client application because page transitions such as product browsing, viewing product detail, opening the cart, checking order history, or switching to vendor screens are handled on the client side without full page reloads.

The React frontend contains both public and authenticated routes. Public routes support browsing products, stores, categories, brands, reviews, and the contact page. Authenticated routes provide customer functions such as cart, checkout, wishlist, profile management, review submission, and order history, as well as vendor functions such as product catalogue management, store management, order management, discounts, and analytics.

This design has several benefits:

- it provides a smoother browsing experience for catalogue-style interaction;
- it allows the same API to serve both customer and vendor interfaces;
- it supports responsive and mobile-specific UI behaviour more easily than server-rendered page transitions;
- it keeps the user interface logic separate from business rules.

### 4.1.3 Server-Side Design

The backend is implemented with Django 5.2 and Django REST Framework 3.16. The API is organised around resource-oriented view sets and API views. Important backend modules include the following:

- authentication views for customer registration, vendor registration, login, logout, profile updates, and password changes;
- product, brand, category, and store endpoints for catalogue browsing;
- cart and order endpoints for the customer purchase flow;
- vendor-specific endpoints for order processing, product management, promotions, and analytics;
- review and wishlist endpoints for engagement features;
- notification endpoints for user updates.

The server applies most business rules centrally. Examples include:

- splitting cart items into separate orders by vendor during checkout;
- applying active promotions when calculating the paid price of an order item;
- reducing stock quantity when an order is created;
- enforcing ownership rules so a vendor can only manage products and orders belonging to that vendor's store;
- creating notifications when significant events occur, such as new orders, order status changes, refund requests, and wishlist sale events.

This backend-centred design is appropriate because order processing and data integrity should not depend on client-side behaviour alone.

### 4.1.4 Development Tools and Environment

Table 4.1 should be inserted here to summarise the development tools and environment.

Suggested caption: Table 4.1. Main technologies used in the Online Shipping System Project.

The main technologies used in the project are summarised below.

| Layer | Technology | Role in the system | Reason for selection |
| --- | --- | --- | --- |
| Frontend | React 19 | Component-based user interface | Suitable for reusable UI components, client-side routing, and responsive interactive pages |
| Frontend build tool | Vite 7 | Development server and bundler | Fast startup and simple development workflow |
| Frontend UI libraries | Material UI, MUI X Charts, custom CSS | Navigation, layout helpers, analytics charts, visual components | Accelerates UI development while allowing custom styling |
| Backend | Django 5.2 | Web framework and application backbone | Mature framework with authentication, ORM, media support, and admin tools |
| Web API | Django REST Framework 3.16 | JSON API layer | Clean serializer and view set model for SPA-to-server communication |
| Authentication | Custom DeviceToken authentication | Multi-device token login | Supports separate tokens per device or browser session |
| Database | SQLite | Persistent relational data storage | Simple setup and suitable for coursework-scale deployment |
| Media processing | Pillow | Image handling for uploads | Standard support for Django image fields |

The use of SQLite is reasonable for this project because the application is intended as a development and demonstration system rather than a high-concurrency production marketplace. SQLite reduces deployment complexity and makes setup simpler for development and testing. However, the data model is relational and portable, so the design could be migrated to a production-grade database such as PostgreSQL with relatively limited structural change.

Uploaded media files are managed through Django ImageField definitions and stored under the backend media directory. This choice is practical for development because it avoids introducing cloud storage complexity too early. The trade-off is that local file storage is not ideal for distributed or large-scale deployment, so an object storage service would be preferable in a production environment.

### 4.1.5 Architectural Rationale

Several technical choices were made for clear design reasons.

First, the project uses a decoupled frontend and backend instead of server-rendered templates. This is justified because the system serves two quite different experiences: a customer-facing shopping interface and a vendor-facing administration interface. A shared API makes it easier to support both roles consistently.

Second, a token-based authentication mechanism was chosen over session-only authentication for the main client-server interaction. The custom DeviceToken model allows each login session to create a separate token, which means logging out on one device does not automatically invalidate all other devices. This is especially useful when customers or vendors access the system from more than one browser or device.

Third, the backend exposes paginated product data. This design decision improves performance and user experience for long catalogues and aligns with the requirement that customers should have practical controls for browsing large product lists.

Fourth, the architecture keeps analytics, discount recommendation logic, and notification generation on the server side. These processes depend on reliable event histories such as product views, search terms, wishlist records, order items, and cart additions, which are more trustworthy when captured and processed centrally.

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
