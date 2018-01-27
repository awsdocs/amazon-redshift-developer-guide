# ALTER TABLE<a name="r_ALTER_TABLE"></a>


+ [Syntax](#r_ALTER_TABLE-synopsis)
+ [Parameters](#r_ALTER_TABLE-parameters)
+ [ALTER TABLE Examples](r_ALTER_TABLE_examples_basic.md)
+ [Alter External Table Examples](r_ALTER_TABLE_external-table.md)
+ [ALTER TABLE ADD and DROP COLUMN Examples](r_ALTER_TABLE_COL_ex-add-drop.md)

Changes the definition of a database table\. This command updates the values and properties set by CREATE TABLE\.

**Note**  
ALTER TABLE locks the table for read and write operations until the ALTER TABLE operation completes\. 

## Syntax<a name="r_ALTER_TABLE-synopsis"></a>

## Parameters<a name="r_ALTER_TABLE-parameters"></a>

 *table\_name*   
The name of the table to alter\. Either specify just the name of the table, or use the format *schema\_name\.table\_name* to use a specific schema\. External tables must be qualified by an external schema name\. You can also specify a view name if you are using the ALTER TABLE statement to rename a view or change its owner\. The maximum length for the table name is 127 bytes; longer names are truncated to 127 bytes\. You can use UTF\-8 multibyte characters up to a maximum of four bytes\. For more information about valid names, see [Names and Identifiers](r_names.md)\.

ADD *table\_constraint*   
A clause that adds the specified constraint to the table\. For descriptions of valid *table\_constraint* values, see [CREATE TABLE](r_CREATE_TABLE_NEW.md)\.  
You cannot add a primary\-key constraint to a nullable column\. If the column was originally created with the NOT NULL constraint, you can add the primary\-key constraint\.

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
A clause that renames a table \(or view\) to the value specified in *new\_name*\. The maximum table name length is 127 characters; longer names are truncated to 127 bytes\.  
You cannot rename a permanent table to a name that begins with '\#'\. A table name beginning with '\#' indicates a temporary table\.

RENAME COLUMN *column\_name* TO *new\_name*   
A clause that renames a column to the value specified in *new\_name*\. The maximum column name length is 127 bytes; longer names are truncated to 127 bytes\. For more information about valid names, see [Names and Identifiers](r_names.md)\.

ADD \[ COLUMN \] *column\_name*   
A clause that adds a column with the specified name to the table\. You can add only one column in each ALTER TABLE statement\.  
You cannot add a column that is the distribution key \(DISTKEY\) or a sort key \(SORTKEY\) of the table\.  
 You cannot use an ALTER TABLE ADD COLUMN command to modify the following table and column attributes:   

+ UNIQUE

+ PRIMARY KEY

+ REFERENCES \(foreign key\)

+ IDENTITY
The maximum column name length is 127 bytes; longer names are truncated to 127 bytes\. The maximum number of columns you can define in a single table is 1,600\.

 *column\_type*   
The data type of the column being added\. For CHAR and VARCHAR columns, you can use the MAX keyword instead of declaring a maximum length\. MAX sets the maximum length to 4,096 bytes for CHAR or 65,535 bytes for VARCHAR\. Amazon Redshift supports the following [data types](c_Supported_data_types.md):   

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

DEFAULT *default\_expr*   
A clause that assigns a default data value for the column\. The data type of *default\_expr* must match the data type of the column\. The DEFAULT value must be a variable\-free expression\. Subqueries, cross\-references to other columns in the current table, and user\-defined functions are not allowed\.  
The *default\_expr* is used in any INSERT operation that doesn't specify a value for the column\. If no default value is specified, the default value for the column is null\.  
If a COPY operation encounters a null field on a column that has a DEFAULT value and a NOT NULL constraint, the COPY command inserts the value of the *default\_expr*\. 

ENCODE *encoding*   
The compression encoding for a column\. If no compression is selected, Amazon Redshift automatically assigns compression encoding as follows:  

+ All columns in temporary tables are assigned RAW compression by default\.

+ Columns that are defined as sort keys are assigned RAW compression\.

+ Columns that are defined as BOOLEAN, REAL, or DOUBLE PRECISION data types are assigned RAW compression\.

+ All other columns are assigned LZO compression\.
If you don't want a column to be compressed, explicitly specify RAW encoding\.
The following [compression encodings](c_Compression_encodings.md#compression-encoding-list) are supported:  

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

NOT NULL | NULL   
NOT NULL specifies that the column is not allowed to contain null values\. NULL, the default, specifies that the column accepts null values\.

DROP \[ COLUMN \] *column\_name*   
The name of the column to delete from the table\.  
You cannot drop a column that is the distribution key \(DISTKEY\) or a sort key \(SORTKEY\) of the table\. The default behavior for DROP COLUMN is RESTRICT if the column has any dependent objects, such as a view, primary key, foreign key, or UNIQUE restriction\.

RESTRICT   
When used with DROP COLUMN, RESTRICT means that if a defined view references the column that is being dropped, or if a foreign key references the column, or if the column takes part in a multi\-part key, then the column will not be dropped\. RESTRICT can't be used with CASCADE\.

CASCADE   
When used with DROP COLUMN, removes the specified column and anything dependent on that column\. CASCADE can't be used with RESTRICT\.

The following options apply only to external tables\.

SET LOCATION \{ 's3://*bucket/folder*/' | 's3://*bucket/manifest\_file*' \}  
The path to the Amazon S3 folder that contains the data files or a manifest file that contains a list of Amazon S3 object paths\. The buckets must be in the same region as the Amazon Redshift cluster\. For a list of supported regions, see [Amazon Redshift Spectrum Considerations](c-using-spectrum.md#c-spectrum-considerations)\. For more information about using a manifest file, see LOCATION in the CREATE EXTERNAL TABLE referencce\.

SET FILE FORMAT *format*  
The file format for external data files\.  
Valid formats are as follows:  

+ AVRO 

+ PARQUET

+ RCFILE

+ SEQUENCEFILE

+ TEXTFILE 

SET TABLE PROPERTIES \( '*property\_name*'='*property\_value*'\)   
A clause that sets table properties table definition for an external table\.   
Table properties are case\-sensitive\.  
'numRows'='*row\_count*'   
A property that sets the numRows value for the table definition\. To explicitly update an external table's statistics, set the numRows property to indicate the size of the table\. Amazon Redshift doesn't analyze external tables to generate the table statistics that the query optimizer uses to generate a query plan\. If table statistics are not set for an external table, Amazon Redshift generates a query execution plan based on an assumption that external tables are the larger tables and local tables are the smaller tables\.  
'skip\.header\.line\.count'='*line\_count*'  
A property that sets number of rows to skip at the beginning of each source file\.

PARTITION \( *partition\_column*=*partition\_value* \[, \.\.\.\] SET LOCATION \{ 's3://*bucket*/*folder*' | 's3://*bucket*/*manifest\_file*' \}  
A clause that sets a new location for one or more partition columns\. 

ADD PARTITION \[ IF NOT EXISTS \] \( *partition\_column*=*partition\_value* \[, \.\.\.\] \) LOCATION \{ 's3://*bucket*/*folder*' | 's3://*bucket*/*manifest\_file*' \}  
A clause that adds a partition\. Only one partition can be added in a single ALTER TABLE statement\.  
The IF NOT EXISTS clause indicates that if the specified partition already exists, the command should make no changes and return a message that the partition exists, rather than terminating with an error\. This clause is useful when scripting, so the script doesn’t fail if ALTER TABLE tries to add a partition that already exists\. 

DROP PARTITION \(*partition\_column*=*partition\_value* \[, \.\.\.\] \)   
A clause that drops the specified partition\. Dropping a partition alters only the external table metadata\. The data on Amazon S3 is not affected\.