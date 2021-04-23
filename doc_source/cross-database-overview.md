# Querying data across databases<a name="cross-database-overview"></a>

By using *cross\-database queries* in Amazon Redshift, you can query across databases in an Amazon Redshift cluster\. With cross\-database queries, you can query data from any database in the Amazon Redshift cluster, regardless of which database you are connected to\. Cross\-database queries eliminate data copies and simplify your data organization to support multiple business groups from the same data warehouse\.

With cross\-database queries, you can do the following:
+ **Query data across databases in your Amazon Redshift cluster**\.

  Not only can you query from databases that you are connected to, you can also read from any other databases that you have permissions to\.

  When you query database objects on any other unconnected databases, you have read access only to those database objects\. You can use cross\-database queries to access data from any of the databases on your Amazon Redshift cluster without having to connect to that specific database\. Doing this can help you query and join data that is spread across multiple databases in your Amazon Redshift cluster quickly and easily\. 

  You can also join datasets from multiple databases in a single query and analyze the data using business intelligence \(BI\) or analytics tools\. You can continue to set up granular table or column\-level access controls for users by using standard Amazon Redshift SQL commands\. By doing so, you can help ensure that users see only the relevant subsets of the data that they have permissions for\. 
+ **Query objects**\.

  You can query other database objects using fully qualified object names expressed with the three\-part notation\. The full path to any database object consists of three components: database name, schema, and name of the object\. You can access any object from any other database using the full path notation, `database_name.schema_name.object_name`\. To access a particular column, use `database_name.schema_name.object_name.column_name`\. 

  You can also create an alias for a schema in another database using the external schema notation\. This external schema references to another database and schema pair\. Query can access the other database object using the external schema notation, `external_schema_name.object_name`\. 

  In the same read\-only query, you can query various database objects, such as user tables, regular views, materialized views, and late\-binding views from other databases\.
+ **Manage permissions**\.

  Users with access privileges for objects in any databases in an Amazon Redshift cluster can query those objects\. You grant privileges to users and user groups using the [GRANT](r_GRANT.md) command\. You can also revoke privileges using the [REVOKE](r_REVOKE.md) command when a user no longer requires the access to specific database objects\. 
+ **Work with metadata and BI tools\.**

  You can create an external schema to refer to a schema in another Amazon Redshift database within the same Amazon Redshift cluster\. For information, see [CREATE EXTERNAL SCHEMA](r_CREATE_EXTERNAL_SCHEMA.md) command\. 

  After external schema references are created, Amazon Redshift shows the tables under the schema of the other database in [SVV\_EXTERNAL\_TABLES](r_SVV_EXTERNAL_TABLES.md) and [SVV\_EXTERNAL\_COLUMNS](r_SVV_EXTERNAL_COLUMNS.md) for the tools to explore the metadata\.

  To integrate cross\-database query with BI tools, you can use the following system views\. These help you view information about the metadata of objects in the connected and other databases on the Amazon Redshift cluster\.

  Following are system views that show all Amazon Redshift objects and external objects of all databases in your Amazon Redshift cluster: 
  + [SVV\_ALL\_COLUMNS](r_SVV_ALL_COLUMNS.md)
  + [SVV\_ALL\_SCHEMAS](r_SVV_ALL_SCHEMAS.md)
  + [SVV\_ALL\_TABLES](r_SVV_ALL_TABLES.md)

  Following are system views that show all Amazon Redshift objects of all databases in your Amazon Redshift cluster:
  + [SVV\_REDSHIFT\_COLUMNS](r_SVV_REDSHIFT_COLUMNS.md)
  + [SVV\_REDSHIFT\_DATABASES](r_SVV_REDSHIFT_DATABASES.md)
  + [SVV\_REDSHIFT\_FUNCTIONS](r_SVV_REDSHIFT_FUNCTIONS.md)
  + [SVV\_REDSHIFT\_SCHEMAS](r_SVV_REDSHIFT_SCHEMAS.md)
  + [SVV\_REDSHIFT\_TABLES](r_SVV_REDSHIFT_TABLES.md)

**Topics**
+ [Considerations](cross-database_usage.md)
+ [Limitations](cross-database_limitation.md)
+ [Examples of using a cross\-database query](cross-database_example.md)
+ [Using cross\-database queries with the query editor](cross-database_console.md)