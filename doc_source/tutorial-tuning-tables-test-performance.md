# Step 2: Test system performance to establish a baseline<a name="tutorial-tuning-tables-test-performance"></a>

As you test system performance before and after tuning your tables, you will record the following details:
+ Load time 
+ Storage use 
+ Query performance 

The examples in this tutorial are based on using a four\-node dc2\.large cluster\. Your results will be different, even if you use the same cluster configuration\. System performance is influenced by many factors, and no two systems will perform exactly the same\.

You will record your results using the following benchmarks table\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/tutorial-tuning-tables-test-performance.html)

## To test system performance to establish a baseline<a name="tutorial-tuning-tables-to-test-performance"></a>

1. Note the cumulative load time for all five tables and enter it in the benchmarks table in the Before column\. 

   This is the value you noted in the previous step\.

1. Record storage use\. 

   Determine how many 1 MB blocks of disk space are used for each table by querying the STV\_BLOCKLIST table and record the results in your benchmarks table\. 

   ```
   select stv_tbl_perm.name as table, count(*) as mb
   from stv_blocklist, stv_tbl_perm
   where stv_blocklist.tbl = stv_tbl_perm.id
   and stv_blocklist.slice = stv_tbl_perm.slice
   and stv_tbl_perm.name in ('lineorder','part','customer','dwdate','supplier')
   group by stv_tbl_perm.name
   order by 1 asc;
   ```

   Your results should look similar to this:

   ```
     table   |  mb
   ----------+------
   customer  | 384
   dwdate    | 160
   lineorder | 51024
   part      | 200
   supplier  | 152
   ```

1. Test query performance\.

   The first time you run a query, Amazon Redshift compiles the code, and then sends compiled code to the compute nodes\. When you compare the execution times for queries, you should not use the results for the first time you execute the query\. Instead, compare the times for the second execution of each query\. For more information, see [Factors affecting query performance](c-query-performance.md)\.
**Note**  
To reduce query execution time and improve system performance, Amazon Redshift caches the results of certain types of queries in memory on the leader node\. When result caching is enabled, subsequent queries run much faster, which invalidates performance comparisons\.

   To disable result caching for the current session, set the [enable\_result\_cache\_for\_session](r_enable_result_cache_for_session.md) parameter to `off`, as shown following\.

   ```
   set enable_result_cache_for_session to off;
   ```

   Run the following queries twice to eliminate compile time\. Record the second time for each query in the benchmarks table\. 

   ```
   -- Query 1
   -- Restrictions on only one dimension. 
   select sum(lo_extendedprice*lo_discount) as revenue
   from lineorder, dwdate
   where lo_orderdate = d_datekey
   and d_year = 1997 
   and lo_discount between 1 and 3 
   and lo_quantity < 24;
   
   -- Query 2
   -- Restrictions on two dimensions 
   
   select sum(lo_revenue), d_year, p_brand1
   from lineorder, dwdate, part, supplier
   where lo_orderdate = d_datekey
   and lo_partkey = p_partkey
   and lo_suppkey = s_suppkey
   and p_category = 'MFGR#12'
   and s_region = 'AMERICA'
   group by d_year, p_brand1
   order by d_year, p_brand1;
   
   -- Query 3
   -- Drill down in time to just one month 
   
   select c_city, s_city, d_year, sum(lo_revenue) as revenue 
   from customer, lineorder, supplier, dwdate
   where lo_custkey = c_custkey
   and lo_suppkey = s_suppkey
   and lo_orderdate = d_datekey
   and (c_city='UNITED KI1' or
   c_city='UNITED KI5')
   and (s_city='UNITED KI1' or
   s_city='UNITED KI5')
   and d_yearmonth = 'Dec1997'
   group by c_city, s_city, d_year
   order by d_year asc, revenue desc;
   ```

   Your results for the second time will look something like this:

   ```
   SELECT executed successfully
   
   Execution time: 6.97s
   (Statement 1 of 3 finished)
   
   SELECT executed successfully
   
   Execution time: 12.81s
   (Statement 2 of 3 finished)
   
   SELECT executed successfully
   
   Execution time: 13.39s
   (Statement 3 of 3 finished)
   
   Script execution finished
   Total script execution time: 33.17s
   ```

The following benchmarks table shows the example results for the cluster used in this tutorial\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/tutorial-tuning-tables-test-performance.html)

## Next step<a name="next-step--sort-keys"></a>

[Step 3: Select sort keys](tutorial-tuning-tables-sort-keys.md)