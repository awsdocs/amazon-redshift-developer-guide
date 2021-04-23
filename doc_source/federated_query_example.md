# Examples of using a federated query<a name="federated_query_example"></a>

The following examples show how to run a federated query\.

## Example of using a federated query with PostgreSQL<a name="federated_query_example_postgres"></a>

The following example shows how to set up a federated query that references an Amazon Redshift database, an Aurora PostgreSQL database, and Amazon S3\. This example illustrates how federated queries works\. To run it on your own environment, change it to fit your environment\. For prerequisites for doing this, see [Getting started with using federated queries to PostgreSQL](getting-started-federated.md)\. 

Create an external schema that references an Aurora PostgreSQL database\.

```
CREATE EXTERNAL SCHEMA apg
FROM POSTGRES
DATABASE 'database-1' SCHEMA 'myschema'
URI 'endpoint to aurora hostname'
IAM_ROLE 'arn:aws:iam::123456789012:role/Redshift-SecretsManager-RO'
SECRET_ARN 'arn:aws:secretsmanager:us-west-2:123456789012:secret:federation/test/dataplane-apg-creds-YbVKQw';
```

Create another external schema that references Amazon S3, which uses Amazon Redshift Spectrum\. Also, grant permission to use the schema to `public`\. 

```
CREATE EXTERNAL SCHEMA s3 
FROM DATA CATALOG 
DATABASE 'default' REGION 'us-west-2' 
IAM_ROLE 'arn:aws:iam::123456789012:role/Redshift-S3'; 

GRANT USAGE ON SCHEMA s3 TO public;
```

Show the count of rows in the Amazon Redshift table\. 

```
SELECT count(*) FROM public.lineitem;
            
  count
----------
25075099
```

Show the count of rows in the Aurora PostgreSQL table\. 

```
SELECT count(*) FROM apg.lineitem;
            
count
-------
11760
```

Show the count of rows in Amazon S3\. 

```
SELECT count(*) FROM s3.lineitem_1t_part;
            
   count
------------
6144008876
```

Create a view of the tables from Amazon Redshift, Aurora PostgreSQL, and Amazon S3\. This view is used to run your federated query\. 

```
CREATE VIEW lineitem_all AS
  SELECT l_orderkey,l_partkey,l_suppkey,l_linenumber,l_quantity,l_extendedprice,l_discount,l_tax,l_returnflag,l_linestatus,
         l_shipdate::date,l_commitdate::date,l_receiptdate::date, l_shipinstruct ,l_shipmode,l_comment 
  FROM s3.lineitem_1t_part 
  UNION ALL SELECT * FROM public.lineitem 
  UNION ALL SELECT * FROM apg.lineitem 
     with no schema binding;
```

Show the count of rows in the view `lineitem_all` with a predicate to limit the results\. 

```
SELECT count(*) from lineitem_all WHERE l_quantity = 10;
               
   count
-----------
123373836
```

Find out how many sales of one item there were in January of each year\. 

```
SELECT extract(year from l_shipdate) as year,
       extract(month from l_shipdate) as month,
       count(*) as orders
FROM lineitem_all
WHERE extract(month from l_shipdate) = 1
AND l_quantity < 2
GROUP BY 1,2
ORDER BY 1,2;

 year | month | orders
------+-------+---------
 1992 |     1 |  196019
 1993 |     1 | 1582034
 1994 |     1 | 1583181
 1995 |     1 | 1583919
 1996 |     1 | 1583622
 1997 |     1 | 1586541
 1998 |     1 | 1583198
 2016 |     1 |   15542
 2017 |     1 |   15414
 2018 |     1 |   15527
 2019 |     1 |     151
```

## Example of using a mixed\-case name<a name="federated_query_example_postgres-mixed"></a>

To query a supported PostgreSQL remote database that has a mixed\-case name of a database, schema, table, or column, then set `downcase_delimited_identifier` to `off`\. For more information about this session parameter, see [downcase\_delimited\_identifier](r_downcase_delimited_identifier.md)\. 

```
SET downcase_delimited_identifier TO OFF;
```

Typically, the database and schema names are in lowercase\. The following example shows how you can connect to a supported PostgreSQL remote database that has lowercase names for database and schema and mixed\-case names for table and column\. 

Create an external schema that references an Aurora PostgreSQL database that has a lowercase database name \(`dblower`\) and lowercase schema name \(`schemalower`\)\. 

```
CREATE EXTERNAL SCHEMA apg_lower
FROM POSTGRES
DATABASE 'dblower' SCHEMA 'schemalower'
URI 'endpoint to aurora hostname'
IAM_ROLE 'arn:aws:iam::123456789012:role/Redshift-SecretsManager-RO'
SECRET_ARN 'arn:aws:secretsmanager:us-west-2:123456789012:secret:federation/test/dataplane-apg-creds-YbVKQw';
```

In the session where the query runs, set `downcase_delimited_identifier` to `off`\.

```
SET downcase_delimited_identifier TO OFF;
```

Run a federated query to select all data from the PostgreSQL database\. The table \(`MixedCaseTab`\) and column \(`MixedCaseName`\) have mixed\-case names\. The result is one row \(`Harry`\)\. 

```
select * from apg_lower."MixedCaseTab";
```

```
 MixedCaseName
-------
 Harry
```

The following example shows how you can connect to a supported PostgreSQL remote database that has a mixed\-case name for the database, schema, table, and column\. 

Set `downcase_delimited_identifier` to `off` before you create the external schema\. If `downcase_delimited_identifier` is not set to `off` before creating the external schema, then a database does not exist error occurs\.

Create an external schema that references an Aurora PostgreSQL database that has a mixed\-case database \(`UpperDB`\) and schema \(`UpperSchema`\) name\.

```
CREATE EXTERNAL SCHEMA apg_upper
FROM POSTGRES
DATABASE 'UpperDB' SCHEMA 'UpperSchema'
URI 'endpoint to aurora hostname'
IAM_ROLE 'arn:aws:iam::123456789012:role/Redshift-SecretsManager-RO'
SECRET_ARN 'arn:aws:secretsmanager:us-west-2:123456789012:secret:federation/test/dataplane-apg-creds-YbVKQw';
```

Run a federated query to select all data from the PostgreSQL database\. The table \(`MixedCaseTab`\) and column \(`MixedCaseName`\) have mixed\-case names\. The result is one row \(`Harry`\)\. 

```
select * from apg_upper."MixedCaseTab";
```

```
 MixedCaseName
-------
 Harry
```

## Example of using a federated query with MySQL<a name="federated_query_example_mysql"></a>


|  | 
| --- |
| The following is prerelease documentation for the federated query to MySQL feature for Amazon Redshift, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

The following example shows how to set up a federated query that references an Aurora MySQL database\. This example illustrates how federated queries works\. To run it on your own environment, change it to fit your environment\. For prerequisites for doing this, see [Getting started with using federated queries to MySQL \(preview\)](getting-started-federated-mysql.md)\. 

It depends on the following prerequisites: 
+ The Amazon Redshift cluster is on the maintenance track for **Preview** with the track named `SQL_PREVIEW`\. For more information, see [Choosing cluster maintenance tracks](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-clusters.html#rs-mgmt-maintenance-tracks) in the *Amazon Redshift Cluster Management Guide*\. 
+ A secret that was set up in Secrets Manager for the Aurora MySQL database\. This secret is referenced in IAM access policies and roles\. For more information, see [Creating a secret and an IAM role to use federated queries](federated-create-secret-iam-role.md)\. 
+ A security group that is set up linking Amazon Redshift and Aurora MySQL\. 

Create an external schema that references an Aurora MySQL database\. 

```
CREATE EXTERNAL SCHEMA amysql
FROM MYSQL
DATABASE 'functional'
URI 'endpoint to remote hostname'
IAM_ROLE 'arn:aws:iam::123456789012:role/Redshift-SecretsManager-RO'
SECRET_ARN 'arn:aws:secretsmanager:us-west-2:123456789012:secret:federation/test/dataplane-apg-creds-YbVKQw';
```

Show an example select of the Aurora MySQL table\. 

```
SELECT level FROM amysql.employees LIMIT 1;
            
 level
-------
     8
```