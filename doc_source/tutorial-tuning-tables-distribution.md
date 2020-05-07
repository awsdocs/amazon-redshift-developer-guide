# Step 4: Select distribution styles<a name="tutorial-tuning-tables-distribution"></a>

When you load data into a table, Amazon Redshift distributes the rows of the table to each of the node slices according to the table's distribution style\. The number of slices per node depends on the node size of the cluster\. For example, the dc2\.large cluster that you are using in this tutorial has four nodes with two slices each, so the cluster has a total of eight slices\. The nodes all participate in parallel query execution, working on data that is distributed across the slices\.

When you execute a query, the query optimizer redistributes the rows to the compute nodes as needed to perform any joins and aggregations\. Redistribution might involve either sending specific rows to nodes for joining or broadcasting an entire table to all of the nodes\. 

You should assign distribution styles to achieve these goals\.
+ Collocate the rows from joining tables 

  When the rows for joining columns are on the same slices, less data needs to be moved during query execution\. 
+ Distribute data evenly among the slices in a cluster\. 

  If data is distributed evenly, workload can be allocated evenly to all the slices\. 

These goals may conflict in some cases, and you will need to evaluate which strategy is the best choice for overall system performance\. For example, even distribution might place all matching values for a column on the same slice\. If a query uses an equality filter on that column, the slice with those values will carry a disproportionate share of the workload\. If tables are collocated based on a distribution key, the rows might be distributed unevenly to the slices because the keys are distributed unevenly through the table\. 

In this step, you evaluate the distribution of the SSB tables with respect to the goals of data distribution, and then select the optimum distribution styles for the tables\.

## Distribution styles<a name="tutorial-tuning-tables-distribution-styles"></a>

When you create a table, you designate one of three distribution styles: KEY, ALL, or EVEN\. 

**KEY distribution** 

The rows are distributed according to the values in one column\. The leader node will attempt to place matching values on the same node slice\. If you distribute a pair of tables on the joining keys, the leader node collocates the rows on the slices according to the values in the joining columns so that matching values from the common columns are physically stored together\. 

**ALL distribution** 

A copy of the entire table is distributed to every node\. Where EVEN distribution or KEY distribution place only a portion of a table's rows on each node, ALL distribution ensures that every row is collocated for every join that the table participates in\. 

**EVEN distribution** 

The rows are distributed across the slices in a round\-robin fashion, regardless of the values in any particular column\. EVEN distribution is appropriate when a table does not participate in joins or when there is not a clear choice between KEY distribution and ALL distribution\. EVEN distribution is the default distribution style\. 

For more information, see [Distribution styles](c_choosing_dist_sort.md)\. 

## To select distribution styles<a name="tutorial-tuning-tables-distribution-to-collocate"></a>

When you execute a query, the query optimizer redistributes the rows to the compute nodes as needed to perform any joins and aggregations\. By locating the data where it needs to be before the query is executed, you can minimize the impact of the redistribution step\. 

The first goal is to distribute the data so that the matching rows from joining tables are collocated, which means that the matching rows from joining tables are located on the same node slice\. 

1. To look for redistribution steps in the query plan, execute an EXPLAIN command followed by the query\. This example uses Query 2 from our set of test queries\. 

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

   The following shows a portion of the query plan\. Look for labels that begin with *DS\_BCAST* or *DS\_DIST* labels

   ```
   QUERY PLAN
   XN Merge  (cost=1038007224737.84..1038007224738.54 rows=280 width=20)
     Merge Key: dwdate.d_year, part.p_brand1
     ->  XN Network  (cost=1038007224737.84..1038007224738.54 rows=280 width=20)
           Send to leader
           ->  XN Sort  (cost=1038007224737.84..1038007224738.54 rows=280 width=20)
                 Sort Key: dwdate.d_year, part.p_brand1
                 ->  XN HashAggregate  (cost=38007224725.76..38007224726.46 rows=280 
                       ->  XN Hash Join DS_BCAST_INNER  (cost=30674.95..38007188507.46 
                             Hash Cond: ("outer".lo_orderdate = "inner".d_datekey)
                             ->  XN Hash Join DS_BCAST_INNER  (cost=30643.00..37598119820.65 
                                   Hash Cond: ("outer".lo_suppkey = "inner".s_suppkey)
                                   ->  XN Hash Join DS_BCAST_INNER  
                                         Hash Cond: ("outer".lo_partkey = "inner".p_partkey)
                                         ->  XN Seq Scan on lineorder  
                                         ->  XN Hash  (cost=17500.00..17500.00 rows=56000 
                                               ->  XN Seq Scan on part  (cost=0.00..17500.00 
                                                     Filter: ((p_category)::text = 
                                   ->  XN Hash  (cost=12500.00..12500.00 rows=201200
                                         ->  XN Seq Scan on supplier  (cost=0.00..12500.00 
                                               Filter: ((s_region)::text = 'AMERICA'::text)
                             ->  XN Hash  (cost=25.56..25.56 rows=2556 width=8)
                                   ->  XN Seq Scan on dwdate  (cost=0.00..25.56 rows=2556
   ```

   DS\_BCAST\_INNER indicates that the inner join table was broadcast to every slice\. A *DS\_DIST\_BOTH* label, if present, would indicate that both the outer join table and the inner join table were redistributed across the slices\. Broadcasting and redistribution can be expensive steps in terms of query performance\. You want to select distribution strategies that reduce or eliminate broadcast and distribution steps\. For more information about evaluating the EXPLAIN plan, see [Evaluating query patterns](t_evaluating_query_patterns.md)\. 

1. Distribute the fact table and one dimension table on their common columns\. 

   The following diagram shows the relationships between the fact table, LINEORDER, and the dimension tables in the SSB schema\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/tutorial-optimize-tables-ssb-data-model-join-keys.png)

   Each table can have only one distribution key, which means that only one pair of tables in the schema can be collocated on their common columns\. The central fact table is the clear first choice\. For the second table in the pair, choose the largest dimension that commonly joins the fact table\. In this design, LINEORDER is the fact table, and PART is the largest dimension\. PART joins LINEORDER on its primary key, `p_partkey`\. 

   Designate `lo_partkey` as the distribution key for LINEORDER and `p_partkey` as the distribution key for PART so that the matching values for the joining keys will be collocated on the same slices when the data is loaded\. 

1. Change some dimension tables to use ALL distribution\. 

   If a dimension table cannot be collocated with the fact table or other important joining tables, you can often improve query performance significantly by distributing the entire table to all of the nodes\. ALL distribution guarantees that the joining rows will be collocated on every slice\. You should weigh all factors before choosing ALL distribution\. Using ALL distribution multiplies storage space requirements and increases load times and maintenance operations\. 

   CUSTOMER, SUPPLIER, and DWDATE also join the LINEORDER table on their primary keys; however, LINEORDER will be collocated with PART, so you will set the remaining tables to use DISTSTYLE ALL\. Because the tables are relatively small and are not updated frequently, using ALL distribution will have minimal impact on storage and load times\. 

1. Use EVEN distribution for the remaining tables\. 

   All of the tables have been assigned with DISTKEY or ALL distribution styles, so you won't assign EVEN to any tables\. After evaluating your performance results, you might decide to change some tables from ALL to EVEN distribution\. 

The following tuning table shows the chosen distribution styles\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/tutorial-tuning-tables-distribution.html)

You can find the steps for setting the distribution style in [Step 6: Recreate the test data set](tutorial-tuning-tables-recreate-test-data.md)\.

For more information, see [Choose the best distribution style](c_best-practices-best-dist-key.md)\.

## Next step<a name="next-step-compression"></a>

[Step 5: Review compression encodings](tutorial-tuning-tables-compression.md)