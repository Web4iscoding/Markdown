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

The database is designed around the concept of a multi-vendor marketplace and currently contains eighteen entity tables. At the highest level, the schema distinguishes between three groups of entities: identity and store management (users, customers, vendors, stores), commercial transactions (products, cart items, orders, wishlists, promotions), and engagement and analytics (reviews, notifications, search queries, product views). This separation keeps business roles clear: customers place orders, vendors own stores, stores contain products, and each order is scoped to a single store.

Figure 4.2 should be inserted here to show the entity relationship diagram of the database.

Suggested caption: Figure 4.2. Entity relationship diagram of the Online Shipping System Project database.

The principal entities and their relationships are listed below.

- `User` is the authentication root provided by Django's built-in user model.
- `Customer` extends `User` through a one-to-one link and adds first name, last name, phone number, three shipping-address fields, and an optional profile image.
- `Vendor` extends `User` in the same manner and adds first name, last name, phone number, and an optional profile image.
- `Store` has a one-to-one relationship with `Vendor`, so each vendor manages exactly one store in the current design.
- `StorePhoto` has a many-to-one relationship with `Store`, allowing a store to display multiple ordered photographs.
- `Category` and `Brand` are independent reference tables; `Category` names are unique, and `Brand` may carry an optional logo image.
- `Product` belongs to one `Store` and optionally references one `Brand` and one `Category`. Deleting a brand or category sets the reference to null rather than cascading.
- `ProductMedia` has a many-to-one relationship with `Product` and supports both image and video media types, each with a sort order and an optional primary flag.
- `CartItem` is an associative entity linking `Customer` and `Product`, storing the selected quantity.
- `Order` belongs to one `Customer` and captures shipping details, total amount, fulfilment status, and optional refund information.
- `OrderItem` belongs to one `Order` and references one `Product`. It stores a snapshot of the product name, quantity, and paid price so that the order record remains meaningful even if the product later changes.
- `WishlistItem` is an associative entity linking `Customer` and `Product`, recording the original price, discount rate, and effective price at the time of addition.
- `Promotion` belongs to one `Product` and stores a discount percentage, start and end dates, an active/inactive status flag, and a flag indicating whether a notification has been sent.
- `Review` has a one-to-one relationship with `OrderItem`, while `ReviewMedia` stores optional image or video attachments for the review.
- `Notification` belongs to one `User` and may optionally reference an `Order` or a `Product` for contextual linking.
- `SearchQuery` and `ProductView` are event-log entities that record search strings with result counts and product-detail visits with source attribution, respectively.

### 4.2.2 Important Modelling Decisions

Several deliberate design choices shape the data model. Each one addresses a specific requirement—preserving commercial history, maintaining marketplace rules, accommodating extra relationship attributes, or handling uploaded media.

#### Snapshotting order details

`OrderItem` stores the `productName` and `paidPrice` as independent columns rather than relying solely on the current `Product` record. This is necessary because product names, base prices, and promotions can all change after a purchase has been made. By capturing the name and the effective price at the moment of sale, the system preserves an accurate historical record of every completed transaction.

#### Splitting orders by vendor

The project specification requires that each purchase order contains products from only one shop. The data model and the checkout logic enforce this rule together: a shopping cart may reference products from multiple stores, but the server groups cart items by vendor and creates a separate `Order` for each group. As a result, every `Order` row in the database is associated with a single vendor's store, and the marketplace constraint is satisfied at the data level.

#### Representing many-to-many relationships through associative entities

Three conceptually many-to-many relationships are modelled with explicit associative entities rather than Django's built-in `ManyToManyField`.

- `CartItem` links `Customer` and `Product`, storing the selected quantity.
- `WishlistItem` links `Customer` and `Product`, storing the original price, discount rate, and effective price at the time of addition.
- `OrderItem` links `Order` and `Product`, storing the product name snapshot, quantity, and paid price.

Associative entities are required here because each relationship carries domain-specific attributes that a bare join table could not accommodate.

#### Media storage design

Uploaded files are stored on the server's local file system, and the database records only the file path. The following entities use Django's `ImageField` for this purpose:

- `Customer` — optional profile image;
- `Vendor` — optional profile image;
- `Brand` — optional logo image;
- `StorePhoto` — ordered store photographs;
- `ProductMedia` — product images or videos;
- `ReviewMedia` — review images or videos.

Storing references rather than binary blobs keeps the relational schema simpler and allows the frontend to retrieve media directly through URL paths served by Django's media handler.

### 4.2.3 Integrity Constraints and Business Rules

Data integrity is maintained through a combination of database-level constraints and application-level validation. The database enforces the following structural rules.

- `CartItem` carries a unique-together constraint on customer and product, preventing duplicate cart rows for the same customer–product pair.
- `WishlistItem` carries an equivalent unique-together constraint, ensuring a customer can wishlist a given product only once.
- `Store` enforces a unique combination of vendor and store name, so a vendor cannot create two stores with the same name.
- `Review` uses a one-to-one relationship with `OrderItem`, making it structurally impossible to attach more than one review to the same purchased line item.

Beyond these schema-level constraints, several business rules are enforced in the server-side application logic.

- A customer may not review the same product more than once. Even if the customer purchased the product in multiple separate orders, the review-creation endpoint queries for any existing review by that customer on that product and rejects duplicates.
- A refund request requires a non-empty reason string. The request is recorded through a boolean flag (`refundRequest`) and a text field (`refundReason`) on the `Order` entity.
- A promotion is considered active only when its status field is set to `Active` and the current date falls within the promotion's start and end dates.

This layered approach is appropriate because structural uniqueness is best guaranteed by the database engine, whereas workflow-dependent rules—such as cross-order review deduplication or date-sensitive promotion activation—require logic that goes beyond what declarative constraints can express.

### 4.2.4 Order Status and Transaction Tracking

Fulfilment progress is tracked through a status field on the `Order` entity with four permitted values:

- `Pending` — the initial state assigned at checkout;
- `Holding` — the vendor has placed the order on hold;
- `Shipped` — the vendor has dispatched the order;
- `Cancelled` — the order has been cancelled by either party.

Alongside the status, each order record stores the following transactional data:

- total monetary amount;
- the customer's shipping name (first and last), phone number, and three address lines, captured at the time of checkout;
- an optional cancellation reason;
- a refund-request boolean flag and an optional refund-reason text;
- a `statusUpdatedDate` timestamp that is automatically refreshed whenever the record is saved.

This flat-status design is straightforward to implement and query from both the customer and vendor interfaces. Its main limitation is that only the most recent status change is timestamped; the model does not maintain a full history of transitions. For the scope of this coursework project the trade-off is acceptable, but a production system would benefit from a dedicated order-status-history table that records every transition with its own timestamp and actor.

### 4.2.5 Imported and Exported Data Formats

Two data-exchange formats are used between the React frontend and the Django backend.

- **JSON** is the default format for both requests and responses. Structured payloads such as product listings, order details, and analytics summaries are serialised as JSON.
- **`multipart/form-data`** is used whenever a request includes file uploads—for example, profile images, product media, store photos, brand logos, and review attachments.

The system does not produce downloadable reports. All data presented to users—including vendor analytics charts and sales summaries—is generated dynamically through API responses and rendered by the frontend.

## 4.3 Dynamic Modelling

### 4.3.1 Customer Checkout Activity

The checkout flow is the most important dynamic process in the system. It begins when a customer has placed one or more products in the shopping cart and ends when the server has created one or more vendor-specific orders, deducted stock, and notified the relevant vendors.

Figure 4.3 should be inserted here to show the activity diagram for the customer checkout workflow.

Suggested caption: Figure 4.3. Activity diagram of the customer checkout and split-order workflow.

The sequence proceeds as follows.

1. The customer browses products and adds selected items to the shopping cart.
2. The customer opens the cart, selects the items to purchase, and enters or confirms shipping details.
3. The frontend sends the selected product IDs together with the shipping name, phone number, and address fields to the backend.
4. The backend validates the customer identity and verifies that the shipping address fields are present.
5. The backend groups the selected cart items by the vendor who owns each product's store.
6. For each vendor group the backend creates a new `Order` record along with one `OrderItem` per product.
7. For each order item the backend checks whether an active promotion exists for the product and, if so, applies the discount percentage to compute the paid price; otherwise the full product price is used.
8. The backend decrements the product's stock quantity by the ordered amount.
9. The backend sums the line-item totals, stores the result as the order's total amount, and saves the order.
10. All checked-out cart items are deleted from the cart.
11. A `new_order` notification is created for each vendor who received an order.
12. The backend returns the list of created orders to the frontend, which displays the purchase confirmation to the customer.

This sequence illustrates why checkout logic must reside on the server: vendor grouping, price calculation, stock deduction, and notification creation all depend on authoritative data and must execute as a single consistent operation. Performing these steps in the client would expose the system to race conditions and data inconsistency.

An important variant occurs when the customer selects only a subset of the cart for checkout. In that case the frontend passes an explicit list of product IDs, and only the matching cart items are processed; the remaining items stay in the cart for a future purchase. This partial-checkout path directly satisfies the split-order requirement defined in the specification.

### 4.3.2 Order State Changes

After an order is created in the `Pending` state, its lifecycle is governed by status updates issued through two separate endpoints: one for vendors and one for customers.

Figure 4.4 should be inserted here to show the order state diagram.

Suggested caption: Figure 4.4. State diagram for purchase order processing.

**Vendor-initiated transitions.** The vendor can set a new status for any order that contains items from the vendor's store. The backend accepts four status values—`Pending`, `Holding`, `Shipped`, and `Cancelled`—and does not enforce guards on the previous state, so the vendor bears responsibility for following the intended progression. The expected workflow is:

- `Pending` → `Holding`, when the vendor needs to pause fulfilment;
- `Pending` → `Shipped`, for straightforward dispatch;
- `Pending` → `Cancelled`, when the vendor cannot fulfil the order (a reason is required);
- `Holding` → `Shipped`, once the issue has been resolved;
- `Holding` → `Cancelled`, if the issue cannot be resolved.

Whenever the vendor sets the status to `Holding`, `Shipped`, or `Cancelled`, the backend creates a notification for the customer indicating the change.

**Customer-initiated cancellation.** The customer can cancel any of their own orders through a dedicated cancellation endpoint. The current implementation does not restrict cancellation by order status, so the customer can submit a cancellation request regardless of whether the order is pending, on hold, or already shipped. A cancellation reason is stored alongside the status change. Unlike vendor-initiated status changes, customer cancellation does not automatically generate a notification for the vendor.

**Refund side-process.** Rather than introducing additional top-level states, refund handling is modelled as a flag on the order. A customer may request a refund for an order whose status is `Pending` or `Holding` by providing a reason; the backend then sets the `refundRequest` flag and stores the reason text. The vendor can subsequently approve or reject the request, and a corresponding notification is sent to the customer in either case. This design keeps the primary state machine simple, although a more advanced implementation could model refund handling as a dedicated state machine with its own transitions.

### 4.3.3 Review and Promotion Interaction

Two additional event-driven flows extend the system beyond basic order processing: post-purchase reviewing and promotion-triggered wishlist notifications.

**Review eligibility.** When an authenticated customer views a product detail page, the backend determines whether that customer has received the product in a shipped order and whether the customer has already submitted a review for that product. If both conditions are met—a shipped purchase exists and no prior review is found—the response includes the eligible order-item identifier, and the frontend enables the review form. The customer may then submit a single review with an optional set of media attachments. This mechanism creates a controlled feedback loop: reviews are tied to verified purchases, and each customer can review a given product only once regardless of how many times it was ordered.

**Promotion and wishlist notification.** When a vendor creates a promotion whose date range makes it immediately active, the backend queries the `WishlistItem` table for all customers who have wishlisted the promoted product. For each match it creates a `wishlist_sale` notification informing the customer that the item is now discounted. If the promotion is created with a future start date and is therefore inactive at the time of creation, no notifications are sent at that point; notifications are issued only when the promotion is active at the moment it is saved. This interaction links product pricing, wishlists, and the notification system in a single event-driven behaviour.

Figure 4.5 should be inserted here to show a sequence diagram for promotion-triggered wishlist notifications.

Suggested caption: Figure 4.5. Sequence diagram for creating a promotion and notifying interested customers.

### 4.3.4 Notification Behaviour

Notifications are generated in response to discrete events rather than through continuous background messaging. This event-driven design avoids unnecessary noise while keeping both customers and vendors informed of important state changes.

The system currently recognises the following notification events:

- **new order** — sent to the vendor when a customer completes checkout;
- **refund request** — sent to the vendor when a customer requests a refund;
- **wishlist sale** — sent to a customer when a wishlisted product receives an active promotion;
- **order shipped** — sent to a customer when the vendor marks the order as shipped;
- **order on hold** — sent to a customer when the vendor places the order on hold;
- **order cancelled** — sent to a customer when the vendor cancels the order;
- **refund approved** or **refund rejected** — sent to a customer after the vendor responds to a refund request.

On the frontend, the navigation component polls the server for the unread-notification count every thirty seconds and renders the result as a badge on the notification icon. When the user opens the notification panel, individual notifications can be marked as read; each notification may also carry a deep link to the related order or product screen. This polling-based approach balances responsiveness and simplicity without introducing the infrastructure overhead of WebSocket-based real-time messaging.

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
