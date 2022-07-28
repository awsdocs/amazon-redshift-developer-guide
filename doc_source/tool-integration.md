# Integrating Amazon Redshift data sharing with business intelligence tools<a name="tool-integration"></a>

To integrate data sharing with business intelligence \(BI\) tools, we recommend that you use the Amazon Redshift JDBC or ODBC drivers\. You can also query the following system metadata views to view information about Amazon Redshift database objects\. 
+ These views include objects from the Amazon Redshift database that you are currently connected to\. They also include objects from all the other databases that you have access to on the cluster, including the databases created from the datashares\.
  + [SVV\_REDSHIFT\_COLUMNS](r_SVV_REDSHIFT_COLUMNS.md)
  + [SVV\_REDSHIFT\_DATABASES](r_SVV_REDSHIFT_DATABASES.md)
  + [SVV\_REDSHIFT\_FUNCTIONS](r_SVV_REDSHIFT_FUNCTIONS.md)
  + [SVV\_REDSHIFT\_SCHEMAS](r_SVV_REDSHIFT_SCHEMAS.md)
  + [SVV\_REDSHIFT\_TABLES](r_SVV_REDSHIFT_TABLES.md)
+ In addition, these views include external objects that you can query using Amazon Redshift Spectrum and federated queries as part of their respective databases\. To see the full list of local cluster and consumer databases that Amazon Redshift uses to navigate these objects, use the SVV\_REDSHIFT\_DATABASES view\. 
  + [SVV\_ALL\_COLUMNS](r_SVV_ALL_COLUMNS.md)
  + [SVV\_ALL\_SCHEMAS](r_SVV_ALL_SCHEMAS.md)
  + [SVV\_ALL\_TABLES](r_SVV_ALL_TABLES.md)
+ You can also use the SVV\_DATASHARE\* system views to view metadata for datashares on any cluster\. If you have explicitly created EXTERNAL SCHEMA references to schemas in other databases, Amazon Redshift also returns the objects in SVV\_EXTERNAL\* system views\.
  + [SVV\_DATASHARES](r_SVV_DATASHARES.md)
  + [SVV\_DATASHARE\_CONSUMERS](r_SVV_DATASHARE_CONSUMERS.md)
  + [SVV\_DATASHARE\_OBJECTS](r_SVV_DATASHARE_OBJECTS.md)

Amazon Redshift JDBC and ODBC drivers support the `GetCatalogs` API operation in the drivers, which returns the list of all databases including those created from datashares\. The drivers also support downstream operations, such as `GetSchemas`, `GetTables`, and so on, that return data from all the databases that `GetCatalogs` returns\. The drivers provide this support even when the catalog isn't explicitly specified in the call\. For more information about JDBC or ODBC drivers, see [Configuring connections in Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/mgmt/configuring-connections.html) in the *Amazon Redshift Cluster Management Guide*\.

You can't connect to consumer databases created from datashares directly\. Connect to local databases on your cluster\. If you have a connection switching user interface in your tool, the list of databases should include only the local cluster databases\. The list should exclude consumer databases created from datashares to provide the best experience\. You can use an option in the SVV\_REDSHIFT\_DATABASES view to filter databases\.