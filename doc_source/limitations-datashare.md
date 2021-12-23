# Limitations for data sharing<a name="limitations-datashare"></a>

The following are limitations when working with datashares in Amazon Redshift:
+ Amazon Redshift data sharing doesn't support sharing stored procedures or Python user\-defined functions\. Only SQL user\-defined functions are supported\.
+ For cross\-account data sharing, both the producer and consumer cluster must be encrypted\.
+ If the producer database has specific collation, use the same collation settings for the consumer database\.
+ Amazon Redshift doesn't support sharing tables with interleaved sort keys and views that refer to tables with interleaved sort keys\.
+ Amazon Redshift doesn't support adding external schemas or tables to datashares\. These external schemas or tables can be from Amazon S3 data lakes or federated queries\.