# Example of using a federated query<a name="federated_query_example"></a>

The following example shows how to set up a federated query that references an Amazon Redshift database, an Aurora PostgreSQL database, and Amazon S3\. This example illustrates how federated queries works\. To run it on your own environment, change it to fit your environment\. For prerequisites for doing this, see [Getting started with using federated queries](getting-started-federated.md)\. 

Create an external schema that references an Aurora PostgreSQL database\.

```
CREATE EXTERNAL SCHEMA apg
FROM POSTGRES
DATABASE ‘database-1’ SCHEMA ’myschema’
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