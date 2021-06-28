# Accessing metadata for datashares<a name="access-datashare"></a>

To help cluster administrators discover datashares, Amazon Redshift provides a set of metadata views to list datashares\. These views list datashares created in your cluster and also those received from other clusters within the same account and from other accounts \(preview\)\. These views display the following information:
+ Datashares that are shared and received by the clusters
+ Contents of database objects in the datashares, including the basic share metadata, objects, and consumers

Use SVV\_DATASHARES to view a list of all datashares created in your cluster \(outbound\) and shared from others \(inbound\)\. For more information, see [SVV\_DATASHARES](r_SVV_DATASHARES.md)\.

Use SVV\_DATASHARE\_CONSUMERS to view a list of data consumers\. For more information, see [SVV\_DATASHARE\_CONSUMERS](r_SVV_DATASHARE_CONSUMERS.md)\.

Use SVV\_DATASHARE\_OBJECTS to view a list of objects in all datashares created in your cluster \(outbound\) and shared from others \(inbound\)\. For more information, see [SVV\_DATASHARE\_OBJECTS](r_SVV_DATASHARE_OBJECTS.md)\.