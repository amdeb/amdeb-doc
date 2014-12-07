# Odoo Amazon Integration Design
This document describes the design for Amazon integration.  

## Integration Requirements
Odoo Amazon Integration supports the following functions:

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
* Download the last two month's order data
* Upload this month's shipping data

Authorized Odoo users should be able to check the synchronization logs and 
should be notified when there is any error.

## Amazon Integration UI
There is an Amazon configuration UI and an Amazon integration UI.
The Amazon configuration UI allows a user to configure Amazon API user 
account and integration parameters. The Amazon integration UI allows 
manual synchronization and checking integration status.  

### 1. Configuration UI

The Amazon configuration UI has the following fields to configure 
integration parameters: 

* Merchant ID: the Amazon merchant ID 
* Access Key: merchant access key
* Secret Key: merchant secret key

* Image URL: the base URL of product images
* Integration interval (seconds): default is 10 minutes
* Active flag: a checkbox to enable/disable automatic integration.
    
### 2. Integration UI

The integration UI shows the integration/synchronization status. 
It has a product tab and an order tab to allow a user to check all 
integration results. 

Additionally, it has two buttons to start manual integration: 

* Synchronize all products: a button to synchronize all product data.
* Synchronize all orders: a button to synchronize all orders
in the past three months. 

The two buttons are enabled only when automatic integration active 
flag is disabled. 

If an integration request reports any errors, the error data are saved 
in database. Users in the integration user group will be notified 
using Odoo messages.

## Data Synchronization
Amazon integration is one of many possible integration services for Odoo
e-commerce. In automatic mode, it reads product and order operations
from tables created by Amdeb integrator and synchronizes data
between Odoo and Amazon.

We create an operation table to log the related data operations 
that need to be synchronized. Because different synchronization 
services have different process and data requirements, we
need to transform the Odoo data operations to a data 
structure used for a synchronization process. We use 
a Amazon synchronization table to store and display synchronization
status. 

### Operation Data Transformation
A synchronization process first transforms product operation data 
into synchronization data. Following are possible operation combinations 
of a product record: 

1. Unlink Operation: ignore all operations followed by an unlink operation. 
    1. Ignore all product template and variant 
    operations of a product if there is an unlink 
    operation for that product template. Ignore all product variant 
    operations of a product if there is an unlink operation for that 
    product. 
    2. A product template unlink operation generates delete requests
    for all its product variants and product relationships. 
    3. A product variant unlink operation generates delete requests
    for the variant and its relationship. 
2. Create Operation: ignore all write operations following a 
creation operation.
    1. For single-variant product creation: merge product template 
    creation and variant creation into a single product creation sync request.
    2. Ignore product variant creation that does not have variation attributes.
    3. Create a creation sync operation for a product creation.
3. Write operation: 
    1. Ignore write operations if its `amazon_asin` is empty except 
    the write operation that set `amazon_sync_active` to `True`. In the
    exceptional case, create a creation sync operation.
    2. If a write operation involves the `amazon_sync_active` flag and 
    the `amazon_asin` is not empty. There are  two possible results: 
    if it is `True`, generate a create operation and 
    ignore all other write operations. If it is `False`, create a `deactivate` 
    sync operation and ignore other write operations.
    3. Multiple write operations for a product: merge all write operations ordered by
    their timestamp into a single write operation.
    4. A write operation involves a price, image, inventory field will generate a new
    price, image, inventory sync operation. 
    5. A write operation on `amazon_sync_most` will generate a `update_most`
    sync operation. Other write operations except price, image, inventory 
    are ignored. 

?? creation, update_most, and creation failure

There are three types of product creations in Amazon: parent creation, 
child creation and single-variant creation. Because
Amazon MWS uses multiple steps to create a product, 
there are two possible methods to handle this: 

* Create multiple synchronization record after a 
successful product creation request.
* use a creation status to remember the creation progress.

The first method is better because it explicitly creates 
all tasks required for a product creation. This enables 
us to send all following requests concurrently as well as
to merge with related operation data. For example, if there is 
a price sync task, we don't need to create a new sync task 
for price synchronization for a product creation. 

The synchronization process saves integration results 
in an Amazon integration table. 
The next time the synchronization process runs, 
it merges unprocessed records in operation table with failed records in
the integration table and re-try the operation again. 
The merge between operation table records and the failed integration
records allows a user to fix update operation efficiently.

### Operation synchronization status
For Odoo operation table, we need to add a field to record the
synchronization status. There are two choices for this field:

* Option 1: we use this field to display Amazon synchronization 
status and an Odoo user can check this field to decide what to 
do to fix a problem if the synchronization for an operation fails.
This decision also means a one-to-one mapping from an Odoo operation 
to an Amazon synchronization request. This option has two issues: 
First it requires one-to-one mapping from an Odoo operation to a
synchronization operation that might be impossible or less efficient. 
Second, if one of multiple updates of the same record fails, it is
hard to tell if it needs a fix because another update in the same
batch may fix the error. For example, setting a price to 
an invalid value then correcting it quickly will generate 
two updates and the first one will fail and the second one
will succeed.
* Option 2: we use this field as a flag to indicate whether 
this operation is processed by synchronization process or not.
The actual synchronization status of a record operation is 
stored in another synchronization result table. This allows
us to combine multiple operations into a single synchronization 
record. For example, a creation operation followed by several
updates can be combined into a single creation operation 
synchronization. All operations of a record followed by 
an unlink operation of the same record can be merged into a single 
unlink operation. This also allows us to report record-level
synchronization status because all operations of a record can 
be combined into a single synchronization request. If something
wrong, it can be retried in the next synchronization process. 
 
We decide to use option 2. An amazon_status_time_stamp column 
is added to each operation table to indicate whether an 
operation is synchronized or not. 

### Error Recovering
The synchronization process will automatically retry 
recoverable synchronization failures. Following are solutions
for failures that cannot be solved by re-sending of a request. 

* creation failure: manual fix by deactivate and reactivate a product in Odoo
* update failure: manual fix by a new update in Odoo
* `update_most` failure: manual fix in Odoo  
* price failure: manual fix by setting a new price in Odoo
* inventory failure: manual fix by setting a new inventory quantity in Odoo
* image failure: manual fix by changing image version in Odoo
* deactivate failure: manual fix in Amazon 
* unlink failure: manual fix in Amazon 

## Amazon Integration Table
### Product Table
Because there are Amazon-specific attributes for both product_template
and product_product tables, we create two tables that inherit from these
two Odoo tables with additional fields.

All amazon-related field names have a prefix of `amazon-`.
These attribute will be displayed in an `Amazon` tab in 
`product_template` view if there is no multiple variants.
It is in an `Amazon` tab in the `product_product` view if there 
are multiple product variants.  

#### The `amazon_sync_active` flag
There is a  flag showing weather to synchronize this record to Amazon or not.
Because all Amazon products require a SKU value, we use 
the presence of SKU to control the `amazon_sync_active` flag. 
In creation, the default value of the `amazon_sync_active` flag is `False`
unless the SKU value is set. When it is set to `False` in creation, 
this record is ignored by Amazon synchronization process. 

Changing this value from `True` to `False`
will de-activate a product in Amazon. 
Changing this flag from `False` to `True` will 
generate an `crate` operation. 

We use a `create` operation instead of a `write` operation 
of all attributes because 1) the `create` operation should
be idempotent in Amazon synchronization and 2) the `create`
operation is simpler than a `write` operation of all attributes and
3) setting `active` to `False` delete a record from Amazon listing. 

#### The `amazon_image_version' field
This is a short string field indicating the image version.  
When there is a change in this field, we send a product image request
thus update Amazon product images.

#### The `amazon_asin` field
This field stores Amazon ASIN number for a product. It is a read-only 
field for a user. This field also serves as a flag indicating 
if a product is successfully created in Amazon. If it is empty, 
all product operations except the `create` are ignored.  

#### The `amazon_sync_most` field
This is used to sync all product attributes except price, inventory 
and images. This field is useful when a user updates many product 
attributes but the synchronization is failed -- it allows the user
to  fix one or more attributes and re-sync with Amazon. 

By default it is `False` and will be set set to `False` when 
it is processed -- regardless the processing result.

### Product Operation Table
This table stores the intercepted product operation data. 
It has the following field: 

* `model_name`: a string of `product_template` or `product_product`.
* `record_id`: the record id of the table.
* `template_id`: the product template record id of the current record. 
For the `product_template` table operation, it is the same 
as `record_id`. 
* `record_operation`: creation, write or unlink. 
* `operation_data`: the combined value in write and unlink synchronization.
* `operation_timestamp`: the operation time stamp. It is set
automatically when an operation record is created. 
* `sync_timestamp`: the synchronization time stamp. This is 
set when a synchronization process processed a record.
 
To control the operation table size, we create a cron job 
that runs once a month to clean up operation records 
that exist longer than 100 days.

### Amazon Product Synchronization Table
All Amazon MWS API calls run in a batch model. AMWS splits a product creation 
into multiple steps. We need to transform product operation data into 
a data structure that is suitable for synchronization process. 
The synchronization table has the following fields: 

* `model_name`: the table name of either `product_template` or `product_product` 
* `record_id`: the record id of the table.
* `template_id`: the product template record id
* `sync_timestamp`: the time stamp of the last synchronization.
* `sync_type`: creation, update, update_most, delete, price, inventory, image, deactivate. 
* `sync_data`: the synchronization data
* `sync_status`: a status of 'Success', or 'Failure'. 
* `sync_message`: a success or error message returned from the 
last synchronization.

For each combination of `model_name`, `record_id` and `sync_type`, there is 
only one record in this table. All `Failure` records of `create` and 
`write` operations will be re-synced if there is a write operation the 
next time a synchronization process runs. All `Failure` records 
of `unlink` operations are only synced once. A user needs to handle the
`unlink` operation manually. 

 
## TBD 
do we allows an option to synchronize all Odoo records 
with Amazon records? i.e, all Amazon records are the same as active 
Odoo records, no more, no less. 

