##Amdeb Feature List with Importance and Urgency##

###High Level Features###
---

Amdeb will add many new features to Odoo. The following is a list of high 
level features and their importance and urgency attributes. 
<table>
  <tr>
    <th>Feature Name</td>
    <th>Importance</td>
    <th>Urgency</td>
  </tr>
  <tr>
    <td>Amazon Integration</td>
    <td>High</td>
    <td>High</td>
  </tr>
  <tr>
    <td>eBay Integration</td>
    <td>High</td>
    <td>Medium</td>
  </tr>
  <tr>
    <td>Online B2B Store</td>
    <td>Medium</td>
    <td>Medium</td>
  </tr>
  <tr>
    <td>淘宝网店 Integration</td>
    <td>Medium</td>
    <td>Medium</td>
  </tr>
  <tr>
    <td>微信网店</td>
    <td>Medium</td>
    <td>Medium</td>
  </tr>
  <tr>
    <td>Facebook Online Store</td>
    <td>Low</td>
    <td>Low</td>
  </tr>
  <tr>
    <td>Google AdWords Integration</td>
    <td>High</td>
    <td>Medium</td>
  </tr>
  <tr>
    <td>Automatic Google AdWords Marketing</td>
    <td>High</td>
    <td>Medium</td>
  </tr>
  <tr>
    <td>Bing Advertisement Integration</td>
    <td>Low</td>
    <td>Low</td>
  </tr>
  <tr>
    <td>Automatic Bing Marketing</td>
    <td>Low</td>
    <td>Low</td>
  </tr>
  <tr>
    <td>Bing Advertisement Integration</td>
    <td>Low</td>
    <td>Low</td>
  </tr>
  <tr>
    <td>Automatic Bing Marketing</td>
    <td>Low</td>
    <td>Low</td>
  </tr>
</table>

###Marketplace Integration Features###
----

The marketplace integration includes the following features. 
All synchronization operations support both 
both automatic batch mode and manual mode. 

* __Product Synchronization__: When a user creates/edit/delete a product, 
the product data is automatically synchronized in all marketplaces. 
The product data includes all product attributes such as 
size, description, keyword, inventory and price. Product images are also 
synchronized. It is a one-way synchronization 
that goes from Odoo database to all marketplaces.
  
* __Customized Product Data for a Marketplace__: Due to different customer 
segments in different marketplaces, it is a common practice to have 
different product data (description, price) for different marketplaces. 
This is also required by different product language and 
currency such as Chinese and RMB. 

* __Template-based product editing__: for the basic product information and
data for each marketplace, there should be a template to facilitate 
quick editing. 
 
* __Fulfillment by Amazon Synchronization__: A company may store some 
products in Amazon warehouse to use
fulfillment by Amazon (FBA) service. A company may even fulfill 
its web site order by FBA. The feature has 
a medium urgency. 

* __Order Synchronization__: When an order is placed or cancelled 
in a marketplace, the order data is pulled from a marketplace and a 
new order is created in Odoo database. 
The order data include product id (UPC code or SKU), quantity, price, 
customer data, shipping address etc. The inventory data is 
updated with the order data.  
   
* __Shipment Synchronization__: When an order is shipped, the shipment 
information is synchronized to the order's marketplace.

* __Inventory management__: support multiple warehouses and inventory control. 

* __Payment Synchronization__: When a payment is collected for an order, 
the data is synchronized with backend accounting system.  

* __Return Synchronization__: when a shipped order is returned, the return 
information is synchronized in 
 the order's original marketplace. 

* __Auction Features__: There are some nice-to-have auction features 
that are specific to eBay. This set of 
 features has a low urgency status. 

###Google AdWords Integration Features###
----

The integration means that a user can perform most, if not all, 
functions of Google AdWords without 
leaving Odoo user interface. Due to the end-to-end nature of embedded 
Google AdWords and Google Analytics 
functions in Odoo system, a user should have a better UI and more 
accurate analytic reports for all AdWords 
performance measures. 
