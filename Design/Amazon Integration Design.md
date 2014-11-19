# Odoo Amazon Integration Design

## Requirements

Odoo Amazon Integration should support the following functions:

* Catalog Synchronization
    - Product feed: Upload newly created or updated product data. 
    This is the first API request that has to be completed before 
    submitting other catalog requests. 
    - Inventory feed: the current stock levels, restock dates
    and fulfillment latency of listed products
    - Pricing feed: current prices, standard or sales flag
    - Image feed: Product image URLs that Amazon can pull images.
    - Relationship feed (optional): relationships between different
    products such as variation or accessory. 
    - Overrides feed (optional): allows you to override the account-level
    shipping settings with SKU-level shipping settings. 
    
* Order Synchronization: Amazon has a 90-minute holding period to validate 
a transaction. 
    - Receiving the order: Once the holding period has expired, Amazon 
    generates an order report. 
    - Acknowledging receipt of the order. It allows you to associate 
    an internal order IDs and order items IDS with Amazon's order IDs 
    and order items IDs,if desired. Additionally, this can be used 
    to cancel the entire order. 
    - Shipping the order and confirming the shipment: It completes the 
    financial transaction and notify the buyer that the order is on the
    way. If Amazon doesn't receive the shipping confirmation in 30 days,
    the order will be canceled by Amazon.
    - Adjusting the order: process refunds and returns.
    - Being paid: Settlement report are generated in each settlement period
    that has all financial transactions in that period. 

All synchronization supports two modes: an automatic mode and a manual mode.
In automatic mode, all synchronizations (including both uploading
and downloading) are executed by background processes 
at the specific interval. In manual mode,
an Odoo user can request a synchronization at any time. 
Following are several manual synchronization examples:

* Upload all catalog data to Amazon
* Download last two month's order data
* Upload this month's shipping data

Authorized Odoo users should be able to check the synchronization logs and 
should be notified when there is any error. 

Amazon integration is one of many possible integrations for Odoo
e-commerce. The integration has two parts: a common 
integration part that is shared by all possible integrations and an 
Amazon-specific part. 

## The Odoo Integration Part

An integration module (called an integrator) intercepts relevant 
Odoo events and post those events into several
integration tables that to be consumed by site-specific integration module. 
For the Amazon integration, we need to intercept the following events:

* Product creation
* Product update
* Product unlink (deletion)
* Order shipment status change and tracking number
* Order cancellation
* Order refund

When an event occurs, the integration module creates a record in 
an integration table. There are two tables: a product integration table and 
an order integration table. Each table has the following columns:

* the record id of the product or order table
* record_operation: product-specific operation (create, write, unlink) or
order-specific operations(confirmation, cancellation, shipped, etc).
* operation_data: operation data. For creation and unlink, it is product_id 
or order_id. For write, it's a list of new values.  
* operation_timestamp: time stamp of the operation
* site_name: the integration site name. If this column is None, the 
record is for all integration sites. Otherwise, it is a site-specific 
record. The column is used in manual mode that a user starts a data 
synchronization with one or more remote sites. 

In automatic synchronization mode, a record is created when an event is 
intercepted. In manual mode, records in the integration tables are created 
in a batch mode. For example, if a manual product data synchronization 
is request, we will create a set of all existing product creation 
records. It is a user's responsibility to manually delete all 
catalog data in Amazon seller center.  

Therefore the difference between the automatic mode and the manual mode 
is who creates the integration table records. In automatic mode, they
are created by event interception. In manual mode, they are created by
an integration module.
 
It is up to each site integration module to synchronize synchronize the 
integration table data with a another site. The site-specific 
synchronization status and results are store in site-specific tables. 

### Intercept Changes

An integrator intercepts relevant Odoo calls such as product creation
and creates a record in operation table.


## Amazon Integration
 
### Amazon Integration Table
Amazon integration module, like any other integration module, will 
add an amazon_status column, an amazon_status_data and an 
amazon_status_timestamp to the product_integration table 
and the order_integration table.
The amazon_status column shows the current status of the integration
result. It could be one of the following enumeration values:

* None: the integration record is just created. 
* Submitted: the integration request is submitted to Amazon
* Pending: the integration request is pending in Amazon
* Success: the integration request is processed successfully
* Error: there is an error for this record.

The amazon_status_data column contains the Amazon call result returned from 
synchronization request. 

The amazon_status_time_stamp column contains the time stamp of the current 
status update. 

### Amazon Data Table

Amazon integration module may add some columns to Odoo product or 
order table to store some Amazon-specific data such as 
Amazon product ASIN number, Amazon Order Id etc. 
 
### Amazon Integration UI

Including Amazon integration configuration UI and integration UI.
The configuration UI allows a user to configure Amazon API user 
account and integration parameters. The integration UI allows 
manual operation and check integration status.  

1. Configuration UI

    The configuration UI has the following fields to configure 
    integration parameters: 
    
    * Marketplace ID: the Amazon marketplace ID
    * Merchant ID: merchant ID of the company
    * Access Key: merchant access key
    * Secret Key: merchant secret key
    * Test connection: a button to test the above authentication parameters
    
    * Integration interval (seconds): default is 60 seconds
    * Enable automatic integration: a checkbox to enable/disable automatic
    integration.
    
2. Integration UI

    The integration UI shows the integration status. It has a product tab
    and an order tab to allow a user to check all integration results. 
    
    Additionally, it has two buttons to start manual integration: 
    
    * Manual product synchronization: a button to synchronize all products.
    * Manual order status synchronization: a button to synchronize 
     all orders in the past three months

    If a integration request reports any errors, the error data are saved 
    in database. Users in the integration user group will be notified 
    using Odoo messages. 
