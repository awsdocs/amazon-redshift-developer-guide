# Analyzing the query plan<a name="c-analyzing-the-query-plan"></a>

Before analyzing the query plan, you should be familiar with how to read it\. If you are unfamiliar with reading a query plan, we recommend that you read [Query plan](c-the-query-plan.md) before proceeding\.

Run the [EXPLAIN](r_EXPLAIN.md) command to get a query plan\. To analyze the data provided by the query plan, follow these steps:

1. Identify the steps with the highest cost\. Concentrate on optimizing those when proceeding through the remaining steps\.

1. Look at the join types:
   + **Nested Loop**: Such joins usually occur because a join condition was omitted\. For recommended solutions, see [Nested loop](query-performance-improvement-opportunities.md#nested-loop)\.
   + **Hash and Hash Join**: Hash joins are used when joining tables where the join columns are not distribution keys and also not sort keys\. For recommended solutions, see [Hash join](query-performance-improvement-opportunities.md#hash-join)\.
   + **Merge Join**: No change is needed\.

1. Notice which table is used for the inner join, and which for the outer join\. The query engine generally chooses the smaller table for the inner join, and the larger table for the outer join\. If such a choice doesn't occur, your statistics are likely out of date\. For recommended solutions, see [Table statistics missing or out of date](query-performance-improvement-opportunities.md#table-statistics-missing-or-out-of-date)\.

1. See if there are any high\-cost sort operations\. If there are, see [Unsorted or missorted rows](query-performance-improvement-opportunities.md#unsorted-or-mis-sorted-rows) for recommended solutions\.

1. Look for the following broadcast operators where there are high\-cost operations:
   + **DS\_BCAST\_INNER**: Indicates the table is broadcast to all the compute nodes, which is fine for a small table but not ideal for a larger table\.
   + **DS\_DIST\_ALL\_INNER**: Indicates that all of the workload is on a single slice\.
   + **DS\_DIST\_BOTH**: Indicates heavy redistribution\.

   For recommended solutions for these situations, see [Suboptimal data distribution](query-performance-improvement-opportunities.md#suboptimal-data-distribution)\.