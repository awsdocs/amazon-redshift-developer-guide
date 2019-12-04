# Limitations and Usage Notes<a name="mv-usage-notes"></a>


|  | 
| --- |
| This is prerelease documentation for the materialized view feature for Amazon Redshift, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

When using materialized views in Amazon Redshift, be aware of the following:

1. You can't define a materialized view that references any of the following:
   + Any other materialized view\.
   + A standard view\. \(However, you *can* define a standard view that references materialized view\(s\)\.\)
   + An external table\.
   + System tables or views
   + Temporary tables

1. A materialized view can't support incremental refreshes if it uses any of the following SQL elements:
   + OUTER JOIN
   + Set operations \(UNION, INTERSECT, EXCEPT, MINUS\)
   + Aggregate functions: AVG, MEDIAN, PERCENTILE\_CONT, MAX, MIN, LISTAGG, STDDEV\_SAMP, STDDEV\_POP, APPROXIMATE COUNT, APPROXIMATE PERCENTILE, and bit\-wise aggregate functions
   + DISTINCT aggregate functions, such as DISTINCT COUNT, DISTINCT SUM etc
   + Queries that require a VOLT temporary table
   + Window functions
   + System administration functions
   + System information functions
   + Leader node\-only functions: CURRENT\_SCHEMA, CURRENT\_SCHEMAS, HAS\_DATABASE\_PRIVILEGE, HAS\_SCHEMA\_PRIVILEGE, HAS\_TABLE\_PRIVILEGE, AGE, CURRENT\_TIME, CURRENT\_TIMESTAMP, LOCALTIME, NOW
   + Date functions: DATE, DATE\_PART, DATE\_TRUNC, DATE\_CMP\_TIMESTAMPTZ, SYSDATE, TO\_TIMESTAMP 
   + Math functions: rand function 
   + Date type formatting functions: TO\_CHAR WITH TIMESTAMPTZ 
   + ORDER BY
   + LIMIT
   + OFFSET
   + Subqueries in any place other than the FROM clause
   + The query can only have a single GROUP BY clause\.

1. If you want to modify any of the base tables for a materialized view, note the following:
   + You can add columns to a base table without affecting any materialized views that reference the table\.
   + You can't drop any columns in a base table\.
   + You can perform DML operations on the base tables \(INSERT, UPDATE or DELETE\)\. However, data changes won't be reflected in associated materialized views until you refresh them\.

1. You can't replace a materialized view's definition \(its SQL statement\)\. You can't rename a materialized view, nor can you rename any of its columns\.

1. Late binding is not allowed when creating materialized views\. In other words, any base tables or related columns referenced in the defining SQL query of the materialized view must exist and must be valid\. In addition, you must ensure that the SELECT privilege has been granted prior to issuing the CREATE MATERIALIZED VIEW statement\. Amazon Redshift executes the SQL query as part of creating the materialized view\.

1. VACUUM operations may be blocked if materialized views are not refreshed\. After an internally\-defined threshold period, VACUUM is allowed to run\. When this happens, any dependent materialized views are marked for recomputation upon the next refresh \(even if they are incremental\)\.

1. Some user initiated operations on base tables force a materialized view to be fully recomputed next time REFRESH is run\. For example, operations such as manually invoked vacuum, classic resize, alter distkey, alter sortkey, and truncate\.  

1. Furthermore, other operations may leave the materialized view in a state that can't be refreshed at all\. For example, operations such as renaming or dropping a column, changing the type of a column\. and changing the name of a schema\. Such materialized views can be queried but can't be refreshed\. In this case, you must drop and recreate the materialized view\. 

1. The creation of a materialized view might fail when using a cursors enabled JDBC connection \(such as the Amazon Redshift console\), and the materialized view is refreshed incrementally\. 

   ```
   [Amazon](500310) Invalid operation: Materialized view could not be created, reason: relation "mv_tbl__name__0" already exists.
   ```

   Where *name* is the name of the materialized view\.

   Don't use a cursors enable JDBC connection when creating an incrementally maintainable materialized view\. Instead, use an interface such as psql or SQL Workbench configured to run without cursors enabled \(`Fetch Size` option\)\. The following is a list of example outcomes when creating a materialized view on the Amazon Redshift console:
   + The following statement fails because this materialized view is incrementally maintainable\.

     ```
     create materialized view mv1 as select venueid from venue;
     ```
   + The following statement succeeds because `distinct count()` is not incrementally maintainable\. 

     ```
     create materialized view mv2 as select distinct count(venuename) from venue;
     ```
   + The following statement succeeds because `UNION` is not incrementally maintainable\. 

     ```
     create materialized view mv3 as select venueid from venue UNION select venueseats from venue;
     ```

1. If you encounter an error creating a materialized view, an Amazon Redshift internal table might be created and prevent you from creating another materialized view with the same name\. Drop the internal table before trying to create a materialized view with the same name\. 

   ```
    DROP TABLE mv_tbl__name__0
   ```

   Where *name* is the name of the materialized view\.