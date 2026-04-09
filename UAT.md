# User Acceptance Testing (UAT) Table

This document maps the requirements in `specifications.txt` to user acceptance testing scenarios. The numbered points A1 to F5 are taken directly from the specification. Blocks T, U, and X are narrative requirement blocks without numbered sub-points in the specification, so they are represented here as block-level UAT items. The `Test Result` column is left as `Not executed` so it can be updated during actual UAT.

## Scenario Count by Requirement

| Item | Scenario Count |
| --- | ---: |
| A1 | 2 |
| A2 | 2 |
| A3 | 1 |
| A4 | 2 |
| A5 | 1 |
| A6 | 1 |
| A7 | 1 |
| A8 | 2 |
| A9 | 1 |
| A10 | 1 |
| A11 | 1 |
| A12 | 2 |
| A13 | 1 |
| A14 | 2 |
| A15 | 1 |
| A16 | 2 |
| A17 | 1 |
| A18 | 2 |
| A19 | 1 |
| A20 | 1 |
| B1 | 2 |
| B2 | 3 |
| B3 | 1 |
| B4 | 1 |
| F1 | 2 |
| F2 | 2 |
| F3 | 2 |
| F4 | 2 |
| F5 | 1 |
| T | 3 |
| U | 4 |
| X | 3 |
| Total | 48 |

## Detailed UAT Scenarios

| Item | Test Description | Test Steps | Test Data | Expected Result | Test Result |
| --- | --- | --- | --- | --- | --- |
| A1.1 | Verify that a customer can register a new account with all required details. | 1. Open customer registration.<br>2. Enter full name, email, password, and shipping address.<br>3. Submit the form. | Full name: Sarah Ali<br>Email: sarah@example.com<br>Password: P@ssword123<br>Address: 12 King Street | Account is created successfully and the customer can log in with the new credentials. | Not executed |
| A1.2 | Verify validation when a required registration field is missing. | 1. Open customer registration.<br>2. Leave one required field blank.<br>3. Submit the form. | Missing shipping address or email | Registration is rejected and a validation message identifies the missing required field. | Not executed |
| A2.1 | Verify that guests can access search and product detail pages before login. | 1. Open the storefront without logging in.<br>2. Search for a product.<br>3. Open a product detail page. | Any active product | Guest user can search products and view product details without authentication. | Not executed |
| A2.2 | Verify that authenticated customers can access cart and orders and that data persists across sessions. | 1. Log in as a customer.<br>2. Add a product to cart and place an order if needed.<br>3. Log out and log in again.<br>4. Open cart and orders. | Existing customer account with at least one cart item and one order | Cart content, order history, and customer information remain available after a new session starts. | Not executed |
| A3.1 | Verify that customers can browse products in a list or grid with basic product information. | 1. Open the product listing page.<br>2. Review several products in the list. | Product catalogue with multiple items | Each listed product shows product name, price, and thumbnail image. | Not executed |
| A4.1 | Verify that customers can search products by product name. | 1. Enter a product name or substring in the search bar.<br>2. Submit the search. | Search term: dress | Matching products are returned in a list. | Not executed |
| A4.2 | Verify behaviour when a search returns no matches. | 1. Enter a non-existent product name.<br>2. Submit the search. | Search term: xyznonexistentitem | The system shows an empty result state or no-match message without errors. | Not executed |
| A5.1 | Verify that suitable controls are available for navigating a long product list. | 1. Open a category or search result with many products.<br>2. Use pagination, load more, or equivalent navigation control to move through the list. | Catalogue with more items than one page can show | The user can move through the full product list using the provided navigation controls. | Not executed |
| A6.1 | Verify that product detail information shows more attributes than the product list. | 1. Open any product from the list.<br>2. Review the detail page. | Any active product | Product detail page shows product name, price, thumbnail, and at least two additional attributes such as description, category, stock, brand, or extra media. | Not executed |
| A7.1 | Verify that a customer can add a product to the shopping cart with default quantity 1. | 1. Open a product detail page.<br>2. Add the product to the cart.<br>3. Open the cart. | Any active product | Product is added to the cart and its quantity defaults to 1. | Not executed |
| A8.1 | Verify that the shopping cart lists product name, price, quantity, and total amount. | 1. Add one or more products to the cart.<br>2. Open the cart page. | Cart containing at least two items | Cart shows each product name, price, quantity, and a total order amount for the cart. | Not executed |
| A8.2 | Verify that a customer can open product detail from a cart item. | 1. Open the cart page.<br>2. Select a product entry in the cart. | Cart with at least one item | Product detail page opens for the selected cart item. | Not executed |
| A9.1 | Verify that a customer can change product quantity in the cart. | 1. Open the cart.<br>2. Increase or decrease quantity for one item.<br>3. Review the cart totals. | Cart item with quantity 1 | Quantity is updated successfully and the cart total is recalculated correctly. | Not executed |
| A10.1 | Verify that a customer can remove a product from the cart. | 1. Open the cart.<br>2. Remove one item. | Cart with at least one item | Selected product is removed from the cart and totals are updated accordingly. | Not executed |
| A11.1 | Verify that checkout creates a purchase order and clears checked out cart content. | 1. Add products to the cart.<br>2. Proceed to checkout.<br>3. Confirm the order.<br>4. Reopen the cart and order history. | Cart with one or more items | A purchase order is created successfully and the checked out items are removed from the cart. | Not executed |
| A12.1 | Verify that customers can list their purchase orders with summary information in reverse chronological order. | 1. Log in as a customer with multiple orders.<br>2. Open the orders page. | Customer with at least three orders on different dates | Each order summary shows P.O. number, purchase date, and status. The newest order appears first. Total amount is accessible via the order detail view. | Not executed |
| A12.2 | Verify that a customer can open the detail page of a selected purchase order. | 1. Open the orders list.<br>2. Select one order entry. | Existing purchase order | The selected purchase order detail page is displayed. | Not executed |
| A13.1 | Verify that purchase order detail shows all required fields and line-item subtotals. | 1. Open a purchase order detail page.<br>2. Review summary fields and line items. | Purchase order with multiple products | Page shows P.O. number, purchase date, shipping address, total amount, status, and for each line item the product name, quantity, unit price, and subtotal. | Not executed |
| A14.1 | Verify that a vendor can browse products in the catalogue. | 1. Open the vendor product management page.<br>2. Review the product list. | Vendor account with several products | Vendor can browse the catalogue records for managed products. | Not executed |
| A14.2 | Verify that a vendor can search products by product name. | 1. Open vendor catalogue management.<br>2. Enter a product name in the search field.<br>3. Submit the search. | Search term: jacket | Matching products are returned for the vendor. | Not executed |
| A15.1 | Verify that a system-generated product ID is visible and searchable by substring. | 1. Open vendor catalogue list.<br>2. Note the displayed product ID for one product.<br>3. Search using part of that ID. | Product ID substring, for example last 4 characters | Product ID is visible in the catalogue and the matching product is returned when searching by substring. | Not executed |
| A16.1 | Verify that a vendor can add a new product with required details. | 1. Open add-product form.<br>2. Enter product name, price, thumbnail, and other required fields.<br>3. Submit the form. | Product: Linen Shirt<br>Price: 49.99<br>Thumbnail: shirt.jpg | Product is created successfully and appears in the vendor catalogue and storefront when enabled. | Not executed |
| A16.2 | Verify validation when required product details are missing during creation. | 1. Open add-product form.<br>2. Omit a required field such as name or price.<br>3. Submit the form. | Missing product name or price | Product is not created and a validation message is shown. | Not executed |
| A17.1 | Verify that a vendor can edit existing product details. | 1. Open an existing product in edit mode.<br>2. Change one or more fields.<br>3. Save changes.<br>4. Reopen the product. | Change price from 49.99 to 44.99 | Updated product information is saved and displayed correctly. | Not executed |
| A18.1 | Verify that a vendor can disable a product to hide it from customers. | 1. Open a product in vendor management.<br>2. Disable the product.<br>3. Search for the product in the storefront. | Existing enabled product | Product is hidden from the storefront after being disabled. | Not executed |
| A18.2 | Verify that a vendor can re-enable a previously disabled product. | 1. Open a disabled product in vendor management.<br>2. Enable the product.<br>3. Search for the product in the storefront. | Previously disabled product | Product becomes visible to customers again after being enabled. | Not executed |
| A19.1 | Verify that a vendor can list customer purchase orders with required fields in newest-first order. | 1. Open vendor orders page.<br>2. Review the order list. | Vendor with multiple customer orders | List shows P.O. numbers, purchase dates, customer names, total amounts, and order status in descending purchase-date order. | Not executed |
| A20.1 | Verify that a vendor can open a purchase order and view its detail and line items. | 1. Open vendor order list.<br>2. Select one order. | Any customer order | Vendor can view the selected order's summary and its line items. | Not executed |
| B1.1 | Verify that a product can contain multiple photos and that customers can view them in product detail. | 1. Open or create a product with multiple photos.<br>2. Open the storefront product detail page.<br>3. Navigate through the image gallery. | Product with at least three photos | Product detail page displays multiple photos in a gallery and allows the user to navigate between them. Video upload and playback are not supported. | Not executed |
| B1.2 | Verify that a vendor can add and remove product photos. | 1. Open product edit page in vendor portal.<br>2. Upload an additional photo.<br>3. Save.<br>4. Remove one photo.<br>5. Save again. | Product media files: front.jpg, side.jpg | Added photo appears on the product and removed photo is no longer associated with it. | Not executed |
| B2.1 | Verify that a newly checked out order starts with the initial workflow status. | 1. Place a new order as a customer.<br>2. Open order detail in customer or vendor view. | New order from checkout | New order is created with the initial status defined by the workflow, such as Pending. | Not executed |
| B2.2 | Verify that a vendor action can move an order to another valid workflow state. | 1. Open a pending order in vendor view.<br>2. Perform a vendor status action such as ship, hold, or equivalent.<br>3. Save the change. | Pending order | Order status changes to the selected valid next state and is visible in order detail. | Not executed |
| B2.3 | Verify that a customer can cancel an order or request a refund. | 1. Open an eligible order as a customer.<br>2. Cancel a pending or held order and confirm.<br>3. For a separate pending or held order, submit a refund request with a reason. | One pending order and one held order | Cancellation changes the order status to Cancelled. Refund request sets a refund flag on the order without changing the status, and notifies the vendor. | Not executed |
| B3.1 | Verify that customers can filter their purchase orders by order status. | 1. Log in as a customer with orders in different statuses.<br>2. Apply a status filter on the orders page. | Orders with statuses such as pending, shipped, cancelled | Only orders matching the selected status are displayed. | Not executed |
| B4.1 | Verify that the most recent status change date is recorded and displayed in order detail. | 1. Trigger a status change such as shipped or cancelled.<br>2. Open order detail. | Order with at least one status transition | A single status-updated date is recorded automatically on each status change and displayed alongside the order information. Per-status dates such as separate shipment or cancellation dates are not tracked individually. | Not executed |
| F1.1 | Verify that a vendor can register a new account with person-in-charge and shop information. | 1. Open vendor registration.<br>2. Enter person-in-charge contact details and shop information.<br>3. Submit the form. | Contact name: Adam Noor<br>Email: vendor@example.com<br>Shop name: Noor Fashion | Vendor account is created successfully and the shop profile is established. | Not executed |
| F1.2 | Verify that vendor registration validates required information and that shop photos are uploaded separately. | 1. Open vendor registration.<br>2. Fill all required fields and submit.<br>3. Repeat with one required field missing.<br>4. After successful registration, upload a shop photo through the store management page. | Required fields filled or missing | Registration rejects incomplete submissions with validation messages. Shop photos are not accepted during registration and must be uploaded separately after the account is created. | Not executed |
| F2.1 | Verify that a vendor manages only their own product catalogue. | 1. Log in as Vendor A.<br>2. Open product management.<br>3. Search for products owned by Vendor B. | Two vendors with separate catalogues | Vendor A can view and manage only their own products. | Not executed |
| F2.2 | Verify that a vendor processes only their own shop orders. | 1. Log in as Vendor A.<br>2. Open vendor orders.<br>3. Compare visible orders with orders placed for Vendor B. | Orders belonging to at least two vendors | Vendor A sees and can process only orders that belong to their own shop. | Not executed |
| F3.1 | Verify that customers can visit a shop and see shop information plus products. | 1. Open a vendor shop page from the storefront.<br>2. Review the shop header and product section. | Existing vendor shop with products | Shop page shows shop information followed by products from that shop. | Not executed |
| F3.2 | Verify that customers can list and search products within a specific shop. | 1. Open a vendor shop page.<br>2. Browse shop products.<br>3. Search within that shop. | Search term that matches only one product in the selected shop | Product listing and search results are limited to the selected shop. | Not executed |
| F4.1 | Verify that checkout splits selected cart items into separate purchase orders by shop. | 1. Add products from Shop A and Shop B to the cart.<br>2. Select items for checkout.<br>3. Complete checkout.<br>4. Review created orders. | Cart with items from at least two shops | System creates separate purchase orders so each order contains products from one shop only. | Not executed |
| F4.2 | Verify that each created split order contains products from only one vendor shop. | 1. Open each order created from a mixed-shop checkout.<br>2. Review line items and shop ownership. | Split orders created from mixed cart | Every order contains items from a single shop and no order mixes products from different shops. | Not executed |
| F5.1 | Verify that items not selected for checkout remain in the cart. | 1. Add multiple items to the cart.<br>2. Select only some items for checkout.<br>3. Complete checkout.<br>4. Reopen the cart. | Cart containing selected and unselected items | Unselected items remain in the shopping cart after checkout. | Not executed |
| T.1 | Verify that a customer can submit user-generated content for a purchased product. | 1. Log in as a customer who purchased a product.<br>2. Open the review form.<br>3. Enter a rating and written review.<br>4. Submit. | Rating: 5/5<br>Review: Excellent quality | Review is saved successfully and linked to the product and customer. | Not executed |
| T.2 | Verify that review media can be uploaded and duplicate reviews are prevented. | 1. Submit a review with an image attachment.<br>2. Attempt to submit a second review for the same product using the same customer account. | Review image: review1.jpg | Media upload is accepted and the system prevents duplicate reviews for the same customer-product pair. | Not executed |
| T.3 | Verify that product detail displays average rating and customer reviews. | 1. Open a product that has existing reviews.<br>2. Review the rating summary and review list. | Product with multiple reviews | Product page shows average rating and visible customer reviews for future buyers. | Not executed |
| U.1 | Verify that a customer can add a product to a wishlist for future purchase. | 1. Log in as a customer.<br>2. Open a product detail page.<br>3. Add the product to wishlist.<br>4. Open the wishlist page. | Any active product | Product is added to the wishlist and appears in the customer's saved list. | Not executed |
| U.2 | Verify that a vendor can create a promotional discount for a product. | 1. Log in as a vendor.<br>2. Open promotion management for a product.<br>3. Create a time-limited promotion.<br>4. Save. | Discount: 20% off<br>Start date and end date set | Promotion is stored successfully and discount information is reflected for the product. | Not executed |
| U.3 | Verify that customers receive a notification when a wishlisted item goes on sale. | 1. Add a product to a customer's wishlist.<br>2. Create a promotion for that product as the vendor.<br>3. Open the customer's notification area. | Wishlisted product with newly added promotion | Customer receives a notification that the wishlisted item is on sale. | Not executed |
| U.4 | Verify that search queries and browsing activity are logged for analytics. | 1. Search for products as a customer.<br>2. Open several product detail pages.<br>3. Open vendor analytics or admin reporting for those products. | Search terms: shoes, denim<br>Viewed products: 3 items | Search terms and product views are recorded and available in analytics outputs such as top searches, top viewed products, or discount recommendations. | Not executed |
| X.1 | Verify that the storefront layout is responsive across different screen sizes. | 1. Open the storefront on desktop width.<br>2. Resize to tablet and mobile widths or use device emulation.<br>3. Review key pages. | Desktop, tablet, and mobile viewport sizes | Layout adapts correctly without broken content, overlapping controls, or hidden essential actions. | Not executed |
| X.2 | Verify that mobile-friendly navigation and touch interactions work correctly. | 1. Open the storefront in a mobile viewport.<br>2. Use the mobile menu or drawer.<br>3. Interact with product gallery using touch or swipe simulation. | Mobile viewport and product with multiple images | Navigation remains usable on mobile and touch-friendly gallery interactions work as intended. | Not executed |
| X.3 | Verify that user notifications and feedback are visible but non-disruptive. | 1. Trigger a notification event such as order status change or wishlist sale alert.<br>2. Open the interface as the target user.<br>3. Review badges, panels, and feedback messages. | Existing notification event | Notification is presented clearly through badges, panels, or messages without blocking primary user actions. | Not executed |
