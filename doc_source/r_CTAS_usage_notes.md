# CTAS usage notes<a name="r_CTAS_usage_notes"></a>

## Limits<a name="r_CTAS_usage_notes-limits"></a>

Amazon Redshift enforces a quota of the number of tables per cluster by node type\. 

The maximum number of characters for a table name is 127\. 

The maximum number of columns you can define in a single table is 1,600\. 

## Inheritance of column and table attributes<a name="r_CTAS_usage_notes-inheritance-of-column-and-table-attributes"></a>

CREATE TABLE AS \(CTAS\) tables don't inherit constraints, identity columns, default column values, or the primary key from the table that they were created from\. 

You can't specify column compression encodings for CTAS tables\. Amazon Redshift automatically assigns compression encoding as follows:
+ Columns that are defined as sort keys are assigned RAW compression\.
+ Columns that are defined as BOOLEAN, REAL, DOUBLE PRECISION, or GEOMETRY data type are assigned RAW compression\.
+ Columns that are defined as SMALLINT, INTEGER, BIGINT, DECIMAL, DATE, TIME, TIMETZ, TIMESTAMP, or TIMESTAMPTZ are assigned AZ64 compression\.
+ Columns that are defined as CHAR or VARCHAR are assigned LZO compression\.

For more information, see [Compression encodings](c_Compression_encodings.md) and [Data types](c_Supported_data_types.md)\. 

To explicitly assign column encodings, use [CREATE TABLE](r_CREATE_TABLE_NEW.md)

CTAS determines distribution style and sort key for the new table based on the query plan for the SELECT clause\. 

If the SELECT clause is a simple select operation from a single table, without a limit clause, order by clause, or group by clause, then CTAS uses the source table's distribution style and sort key\.

For complex queries, such as queries that include joins, aggregations, an order by clause, or a limit clause, CTAS makes a best effort to choose the optimal distribution style and sort key based on the query plan\. 

**Note**  
For best performance with large data sets or complex queries, we recommend testing using typical data sets\.

You can often predict which distribution key and sort key CTAS chooses by examining the query plan to see which columns, if any, the query optimizer chooses for sorting and distributing data\. If the top node of the query plan is a simple sequential scan from a single table \(XN Seq Scan\), then CTAS generally uses the source table's distribution style and sort key\. If the top node of the query plan is anything other a sequential scan \(such as XN Limit, XN Sort, XN HashAggregate, and so on\), CTAS makes a best effort to choose the optimal distribution style and sort key based on the query plan\.

For example, suppose you create five tables using the following types of SELECT clauses:
+ A simple select statement 
+ A limit clause 
+ An order by clause using LISTID 
+ An order by clause using QTYSOLD 
+ A SUM aggregate function with a group by clause\.

The following examples show the query plan for each CTAS statement\.

```
explain create table sales1_simple as select listid, dateid, qtysold from sales;
                           QUERY PLAN
----------------------------------------------------------------
 XN Seq Scan on sales  (cost=0.00..1724.56 rows=172456 width=8)
(1 row)


explain create table sales2_limit as select listid, dateid, qtysold from sales limit 100;
                              QUERY PLAN
----------------------------------------------------------------------
 XN Limit  (cost=0.00..1.00 rows=100 width=8)
   ->  XN Seq Scan on sales  (cost=0.00..1724.56 rows=172456 width=8)
(2 rows)


explain create table sales3_orderbylistid as select listid, dateid, qtysold from sales order by listid;
                               QUERY PLAN
------------------------------------------------------------------------
 XN Sort  (cost=1000000016724.67..1000000017155.81 rows=172456 width=8)
   Sort Key: listid
   ->  XN Seq Scan on sales  (cost=0.00..1724.56 rows=172456 width=8)
(3 rows)


explain create table sales4_orderbyqty as select listid, dateid, qtysold from sales order by qtysold;
                               QUERY PLAN
------------------------------------------------------------------------
 XN Sort  (cost=1000000016724.67..1000000017155.81 rows=172456 width=8)
   Sort Key: qtysold
   ->  XN Seq Scan on sales  (cost=0.00..1724.56 rows=172456 width=8)
(3 rows)


explain create table sales5_groupby as select listid, dateid, sum(qtysold) from sales group by listid, dateid;
                              QUERY PLAN
----------------------------------------------------------------------
 XN HashAggregate  (cost=3017.98..3226.75 rows=83509 width=8)
   ->  XN Seq Scan on sales  (cost=0.00..1724.56 rows=172456 width=8)
(2 rows)
```

To view the distribution key and sortkey for each table, query the PG\_TABLE\_DEF system catalog table, as shown following\. 

```
select * from pg_table_def where tablename like 'sales%';
            
      tablename       |   column   | distkey | sortkey
----------------------+------------+---------+---------
 sales                | salesid    | f       |       0
 sales                | listid     | t       |       0
 sales                | sellerid   | f       |       0
 sales                | buyerid    | f       |       0
 sales                | eventid    | f       |       0
 sales                | dateid     | f       |       1
 sales                | qtysold    | f       |       0
 sales                | pricepaid  | f       |       0
 sales                | commission | f       |       0
 sales                | saletime   | f       |       0
 sales1_simple        | listid     | t       |       0
 sales1_simple        | dateid     | f       |       1
 sales1_simple        | qtysold    | f       |       0
 sales2_limit         | listid     | f       |       0
 sales2_limit         | dateid     | f       |       0
 sales2_limit         | qtysold    | f       |       0
 sales3_orderbylistid | listid     | t       |       1
 sales3_orderbylistid | dateid     | f       |       0
 sales3_orderbylistid | qtysold    | f       |       0
 sales4_orderbyqty    | listid     | t       |       0
 sales4_orderbyqty    | dateid     | f       |       0
 sales4_orderbyqty    | qtysold    | f       |       1
 sales5_groupby       | listid     | f       |       0
 sales5_groupby       | dateid     | f       |       0
 sales5_groupby       | sum        | f       |       0
```

The following table summarizes the results\. For simplicity, we omit cost, rows, and width details from the explain plan\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_CTAS_usage_notes.html)

You can explicitly specify distribution style and sort key in the CTAS statement\. For example, the following statement creates a table using EVEN distribution and specifies SALESID as the sort key\.

```
create table sales_disteven
diststyle even
sortkey (salesid)
as
select eventid, venueid, dateid, eventname
from event;
```

## Distribution of incoming data<a name="r_CTAS_usage_notes-distribution-of-incoming-data"></a>

When the hash distribution scheme of the incoming data matches that of the target table, no physical distribution of the data is actually necessary when the data is loaded\. For example, if a distribution key is set for the new table and the data is being inserted from another table that is distributed on the same key column, the data is loaded in place, using the same nodes and slices\. However, if the source and target tables are both set to EVEN distribution, data is redistributed into the target table\. 

## Automatic ANALYZE operations<a name="r_CTAS_usage_notes-automatic-analyze-operations"></a>

Amazon Redshift automatically analyzes tables that you create with CTAS commands\. You do not need to run the ANALYZE command on these tables when they are first created\. If you modify them, you should analyze them in the same way as other tables\. 