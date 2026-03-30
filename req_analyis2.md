# Chapter 3: Requirements Analysis

This chapter explains how the requirements of the Online Shipping System Project were analysed and translated into implemented features. The project was developed as a multi-vendor fashion e-commerce platform in which customers browse products and place orders in the storefront, while vendors manage catalogues, promotions, stores, and order processing through a separate administration interface. The analysis focuses on the chosen system specification blocks, the needs of the main user groups, and the extent to which the selected requirements have been fulfilled.

## 3.1 Analysis Scheme and Methods

### 3.1.1 Product Domain and Project Scope

The project targets the online fashion retail domain. Compared with a general-purpose e-commerce site, a fashion marketplace places stronger emphasis on product presentation, shop identity, visual trust, and repeat engagement. Customers usually compare items by image quality, brand, category, price, and reviews. Vendors need tools not only to upload products, but also to present their own shop image, manage orders, and run promotions.

Based on this product domain, the team selected the following requirement blocks from the system specification:

- Block A: Core functions
- Block B: Multiple photos and order processing
- Block F: Multiple vendors
- Block T: User generated content
- Block U: Wish list and promotional pricing strategy
- Block X: User experience design

These blocks were selected because they fit the intended marketplace model and because together they form a coherent user journey: discover products, evaluate them visually, save or buy them, receive updates, and interact with vendors in a multi-store environment.

### 3.1.2 Requirement Elicitation Approach

The requirements were identified through a combination of reference study, feature comparison, and iterative implementation review.

First, common patterns of modern e-commerce systems were studied from established platforms such as Shopee, Lazada, Amazon, and fashion-oriented marketplaces. The purpose was not to copy an existing system directly, but to identify the common expectations users already have when shopping online. Examples include product search, product cards with thumbnails, order history, customer reviews, and discount-driven re-engagement.

Second, the team mapped those common patterns to the specific needs of a multi-vendor fashion marketplace. This led to design decisions such as the following:

- multiple images per product were treated as essential rather than optional because fashion products are judged visually;
- a separate storefront was created for each vendor because shop identity matters in a marketplace setting;
- orders are split by shop during checkout so that each vendor only manages their own fulfilment;
- review media and wishlist notifications were prioritised because they support trust and return visits;
- responsive navigation, dark mode, and mobile-friendly product interaction were included to improve usability across devices.

Third, the team compared the implemented system against the chosen specification blocks and recorded which items were fully implemented, partially implemented, or adapted. This final comparison is summarised in Section 3.3.

### 3.1.3 Reference Systems and Adaptation

Several reference systems informed the requirement analysis and interface decisions.

| Reference system | Relevant ideas adopted and adapted |
| --- | --- |
| Shopee and Lazada | multi-vendor structure, shop pages, split orders by seller, storefront browsing, promotion-driven engagement |
| Amazon | product reviews, wishlist behaviour, order history, status tracking, rating visibility |
| Fashion-oriented online stores | image-first product presentation, stronger emphasis on brand and category navigation, visual storefront identity |

The implemented system simplifies some of these ideas to match the project scope. For example, the project supports product and order management, promotions, notifications, and reviews, but it does not implement payment gateway integration, live chat, or courier tracking. The goal was to focus on the chosen specification blocks and to deliver a coherent end-to-end marketplace workflow.

### 3.1.4 Requirements Beyond the Current Scope

During analysis, some desirable features were identified but not prioritised for this iteration. Examples include external payment processing, live messaging between customer and vendor, and more detailed shipment tracking. These were considered lower priority than the selected blocks because they are not necessary to demonstrate the core marketplace workflow required by this project.

## 3.2 User Requirements

### 3.2.1 Main User Groups

The system serves three main user groups: customers, vendors, and administrators.

Customers are the primary users of the storefront. They need to discover products efficiently, compare them visually, save items for later purchase, place orders, and track those orders afterwards. Since fashion products are image-sensitive, customers also require confidence-building features such as multiple product photos, product reviews, and review media.

Vendors are the main users of the administration portal. They need to register a shop, manage store information, upload and edit products, process orders, create promotions, and observe customer interest through analytics. Since the platform is multi-vendor, each vendor must only be able to manage their own products and their own customer orders.

Administrators are not the focus of the selected specification blocks, but the project still benefits from Django's administration tools for data oversight and maintenance.

### 3.2.2 Customer Requirements

From the customer perspective, the most important requirements are as follows:

- convenient account registration and login;
- product browsing before login;
- search and filter tools for large product catalogues;
- clear product detail pages with multiple photos;
- persistent shopping cart and order history;
- order details and order status visibility;
- trust-building features such as ratings and reviews;
- a wishlist for future purchase planning;
- notifications when an order changes status or when a wishlisted item goes on sale;
- an interface that works well on both desktop and mobile devices.

These requirements strongly influenced the frontend design. The implemented system includes product grids, pagination, product detail galleries, cart quantity controls, mobile navigation, dark mode, and a notification center. The order pages also support status filtering and detail views so that customers can review previous purchases conveniently.

### 3.2.3 Vendor Requirements

From the vendor perspective, the main requirements are as follows:

- register an account and open a shop;
- manage shop information and shop photos;
- add, edit, hide, and organise products;
- upload multiple product images;
- view and process orders belonging only to their own shop;
- update order status as the fulfilment workflow progresses;
- create promotions for products;
- understand customer interest through sales and search analytics.

These needs explain why the backend was designed around separate vendor, store, product, order, promotion, and analytics features. They also explain the separation between storefront functions and vendor-only management functions.

### 3.2.4 User Experience and Quality Requirements

In addition to functional requirements, the project identified several important quality requirements.

The first is responsiveness. Customers may browse on desktop or on mobile devices, so the interface must adapt to different screen sizes. The second is clarity. Product cards, shop pages, cart pages, and vendor order pages must present a large amount of information without overwhelming the user. The third is feedback. The system should inform users when actions succeed, when stock is limited, when a discount is available, or when an order status changes. The fourth is continuity. Theme preference, cart data, login sessions, and notifications should persist across sessions where appropriate.

These considerations are reflected most clearly in Block X and are discussed further in Sections 3.3 and 3.4.

## 3.3 Implemented Features of the Specification

This section evaluates the chosen specification blocks against the developed system. For each block an overview is given first, followed by a per-item table that maps every specification requirement to its implementation status.

| Block | Overall status |
| --- | --- |
| A – Core functions | All 20 requirements implemented. A15 uses exact-match product ID lookup via a `byID:{id}` search syntax rather than substring matching, which is a minor adaptation. |
| B – Multiple photos and order processing | 3 of 4 requirements fully implemented. B4 (separate dates per status change) is partially implemented; a general last-updated timestamp is recorded instead of individual dates for each transition. |
| F – Multiple vendors | 5 of 5 requirements implemented. F1 is adapted slightly: shop photos are managed through the store management interface after registration rather than being collected on the initial registration form. |
| T – User generated content | Implemented. Customers can rate and review purchased products and upload review media. |
| U – Wish list and promotional pricing | Implemented. Customers can maintain wishlists, vendors can create promotions, wishlist sale notifications are sent, and search queries and product views are logged for analytics. |
| X – User experience design | Implemented. The system features responsive layouts, mobile navigation, swipe-friendly galleries, dark mode, notification badges, and accessible interaction labels. |

### 3.3.1 Block A – Core Functions

Block A defines the storefront functions used by customers and the administration portal used by vendors. All twenty requirements are implemented. A15 is adapted slightly: the vendor catalogue provides a `byID:{id}` search syntax for product ID lookup using exact match rather than substring matching.

| ID | Specification requirement | Status | Implementation notes |
| --- | --- | --- | --- |
| A1 | Customers may register a new account with full name, email, password and shipping address. | Implemented | The customer registration form collects first name, last name, email, password, and a multi-part shipping address. |
| A2 | Product search and detail are accessible before login. After login customers can manage carts and orders. Data is persisted server-side across sessions. | Implemented | Storefront routes allow product browsing and search without authentication. Cart, order, and profile data are stored in the server database and persist across sessions. |
| A3 | Customers may browse products in a list or grid showing product name, price and a thumbnail image. | Implemented | Products are displayed in a responsive grid. Each product card shows the product name, price (with discount badge if applicable), and a thumbnail image. |
| A4 | A customer can search products by product name. | Implemented | A search modal and the catalogue page both support keyword search by product name. |
| A5 | Suitable controls for navigating a long product list, such as pagination or infinite scroll. | Implemented | Desktop product lists use pagination with first, previous, next, and last page controls at eight items per page. |
| A6 | Clicking a product shows product detail with at least two more attributes beyond name, price and thumbnail. | Implemented | The product detail page shows description, stock level indicator, brand and store information, customer reviews, and an image gallery, which is well beyond the minimum of two additional attributes. |
| A7 | Customers can add a product to their shopping cart with a default quantity of 1. | Implemented | The add-to-cart action on the product detail page defaults to a quantity of one. |
| A8 | Cart lists products with name, price, quantity and total order amount. Clicking an item shows product detail. | Implemented | The cart page shows each item with product name, unit price, and quantity, along with the overall total amount. Clicking a cart item navigates to the corresponding product detail page. |
| A9 | Customers can change the quantity of a product in the cart. | Implemented | The cart provides increment and decrement quantity controls for each item. |
| A10 | Customers can remove a product from the cart. | Implemented | Each cart item has a delete button for removal. |
| A11 | Checking out creates a purchase order and clears the cart. | Implemented (adapted for F4) | Checkout creates a purchase order from the selected items. Because Block F is also implemented, customers select specific items and unchecked items remain in the cart per F4 and F5. Selected items are removed from the cart after checkout. |
| A12 | Customers can list purchase orders showing P.O. number, purchase date, total amount and status in reverse chronological order. Clicking an entry shows the order detail. | Implemented | The My Orders page lists orders with order ID, order date, total amount, and status, sorted newest first. Each entry links to the order detail page. |
| A13 | Order detail shows P.O. number, purchase date, shipping address, total amount and status. For each product: product name, quantity, unit price and subtotal. | Implemented | The customer order detail page displays order ID, order date, shipping address, total amount, and status. Each line item shows the product name, quantity, and unit price. |
| A14 | The vendor can browse products in the catalogue and search by product name. | Implemented | The vendor catalogue page lists the vendor's own products and supports search filtering. |
| A15 | A system-generated product ID is shown. The vendor can search by entering a substring of the product ID. | Implemented (adapted) | The catalogue displays the system-generated product ID and provides a `byID:{id}` search syntax for looking up products by their ID. The lookup uses exact match rather than substring matching, but it serves the same practical purpose of letting a vendor locate a specific product by its unique identifier. |
| A16 | The vendor may add a new product with at least product name, price and a thumbnail image. | Implemented | The create-product form accepts product name, price, description, images, category, brand, and stock quantity. |
| A17 | The vendor can edit product detail information. | Implemented | All product fields can be edited through the vendor catalogue detail page. |
| A18 | The vendor can disable a product to hide it from customers, and later enable it. | Implemented | The vendor can toggle a product's visibility to hide or show it in the storefront. |
| A19 | The vendor can list purchase orders sorted newest first, showing P.O. numbers, dates, customer names, totals and status. | Implemented | The vendor order list displays order ID, date, customer name, total amount, and status, sorted by newest first. |
| A20 | The vendor can click a purchase order to show its detail and line items. | Implemented | The vendor order detail page shows full order information including individual line items and customer details. |

### 3.3.2 Block B – Multiple Photos and Order Processing

Block B adds multi-photo product support and an order status workflow. Three of the four requirements are fully implemented. B4 is partially implemented because a single general timestamp is used instead of separate dates for each status transition.

| ID | Specification requirement | Status | Implementation notes |
| --- | --- | --- | --- |
| B1 | The system supports multiple photos per product. The storefront displays them in product detail; the administration portal allows adding and removing photos. | Implemented | Products support multiple images through the ProductMedia model. The storefront displays an image gallery with swipe support on mobile. The vendor portal provides interfaces for uploading and removing product images. |
| B2 | A order status workflow with at least four values. Customers or vendors carry out actions that trigger status changes. | Implemented | Four statuses are defined: Pending, Holding, Shipped, and Cancelled. Customers can request refunds for Pending or Holding orders. Vendors can update an order to Holding, Shipped, or Cancelled, and can approve or dismiss refund requests. |
| B3 | Customers can filter the list of purchase orders by order status. | Implemented | The customer order list provides a status filter so customers can view only orders with a particular status. |
| B4 | The system keeps record of the date for some changes of order status and displays them with order detail. | Partially implemented | The system records a general last-updated timestamp (`statusUpdatedDate`) that is shown in the order detail whenever the status has changed from Pending. Separate dedicated date fields for each type of status transition (e.g. shipment date, cancellation date) are not maintained. |

### 3.3.3 Block F – Multiple Vendors

Block F enables a multi-vendor marketplace where each vendor runs a separate shop. All five requirements are implemented. F1 is adapted slightly in that shop photos are uploaded through store management after registration rather than during the initial registration form.

| ID | Specification requirement | Status | Implementation notes |
| --- | --- | --- | --- |
| F1 | A vendor can register to open a shop, providing contact information and shop information with photos. | Implemented (adapted) | Vendor registration collects personal contact information and creates a shop. Shop photos are uploaded and managed through the vendor's store management page after registration, rather than being collected on the registration form itself. |
| F2 | A vendor manages their own product catalogue and processes purchase orders and shipment. | Implemented | Each vendor has a dedicated administration portal for their own product catalogue, order list, and order processing functions. |
| F3 | Customers can visit a shop and see shop information followed by products. Customers can list and search products within a shop. | Implemented | Each store has a public page showing shop photos, store details, and the product catalogue. Customers can search for products within a specific store. |
| F4 | Customers can put products from different shops in their cart. A purchase order contains products from one shop only. The system splits items into separate orders per shop. | Implemented | The cart holds items from any store. During checkout, selected items are automatically grouped by store and a separate purchase order is created for each store. |
| F5 | Items not checked out remain in the shopping cart. | Implemented | Items not selected during checkout remain in the cart for future purchase. |

### 3.3.4 Further Requirement Blocks – T, U, and X

The further requirement blocks were selected because they add value beyond the basic transaction flow. Unlike Blocks A, B and F, these blocks do not define individually numbered sub-requirements. They describe broader capabilities whose implementation is summarised below.

**Block T – User Generated Content**

The specification describes UGC as customer feedback such as ratings and reviews that promote trust and sales. The system implements this through a review feature that allows customers to rate purchased products on a five-point scale, write a text review, and optionally upload review media (images). Duplicate reviews by the same customer for the same product are prevented. Product detail pages display the average rating and individual reviews, giving future buyers access to authentic customer feedback.

**Block U – Wish List and Promotional Pricing Strategy**

The specification describes wishlists, promotional discounts, wishlist sale notifications, and monitoring of search queries and browsing to anticipate sales. The system implements all of these capabilities. Customers can add products to a wishlist for future purchase. Vendors can create time-limited promotional discounts on their products. When a promotion is created for a product, customers who have that product in their wishlist receive a notification that the item has gone on sale. The system also logs search queries and product views, and the vendor analytics dashboard exposes top search terms, top viewed products, wishlist popularity, and recommended discount candidates derived from this data.

**Block X – User Experience Design**

The specification emphasises intuitive, user-friendly interfaces that work across devices, responsive design, mobile-friendliness, and notification management. The system addresses these through responsive layouts that adapt to different screen sizes, a desktop navigation menu with a mega-dropdown and a mobile drawer menu, swipe-enabled product image galleries for touch devices, persistent dark mode support, pagination controls for large catalogues, loading states and feedback messages, notification badges and a notification panel displaying order updates and wishlist sale alerts, and accessible labels on interactive controls.

## 3.4 Additional Requirement Discussion

### 3.4.1 Core Functional Design Rationale

The overall functional design follows a marketplace structure rather than a single-seller online store. This directly explains the implementation of vendors, stores, and split checkout by shop (F2–F5). It also explains why the administration interface focuses on product and order management for each vendor individually.

For customers, the system was designed to make the shopping flow straightforward: search or browse products (A3–A5), inspect product details (A6), add products to cart (A7–A10), select which items to check out (A11, adapted for F4), and then monitor order progress afterwards (A12–A13). This flow is supported by both backend persistence (A2) and frontend navigation.

For vendors, the design places equal emphasis on product management (A14–A18) and order fulfilment (A19–A20, B2). Products can be created, edited, hidden, and decorated with multiple images (B1). Orders can be reviewed in detail and moved through the four-state workflow. This supports the daily operation of an individual shop within the wider marketplace.

### 3.4.2 Advanced Function Rationale

The advanced functions selected for this project were chosen because they are especially relevant to fashion e-commerce.

First, user-generated content (Block T) is important because customers often rely on the experience of previous buyers before purchasing fashion items. The review system therefore supports ratings, written comments, and review media. This helps later customers evaluate quality and appearance more confidently with authentic UGC rather than brand-created content, which aligns with the specification's emphasis on consumer trust.

Second, wishlist and promotional pricing features (Block U) are important because customers do not always buy immediately. A wishlist provides a natural way to record purchase interest, while vendor promotions and sale notifications encourage return visits and delayed conversion. The specification highlights informing customers that wish-listed items have gone on sale as a tactic to pull them back to complete the purchase, and this is implemented through wishlist sale notifications. The project extends this further through analytics features that track search queries and product views, helping vendors identify demand and recommend discount candidates, which satisfies the specification's guidance on monitoring search queries and browsing to anticipate potential sales.

Third, multiple product photos (B1) were treated as a practical necessity. Fashion items cannot be evaluated well from a single image, so the project allows vendors to upload multiple product images and allows customers to inspect them in a swipe-enabled gallery.

### 3.4.3 Business Requirements

Several business-oriented non-functional requirements influenced the system design.

One important requirement is vendor self-service. A marketplace becomes difficult to maintain if administrators must manually manage every product and every order. The project therefore gives vendors direct control over products, promotions, store information, and order handling.

Another important requirement is sales visibility. The analytics functions give vendors access to total sales, order counts, product counts, top viewed products, top search queries, product-view trends, category breakdowns, wishlist popularity, and recommended discount candidates. These features help vendors make better business decisions rather than using the platform only as a passive catalogue.

A third requirement is account and data protection. The project uses authenticated API access, separates customer and vendor data access, and persists cart, order, and profile information on the server side. This is essential because both commercial data and customer information must remain isolated by user role.

### 3.4.4 User Experience Requirements

User experience was treated as a separate requirement area (Block X) rather than as decoration added after implementation.

The specification emphasises creating an application that is intuitive, user-friendly and provides a positive experience, and it highlights that desktop and mobile interfaces have distinct UX characteristics. The storefront therefore supports responsive layouts so that customers can browse from desktop and mobile devices. Navigation includes a desktop mega-dropdown menu and a mobile drawer menu. Product interaction is also adapted for touch devices through swipe-friendly galleries and mobile-oriented browsing behaviour.

The specification also describes notifications as powerful tools to inform users of relevant messages and to promote engagement, while noting that they can cause annoyance if mismanaged. The project addresses this by sending notifications only for specific meaningful events (order status changes; wishlist items going on sale; new orders and refund requests for vendors) and by displaying them in a non-intrusive notification panel accessible through a badge icon, rather than using disruptive pop-ups.

The system includes several additional UX decisions intended to improve ease of use:

- persistent dark mode for visual comfort across sessions;
- product grids and pagination for large catalogues;
- modal search and quick navigation tools;
- notification badges and notification panels for timely updates;
- clear cart quantity controls and selected-item checkout flow;
- loading placeholders and visible feedback messages;
- accessible labels on interactive controls.

These features make Block X meaningful in practice because they affect how easily users can perform the core tasks defined in Blocks A, B, F, T, and U.

### 3.4.5 Societal and Environmental Considerations

Although this project is primarily focused on marketplace functions, some design choices also have broader value.

The use of multiple product images and customer review media can reduce uncertainty before purchase. In a fashion marketplace, this may help reduce avoidable returns caused by inaccurate expectations. Likewise, clear product information, visible stock levels, and order status updates improve communication and reduce friction between customer and vendor.

The responsive interface and dark mode support also contribute to inclusive design by making the system more comfortable to use across devices and user preferences. While the project does not attempt to solve every accessibility or sustainability issue, it does show awareness that product design should consider more than transaction completion alone.
