# Evaluating the query plan<a name="c_data_redistribution"></a>

 You can use query plans to identify candidates for optimizing the distribution style\. 

After making your initial design decisions, create your tables, load them with data, and test them\. Use a test dataset that is as close as possible to the real data\. Measure load times to use as a baseline for comparisons\. 

 Evaluate queries that are representative of the most costly queries you expect to execute; specifically, queries that use joins and aggregations\. Compare execution times for various design options\. When you compare execution times, do not count the first time the query is executed, because the first run time includes the compilation time\. 

**DS\_DIST\_NONE**  
 No redistribution is required, because corresponding slices are collocated on the compute nodes\. You will typically have only one DS\_DIST\_NONE step, the join between the fact table and one dimension table\. 

**DS\_DIST\_ALL\_NONE**  
 No redistribution is required, because the inner join table used DISTSTYLE ALL\. The entire table is located on every node\. 

**DS\_DIST\_INNER**  
 The inner table is redistributed\. 

**DS\_DIST\_OUTER**  
 The outer table is redistributed\. 

**DS\_BCAST\_INNER**  
 A copy of the entire inner table is broadcast to all the compute nodes\. 

**DS\_DIST\_ALL\_INNER**  
The entire inner table is redistributed to a single slice because the outer table uses DISTSTYLE ALL\.

**DS\_DIST\_BOTH**  
 Both tables are redistributed\. 

DS\_DIST\_NONE and DS\_DIST\_ALL\_NONE are good\. They indicate that no distribution was required for that step because all of the joins are collocated\. 

DS\_DIST\_INNER means that the step will probably have a relatively high cost because the inner table is being redistributed to the nodes\. DS\_DIST\_INNER indicates that the outer table is already properly distributed on the join key\. Set the inner table's distribution key to the join key to convert this to DS\_DIST\_NONE\. If distributing the inner table on the join key is not possible because the outer table is not distributed on the join key, evaluate whether to use ALL distribution for the inner table\. If the table is relatively slow moving, that is, it is not updated frequently or extensively, and it is large enough to carry a high redistribution cost, change the distribution style to ALL and test again\. ALL distribution causes increased load times, so when you retest, include the load time in your evaluation factors\. 

DS\_DIST\_ALL\_INNER is not good\. It means the entire inner table is redistributed to a single slice because the outer table uses DISTSTYLE ALL, so that a copy of the entire outer table is located on each node\. This results in inefficient serial execution of the join on a single node instead taking advantage of parallel execution using all of the nodes\. DISTSTYLE ALL is meant to be used only for the inner join table\. Instead, specify a distribution key or use even distribution for the outer table\.

DS\_BCAST\_INNER and DS\_DIST\_BOTH are not good\. Usually these redistributions occur because the tables are not joined on their distribution keys\. If the fact table does not already have a distribution key, specify the joining column as the distribution key for both tables\. If the fact table already has a distribution key on another column, you should evaluate whether changing the distribution key to collocate this join will improve overall performance\. If changing the distribution key of the outer table is not an optimal choice, you can achieve collocation by specifying DISTSTYLE ALL for the inner table\. 

 The following example shows a portion of a query plan with DS\_BCAST\_INNER and DS\_DIST\_NONE labels\.

```
->  XN Hash Join DS_BCAST_INNER  (cost=112.50..3272334142.59 rows=170771 width=84)
        Hash Cond: ("outer".venueid = "inner".venueid)
        ->  XN Hash Join DS_BCAST_INNER  (cost=109.98..3167290276.71 rows=172456 width=47)
              Hash Cond: ("outer".eventid = "inner".eventid)
              ->  XN Merge Join DS_DIST_NONE  (cost=0.00..6286.47 rows=172456 width=30)
                    Merge Cond: ("outer".listid = "inner".listid)
                    ->  XN Seq Scan on listing  (cost=0.00..1924.97 rows=192497 width=14)
                    ->  XN Seq Scan on sales  (cost=0.00..1724.56 rows=172456 width=24)
```

After changing the dimension tables to use DISTSTYLE ALL, the query plan for the same query shows DS\_DIST\_ALL\_NONE in place of DS\_BCAST\_INNER\. Also, there is a dramatic change in the relative cost for the join steps\.

```
->  XN Hash Join DS_DIST_ALL_NONE  (cost=112.50..14142.59 rows=170771 width=84)
        Hash Cond: ("outer".venueid = "inner".venueid)
        ->  XN Hash Join DS_DIST_ALL_NONE  (cost=109.98..10276.71 rows=172456 width=47)
              Hash Cond: ("outer".eventid = "inner".eventid)
              ->  XN Merge Join DS_DIST_NONE  (cost=0.00..6286.47 rows=172456 width=30)
                    Merge Cond: ("outer".listid = "inner".listid)
                    ->  XN Seq Scan on listing  (cost=0.00..1924.97 rows=192497 width=14)
                    ->  XN Seq Scan on sales  (cost=0.00..1724.56 rows=172456 width=24)
```