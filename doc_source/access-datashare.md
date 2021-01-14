# Accessing metadata for data shares<a name="access-datashare"></a>

To help cluster administrators discover data shares, Amazon Redshift provides a set of metadata views to list data shares created in your cluster and those received from other clusters in the account\. These views display the following information:
+ Data shares that are shared and received by the clusters
+ Content of database objects in the data shares including the basic share metadata, objects and consumers

Use SVV\_DATASHARES to view a list of all data shares created in your account or shared from others\. For more information, see [SVV\_DATASHARES](r_SVV_DATASHARES.md)\.

Use SVV\_DATASHARE\_CONSUMERS to view a list of data consumers\. For more information, see [SVV\_DATASHARE\_CONSUMERS](r_SVV_DATASHARE_CONSUMERS.md)\.

Use SVV\_DATASHARE\_OBJECTS to view a list of objects in all data shares created in your account or shared from others\. For more information, see [SVV\_DATASHARE\_OBJECTS](r_SVV_DATASHARE_OBJECTS.md)\.