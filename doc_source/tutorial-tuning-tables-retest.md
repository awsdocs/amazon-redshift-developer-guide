# Step 7: Retest system performance after tuning<a name="tutorial-tuning-tables-retest"></a>

After recreating the test data set with the selected sort keys, distribution styles, and compressions encodings, you will retest the system performance\.

## To retest system performance after tuning<a name="tutorial-tuning-tables-to-retest"></a>

1. Record storage use\.

   Determine how many 1 MB blocks of disk space are used for each table by querying the STV\_BLOCKLIST table and record the results in your benchmarks table\. 

   ```
   select stv_tbl_perm.name as "table", count(*) as "blocks (mb)"
   from stv_blocklist, stv_tbl_perm
   where stv_blocklist.tbl = stv_tbl_perm.id
   and stv_blocklist.slice = stv_tbl_perm.slice
   and stv_tbl_perm.name in ('customer', 'part', 'supplier', 'dwdate', 'lineorder')
   group by stv_tbl_perm.name
   order by 1 asc;
   ```

   Your results will look similar to this:

   ```
    table     |  blocks (mb)
   -----------+-----------------
   customer    	604
   dwdate      	160
   lineorder   	27152
   part        	200
   supplier    	236
   ```

1. Check for distribution skew\.

   Uneven distribution, or data distribution skew, forces some nodes to do more work than others, which limits query performance\. 

   To check for distribution skew, query the SVV\_DISKUSAGE system view\. Each row in SVV\_DISKUSAGE records the statistics for one disk block\. The `num_values` column gives the number of rows in that disk block, so `sum(num_values)` returns the number of rows on each slice\. 

   Execute the following query to see the distribution for all of the tables in the SSB database\. 

   ```
   select trim(name) as table, slice, sum(num_values) as rows, min(minvalue), max(maxvalue)
   from svv_diskusage
   where name in ('customer', 'part', 'supplier', 'dwdate', 'lineorder') 
   and col =0
   group by name, slice
   order by name, slice;
   ```

   Your results will look something like this:

   ```
      table   | slice |   rows   |   min    |    max
   -----------+-------+----------+----------+-----------
    customer  |     0 |  3000000 |        1 |   3000000
    customer  |     2 |  3000000 |        1 |   3000000
    customer  |     4 |  3000000 |        1 |   3000000
    customer  |     6 |  3000000 |        1 |   3000000
    dwdate    |     0 |     2556 | 19920101 |  19981230
    dwdate    |     2 |     2556 | 19920101 |  19981230
    dwdate    |     4 |     2556 | 19920101 |  19981230
    dwdate    |     6 |     2556 | 19920101 |  19981230
    lineorder |     0 | 75029991 |        3 | 599999975
    lineorder |     1 | 75059242 |        7 | 600000000
    lineorder |     2 | 75238172 |        1 | 599999975
    lineorder |     3 | 75065416 |        1 | 599999973
    lineorder |     4 | 74801845 |        3 | 599999975
    lineorder |     5 | 75177053 |        1 | 599999975
    lineorder |     6 | 74631775 |        1 | 600000000
    lineorder |     7 | 75034408 |        1 | 599999974
    part      |     0 |   175006 |       15 |   1399997
    part      |     1 |   175199 |        1 |   1399999
    part      |     2 |   175441 |        4 |   1399989
    part      |     3 |   175000 |        3 |   1399995
    part      |     4 |   175018 |        5 |   1399979
    part      |     5 |   175091 |       11 |   1400000
    part      |     6 |   174253 |        2 |   1399969
    part      |     7 |   174992 |       13 |   1399996
    supplier  |     0 |  1000000 |        1 |   1000000
    supplier  |     2 |  1000000 |        1 |   1000000
    supplier  |     4 |  1000000 |        1 |   1000000
    supplier  |     6 |  1000000 |        1 |   1000000
   (28 rows)
   ```

   The following chart illustrates the distribution of the three largest tables\. \(The columns are not to scale\.\) Notice that because CUSTOMER uses ALL distribution, it was distributed to only one slice per node\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/tutorial-optimize-tables-compression-chart.png)

   The distribution is relatively even, so you don't need to adjust for distribution skew\.

1. Run an EXPLAIN command with each query to view the query plans\.

   The following example shows the EXPLAIN command with Query 2\.

   ```
   explain
   select sum(lo_revenue), d_year, p_brand1
   from lineorder, dwdate, part, supplier
   where lo_orderdate = d_datekey
   and lo_partkey = p_partkey
   and lo_suppkey = s_suppkey
   and p_category = 'MFGR#12'
   and s_region = 'AMERICA'
   group by d_year, p_brand1
   order by d_year, p_brand1;
   ```

   In the EXPLAIN plan for Query 2, notice that the DS\_BCAST\_INNER labels have been replaced by DS\_DIST\_ALL\_NONE and DS\_DIST\_NONE, which means that no redistribution was required for those steps, and the query should run much more quickly\. 

   ```
   QUERY PLAN
   XN Merge  (cost=1000014243538.45..1000014243539.15 rows=280 width=20)
     Merge Key: dwdate.d_year, part.p_brand1
     ->  XN Network  (cost=1000014243538.45..1000014243539.15 rows=280 width=20)
           Send to leader
           ->  XN Sort  (cost=1000014243538.45..1000014243539.15 rows=280 width=20)
                 Sort Key: dwdate.d_year, part.p_brand1
                 ->  XN HashAggregate  (cost=14243526.37..14243527.07 rows=280 width=20)
                       ->  XN Hash Join DS_DIST_ALL_NONE  (cost=30643.30..14211277.03 rows=4299912 
                             Hash Cond: ("outer".lo_orderdate = "inner".d_datekey)
                             ->  XN Hash Join DS_DIST_ALL_NONE  (cost=30611.35..14114497.06 
                                   Hash Cond: ("outer".lo_suppkey = "inner".s_suppkey)
                                   ->  XN Hash Join DS_DIST_NONE  (cost=17640.00..13758507.64 
                                         Hash Cond: ("outer".lo_partkey = "inner".p_partkey)
                                         ->  XN Seq Scan on lineorder  (cost=0.00..6000378.88 
                                         ->  XN Hash  (cost=17500.00..17500.00 rows=56000 width=16)
                                               ->  XN Seq Scan on part  (cost=0.00..17500.00 
                                                     Filter: ((p_category)::text = 'MFGR#12'::text)
                                   ->  XN Hash  (cost=12500.00..12500.00 rows=188541 width=4)
                                         ->  XN Seq Scan on supplier  (cost=0.00..12500.00 
                                               Filter: ((s_region)::text = 'AMERICA'::text)
                             ->  XN Hash  (cost=25.56..25.56 rows=2556 width=8)
                                   ->  XN Seq Scan on dwdate  (cost=0.00..25.56 rows=2556 width=8)
   ```

1. Run the same test queries again\.

   If you reconnected to the database since your first set of tests, disable result caching for this session\. To disable result caching for the current session, set the [enable\_result\_cache\_for\_session](r_enable_result_cache_for_session.md) parameter to `off`, as shown following\.

   ```
   set enable_result_cache_for_session to off;
   ```

   As you did earlier, run the following queries twice to eliminate compile time\. Record the second time for each query in the benchmarks table\.

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

The following benchmarks table shows the results based on the cluster used in this example\. Your results will vary based on a number of factors, but the relative results should be similar\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/tutorial-tuning-tables-retest.html)

## Next step<a name="next-step-retest"></a>

[Step 8: Evaluate the results](tutorial-tuning-tables-evaluate.md)