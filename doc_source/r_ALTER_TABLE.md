# ALTER TABLE<a name="r_ALTER_TABLE"></a>

**Topics**
+ [Syntax](#r_ALTER_TABLE-synopsis)
+ [Parameters](#r_ALTER_TABLE-parameters)
+ [ALTER TABLE examples](r_ALTER_TABLE_examples_basic.md)
+ [ALTER EXTERNAL TABLE examples](r_ALTER_TABLE_external-table.md)
+ [ALTER TABLE ADD and DROP COLUMN examples](r_ALTER_TABLE_COL_ex-add-drop.md)

Changes the definition of a database table or Amazon Redshift Spectrum external table\. This command updates the values and properties set by CREATE TABLE or CREATE EXTERNAL TABLE\.

You can't run ALTER TABLE on an external table within a transaction block \(BEGIN \.\.\. END\)\. For more information about transactions, see [Serializable isolation](c_serial_isolation.md)\. 

**Note**  
ALTER TABLE locks the table for read and write operations until the transaction enclosing the ALTER TABLE operation completes\. 

## Syntax<a name="r_ALTER_TABLE-synopsis"></a>

```
ALTER TABLE table_name
{
ADD table_constraint 
| DROP CONSTRAINT constraint_name [ RESTRICT | CASCADE ] 
| OWNER TO new_owner 
| RENAME TO new_name 
| RENAME COLUMN column_name TO new_name            
| ALTER COLUMN column_name TYPE new_data_type              
| ALTER DISTKEY column_name 
| ALTER DISTSTYLE ALL       
| ALTER DISTSTYLE EVEN
| ALTER DISTSTYLE KEY DISTKEY column_name 
| ALTER DISTSTYLE AUTO             
| ALTER [COMPOUND] SORTKEY ( column_name [,...] ) 
| ALTER SORTKEY AUTO 
| ALTER SORTKEY NONE
| ADD [ COLUMN ] column_name column_type
  [ DEFAULT default_expr ]
  [ ENCODE encoding ]
  [ NOT NULL | NULL ] |
| DROP [ COLUMN ] column_name [ RESTRICT | CASCADE ] }

where table_constraint is:

[ CONSTRAINT constraint_name ]
{ UNIQUE ( column_name [, ... ] )  
| PRIMARY KEY ( column_name [, ... ] ) 
| FOREIGN KEY (column_name [, ... ] )
   REFERENCES  reftable [ ( refcolumn ) ]}

The following options apply only to external tables:

SET LOCATION { 's3://bucket/folder/' | 's3://bucket/manifest_file' } 
| SET FILE FORMAT format |
| SET TABLE PROPERTIES ('property_name'='property_value') 
| PARTITION ( partition_column=partition_value [, ...] ) 
  SET LOCATION { 's3://bucket/folder' |'s3://bucket/manifest_file' } 
| ADD [IF NOT EXISTS] 
    PARTITION ( partition_column=partition_value [, ...] ) LOCATION { 's3://bucket/folder' |'s3://bucket/manifest_file' }
    [, ... ]
| DROP PARTITION ( partition_column=partition_value [, ...] )
```

To reduce the time to run the ALTER TABLE command, you can combine some clauses of the ALTER TABLE command\.

Amazon Redshift supports the following combinations of the ALTER TABLE clauses:

```
ALTER TABLE tablename ALTER SORTKEY (column_list), ALTER DISTKEY column_Id;
ALTER TABLE tablename ALTER DISTKEY column_Id, ALTER SORTKEY (column_list);
ALTER TABLE tablename ALTER SORTKEY (column_list), ALTER DISTSTYLE ALL;
ALTER TABLE tablename ALTER DISTSTYLE ALL, ALTER SORTKEY (column_list);
```

## Parameters<a name="r_ALTER_TABLE-parameters"></a>

 *table\_name*   
The name of the table to alter\. Either specify just the name of the table, or use the format *schema\_name\.table\_name* to use a specific schema\. External tables must be qualified by an external schema name\. You can also specify a view name if you are using the ALTER TABLE statement to rename a view or change its owner\. The maximum length for the table name is 127 bytes; longer names are truncated to 127 bytes\. You can use UTF\-8 multibyte characters up to a maximum of four bytes\. For more information about valid names, see [Names and identifiers](r_names.md)\.

ADD *table\_constraint*   
A clause that adds the specified constraint to the table\. For descriptions of valid *table\_constraint* values, see [CREATE TABLE](r_CREATE_TABLE_NEW.md)\.  
You can't add a primary\-key constraint to a nullable column\. If the column was originally created with the NOT NULL constraint, you can add the primary\-key constraint\.

DROP CONSTRAINT *constraint\_name*   
A clause that drops the named constraint from the table\. To drop a constraint, specify the constraint name, not the constraint type\. To view table constraint names, run the following query\.  

```
select constraint_name, constraint_type 
from information_schema.table_constraints;
```

RESTRICT   
A clause that removes only the specified constraint\. RESTRICT is an option for DROP CONSTRAINT\. RESTRICT can't be used with CASCADE\. 

CASCADE   
A clause that removes the specified constraint and anything dependent on that constraint\. CASCADE is an option for DROP CONSTRAINT\. CASCADE can't be used with RESTRICT\.

OWNER TO *new\_owner*   
A clause that changes the owner of the table \(or view\) to the *new\_owner* value\.

RENAME TO *new\_name*   
A clause that renames a table \(or view\) to the value specified in *new\_name*\. The maximum table name length is 127 bytes; longer names are truncated to 127 bytes\.  
You can't rename a permanent table to a name that begins with '\#'\. A table name beginning with '\#' indicates a temporary table\.  
You can't rename an external table\.

ALTER COLUMN *column\_name* TYPE *new\_data\_type*   
A clause that changes the size of a column defined as a VARCHAR data type\. Consider the following limitations:  
+ You can't alter a column with compression encodings BYTEDICT, RUNLENGTH, TEXT255, or TEXT32K\. 
+ You can't decrease the size less than maximum size of existing data\. 
+ You can't alter columns with default values\. 
+ You can't alter columns with UNIQUE, PRIMARY KEY, or FOREIGN KEY\. 
+ You can't alter columns within a transaction block \(BEGIN \.\.\. END\)\. For more information about transactions, see [Serializable isolation](c_serial_isolation.md)\. 

ALTER DISTSTYLE ALL  
A clause that changes the existing distribution style of a table to `ALL`\. Consider the following:  
+ An ALTER DISTSYTLE, ALTER SORTKEY, and VACUUM can't run concurrently on the same table\. 
  + If VACUUM is currently running, then running ALTER DISTSTYLE ALL returns an error\. 
  + If ALTER DISTSTYLE ALL is running, then a background vacuum doesn't start on a table\. 
+ The ALTER DISTSTYLE ALL command is not supported for tables with interleaved sort keys and temporary tables\.
+ If the distribution style was previously defined as AUTO, then the table is no longer a candidate for automatic table optimization\. 
For more information about DISTSTYLE ALL, see [CREATE TABLE](r_CREATE_TABLE_NEW.md)\.

ALTER DISTSTYLE EVEN  
A clause that changes the existing distribution style of a table to `EVEN`\. Consider the following:  
+ An ALTER DISTSYTLE, ALTER SORTKEY, and VACUUM can't run concurrently on the same table\. 
  + If VACUUM is currently running, then running ALTER DISTSTYLE EVEN returns an error\. 
  + If ALTER DISTSTYLE EVEN is running, then a background vacuum doesn't start on a table\. 
+ The ALTER DISTSTYLE EVEN command is not supported for tables with interleaved sort keys and temporary tables\.
+ If the distribution style was previously defined as AUTO, then the table is no longer a candidate for automatic table optimization\. 
For more information about DISTSTYLE EVEN, see [CREATE TABLE](r_CREATE_TABLE_NEW.md)\.

ALTER DISTKEY *column\_name* or ALTER DISTSTYLE KEY DISTKEY *column\_name*  
A clause that changes the column used as the distribution key of a table\. Consider the following:  
+ VACUUM and ALTER DISTKEY can't run concurrently on the same table\. 
  + If VACUUM is already running, then ALTER DISTKEY returns an error\.
  + If ALTER DISTKEY is running, then background vacuum doesn't start on a table\.
  + If ALTER DISTKEY is running, then foreground vacuum returns an error\.
+ You can only run one ALTER DISTKEY command on a table at a time\. 
+ The ALTER DISTKEY command is not supported for tables with interleaved sort keys\. 
+ If the distribution style was previously defined as AUTO, then the table is no longer a candidate for automatic table optimization\. 
When specifying DISTSTYLE KEY, the data is distributed by the values in the DISTKEY column\. For more information about DISTSTYLE, see [CREATE TABLE](r_CREATE_TABLE_NEW.md)\.

ALTER DISTSTYLE AUTO  
A clause that changes the existing distribution style of a table to AUTO\.   
When you alter a distribution style to AUTO, the distribution style of the table is set to the following:   
+ A small table with DISTSTYLE ALL is converted to AUTO\(ALL\)\. 
+ A small table with DISTSTYLE EVEN is converted to AUTO\(ALL\)\. 
+ A small table with DISTSTYLE KEY is converted to AUTO\(ALL\)\. 
+ A large table with DISTSTYLE ALL is converted to AUTO\(EVEN\)\. 
+ A large table with DISTSTYLE EVEN is converted to AUTO\(EVEN\)\. 
+ A large table with DISTSTYLE KEY is converted to AUTO\(KEY\) and the DISTKEY is preserved\. 
If Amazon Redshift determines that a new distribution style or key will improve the performance of queries, then Amazon Redshift might change the distribution style or key of your table in the future\.   
For more information about DISTSTYLE AUTO, see [CREATE TABLE](r_CREATE_TABLE_NEW.md)\.   
To view the distribution style of a table, query the SVV\_TABLE\_INFO system catalog view\. For more information, see [SVV\_TABLE\_INFO](r_SVV_TABLE_INFO.md)\. To view the Amazon Redshift Advisor recommendations for tables, query the SVV\_ALTER\_TABLE\_RECOMMENDATIONS system catalog view\. For more information, see [SVV\_ALTER\_TABLE\_RECOMMENDATIONS](r_SVV_ALTER_TABLE_RECOMMENDATIONS.md)\. To view the actions taken by Amazon Redshift, query the SVL\_AUTO\_WORKER\_ACTION system catalog view\. For more information, see [SVL\_AUTO\_WORKER\_ACTION](r_SVL_AUTO_WORKER_ACTION.md)\. 

ALTER \[COMPOUND\] SORTKEY \( *column\_name* \[,\.\.\.\] \)  
A clause that changes or adds the sort key used for a table\.   
When you alter a sort key, the compression encoding of columns in the new or original sort key can change\. If no encoding is explicitly defined for the table, then Amazon Redshift automatically assigns compression encodings as follows:  
+ Columns that are defined as sort keys are assigned RAW compression\.
+ Columns that are defined as BOOLEAN, REAL, or DOUBLE PRECISION data types are assigned RAW compression\.
+ Columns that are defined as SMALLINT, INTEGER, BIGINT, DECIMAL, DATE, TIME, TIMETZ, TIMESTAMP, or TIMESTAMPTZ are assigned AZ64 compression\.
+ Columns that are defined as CHAR or VARCHAR are assigned LZO compression\.
Consider the following:  
+ You can define a maximum of 400 columns for a sort key per table\. 
+ You can only alter a compound sort key\. You can't alter an interleaved sort key\. 
+ If the sort key was previously defined as AUTO, then the table is no longer a candidate for automatic table optimization\. 
When data is loaded into a table, the data is loaded in the order of the sort key\. When you alter the sort key, Amazon Redshift reorders the data\. For more information about SORTKEY, see [CREATE TABLE](r_CREATE_TABLE_NEW.md)\.

ALTER SORTKEY AUTO  
A clause that changes or adds the sort key of the target table to AUTO\.   
When you alter a sort key to AUTO, Amazon Redshift preserves the existing sort key of the table\.    
If Amazon Redshift determines that a new sort key will improve the performance of queries, then Amazon Redshift might change the sort key of your table in the future\.   
For more information about SORTKEY AUTO, see [CREATE TABLE](r_CREATE_TABLE_NEW.md)\.   
To view the sort key of a table, query the SVV\_TABLE\_INFO system catalog view\. For more information, see [SVV\_TABLE\_INFO](r_SVV_TABLE_INFO.md)\. To view the Amazon Redshift Advisor recommendations for tables, query the SVV\_ALTER\_TABLE\_RECOMMENDATIONS system catalog view\. For more information, see [SVV\_ALTER\_TABLE\_RECOMMENDATIONS](r_SVV_ALTER_TABLE_RECOMMENDATIONS.md)\. To view the actions taken by Amazon Redshift, query the SVL\_AUTO\_WORKER\_ACTION system catalog view\. For more information, see [SVL\_AUTO\_WORKER\_ACTION](r_SVL_AUTO_WORKER_ACTION.md)\. 

ALTER SORTKEY NONE  
A clause that removes the sort key of the target table\.   
If the sort key was previously defined as AUTO, then the table is no longer a candidate for automatic table optimization\. 

RENAME COLUMN *column\_name* TO *new\_name*   
A clause that renames a column to the value specified in *new\_name*\. The maximum column name length is 127 bytes; longer names are truncated to 127 bytes\. For more information about valid names, see [Names and identifiers](r_names.md)\.

ADD \[ COLUMN \] *column\_name*   
A clause that adds a column with the specified name to the table\. You can add only one column in each ALTER TABLE statement\.  
You can't add a column that is the distribution key \(DISTKEY\) or a sort key \(SORTKEY\) of the table\.  
 You can't use an ALTER TABLE ADD COLUMN command to modify the following table and column attributes:   
+ UNIQUE
+ PRIMARY KEY
+ REFERENCES \(foreign key\)
+ IDENTITY or GENERATED BY DEFAULT AS IDENTITY
The maximum column name length is 127 bytes; longer names are truncated to 127 bytes\. The maximum number of columns you can define in a single table is 1,600\.  
The following restrictions apply when adding a column to an external table:  
+ You can't add a column to an external table with the column constraints DEFAULT, ENCODE, NOT NULL, or NULL\. 
+ You can't add columns to an external table that's defined using the AVRO file format\. 
+ If pseudocolumns are enabled, the maximum number of columns that you can define in a single external table is 1,598\. If pseudocolumns aren't enabled, the maximum number of columns that you can define in a single table is 1,600\. 
For more information, see [CREATE EXTERNAL TABLE](r_CREATE_EXTERNAL_TABLE.md)\.

 *column\_type*   
The data type of the column being added\. For CHAR and VARCHAR columns, you can use the MAX keyword instead of declaring a maximum length\. MAX sets the maximum length to 4,096 bytes for CHAR or 65,535 bytes for VARCHAR\. The maximum size of a GEOMETRY object is 1,048,447 bytes\.   
Amazon Redshift supports the following [Data types](c_Supported_data_types.md):   
+ SMALLINT \(INT2\)
+ INTEGER \(INT, INT4\)
+ BIGINT \(INT8\)
+ DECIMAL \(NUMERIC\)
+ REAL \(FLOAT4\)
+ DOUBLE PRECISION \(FLOAT8\)
+ BOOLEAN \(BOOL\)
+ CHAR \(CHARACTER\)
+ VARCHAR \(CHARACTER VARYING\)
+ DATE
+ TIMESTAMP
+ GEOMETRY
+ TIME
+ TIMETZ

DEFAULT *default\_expr*   <a name="alter-table-default"></a>
A clause that assigns a default data value for the column\. The data type of *default\_expr* must match the data type of the column\. The DEFAULT value must be a variable\-free expression\. Subqueries, cross\-references to other columns in the current table, and user\-defined functions aren't allowed\.  
The *default\_expr* is used in any INSERT operation that doesn't specify a value for the column\. If no default value is specified, the default value for the column is null\.  
If a COPY operation encounters a null field on a column that has a DEFAULT value and a NOT NULL constraint, the COPY command inserts the value of the *default\_expr*\.   
DEFAULT isn't supported for external tables\.

ENCODE *encoding*   
The compression encoding for a column\. If no compression is selected, Amazon Redshift automatically assigns compression encoding as follows:  
+ All columns in temporary tables are assigned RAW compression by default\.
+ Columns that are defined as sort keys are assigned RAW compression\.
+ Columns that are defined as BOOLEAN, REAL, DOUBLE PRECISION, or GEOMETRY data types are assigned RAW compression\.
+ Columns that are defined as SMALLINT, INTEGER, BIGINT, DECIMAL, DATE, TIME, TIMETZ, TIMESTAMP, or TIMESTAMPTZ are assigned AZ64 compression\.
+ Columns that are defined as CHAR or VARCHAR are assigned LZO compression\.
If you don't want a column to be compressed, explicitly specify RAW encoding\.
The following [compression encodings](c_Compression_encodings.md#compression-encoding-list) are supported:  
+ AZ64
+ BYTEDICT
+ DELTA
+ DELTA32K
+ LZO
+ MOSTLY8
+ MOSTLY16
+ MOSTLY32
+ RAW \(no compression\)
+ RUNLENGTH
+ TEXT255
+ TEXT32K
+ ZSTD
ENCODE isn't supported for external tables\.

NOT NULL \| NULL   
NOT NULL specifies that the column isn't allowed to contain null values\. NULL, the default, specifies that the column accepts null values\.  
NOT NULL and NULL aren't supported for external tables\.

DROP \[ COLUMN \] *column\_name*   
The name of the column to delete from the table\.  
You can't drop the last column in a table\. A table must have at least one column\.  
You can't drop a column that is the distribution key \(DISTKEY\) or a sort key \(SORTKEY\) of the table\. The default behavior for DROP COLUMN is RESTRICT if the column has any dependent objects, such as a view, primary key, foreign key, or UNIQUE restriction\.  
The following restrictions apply when dropping a column from an external table:  
+ You can't drop a column from an external table if the column is used as a partition\.
+ You can't drop a column from an external table that is defined using the AVRO file format\. 
+ RESTRICT and CASCADE are ignored for external tables\.
For more information, see [CREATE EXTERNAL TABLE](r_CREATE_EXTERNAL_TABLE.md)\.

RESTRICT   
When used with DROP COLUMN, RESTRICT means that column to be dropped isn't dropped, in these cases:  
+ If a defined view references the column that is being dropped
+ If a foreign key references the column
+ If the column takes part in a multipart key
RESTRICT can't be used with CASCADE\.  
RESTRICT and CASCADE are ignored for external tables\.

CASCADE   
When used with DROP COLUMN, removes the specified column and anything dependent on that column\. CASCADE can't be used with RESTRICT\.  
RESTRICT and CASCADE are ignored for external tables\.

The following options apply only to external tables\.

SET LOCATION \{ 's3://*bucket/folder*/' \| 's3://*bucket/manifest\_file*' \}  
The path to the Amazon S3 folder that contains the data files or a manifest file that contains a list of Amazon S3 object paths\. The buckets must be in the same AWS Region as the Amazon Redshift cluster\. For a list of supported AWS Regions, see [Amazon Redshift Spectrum considerations](c-using-spectrum.md#c-spectrum-considerations)\. For more information about using a manifest file, see LOCATION in the CREATE EXTERNAL TABLE [Parameters](r_CREATE_EXTERNAL_TABLE.md#r_CREATE_EXTERNAL_TABLE-parameters) reference\.

SET FILE FORMAT *format*  
The file format for external data files\.  
Valid formats are as follows:  
+ AVRO 
+ PARQUET
+ RCFILE
+ SEQUENCEFILE
+ TEXTFILE 

SET TABLE PROPERTIES \( '*property\_name*'='*property\_value*'\)   
A clause that sets the table definition for table properties for an external table\.   
Table properties are case\-sensitive\.  
'numRows'='*row\_count*'   
A property that sets the numRows value for the table definition\. To explicitly update an external table's statistics, set the numRows property to indicate the size of the table\. Amazon Redshift doesn't analyze external tables to generate the table statistics that the query optimizer uses to generate a query plan\. If table statistics aren't set for an external table, Amazon Redshift generates a query execution plan\. This plan is based on an assumption that external tables are the larger tables and local tables are the smaller tables\.  
'skip\.header\.line\.count'='*line\_count*'  
A property that sets number of rows to skip at the beginning of each source file\.

PARTITION \( *partition\_column*=*partition\_value* \[, \.\.\.\] SET LOCATION \{ 's3://*bucket*/*folder*' \| 's3://*bucket*/*manifest\_file*' \}  
A clause that sets a new location for one or more partition columns\. 

ADD \[ IF NOT EXISTS \] PARTITION \( *partition\_column*=*partition\_value* \[, \.\.\.\] \) LOCATION \{ 's3://*bucket*/*folder*' \| 's3://*bucket*/*manifest\_file*' \} \[, \.\.\. \]  
A clause that adds one or more partitions\. You can specify multiple PARTITION clauses using a single ALTER TABLE … ADD statement\.  
If you use the AWS Glue catalog, you can add up to 100 partitions using a single ALTER TABLE statement\.
The IF NOT EXISTS clause indicates that if the specified partition already exists, the command should make no changes\. It also indicates that the command should return a message that the partition exists, rather than terminating with an error\. This clause is useful when scripting, so the script doesn’t fail if ALTER TABLE tries to add a partition that already exists\. 

DROP PARTITION \(*partition\_column*=*partition\_value* \[, \.\.\.\] \)   
A clause that drops the specified partition\. Dropping a partition alters only the external table metadata\. The data on Amazon S3 isn't affected\.