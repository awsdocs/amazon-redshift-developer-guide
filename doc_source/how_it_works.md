# How data sharing works in Amazon Redshift<a name="how_it_works"></a>

Data sharing between Amazon Redshift clusters is a two\-step process: 

1. The producer cluster administrator that wants to share data creates an Amazon Redshift datashare\. The producer cluster administrator then adds the needed database objects such as schemas, tables, views to the datashare and specifies a list of consumers that the objects to be shared with\. 

   Use the following system views to see datashare objects and data consumer information for outbound datashares:
   + [SVV\_DATASHARES](r_SVV_DATASHARES.md)
   + [SVV\_DATASHARE\_CONSUMERS](r_SVV_DATASHARE_CONSUMERS.md)
   + [SVV\_DATASHARE\_OBJECTS](r_SVV_DATASHARE_OBJECTS.md)

1. The consumer cluster administrators look at the datashares for which they're granted usage and review the contents of each datashare by viewing inbound datashares using [SVV\_DATASHARES](r_SVV_DATASHARES.md)\. 

   To consume shared data, each consumer cluster administrator creates an Amazon Redshift database from the datashare\. The administrator then assigns permissions to appropriate users and groups in the consumer cluster\. Users and groups can list the shared objects as part of the standard metadata queries by viewing the following metadata system views and can start querying data immediately\.
   + [SVV\_REDSHIFT\_COLUMNS](r_SVV_REDSHIFT_COLUMNS.md)
   + [SVV\_REDSHIFT\_DATABASES](r_SVV_REDSHIFT_DATABASES.md)
   + [SVV\_REDSHIFT\_FUNCTIONS](r_SVV_REDSHIFT_FUNCTIONS.md)
   + [SVV\_REDSHIFT\_SCHEMAS](r_SVV_REDSHIFT_SCHEMAS.md)
   + [SVV\_REDSHIFT\_TABLES](r_SVV_REDSHIFT_TABLES.md)

   To view objects of both Amazon Redshift local and shared schemas and external schemas, use the following metadata system views to query them\.
   + [SVV\_ALL\_COLUMNS](r_SVV_ALL_COLUMNS.md)
   + [SVV\_ALL\_SCHEMAS](r_SVV_ALL_SCHEMAS.md)
   + [SVV\_ALL\_TABLES](r_SVV_ALL_TABLES.md)