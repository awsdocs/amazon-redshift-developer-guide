# SVV\_COLUMNS<a name="r_SVV_COLUMNS"></a>

Use SVV\_COLUMNS to view catalog information about the columns of local and external tables and views, including [late\-binding views](r_CREATE_VIEW.md#r_CREATE_VIEW_late-binding-views)\.

SVV\_COLUMNS is visible to all users\. Superusers can see all rows; regular users can see only metadata to which they have access\. 

The SVV\_COLUMNS view joins table metadata from the [System catalog tables](c_intro_catalog_views.md) \(tables with a PG prefix\) and the [SVV\_EXTERNAL\_COLUMNS](r_SVV_EXTERNAL_COLUMNS.md) system view\. The system catalog tables describe Amazon Redshift database tables\. SVV\_EXTERNAL\_COLUMNS describes external tables that are used with Amazon Redshift Spectrum\. 

All users can see all rows from the system catalog tables\. Regular users can see column definitions from the SVV\_EXTERNAL\_COLUMNS view only for external tables to which they have been granted access\. Although regular users can see table metadata in the system catalog tables, they can only select data from the user\-defined tables if they own the table or have been granted access\. 

## Table columns<a name="r_SVV_COLUMNS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_COLUMNS.html)