# Cluster management and data sharing<a name="cluster-mgmt"></a>

Consider the following for cluster management tasks:
+ Data sharing continues to work when clusters are resized\.
+ Data sharing works across Availability Zones\.
+ When a producer cluster is deleted, Amazon Redshift deletes the data shares created by the producer cluster\. If the producer cluster is recreated by restoring from a snapshot, you must create data shares and grant permissions again\. When a producer cluster is backed up and restored, the data shares on the restored cluster still persist\. However, you must set up data sharing again by adding consumers\.
+ When a consumer cluster is deleted and restored from a snapshot, the producer cluster administrator must share data shares to the restored consumer cluster again\.