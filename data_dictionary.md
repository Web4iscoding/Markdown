# Data Dictionary

This document defines every attribute in the Online Shipping System Project database. Each table corresponds to one Django model. Data types and lengths reflect the underlying SQLite storage as configured through Django's ORM field definitions.

---

## User

The built-in Django authentication model. Provides username, email, and password-based identity management.

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| id | Auto-generated primary key | Integer | PK, Auto-increment | No |
| username | Unique login identifier | VARCHAR (150) | Unique | No |
| password | Hashed password string | VARCHAR (128) | — | No |
| email | User email address | VARCHAR (254) | — | No |
| first_name | User first name | VARCHAR (150) | — | No |
| last_name | User last name | VARCHAR (150) | — | No |
| is_active | Whether the account is active | Boolean | Default: True | No |
| is_staff | Whether the user can access the admin site | Boolean | Default: False | No |
| is_superuser | Whether the user has all permissions | Boolean | Default: False | No |
| date_joined | Timestamp of account creation | DateTime | Auto-set on create | No |
| last_login | Timestamp of most recent login | DateTime | — | Yes |

---

## Customer

Extends the User model with customer-specific profile information including shipping details and a profile image.

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| customerID | Auto-generated primary key | Integer | PK, Auto-increment | No |
| user | Reference to the Django User | Integer (FK → User.id) | Unique, ON DELETE CASCADE | No |
| firstName | Customer first name | VARCHAR (50) | — | No |
| lastName | Customer last name | VARCHAR (50) | — | No |
| phoneNo | Customer phone number | VARCHAR (20) | — | No |
| shippingAddress1 | First line of shipping address | Text | — | No |
| shippingAddress2 | Second line of shipping address | Text | — | No |
| shippingAddress3 | Third line of shipping address | Text | — | No |
| profileImage | Path to uploaded profile image | ImageField (upload_to: customer_profiles/) | — | Yes |
| createdTime | Timestamp of profile creation | DateTime | Auto-set on create | No |

---

## Vendor

Extends the User model with vendor-specific profile information.

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| vendorID | Auto-generated primary key | Integer | PK, Auto-increment | No |
| user | Reference to the Django User | Integer (FK → User.id) | Unique, ON DELETE CASCADE | No |
| phoneNo | Vendor phone number | VARCHAR (20) | — | No |
| firstName | Vendor first name | VARCHAR (50) | — | No |
| lastName | Vendor last name | VARCHAR (50) | — | No |
| profileImage | Path to uploaded profile image | ImageField (upload_to: vendor_profiles/) | — | Yes |
| createdTime | Timestamp of profile creation | DateTime | Auto-set on create | No |

---

## Store

Represents a vendor's store. Each vendor owns exactly one store.

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| storeID | Auto-generated primary key | Integer | PK, Auto-increment | No |
| vendorID | Reference to the owning Vendor | Integer (FK → Vendor.vendorID) | Unique, ON DELETE CASCADE | No |
| storeName | Display name of the store | VARCHAR (100) | Unique together with vendorID | No |
| description | Store description text | Text | — | No |
| createdTime | Timestamp of store creation | DateTime | Auto-set on create | No |

---

## StorePhoto

Stores ordered photographs associated with a store.

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| storePhotoID | Auto-generated primary key | Integer | PK, Auto-increment | No |
| storeID | Reference to the parent Store | Integer (FK → Store.storeID) | ON DELETE CASCADE | No |
| photoURL | Path to uploaded store photo | ImageField (upload_to: store_photos/) | — | No |
| sortedOrder | Display ordering index | Integer | Default: 0 | No |
| isPrimary | Whether this is the store's primary photo | Boolean | Default: False | No |
| uploadedTime | Timestamp of photo upload | DateTime | Auto-set on create | No |

---

## Category

Reference table for grouping fashion items into categories (e.g. accessories, bags, shoes).

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| categoryID | Auto-generated primary key | Integer | PK, Auto-increment | No |
| categoryName | Unique category display name | VARCHAR (100) | Unique | No |
| description | Category description text | Text | — | No |

---

## Brand

Reference table for fashion brands (e.g. Gucci, Balenciaga).

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| brandID | Auto-generated primary key | Integer | PK, Auto-increment | No |
| brandName | Unique brand display name | VARCHAR (100) | Unique | No |
| description | Brand description text | Text | — | No |
| logoURL | Path to uploaded brand logo | ImageField (upload_to: brand_logos/) | — | Yes |

---

## Product

Represents a fashion item listed for sale within a store.

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| productID | Auto-generated primary key | Integer | PK, Auto-increment | No |
| storeID | Reference to the parent Store | Integer (FK → Store.storeID) | ON DELETE CASCADE | No |
| productName | Display name of the product | VARCHAR (200) | — | No |
| description | Product description text | Text | — | No |
| price | Unit price of the product | Decimal (10, 2) | Min: 0 | No |
| quantity | Available stock quantity | Integer | Default: 0, Min: 0 | No |
| availability | Whether the product is available for purchase | Boolean | Default: True | No |
| createdTime | Timestamp of product creation | DateTime | Auto-set on create | No |
| updatedTime | Timestamp of last modification | DateTime | Auto-set on save | No |
| isHidden | Whether the product is hidden from listings | Boolean | Default: False | No |
| brand | Reference to the Brand | Integer (FK → Brand.brandID) | ON DELETE SET NULL | Yes |
| category | Reference to the Category | Integer (FK → Category.categoryID) | ON DELETE SET NULL | Yes |

---

## ProductMedia

Stores image or video media files associated with a product.

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| productMediaID | Auto-generated primary key | Integer | PK, Auto-increment | No |
| productID | Reference to the parent Product | Integer (FK → Product.productID) | ON DELETE CASCADE | No |
| mediaURL | Path to uploaded media file | ImageField (upload_to: product_media/) | — | No |
| mediaType | Type of media | VARCHAR (10) | Choices: 'image', 'video'; Default: 'image' | No |
| mediaContent | Optional text content or caption | Text | — | No |
| isPrimary | Whether this is the product's primary media | Boolean | Default: False | No |
| sortedOrder | Display ordering index | Integer | Default: 0 | No |

---

## CartItem

Associative entity linking a customer to a product in their shopping cart.

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| id | Auto-generated primary key | Integer | PK, Auto-increment | No |
| customerID | Reference to the Customer | Integer (FK → Customer.customerID) | Unique together with productID, ON DELETE CASCADE | No |
| productID | Reference to the Product | Integer (FK → Product.productID) | Unique together with customerID, ON DELETE CASCADE | No |
| quantity | Number of units in the cart | Integer | Default: 1, Min: 1 | No |
| addedTime | Timestamp when item was added to cart | DateTime | Auto-set on create | No |
| updatedTime | Timestamp of last quantity change | DateTime | Auto-set on save | No |

---

## Order

Represents a customer purchase order scoped to a single vendor's store.

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| orderID | Auto-generated primary key | Integer | PK, Auto-increment | No |
| customerID | Reference to the ordering Customer | Integer (FK → Customer.customerID) | ON DELETE CASCADE | No |
| orderDate | Timestamp when the order was placed | DateTime | Auto-set on create | No |
| firstName | Customer first name snapshot at checkout | VARCHAR (50) | — | No |
| lastName | Customer last name snapshot at checkout | VARCHAR (50) | — | No |
| phoneNo | Customer phone number snapshot at checkout | VARCHAR (20) | — | No |
| shippingAddress1 | First line of shipping address at checkout | Text | — | No |
| shippingAddress2 | Second line of shipping address at checkout | Text | — | No |
| shippingAddress3 | Third line of shipping address at checkout | Text | — | No |
| totalAmount | Total monetary value of the order | Decimal (12, 2) | Default: 0 | No |
| status | Current fulfilment status | VARCHAR (20) | Choices: 'Pending', 'Holding', 'Shipped', 'Cancelled'; Default: 'Pending' | No |
| statusUpdatedDate | Timestamp of last status change | DateTime | Auto-set on save | No |
| cancellationReason | Reason provided when the order is cancelled | Text | — | Yes |
| refundRequest | Whether a refund has been requested | Boolean | Default: False | No |
| refundReason | Reason provided for the refund request | Text | — | Yes |

---

## OrderItem

Represents a single product line item within an order, storing a snapshot of the product details at purchase time.

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| orderItemID | Auto-generated primary key | Integer | PK, Auto-increment | No |
| orderID | Reference to the parent Order | Integer (FK → Order.orderID) | ON DELETE CASCADE | No |
| productID | Reference to the original Product | Integer (FK → Product.productID) | ON DELETE SET NULL | Yes |
| productName | Snapshot of the product name at purchase | VARCHAR (200) | — | No |
| quantity | Number of units purchased | Integer | Min: 1 | No |
| paidPrice | Effective unit price paid (after any discount) | Decimal (10, 2) | — | No |

---

## WishlistItem

Associative entity linking a customer to a wishlisted product, recording price information at the time of addition.

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| wishlistItemID | Auto-generated primary key | Integer | PK, Auto-increment | No |
| customerID | Reference to the Customer | Integer (FK → Customer.customerID) | Unique together with productID, ON DELETE CASCADE | No |
| productID | Reference to the Product | Integer (FK → Product.productID) | Unique together with customerID, ON DELETE CASCADE | No |
| addedDate | Timestamp when item was wishlisted | DateTime | Auto-set on create | No |
| isNotified | Whether the customer was notified of a sale | Boolean | Default: False | No |
| original_price_at_added | Product base price at time of addition | Decimal (10, 2) | — | No |
| discount_rate_at_added | Discount rate active at time of addition | Decimal (5, 2) | Default: 0 | No |
| price_at_added | Effective price at time of addition | Decimal (10, 2) | — | No |

---

## Promotion

Represents a time-bound promotional discount applied to a product.

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| promotionID | Auto-generated primary key | Integer | PK, Auto-increment | No |
| productID | Reference to the discounted Product | Integer (FK → Product.productID) | ON DELETE CASCADE | No |
| discountRate | Discount percentage | Decimal (5, 2) | Min: 0, Max: 100 | No |
| startDate | Promotion start date and time | DateTime | — | No |
| endDate | Promotion end date and time | DateTime | — | No |
| status | Current promotion status | VARCHAR (20) | Choices: 'Active', 'Inactive'; Default: 'Inactive' | No |
| notificationSent | Whether wishlist notifications have been sent | Boolean | Default: False | No |
| createdTime | Timestamp of promotion creation | DateTime | Auto-set on create | No |
| updatedTime | Timestamp of last modification | DateTime | Auto-set on save | No |

---

## Review

Represents a customer review for a purchased order item. Each order item may have at most one review.

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| reviewID | Auto-generated primary key | Integer | PK, Auto-increment | No |
| orderItemID | Reference to the reviewed OrderItem | Integer (FK → OrderItem.orderItemID) | Unique (OneToOne), ON DELETE CASCADE | No |
| comment | Review text body | Text | — | No |
| rating | Numeric star rating | Decimal (2, 1) | Default: 5, Min: 0.5, Max: 5 | No |
| createdDate | Timestamp of review submission | DateTime | Auto-set on create | No |

---

## ReviewMedia

Stores optional image or video attachments associated with a review.

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| reviewMediaID | Auto-generated primary key | Integer | PK, Auto-increment | No |
| reviewID | Reference to the parent Review | Integer (FK → Review.reviewID) | ON DELETE CASCADE | No |
| mediaURL | Path to uploaded media file | ImageField (upload_to: review_media/) | — | No |
| mediaType | Type of media | VARCHAR (10) | Choices: 'image', 'video'; Default: 'image' | No |
| sortedOrder | Display ordering index | Integer | Default: 0 | No |

---

## Notification

Represents a notification sent to a user (customer or vendor) in response to a system event.

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| notificationID | Auto-generated primary key | Integer | PK, Auto-increment | No |
| user | Reference to the recipient User | Integer (FK → User.id) | ON DELETE CASCADE | No |
| notificationType | Category of notification event | VARCHAR (30) | Choices: 'wishlist_sale', 'order_shipped', 'order_holding', 'refund_approved', 'order_cancelled', 'new_order', 'refund_request' | No |
| title | Short notification headline | VARCHAR (200) | — | No |
| message | Detailed notification text | Text | — | No |
| link | Frontend route for contextual navigation | VARCHAR (500) | — | No |
| isRead | Whether the notification has been read | Boolean | Default: False | No |
| createdTime | Timestamp of notification creation | DateTime | Auto-set on create | No |
| orderID | Optional reference to a related Order | Integer (FK → Order.orderID) | ON DELETE SET NULL | Yes |
| productID | Optional reference to a related Product | Integer (FK → Product.productID) | ON DELETE SET NULL | Yes |

---

## DeviceToken

Authentication token that supports multiple simultaneous sessions per user (one token per device or browser).

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| key | Hex-encoded random token string | VARCHAR (40) | PK | No |
| user | Reference to the authenticated User | Integer (FK → User.id) | ON DELETE CASCADE | No |
| created | Timestamp of token creation | DateTime | Auto-set on create | No |

---

## SearchQuery

Event log recording search queries submitted by users, used for vendor analytics.

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| id | Auto-generated primary key | Integer | PK, Auto-increment | No |
| query | Search text entered by the user | VARCHAR (500) | — | No |
| user | Reference to the searching User (if authenticated) | Integer (FK → User.id) | ON DELETE SET NULL | Yes |
| resultCount | Number of products returned by the search | Integer | Default: 0 | No |
| timestamp | Timestamp of the search event | DateTime | Auto-set on create | No |

---

## ProductView

Event log recording product detail page visits, used for vendor analytics.

| Attribute | Description | Data Type (Length) | Constraint | Nullable |
| --- | --- | --- | --- | --- |
| id | Auto-generated primary key | Integer | PK, Auto-increment | No |
| product | Reference to the viewed Product | Integer (FK → Product.productID) | ON DELETE CASCADE | No |
| user | Reference to the viewing User (if authenticated) | Integer (FK → User.id) | ON DELETE SET NULL | Yes |
| source | How the user arrived at the product page | VARCHAR (20) | Choices: 'search', 'browse', 'direct'; Default: 'browse' | No |
| searchQuery | The search text that led to this view (if applicable) | VARCHAR (500) | — | No |
| timestamp | Timestamp of the view event | DateTime | Auto-set on create | No |
