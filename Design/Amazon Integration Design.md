# Odoo Amazon Integration Design

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
Amazon integration is one of many possible integrations for Odoo
e-commerce. In automatic mode, it reads product and order operations
from tables created by Amdeb integrator and synchronizes data
between Odoo and Amazon.
 
### Operation synchronization status
For Odoo operation table, we need to add a field to record the
synchronization status. There are two choices for this field:

* Option 1: we use this field to display Amazon synchronization 
status and an Odoo user can check this field to decide what to 
do to fix a problem if the synchronization for an operation fails.
This decision also means a one-to-one mapping from an Odoo operation 
to an Amazon synchronization request. This option two issues: 
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

### Operation merge
Following are possible operation combinations of a record: 

1. Any operation followed by an unlink operation: ignore everything
exception the unlink operation. 
2. A creation operation following by some write operations: ignore
all write operations. 
3. Multiple write operations: merge all write operation ordered by
their timestamp into a single write operation. 

In a single synchronization process, for each record, there can be 
only one operation after merging. The synchronization process
saves integration results in an Amazon integration table. 
The next time the synchronization process runs, 
it merges unprocessed records in operation table with failed records in
the integration table and re-try the operation again. 
The merge between operation table records and the failed integration
records allows a user to fix update operation efficiently. 

## Amazon Integration Table
### Product Integration Table
Because there are Amazon-specific attributes for either product_template
or product_product tables, we create two tables that inherit from these
two Odoo tables with additional fields.

All amazon-related field names have a prefix of `amazon-`.
These attribute will will be displayed in an `Amaozn` tab in 
`product_template` and `product_product` tables. 

Among these attributes, there is a `amazon_sync_active` flag showing
weather to synchronize this record to Amazon or not.
The default value is `True`. Changing this value to `False` 
then to `True` will generate an `crate` operation. 
We use a `create` operation instead of a `write` operation 
of all attributes because 1) the `create` operation` should
be idempotent in Amazon synchronization and 2) the `create`
operation is simpler than `write` operation of all attributes and
3) setting `active` to `False` delete a record from Amazon listing. 

### Product Synchronization Table
For the synchronization operation, there are some sync-related fields 
added to each record: 
* `model_name`: the `product_template` or `product_product` table.
* `record_id`: the record id of the table.
* `sync_timestamp`: the time stamp of the last synchronization.
* `sync_type`: creation, write or unlink. 
* `sync_values`: the combined value in write and unlink synchronization.
* `sync_status`: a code from 'New', 'Success', or 'Failure'. Default is 'New'.
* `sync_message`: a success or error message from the last synchronization.

For each combination of `model_name` and `record_id`, there is 
only one record in this table. All `Failure` records of `create` and 
`write` operations will be re-synced if there is a write operation the 
next time a synchronization process runs. All `Failure` records 
of `unlink` operations are only synced once. A user needs to handle the
`unlink` operation manually. 
 
TBD: do we allows a an option to synchronize all Odoo records 
with Amazon records? i.e, all Amazon records are same as active Odoo records, 
no more, no less. 

