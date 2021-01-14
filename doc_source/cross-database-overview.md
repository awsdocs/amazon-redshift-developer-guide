# Querying data across databases \(preview\)<a name="cross-database-overview"></a>


|  | 
| --- |
| This is prerelease documentation for the cross\-database queries feature for Amazon Redshift, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-crossdb@amazon\.com\.   | 

*Cross\-database queries* in Amazon Redshift enable you to query across databases in an Amazon Redshift cluster\. With cross\-database queries, you can query data from any database in the Amazon Redshift cluster, regardless of which database you are connected to\. Cross\-database queries eliminate data copies and simplify your data organization to support multiple business groups from the same data warehouse\.

With cross\-database queries, you can do the following:
+ **Query data across databases in Amazon Redshift cluster**

  Not only can you query from databases that you are connected to, you can also read from any other databases that you have permissions to\.

  When you query database objects on any other unconnected databases, you have read access only to those database objects\. You can use cross\-database queries to access data from any of the databases on your Amazon Redshift cluster without having to connect to that specific database\. Doing this can help you improve performance of common extract, transform, load \(ETL\) staging and processing for data that is spread across multiple databases in your Amazon Redshift cluster\. 

  You can also join datasets from multiple databases in a single query and analyze the data using business intelligence \(BI\) or analytics tools\. You can continue to set up granular table or column\-level access controls for users by using standard Amazon Redshift SQL commands\. By doing so, you can help ensure that users see only the relevant subsets of the data that they have permissions for\. 
+ **Query objects**

  You can query other database objects using fully qualified object name expressed with the three\-part notation\. The full path to any database object consists of three components, such as database name, schema, and name of the object\. You can access any object from any other database using the full path notation, such as *database\_name\.schema\.object*\. To access a particular column, use *database\_name\.schema\.object\.column*\. 

  You can also create an alias for a schema in another database using the external schema notation\. This external schema references to another database and schema pair\. Query and access the other database object using the external schema notation, such as *schema\_name\.object*\. 

  You can query various database objects, such as user tables, regular views, materialized views, and late binding views from other databases in the same read\-only query\.
+ **Manage permissions**

  Users with access privileges on objects in any databases in the Amazon Redshift cluster can query those objects\. You grant privileges to users and user groups using GRANT\. For more information, see the [GRANT](r_GRANT.md) command\. You can also revoke privileges using REVOKE when a user no longer requires to be given the access to specific database objects\. For more information, see the [REVOKE](r_REVOKE.md) command\.
+ **Work with metadata and BI tools**

  You can create an external schema to refer to a schema in another Amazon Redshift database within the same Amazon Redshift cluster\. For information, see [CREATE EXTERNAL SCHEMA](r_CREATE_EXTERNAL_SCHEMA.md) command\. After external schema references are created, Amazon Redshift shows the tables under the schema of the other database in [SVV\_EXTERNAL\_TABLES](r_SVV_EXTERNAL_TABLES.md) and [SVV\_EXTERNAL\_COLUMNS](r_SVV_EXTERNAL_COLUMNS.md) for the tools to explore the metadata\.

  To integrate cross\-database query with BI tools, you can use the following system views\. These help you view information about the metadata of objects in the connected and other databases on the Amazon Redshift cluster\.
  + [SVV\_ALL\_COLUMNS](r_SVV_ALL_COLUMNS.md)
  + [SVV\_ALL\_SCHEMAS](r_SVV_ALL_SCHEMAS.md)
  + [SVV\_ALL\_TABLES](r_SVV_ALL_TABLES.md)
  + [SVV\_REDSHIFT\_COLUMNS](r_SVV_REDSHIFT_COLUMNS.md)
  + [SVV\_REDSHIFT\_DATABASES](r_SVV_REDSHIFT_DATABASES.md)
  + [SVV\_REDSHIFT\_FUNCTIONS](r_SVV_REDSHIFT_FUNCTIONS.md)
  + [SVV\_REDSHIFT\_SCHEMAS](r_SVV_REDSHIFT_SCHEMAS.md)
  + [SVV\_REDSHIFT\_TABLES](r_SVV_REDSHIFT_TABLES.md)

**Note**  
When working with the preview, be aware of the following:  
New Amazon Redshift clusters must be created with the **PREVIEW\_2020** maintenance track\. For more information about preview tracks, see [Choosing cluster maintenance tracks](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-mgmt-maintenance-tracks)\.
This feature is currently available for test purposes only\. Don't use the feature for production use cases\.
You can't switch an existing Amazon Redshift cluster from the current or trailing state to this preview track, or vice versa\. However, you can restore data from a cluster snapshot running on the current or trailing track\.
The cross\-database queries preview period is expected to run until January 29, 2021\. Unless extended, clusters created on this track will be automatically deleted by AWS on this date\.
You can use a cluster in any AWS Regions with Amazon Redshift RA3 instance types to preview the cross\-database queries feature\.
For any questions, issues, or feedback related to the preview features during the preview period, email `redshift-crossdb@amazon.com` or open a support case with AWS Support\. 

**Topics**
+ [Considerations](cross-database_usage.md)
+ [Limitations](cross-database_limitation.md)
+ [Examples of using a cross\-database query](cross-database_example.md)
+ [Using cross\-database queries with the query editor](cross-database_console.md)