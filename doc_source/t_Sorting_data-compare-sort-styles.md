# Comparing sort styles<a name="t_Sorting_data-compare-sort-styles"></a>

This section compares the performance differences when using a single\-column sort key, a compound sort key, and an interleaved sort key for different types of queries\. 

For this example, you'll create a denormalized table named CUST\_SALES, using data from the CUSTOMER and LINEORDER tables\. CUSTOMER and LINEORDER are part of the SSB dataset, which is used in the [Tutorial: Tuning table design](tutorial-tuning-tables.md)\.

The new CUST\_SALES table has 480 million rows, which is not large by Amazon Redshift standards, but it is large enough to show the performance differences\. Larger tables will tend to show greater differences, especially for interleaved sorting\. 

To compare the three sort methods, perform the following steps: 

1. Create the SSB dataset\.

1. Create the CUST\_SALES\_DATE table\. 

1. Create three tables to compare sort styles\. 

1. Execute queries and compare the results\. 

## Create the SSB dataset<a name="t_Sorting_data-compare-sort-styles-create-ssb"></a>

If you haven't already done so, follow the steps in [Step 1: Create a test data set](tutorial-tuning-tables-create-test-data.md) in the Tuning Table Design tutorial to create the tables in the SSB dataset and load them with data\. The data load will take about 10 to 15 minutes\.

The example in the Tuning Table Design tutorial uses a four\-node cluster\. The comparisons in this example use a two\-node cluster\. Your results will vary with different cluster configurations\. 

## Create the CUST\_SALES\_DATE table<a name="t_Sorting_data-compare-sort-styles-create-cust_sales"></a>

The CUST\_SALES\_DATE table is a denormalized table that contains data about customers and revenues\. To create the CUST\_SALES\_DATE table, execute the following statement\.

```
create table cust_sales_date as
(select c_custkey, c_nation, c_region, c_mktsegment, d_date::date, lo_revenue
from customer, lineorder, dwdate
where lo_custkey = c_custkey 
and lo_orderdate = dwdate.d_datekey
and lo_revenue > 0);
```

The following query shows the row count for CUST\_SALES\.

```
select count(*) from cust_sales_date;

   count
-----------
 480027069
(1 row)
```

Execute the following query to view the first row of the CUST\_SALES table\.

```
select * from cust_sales_date limit 1;

c_custkey | c_nation | c_region | c_mktsegment | d_date     | lo_revenue
----------+----------+----------+--------------+------------+-----------
        1 | MOROCCO  | AFRICA   | BUILDING     | 1994-10-28 |    1924330
```

## Create tables for comparing sort styles<a name="t_Sorting_data-compare-sort-styles-create-comparison-tables"></a>

To compare the sort styles, create three tables\. The first will use a single\-column sort key; the second will use a compound sort key; the third will use an interleaved sort key\. The single\-column sort will use the `c_custkey` column\. The compound sort and the interleaved sort will both use the `c_custkey`, `c_region`, `c_mktsegment`, and `d_date` columns\.

To create the tables for comparison, execute the following CREATE TABLE statements\.

```
create table cust_sales_date_single
sortkey (c_custkey)
as select * from cust_sales_date;

create table cust_sales_date_compound
compound sortkey (c_custkey, c_region, c_mktsegment, d_date)
as select * from cust_sales_date;

create table cust_sales_date_interleaved
interleaved sortkey (c_custkey, c_region, c_mktsegment, d_date)
as select * from cust_sales_date;
```

## Execute queries and compare the results<a name="t_Sorting_data-compare-sort-styles-compare-results"></a>

Execute the same queries against each of the tables to compare execution times for each table\. To eliminate differences due to compile time, run each of the queries twice, and record the second time\.

1. Test a query that restricts on the `c_custkey` column, which is the first column in the sort key for each table\. Execute the following queries\.

   ```
   -- Query 1
   
   select max(lo_revenue), min(lo_revenue)
   from cust_sales_date_single
   where c_custkey < 100000;
   
   select max(lo_revenue), min(lo_revenue)
   from cust_sales_date_compound
   where c_custkey < 100000;
   
   select max(lo_revenue), min(lo_revenue)
   from cust_sales_date_interleaved
   where c_custkey < 100000;
   ```

1. Test a query that restricts on the `c_region` column, which is the second column in the sort key for the compound and interleaved keys\. Execute the following queries\.

   ```
   -- Query 2
   
   select max(lo_revenue), min(lo_revenue)
   from cust_sales_date_single
   where c_region = 'ASIA'
   and c_mktsegment = 'FURNITURE';
   
   select max(lo_revenue), min(lo_revenue)
   from cust_sales_date_compound
   where c_region = 'ASIA'
   and c_mktsegment = 'FURNITURE';
   
   select max(lo_revenue), min(lo_revenue)
   from cust_sales_date_interleaved
   where c_region = 'ASIA'
   and c_mktsegment = 'FURNITURE';
   ```

1. Test a query that restricts on both the `c_region` column and the `c_mktsegment` column\. Execute the following queries\.

   ```
   -- Query 3
   
   select max(lo_revenue), min(lo_revenue)
   from cust_sales_date_single
   where d_date between '01/01/1996' and '01/14/1996'
   and c_mktsegment = 'FURNITURE'
   and c_region = 'ASIA';
   
   select max(lo_revenue), min(lo_revenue)
   from cust_sales_date_compound
   where d_date between '01/01/1996' and '01/14/1996'
   and c_mktsegment = 'FURNITURE'
   and c_region = 'ASIA';
   
   select max(lo_revenue), min(lo_revenue)
   from cust_sales_date_interleaved
   where d_date between '01/01/1996' and '01/14/1996'
   and c_mktsegment = 'FURNITURE'
   and c_region = 'ASIA';
   ```

1. Evaluate the results\.

   The following table summarizes the performance of the three sort styles\.
**Important**  
These results show relative performance for the two\-node cluster that was used for these examples\. Your results will vary, depending on multiple factors, such as your node type, number of nodes, and other concurrent operations contending for resources\.     
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/t_Sorting_data-compare-sort-styles.html)

   In Query 1, the results for all three sort styles are very similar, because the WHERE clause restricts only on the first column\. There is a small overhead cost for accessing an interleaved table\. 

   In Query 2, there is no benefit to the single\-column sort key because that column is not used in the WHERE clause\. There is no performance improvement for the compound sort key, because the query was restricted using the second and third columns in the sort key\. The query against the interleaved table shows the best performance because interleaved sorting is able to efficiently filter on secondary columns in the sort key\. 

   In Query 3, the interleaved sort is much faster than the other styles because it is able to filter on the combination of the `d_date`, `c_mktsegment`, and `c_region` columns\. 

This example uses a relatively small table, by Amazon Redshift standards, with 480 million rows\. With larger tables, containing billions of rows and more, interleaved sorting can improve performance by an order of magnitude or more for certain types of queries\.