# Reviewing query plan steps<a name="reviewing-query-plan-steps"></a>

You can see the steps in a query plan by running the EXPLAIN command\. The following example shows an SQL query and explains the output\. Reading the query plan from the bottom up, you can see each of the logical operations used to perform the query\. For more information, see [Query plan](c-the-query-plan.md)\.

```
explain
select eventname, sum(pricepaid) from sales, event
where sales.eventid = event.eventid
group by eventname
order by 2 desc;
```

```
XN Merge  (cost=1002815366604.92..1002815366606.36 rows=576 width=27)
  Merge Key: sum(sales.pricepaid)
  ->  XN Network  (cost=1002815366604.92..1002815366606.36 rows=576 width=27)
        Send to leader
        ->  XN Sort  (cost=1002815366604.92..1002815366606.36 rows=576 width=27)
              Sort Key: sum(sales.pricepaid)
              ->  XN HashAggregate  (cost=2815366577.07..2815366578.51 rows=576 width=27)
                    ->  XN Hash Join DS_BCAST_INNER  (cost=109.98..2815365714.80 rows=172456 width=27)
                          Hash Cond: ("outer".eventid = "inner".eventid)
                          ->  XN Seq Scan on sales  (cost=0.00..1724.56 rows=172456 width=14)
                          ->  XN Hash  (cost=87.98..87.98 rows=8798 width=21)
                                ->  XN Seq Scan on event  (cost=0.00..87.98 rows=8798 width=21)
```

As part of generating a query plan, the query optimizer breaks down the plan into streams, segments, and steps\. The query optimizer breaks the plan down to prepare for distributing the data and query workload to the compute nodes\. For more information about streams, segments, and steps, see [Query planning and execution workflow](c-query-planning.md)\. 

The following illustration shows the preceding query and associated query plan\. It displays how the query operations involved map to steps that Amazon Redshift uses to generate compiled code for the compute node slices\. Each query plan operation maps to multiple steps within the segments, and sometimes to multiple segments within the streams\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/map-plan-to-streams.png)

In this illustration, the query optimizer runs the query plan as follows:

1. In `Stream 0`, the query runs `Segment 0` with a sequential scan operation to scan the `events` table\. The query continues to `Segment 1` with a hash operation to create the hash table for the inner table in the join\.

1. In `Stream 1`, the query runs `Segment 2` with a sequential scan operation to scan the `sales` table\. It continues with `Segment 2` with a hash join to join tables where the join columns are not both distribution keys and sort keys\. It again continues with `Segment 2` with a hash aggregate to aggregate results\. Then the query runs `Segment 3` with a hash aggregate operation to perform unsorted grouped aggregate functions and a sort operation to evaluate the ORDER BY clause and other sort operations\.

1. In `Stream 2`, the query runs a network operation in `Segment 4` and `Segment 5` to send intermediate results to the leader node for further processing\.

The last segment of a query returns the data\. If the return set is aggregated or sorted, the compute nodes each send their piece of the intermediate result to the leader node\. The leader node then merges the data so the final result can be sent back to the requesting client\.

For more information about EXPLAIN operators, see [EXPLAIN](r_EXPLAIN.md)\.