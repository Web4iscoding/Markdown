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

This section provides a consolidated account of the chosen basic and further requirement blocks and the degree to which they were fulfilled in the developed system.

| Block | Completion status and relevant sections in this report |
| --- | --- |
| A | Implemented except A15. Customer registration, public product browsing, search, cart, checkout, order history, vendor catalogue browsing, product editing, hide and enable functions, and vendor order views are completed. Vendor product lookup supports exact ID-style search from the catalogue interface, but not substring search of the system-generated product ID as specified in A15. More information: Sections 3.1, 3.2, 3.4.1. |
| B | Implemented except B4. The system supports multiple product photos, a four-state order workflow, customer-side order status filtering, and vendor-side order processing. However, the system stores and displays a general last-updated timestamp rather than separate shipment date, cancellation date, and refund date fields. More information: Sections 3.2, 3.4.1, 3.4.2. |
| F | Implemented with a minor adaptation to F1. Vendors can register accounts, each vendor owns a separate shop, customers can visit shop pages and search within shops, and checkout splits orders by shop while leaving unselected cart items unchanged. Shop photos are managed after registration through store management rather than being fully collected during the initial registration form. More information: Sections 3.1, 3.2, 3.4.1. |
| T | Implemented. The system supports rating and review submission for purchased items, restricts duplicate reviews, and allows optional media uploads so that user-generated content can influence future buyers. More information: Sections 3.2, 3.4.2. |
| U | Implemented. Customers can maintain wishlists, vendors can create promotional discounts, wishlisted customers are notified when a product goes on sale, and the system records search queries and product views to support sales strategy and vendor analytics. More information: Sections 3.2, 3.4.2, 3.4.3. |
| X | Implemented. The system includes responsive layouts, desktop and mobile navigation, swipe-friendly image galleries, dark mode, notification badges and panels, loading states, and accessible interaction labels. More information: Sections 3.2.4 and 3.4.4. |

### 3.3.1 Summary of Block A

Block A forms the foundation of the project and is largely implemented. Customers can register accounts, browse products before login, manage carts, place orders, and review previous orders. Vendors can manage their catalogues and inspect customer orders through their own portal. The main shortcoming against the original specification is A15, because product ID search is supported in a practical exact-match format rather than substring matching.

### 3.3.2 Summary of Block B

Block B is also largely implemented. The system supports multiple images for each product and provides the necessary interfaces for vendors to upload and manage those images. Order processing is implemented through the statuses Pending, Holding, Shipped, and Cancelled. Customers can filter their order list by status, and both customers and vendors can view status information. The main limitation is that the project keeps one general update timestamp instead of maintaining separate dedicated dates for every status transition.

### 3.3.3 Summary of Block F

Block F is implemented because the project is designed as a multi-vendor marketplace. A vendor account creates a store, each store has its own products, and the storefront exposes store pages for customers to browse. The checkout process groups selected cart items by store and creates separate purchase orders accordingly. Items not selected for checkout remain in the cart, which satisfies the split-order requirement in practice.

### 3.3.4 Summary of Further Requirement Blocks T, U, and X

The further requirement blocks were selected because they add value beyond the basic transaction flow.

Block T improves trust and product evaluation through user-generated reviews, ratings, and media.

Block U supports re-engagement and sales strategy through wishlists, discounts, wishlist sale notifications, search query logging, product-view logging, and vendor analytics.

Block X improves usability through responsive design, mobile-friendly navigation, image interaction, notification design, and persistent dark mode support.

## 3.4 Additional Requirement Discussion

### 3.4.1 Core Functional Design Rationale

The overall functional design follows a marketplace structure rather than a single-seller online store. This directly explains the implementation of vendors, stores, and split checkout by shop. It also explains why the administration interface focuses on product and order management for each vendor individually.

For customers, the system was designed to make the shopping flow straightforward: search or browse products, inspect product details, add products to cart, select which items to check out, and then monitor order progress afterwards. This flow aligns with Blocks A and B and is supported by both backend persistence and frontend navigation.

For vendors, the design places equal emphasis on product management and order fulfilment. Products can be created, edited, hidden, and decorated with multiple images. Orders can be reviewed in detail and moved through the defined workflow. This supports the daily operation of a small online shop within a larger marketplace.

### 3.4.2 Advanced Function Rationale

The advanced functions selected for this project were chosen because they are especially relevant to fashion e-commerce.

First, user-generated content is important because customers often rely on the experience of previous buyers before purchasing fashion items. The review system therefore supports ratings, written comments, and review media. This helps later customers evaluate quality and appearance more confidently.

Second, wishlist and promotional pricing features are important because customers do not always buy immediately. A wishlist provides a natural way to record purchase interest, while vendor promotions and sale notifications encourage return visits and delayed conversion. The project extends this further through analytics features that track search queries and product views, helping vendors identify demand and possible discount candidates.

Third, multiple product photos were treated as a practical necessity. Fashion items cannot be evaluated well from a single image, so the project allows vendors to upload multiple product images and allows customers to inspect them in a more interactive gallery.

### 3.4.3 Business Requirements

Several business-oriented non-functional requirements influenced the system design.

One important requirement is vendor self-service. A marketplace becomes difficult to maintain if administrators must manually manage every product and every order. The project therefore gives vendors direct control over products, promotions, store information, and order handling.

Another important requirement is sales visibility. The analytics functions give vendors access to total sales, order counts, product counts, top viewed products, top search queries, product-view trends, category breakdowns, wishlist popularity, and recommended discount candidates. These features help vendors make better business decisions rather than using the platform only as a passive catalogue.

A third requirement is account and data protection. The project uses authenticated API access, separates customer and vendor data access, and persists cart, order, and profile information on the server side. This is essential because both commercial data and customer information must remain isolated by user role.

### 3.4.4 User Experience Requirements

User experience was treated as a separate requirement area rather than as decoration added after implementation.

The storefront supports responsive layouts so that customers can browse from desktop and mobile devices. Navigation includes a desktop menu and a mobile drawer menu. Product interaction is also adapted for touch devices through swipe-friendly galleries and mobile-oriented browsing behaviour.

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
