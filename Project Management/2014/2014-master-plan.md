## 2014 Master Plan
---

This is a master development plan for 2014. The detail monthly plans 
and related status documents are in the subdirectories. 

### Resources

We plan to have two developers at the end of September 2014.
A tester and a part-time UI engineer will join the project 
in November 2014. 

### Development Plan

#### Sep. 2014

Port [OCA Connector](https://github.com/OCA/connector)
to Odoo 8.0
  
#### Oct 2014  to Nov. 2014

Implement Amazon Integration -- a minimum viable product

The development order of features are as the following: 

* __Product Synchronization__: When a user creates/edit/delete a product, 
the product data is automatically synchronized in all marketplaces. 
The product data includes all product attributes such as 
size, description, keyword, inventory and price. Product images are also 
synchronized. It is a one-way synchronization 
that goes from Odoo database to all marketplaces.

* __Order Synchronization__: When an order is placed or cancelled 
in a marketplace, the order data is pulled from a marketplace and a 
new order is created in Odoo database. 
The order data include product id (UPC code or SKU), quantity, price, 
customer data, shipping address etc. The inventory data is 
updated with the order data.  
   
* __Shipment Synchronization__: When an order is shipped, the shipment 
information is synchronized to the order's marketplace.

* __Product return synchronization__: When an order is returned, refund
the customer. 

#### Nov. 2014  to Dec. 2014

Implement eBay Integration -- a minimum viable product
Pilot test in one customer site

The tasks are the same four synchronization as Amazon integration.


