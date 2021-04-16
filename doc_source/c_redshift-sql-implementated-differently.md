# Features that are implemented differently<a name="c_redshift-sql-implementated-differently"></a>

Many Amazon Redshift SQL language elements have different performance characteristics and use syntax and semantics and that are quite different from the equivalent PostgreSQL implementation\.

**Important**  
Do not assume that the semantics of elements that Amazon Redshift and PostgreSQL have in common are identical\. Make sure to consult the *Amazon Redshift Developer Guide * [SQL commands](c_SQL_commands.md) to understand the often subtle differences\.

One example in particular is the [VACUUM](r_VACUUM_command.md) command, which is used to clean up and reorganize tables\. VACUUM functions differently and uses a different set of parameters than the PostgreSQL version\. See [Vacuuming tables](t_Reclaiming_storage_space202.md) for more about information about using VACUUM in Amazon Redshift\.

Often, database management and administration features and tools are different as well\. For example, Amazon Redshift maintains a set of system tables and views that provide information about how the system is functioning\. See [System tables and views](c_intro_system_tables.md) for more information\.

The following list includes some examples of SQL features that are implemented differently in Amazon Redshift\.
+  [CREATE TABLE](r_CREATE_TABLE_NEW.md) 

  Amazon Redshift does not support tablespaces, table partitioning, inheritance, and certain constraints\. The Amazon Redshift implementation of CREATE TABLE enables you to define the sort and distribution algorithms for tables to optimize parallel processing\.

  Amazon Redshift Spectrum supports table partitioning using the [CREATE EXTERNAL TABLE](r_CREATE_EXTERNAL_TABLE.md) command\.
+  [ALTER TABLE](r_ALTER_TABLE.md) 

  Only a subset of ALTER COLUMN actions are supported\.

  ADD COLUMN supports adding only one column in each ALTER TABLE statement\.
+  [COPY](r_COPY.md) 

  The Amazon Redshift COPY command is highly specialized to enable the loading of data from Amazon S3 buckets and Amazon DynamoDB tables and to facilitate automatic compression\. See the [Loading data](t_Loading_data.md) section and the COPY command reference for details\.
+  [INSERT](r_INSERT_30.md), [UPDATE](r_UPDATE.md), and [DELETE](r_DELETE.md) 

  WITH is not supported\.
+  [VACUUM](r_VACUUM_command.md) 

  The parameters for VACUUM are entirely different\. For example, the default VACUUM operation in PostgreSQL simply reclaims space and makes it available for re\-use; however, the default VACUUM operation in Amazon Redshift is VACUUM FULL, which reclaims disk space and resorts all rows\.
+ Trailing spaces in VARCHAR values are ignored when string values are compared\. For more information, see [Significance of trailing blanks](r_Character_types.md#r_Character_types-significance-of-trailing-blanks)\.