# Reviewing Query Plan Steps<a name="reviewing-query-plan-steps"></a>

You can see the steps in a query plan by running the EXPLAIN command\. The following example shows a SQL query and the query plan that the EXPLAIN command produces for it\. Reading the query plan from the bottom up, you can see each of the logical operations needed to perform the query\. For more information, see [Query Plan](c-the-query-plan.md)\.

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

The following illustration uses the preceding query and associated query plan to show how those query operations are mapped to steps, segments, and streams\. Each query plan operation maps to multiple steps within the segments, and sometimes to multiple segments within the streams\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/map-plan-to-streams.png)