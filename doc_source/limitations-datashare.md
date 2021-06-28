# Limitations for data sharing<a name="limitations-datashare"></a>

The following are limitations when working with datashares in Amazon Redshift:
+ Amazon Redshift data sharing doesn't support sharing stored procedures or Python user\-defined functions\. Only SQL user\-defined functions are supported\.
+ Amazon Redshift data sharing doesn't support concurrency scaling for queries that query for shared objects\.
+ Amazon Redshift doesn't support sharing data across AWS Regions\.
+ Amazon Redshift doesn't support sharing tables with interleaved sort keys and views that refer to tables with interleaved sort keys\.
+ Amazon Redshift doesn't support adding external schemas or tables to datashares\. These external schemas or tables can be from Amazon S3 data lakes or federated queries\.