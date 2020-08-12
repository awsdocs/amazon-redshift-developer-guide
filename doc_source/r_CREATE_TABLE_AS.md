# CREATE TABLE AS<a name="r_CREATE_TABLE_AS"></a>

**Topics**
+ [Syntax](#r_CREATE_TABLE_AS-synopsis)
+ [Parameters](#r_CREATE_TABLE_AS-parameters)
+ [CTAS usage notes](r_CTAS_usage_notes.md)
+ [CTAS examples](r_CTAS_examples.md)

Creates a new table based on a query\. The owner of this table is the user that issues the command\.

The new table is loaded with data defined by the query in the command\. The table columns have names and data types associated with the output columns of the query\. The CREATE TABLE AS \(CTAS\) command creates a new table and evaluates the query to load the new table\.

## Syntax<a name="r_CREATE_TABLE_AS-synopsis"></a>

```
CREATE [ [LOCAL ] { TEMPORARY | TEMP } ]
TABLE table_name     
[ ( column_name [, ... ] ) ]
[ BACKUP { YES | NO } ]
[ table_attributes ]   
AS query 

where table_attributes are:  
[ DISTSTYLE { EVEN | ALL | KEY } ]
[ DISTKEY( distkey_identifier ) ]
[ [ COMPOUND | INTERLEAVED ] SORTKEY( column_name [, ...] ) ]
```

## Parameters<a name="r_CREATE_TABLE_AS-parameters"></a>

LOCAL   
Although this optional keyword is accepted in the statement, it has no effect in Amazon Redshift\.

TEMPORARY \| TEMP   
Creates a temporary table\. A temporary table is automatically dropped at the end of the session in which it was created\.

 *table\_name*   
The name of the table to be created\.  
If you specify a table name that begins with '\# ', the table is created as a temporary table\. For example:  

```
create table #newtable (id) as select * from oldtable;
```
The maximum table name length is 127 bytes; longer names are truncated to 127 bytes\. Amazon Redshift enforces a quota of the number of tables per cluster by node type\. The table name can be qualified with the database and schema name, as the following table shows\.  

```
create table tickit.public.test (c1) as select * from oldtable;
```
In this example, `tickit` is the database name and `public` is the schema name\. If the database or schema doesn't exist, the statement returns an error\.  
If a schema name is given, the new table is created in that schema \(assuming the creator has access to the schema\)\. The table name must be a unique name for that schema\. If no schema is specified, the table is created using the current database schema\. If you are creating a temporary table, you can't specify a schema name, since temporary tables exist in a special schema\.  
Multiple temporary tables with the same name are allowed to exist at the same time in the same database if they are created in separate sessions\. These tables are assigned to different schemas\.

 *column\_name*   
The name of a column in the new table\. If no column names are provided, the column names are taken from the output column names of the query\. Default column names are used for expressions\.

BACKUP \{ YES \| NO \}   
A clause that specifies whether the table should be included in automated and manual cluster snapshots\. For tables, such as staging tables, that won't contain critical data, specify BACKUP NO to save processing time when creating snapshots and restoring from snapshots and to reduce storage space on Amazon Simple Storage Service\. The BACKUP NO setting has no effect on automatic replication of data to other nodes within the cluster, so tables with BACKUP NO specified are restored in the event of a node failure\. The default is BACKUP YES\.

DISTSTYLE \{ EVEN \| KEY \| ALL \}  
Defines the data distribution style for the whole table\. Amazon Redshift distributes the rows of a table to the compute nodes according the distribution style specified for the table\.  
The distribution style that you select for tables affects the overall performance of your database\. For more information, see [Choosing a data distribution style](t_Distributing_data.md)\.  
+ EVEN: The data in the table is spread evenly across the nodes in a cluster in a round\-robin distribution\. Row IDs are used to determine the distribution, and roughly the same number of rows are distributed to each node\. This is the default distribution method\.
+ KEY: The data is distributed by the values in the DISTKEY column\. When you set the joining columns of joining tables as distribution keys, the joining rows from both tables are collocated on the compute nodes\. When data is collocated, the optimizer can perform joins more efficiently\. If you specify DISTSTYLE KEY, you must name a DISTKEY column\.
+  ALL: A copy of the entire table is distributed to every node\. This distribution style ensures that all the rows required for any join are available on every node, but it multiplies storage requirements and increases the load and maintenance times for the table\. ALL distribution can improve execution time when used with certain dimension tables where KEY distribution isn't appropriate, but performance improvements must be weighed against maintenance costs\. 

DISTKEY \(*column*\)  
Specifies a column name or positional number for the distribution key\. Use the name specified in either the optional column list for the table or the select list of the query\. Alternatively, use a positional number, where the first column selected is 1, the second is 2, and so on\. Only one column in a table can be the distribution key:  
+ If you declare a column as the DISTKEY column, DISTSTYLE must be set to KEY or not set at all\.
+ If you don't declare a DISTKEY column, you can set DISTSTYLE to EVEN\.
+ If you don't specify DISTKEY or DISTSTYLE, CTAS determines the distribution style for the new table based on the query plan for the SELECT clause\. For more information, see [Inheritance of column and table attributes](r_CTAS_usage_notes.md#r_CTAS_usage_notes-inheritance-of-column-and-table-attributes)\.
You can define the same column as the distribution key and the sort key; this approach tends to accelerate joins when the column in question is a joining column in the query\.

\[ COMPOUND \| INTERLEAVED \] SORTKEY \( *column\_name* \[, \.\.\. \] \)  
Specifies one or more sort keys for the table\. When data is loaded into the table, the data is sorted by the columns that are designated as sort keys\.   
You can optionally specify COMPOUND or INTERLEAVED sort style\. The default is COMPOUND\. For more information, see [Choosing sort keys](t_Sorting_data.md)\.  
You can define a maximum of 400 COMPOUND SORTKEY columns or 8 INTERLEAVED SORTKEY columns per table\.   
If you don't specify SORTKEY, CTAS determines the sort keys for the new table based on the query plan for the SELECT clause\. For more information, see [Inheritance of column and table attributes](r_CTAS_usage_notes.md#r_CTAS_usage_notes-inheritance-of-column-and-table-attributes)\.    
COMPOUND  
Specifies that the data is sorted using a compound key made up of all of the listed columns, in the order they are listed\. A compound sort key is most useful when a query scans rows according to the order of the sort columns\. The performance benefits of sorting with a compound key decrease when queries rely on secondary sort columns\. You can define a maximum of 400 COMPOUND SORTKEY columns per table\.   
INTERLEAVED  
Specifies that the data is sorted using an interleaved sort key\. A maximum of eight columns can be specified for an interleaved sort key\.   
An interleaved sort gives equal weight to each column, or subset of columns, in the sort key, so queries don't depend on the order of the columns in the sort key\. When a query uses one or more secondary sort columns, interleaved sorting significantly improves query performance\. Interleaved sorting carries a small overhead cost for data loading and vacuuming operations\. 

AS *query*   
Any query \(SELECT statement\) that Amazon Redshift supports\.