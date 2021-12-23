# Accessing shared data in Amazon Redshift<a name="access_shared_data"></a>

You can discover shared data using standard SQL interfaces, JDBC or ODBC drivers, and the Data API\. You can also query data with high performance from familiar business intelligence \(BI\) and analytic tools\. You can perform queries by referring to the objects from other Amazon Redshift databases that are both local to and remote from your cluster that you have permissions to access\.

You can do so simply by staying connected to local databases in your cluster\. Then you can create consumer databases from datashares to consume shared data\.

After you have done so, you can perform cross\-database queries joining the datasets\. You can query objects in consumer databases using the 3\-part notation \(`consumer_database_name.schema_name.table_name`\)\. You can also query using external schema links to schemas in the consumer database\. You can query both local data and data shared from other clusters within the same query\. Such a query can reference objects from the current connected database and from other nonconnected databases, including consumer databases created from datashares\. 