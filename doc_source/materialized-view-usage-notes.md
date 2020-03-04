# Limitations and Usage Notes for Materialized Views<a name="materialized-view-usage-notes"></a>

When using materialized views in Amazon Redshift, be aware of the following limitations:
+ You can't define a materialized view that references or includes any of the following:
  + Any other materialized view\.
  + A standard view\. \(However, you *can* define a standard view that references materialized views\.\)
  + An external table, such as used by Amazon Redshift Spectrum or a federated query\. 
  + System tables or views\.
  + Temporary tables\.
  + User\-defined functions\.
  + The ORDER BY clause\.
  + The LIMIT clause\.
  + The OFFSET clause\.
+ A materialized view can't support incremental refreshes if it uses any of the following SQL elements:
  + OUTER JOIN \(RIGHT, LEFT, or FULL\)\.
  + Set operations: UNION, INTERSECT, EXCEPT, MINUS\.
  + Aggregate functions: AVG, MEDIAN, PERCENTILE\_CONT, MAX, MIN, LISTAGG, STDDEV\_SAMP, STDDEV\_POP, APPROXIMATE COUNT, APPROXIMATE PERCENTILE, and bitwise aggregate functions\.
**Note**  
The COUNT and SUM aggregate functions are supported\.
  + DISTINCT aggregate functions, such as DISTINCT COUNT, DISTINCT SUM, and so on\.
  + Window functions\.
  + System administration functions\. For a list, see [System Administration Functions](r_System_administration_functions.md)\. 
  + System information functions\. For a list, see [System Information Functions](r_System_information_functions.md)\. 
  + Leader node\-only functions: CURRENT\_SCHEMA, CURRENT\_SCHEMAS, HAS\_DATABASE\_PRIVILEGE, HAS\_SCHEMA\_PRIVILEGE, HAS\_TABLE\_PRIVILEGE, AGE, CURRENT\_TIME, CURRENT\_TIMESTAMP, LOCALTIME, NOW\.
  + Date functions: CURRENT\_DATE, DATE, DATE\_PART, DATE\_TRUNC, DATE\_CMP\_TIMESTAMPTZ, SYSDATE, TIMEOFDAY, TO\_TIMESTAMP\.
  + Math functions: RANDOM\.
  + Date type formatting functions: TO\_CHAR WITH TIMESTAMPTZ\. 
  + A query that uses temporary tables for query optimization, such as optimizing common subexpressions\. 
  + Subqueries in any place other than the FROM clause\.
+ If you want to modify any of the base tables for a materialized view, note the following:
  + You can add columns to a base table without affecting any materialized views that reference the table\. Other operations can leave the materialized view in a state that can't be refreshed at all\. Examples are operations such as renaming or dropping a column, changing the type of a column, and changing the name of a schema\. Such materialized views can be queried but can't be refreshed\. In this case, you must drop and recreate the materialized view\. 
  + You can perform data manipulation language \(DML\) operations on the base tables \(INSERT, UPDATE, and DELETE\)\. However, data changes aren't reflected in associated materialized views until you refresh the views\.
+ In general, you can't alter a materialized view's definition \(its SQL statement\)\. You can't rename a materialized view\. 
+ Materialized views compute their result and store it when created\. This functionality means that you can't perform late binding when creating materialized views\. In other words, any base tables or related columns referenced in the defining SQL query of the materialized view must exist and must be valid\. In addition, you must ensure that the SELECT privilege has been granted before issuing the CREATE MATERIALIZED VIEW statement\. Amazon Redshift runs the SQL query as part of creating the materialized view\. 
+ Background vacuum operations might be blocked if materialized views aren't refreshed\. After an internally defined threshold period, a vacuum operation is allowed to run\. When this vacuum operation happens, any dependent materialized views are marked for recomputation upon the next refresh \(even if they are incremental\)\. For more information about events and state changes, see [STL\_MV\_STATE](r_STL_MV_STATE.md)\. 
+ Some user\-initiated operations on base tables force a materialized view to be fully recomputed next time that a REFRESH operation is run\. Examples of such operations are a manually invoked VACUUM, a classic resize, an ALTER DISTKEY operation, an ALTER SORTKEY operation, and a truncate operation\. For more information about events and state changes, see [STL\_MV\_STATE](r_STL_MV_STATE.md)\. 
+ You must have table\-level SELECT privilege on the base tables to create a materialized view\. Even if you have column\-level privileges on specific columns, you can't create a materialized view on only those columns\. 