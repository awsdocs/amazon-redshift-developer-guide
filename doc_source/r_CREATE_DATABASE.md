# CREATE DATABASE<a name="r_CREATE_DATABASE"></a>

Creates a new database\.

You can't run CREATE DATABASE within a transaction block \(BEGIN \.\.\. END\)\. For more information about transactions, see [Serializable isolation](c_serial_isolation.md)\. 

## Syntax<a name="r_CREATE_DATABASE-synopsis"></a>

```
CREATE DATABASE database_name [ WITH ]
[ OWNER [=] db_owner ]
[ CONNECTION LIMIT { limit | UNLIMITED } ]
[ COLLATE { CASE_SENSITIVE | CASE_INSENSITIVE } ]
[ ISOLATION LEVEL { SERIALIZABLE | SNAPSHOT } ]
FROM [ DATASHARE datashare_name | ARN '<arn>' ] OF [ ACCOUNT account_id ] NAMESPACE namespace_guid 
[ WITH DATA CATALOG SCHEMA '<schema>' | WITH NO DATA CATALOG SCHEMA ]
```

## Parameters<a name="r_CREATE_DATABASE-parameters"></a>

 *database\_name*   
Name of the new database\. For more information about valid names, see [Names and identifiers](r_names.md)\.

WITH   
Optional keyword\.

OWNER   
Specifies a database owner\.

=   
Optional character\.

 *db\_owner*   
Username for the database owner\.

CONNECTION LIMIT \{ *limit* \| UNLIMITED \}   
The maximum number of database connections users are permitted to have open concurrently\. The limit isn't enforced for superusers\. Use the UNLIMITED keyword to permit the maximum number of concurrent connections\.  A limit on the number of connections for each user might also apply\. For more information, see [CREATE USER](r_CREATE_USER.md)\. The default is UNLIMITED\. To view current connections, query the [STV\_SESSIONS](r_STV_SESSIONS.md) system view\.  
If both user and database connection limits apply, an unused connection slot must be available that is within both limits when a user attempts to connect\.

COLLATE \{ CASE\_SENSITIVE \| CASE\_INSENSITIVE \}  
A clause that specifies whether string search or comparison is CASE\_SENSITIVE or CASE\_INSENSITIVE\. The default is CASE\_SENSITIVE\.

ISOLATION LEVEL \{ SERIALIZABLE \| SNAPSHOT \}  
A clause that specifies the isolation level used when queries run against a database\.  
+ SERIALIZABLE isolation – provides full serializability for concurrent transactions\. This is the default for a database created in a provisioned cluster\. For more information, see [Serializable isolation](c_serial_isolation.md)\.
+ SNAPSHOT isolation – provides an isolation level with protection against update and delete conflicts\. This is the default for a database created in a serverless endpoint\. 
You can view which concurrency model your database is running as follows:   
+ Query the STV\_DB\_ISOLATION\_LEVEL catalog view\. For more information, see [STV\_DB\_ISOLATION\_LEVEL](r_STV_DB_ISOLATION_LEVEL.md)\.

  ```
  SELECT * FROM stv_db_isolation_level;
  ```
+ Query the PG\_DATABASE\_INFO view\. 

  ```
  SELECT datname, datconfig FROM pg_database_info;
  ```

  The isolation level per database appears next to the key `concurrency_model`\. A value of `1` denotes SNAPSHOT\. A value of `2` denotes SERIALIZABLE\.  
In Amazon Redshift databases, both SERIALIZABLE and SNAPSHOT isolation are types of serializable isolation levels\. That is, dirty reads, non\-repeatable reads, and phantom reads are prevented according to the SQL standard\. Both isolation levels guarantee that a transaction operates on a snapshot of data as it exists when the transaction begins, and that no other transaction can change that snapshot\. However, SNAPSHOT isolation doesn't provide full serializability, because it doesn't prevent write skew inserts and updates on different table rows\.  
The following scenario illustrates write skew updates using the SNAPSHOT isolation level\. A table named `Numbers` contains a column named `digits` that contains `0` and `1` values\. Each user's UPDATE statement doesn't overlap the other user\. However, the `0` and `1` values are swapped\. The SQL they run follows this timeline with these results:      
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_CREATE_DATABASE.html)
If the same scenario is run using serializable isolation, then Amazon Redshift terminates user 2 due to a serializable violation and returns error `1023`\. For more information, see [How to fix serializable isolation errors](c_serial_isolation.md#c_serial_isolation-serializable-isolation-troubleshooting)\. In this case, only user 1 can commit successfully\. Not all workloads require serializable isolation as a requirement, in which case snapshot isolation suffices as the target isolation level for your database\.

ARN '<ARN>'  
The AWS Glue database ARN to use to create the database\.

\[ WITH NO DATA CATALOG SCHEMA \| DATA CATALOG SCHEMA '<schema>' \]  
Specifies whether to create the database using a schema from the AWS Glue Data Catalog\. Must be used together with the ARN parameter\.

## Syntax for using CREATE DATABASE with a datashare<a name="r_CREATE_DATABASE-datashare-synopsis"></a>

The following syntax describes the CREATE DATABASE command used to create databases from a datashare for sharing data within the same AWS account\.

```
CREATE DATABASE database_name
FROM DATASHARE datashare_name OF [ ACCOUNT account_id ] NAMESPACE namespace_guid
```

The following syntax describes the CREATE DATABASE command used to create databases from a datashare for sharing data across AWS accounts\.

```
CREATE DATABASE database_name
FROM DATASHARE datashare_name OF ACCOUNT account_id NAMESPACE namespace_guid
```

### Parameters for using CREATE DATABASE with a datashare<a name="r_CREATE_DATABASE-parameters-datashare"></a>

FROM DATASHARE   
A keyword that indicates where the datashare is located\.

 *datashare\_name*   
The name of the datashare that the consumer database is created on\.

 NAMESPACE *namespace\_guid*   
A value that specifies the producer namespace that the datashare belongs to\.

ACCOUNT *account\_id*  
A value that specifies the producer account that the datashare belongs to\.

## Usage notes for CREATE DATABASE for data sharing<a name="r_CREATE_DATABASE-usage"></a>

As a consumer account administrator, when you use CREATE DATABASE to create databases from datashares within the AWS account, specify the NAMESPACE option\. The ACCOUNT option is optional\.

When you use CREATE DATABASE to create databases from datashares across AWS accounts, specify both the ACCOUNT and NAMESPACE options\.

You can create only one consumer database for one datashare on a consumer cluster\. You can't create multiple consumer databases referring to the same datashare\.

## CREATE DATABASE limits<a name="r_CREATE_DATABASE-create-database-limits"></a>

Amazon Redshift enforces these limits for databases:
+ Maximum of 60 user\-defined databases per cluster\.
+ Maximum of 127 bytes for a database name\.
+ A database name can't be a reserved word\. 

## Database collation<a name="r_CREATE_DATABASE-collation"></a>

Collation is a set of rules that defines how database engine compares and sorts the character type data in SQL\. Case\-insensitive collation is the most commonly used collation\. Amazon Redshift uses case\-insensitive collation to facilitate migration from other data warehouse systems\. With the native support of case\-insensitive collation, Amazon Redshift continues to use important tuning or optimization methods, such as distribution keys, sort keys, or range restricted scan\. 

The COLLATE clause specifies the default collation for all CHAR and VARCHAR columns in the database\. If CASE\_INSENSITIVE is specified, all CHAR or VARCHAR columns use case\-insensitive collation\. For information about collation, see [Collation sequences](c_collation_sequences.md)\.

Data inserted or ingested in case\-insensitive columns will keep its original case\. But all comparison\-based string operations including sorting and grouping are case\-insensitive\. Pattern matching operations such as LIKE predicates, similar to, and regular expression functions are also case\-insensitive\.

The following SQL operations support applicable collation semantics:
+ Comparison operators: =, <>, <, <=, >, >=\.
+ LIKE operator
+ ORDER BY clauses
+ GROUP BY clauses
+ Aggregate functions that use string comparison, such as MIN and MAX and LISTAGG
+ Window functions, such as PARTITION BY clauses and ORDER BY clauses
+ Scalar functions greatest\(\) and least\(\), STRPOS\(\), REGEXP\_COUNT\(\), REGEXP\_REPLACE\(\), REGEXP\_INSTR\(\), REGEXP\_SUBSTR\(\)
+ Distinct clause
+ UNION, INTERSECT and EXCEPT
+ IN LIST

For external queries, including Amazon Redshift Spectrum and Aurora PostgreSQL federated queries, collation of VARCHAR or CHAR column is the same as the current database\-level collation\.

The following example queries a Amazon Redshift Spectrum table:

```
SELECT ci_varchar FROM spectrum.test_collation 
WHERE ci_varchar = 'AMAZON';

ci_varchar
----------
amazon
Amazon
AMAZON
AmaZon
(4 rows)
```

For information on how to create tables using database collation, see [CREATE TABLE](r_CREATE_TABLE_NEW.md)\.

For information on the COLLATE function, see [COLLATE function](r_COLLATE.md)\.

### Database collation limitations<a name="r_CREATE_DATABASE-collation-limitations"></a>

The following are limitations when working with database collation in Amazon Redshift:
+ All system tables or views, including PG catalog tables and Amazon Redshift system tables are case\-sensitive\.
+ When consumer database and producer database have different database\-level collations, Amazon Redshift doesn't support cross\-database and cross\-cluster queries\.
+ Amazon Redshift doesn't support case\-insensitive collation in leader node\-only query\.

  The following example shows an unsupported case\-insensitive query and the error that Amazon Redshift sends:

  ```
  SELECT collate(usename, 'case_insensitive') FROM pg_user;
  ERROR:  Case insensitive collation is not supported in leader node only query.
  ```
+ Amazon Redshift doesn't support interaction between case\-sensitive and case\-insensitive columns, such as comparison, function, join, or set operations\.

  The following examples show errors when case\-sensitive and case\-insensitive columns interact:

  ```
  CREATE TABLE test
    (ci_col varchar(10) COLLATE case_insensitive,
     cs_col varchar(10) COLLATE case_sensitive,
     cint int,
     cbigint bigint);
  ```

  ```
  SELECT ci_col = cs_col FROM test;
  ERROR:  Query with different collations is not supported yet.
  ```

  ```
  SELECT concat(ci_col, cs_col) FROM test;
  ERROR:  Query with different collations is not supported yet.
  ```

  ```
  SELECT ci_col FROM test UNION SELECT cs_col FROM test;
  ERROR:  Query with different collations is not supported yet.
  ```

  ```
  SELECT * FROM test a, test b WHERE a.ci_col = b.cs_col;
  ERROR:  Query with different collations is not supported yet.
  ```

  ```
  Select Coalesce(ci_col, cs_col) from test;
  ERROR:  Query with different collations is not supported yet.
  ```

  ```
  Select case when cint > 0 then ci_col else cs_col end from test;
  ERROR:  Query with different collations is not supported yet.
  ```
+ Amazon Redshift doesn't support collation for SUPER data type\. Creating SUPER columns in case\-insensitive databases and interactions between SUPER and case\-insensitive columns aren't supported\.

  The following example creates a table with the SUPER as the data type in the case\-insensitive database:

  ```
  CREATE TABLE super_table (a super);
  ERROR: SUPER column is not supported in case insensitive database.
  ```

  The following example queries data with a case\-insensitive string comparing with the SUPER data: 

  ```
  CREATE TABLE test_super_collation
  (s super, c varchar(10) COLLATE case_insensitive, i int);
  ```

  ```
  SELECT s = c FROM test_super_collation;
  ERROR:  Coercing from case insensitive string to SUPER is not supported.
  ```

To make these queries work, use the COLLATE function to convert collation of one column to match the other\. For more information, see [COLLATE function](r_COLLATE.md)\.

## Examples<a name="r_CREATE_DATABASE-examples"></a>

**Creating a database**  
The following example creates a database named TICKIT and gives ownership to the user DWUSER\.

```
create database tickit
with owner dwuser;
```

To view details about databases, query the PG\_DATABASE\_INFO catalog table\. 

```
select datname, datdba, datconnlimit 
from pg_database_info
where datdba > 1;

 datname     | datdba | datconnlimit
-------------+--------+-------------
 admin       |    100 | UNLIMITED   
 reports     |    100 | 100         
 tickit      |    100 | 100
```

The following example creates a database named **sampledb** with SNAPSHOT isolation level\.

```
CREATE DATABASE sampledb ISOLATION LEVEL SNAPSHOT;
```

The following example creates the database sales\_db from the datashare salesshare\.

```
CREATE DATABASE sales_db FROM DATASHARE salesshare OF NAMESPACE '13b8833d-17c6-4f16-8fe4-1a018f5ed00d';
```

### Database collation examples<a name="r_CREATE_DATABASE-collation-examples"></a>

**Creating a case\-insensitive database**  
The following example creates the `sampledb` database, creates the `T1` table, and inserts data into the `T1` table\.

```
create database sampledb collate case_insensitive;
```

```
\connect sampledb;
```

```
CREATE TABLE T1 (
  col1 Varchar(20) distkey sortkey
);
```

```
INSERT INTO T1 VALUES ('bob'), ('john'), ('Mary'), ('JOHN'), ('Bob');
```

Then the query finds results with `John`\.

```
SELECT * FROM T1 WHERE col1 = 'John';

 col1
 ------
 john
 JOHN
(2 row)
```

**Ordering in a case\-insensitive order**  
The following example shows the case\-insensitive ordering with table T1\. The ordering of *Bob* and *bob* or *John* and *john* is nondeterministic because they are equal in case\-insensitive column\.

```
SELECT * FROM T1 ORDER BY 1;

 col1
 ------
 bob
 Bob
 JOHN
 john
 Mary
(5 rows)
```

Similarly, the following example shows case\-insensitive ordering with the GROUP BY clause\. *Bob* and *bob* are equal and belong to the same group\. It is nondeterministic which one shows up in the result\.

```
SELECT col1, count(*) FROM T1 GROUP BY 1;

 col1 | count
 -----+------
 Mary |  1
 bob  |  2
 JOHN |  2
(3 rows)
```

**Querying with a window function on case\-insensitive columns**  
The following example queries a window function on a case\-insensitive column\.

```
SELECT col1, rank() over (ORDER BY col1) FROM T1;

 col1 | rank
 -----+------
 bob  |   1
 Bob  |   1
 john |   3
 JOHN |   3
 Mary |   5
(5 rows)
```

**Querying with the DISTINCT keyword**  
The following example queries the `T1` table with the DISTINCT keyword\.

```
SELECT DISTINCT col1 FROM T1;
 
 col1
 ------
 bob
 Mary
 john
(3 rows)
```

**Querying with the UNION clause**  
The following example shows the results from the UNION of the tables `T1` and `T2`\.

```
CREATE TABLE T2 AS SELECT * FROM T1;
```

```
SELECT col1 FROM T1 UNION SELECT col1 FROM T2;
 
 col1
 ------
 john 
 bob
 Mary
(3 rows)
```