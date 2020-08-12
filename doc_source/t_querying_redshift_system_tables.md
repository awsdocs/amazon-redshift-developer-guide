# Step 5: Query the system tables<a name="t_querying_redshift_system_tables"></a>

In addition to the tables that you create, your database contains a number of system tables\. These system tables contain information about your installation and about the various queries and processes that are running on the system\. You can query these system tables to collect information about your database\.

**Note**  
The description for each table in the System Tables Reference indicates whether a table is visible to all users or visible only to superusers\. You must be logged in as a superuser to query tables that are visible only to superusers\.

Amazon Redshift provides access to the following types of system tables:
+  [STL views for logging](c_intro_STL_tables.md) 

  These system tables are generated from Amazon Redshift log files to provide a history of the system\. Logging tables have an STL prefix\.
+  [STV tables for snapshot data](c_intro_STV_tables.md) 

  These tables are virtual system tables that contain snapshots of the current system data\. Snapshot tables have an STV prefix\.
+  [System views](c_intro_system_views.md) 

  System views contain a subset of data found in several of the STL and STV system tables\. Systems views have an SVV or SVL prefix\.
+  [System catalog tables](c_intro_catalog_views.md) 

  The system catalog tables store schema metadata, such as information about tables and columns\. System catalog tables have a PG prefix\.

You may need to specify the process ID associated with a query to retrieve system table information about that query\. For information, see [Determine the process ID of a running query](determine_pid.md)\.

## View a list of table names<a name="t_querying_redshift_system_tables-view-a-list-of-table-names"></a>

For example, to view a list of all tables in the public schema, you can query the PG\_TABLE\_DEF system catalog table\. 

```
select distinct(tablename) from pg_table_def where schemaname = 'public'; 
```

The result will look something like this: 

```
tablename
---------
category
date
event
listing
sales
testtable
users
venue
```

## View database users<a name="t_querying_redshift_system_tables-view-database-users"></a>

You can query the PG\_USER catalog to view a list of all database users, along with the user ID \(USESYSID\) and user privileges\. 

```
select * from pg_user;
  usename   | usesysid | usecreatedb | usesuper | usecatupd |  passwd  | valuntil | useconfig

------------+----------+-------------+----------+-----------+----------+----------+-----------
 rdsdb      |        1 | t           | t        | t         | ******** |          |
 masteruser |      100 | t           | t        | f         | ******** |          |
 dwuser     |      101 | f           | f        | f         | ******** |          |
 simpleuser |      102 | f           | f        | f         | ******** |          |
 poweruser  |      103 | f           | t        | f         | ******** |          |
 dbuser     |      104 | t           | f        | f         | ******** |          |
(6 rows)
```

The user name `rdsdb` is used internally by Amazon Redshift to perform routine administrative and maintenance tasks\. You can filter your query to show only user\-defined user names by adding `where usesysid > 1` to your select statement\.

```
select * from pg_user
where usesysid > 1;

  usename   | usesysid | usecreatedb | usesuper | usecatupd |  passwd  | valuntil | useconfig
------------+----------+-------------+----------+-----------+----------+----------+-----------
 masteruser |      100 | t           | t        | f         | ******** |          |
 dwuser     |      101 | f           | f        | f         | ******** |          |
 simpleuser |      102 | f           | f        | f         | ******** |          |
 poweruser  |      103 | f           | t        | f         | ******** |          |
 dbuser     |      104 | t           | f        | f         | ******** |          |
(5 rows)
```

## View recent queries<a name="t_querying_redshift_system_tables-view-recent-queries"></a>

In the previous example, you found that the user ID \(USESYSID\) for masteruser is 100\. To list the five most recent queries executed by masteruser, you can query the SVL\_QLOG view\. The SVL\_QLOG view is a friendlier subset of information from the STL\_QUERY table\. You can use this view to find the query ID \(QUERY\) or process ID \(PID\) for a recently run query or to see how long it took a query to complete\. SVL\_QLOG includes the first 60 characters of the query string \(SUBSTRING\) to help you locate a specific query\. Use the LIMIT clause with your SELECT statement to limit the results to five rows\. 

```
select query, pid, elapsed, substring from svl_qlog
where userid = 100
order by starttime desc
limit 5;
```

The result will look something like this: 

```
 query  |  pid  | elapsed  |                          substring
--------+-------+----------+--------------------------------------------------------------
 187752 | 18921 | 18465685 | select query, elapsed, substring from svl_qlog order by query
 204168 |  5117 |    59603 | insert into testtable values (100);
 187561 | 17046 |  1003052 | select * from pg_table_def where tablename = 'testtable';
 187549 | 17046 |  1108584 | select * from STV_WLM_SERVICE_CLASS_CONFIG
 187468 | 17046 |  5670661 | select * from pg_table_def where schemaname = 'public';
(5 rows)
```