# Odoo Amdeb Integrator Design

## Product Operation Integration

Odoo Amdeb integrator intercepts eCommerce-related operations 
and log those operations into integration tables. 
The logs are consumed by site-specific integration module. 
It needs to intercept and log the following operations:

* Product creation 
* Product update
* Product unlink (deletion)
* Order shipment status change and tracking number
* Order cancellation
* Order refund

When an operation occurs, the integration module creates a record in 
an integration table. There are two tables: a product operation table and 
an order operation table. 

### Product operation table
Odoo uses two tables to manage product information: product_template and 
product_product. The integrator needs to intercept operations of both 
tables. The product operation table has the following columns: 

* model name: either product_template or product_product
* the record id of the operation 
* template_id: the record id of the product_template table. In unlink
operations, it is used to link a product_product id with its template id. 
* record_operation: operation types including create, write, unlink
* operation_data: operation data. For creation, it is not used. 
For write, it's a list of new values. For unlink, it includes
ean13 and default_code of a product. 
* operation_timestamp: time stamp of the operation

### Site-specific integration status
The product operation table is to be inherited by a site-specific 
integration addon to record the integration status of each operation. 
For example, an Amazon integration addon may add an 
Amazon_sync_status to record the synchronization status of an Amazon store. 

### Intercept Product operations
When Odoo creates a product, it creates a product_template record and 
at least one product_product record. Conceptually, a product_product record
is a product variant of a product_template. A product_template may 
have one or multiple product variants. Some product data are attributes
of a product_template record while some product data are attributes
of a product_product record. Therefore, an integrator needs to 
intercept and log operations of both tables. 



