# Correlated subqueries<a name="r_correlated_subqueries"></a>

The following example contains a *correlated subquery* in the WHERE clause; this kind of subquery contains one or more correlations between its columns and the columns produced by the outer query\. In this case, the correlation is `where s.listid=l.listid`\. For each row that the outer query produces, the subquery is executed to qualify or disqualify the row\. 

```
select salesid, listid, sum(pricepaid) from sales s
where qtysold=
(select max(numtickets) from listing l
where s.listid=l.listid)
group by 1,2
order by 1,2
limit 5;

salesid | listid |   sum
---------+--------+----------
27 |     28 | 111.00
81 |    103 | 181.00
142 |    149 | 240.00
146 |    152 | 231.00
194 |    210 | 144.00
(5 rows)
```

## Correlated subquery patterns that are not supported<a name="r_correlated_subqueries-correlated-subquery-patterns-that-are-not-supported"></a>

The query planner uses a query rewrite method called subquery decorrelation to optimize several patterns of correlated subqueries for execution in an MPP environment\. A few types of correlated subqueries follow patterns that Amazon Redshift can't decorrelate and doesn't support\. Queries that contain the following correlation references return errors: 
+  Correlation references that skip a query block, also known as "skip\-level correlation references\." For example, in the following query, the block containing the correlation reference and the skipped block are connected by a NOT EXISTS predicate: 

  ```
  select event.eventname from event
  where not exists
  (select * from listing
  where not exists
  (select * from sales where event.eventid=sales.eventid));
  ```

  The skipped block in this case is the subquery against the LISTING table\. The correlation reference correlates the EVENT and SALES tables\. 
+  Correlation references from a subquery that is part of an ON clause in an outer join: 

  ```
  select * from category
  left join event
  on category.catid=event.catid and eventid =
  (select max(eventid) from sales where sales.eventid=event.eventid);
  ```

  The ON clause contains a correlation reference from SALES in the subquery to EVENT in the outer query\. 
+ Null\-sensitive correlation references to an Amazon Redshift system table\. For example: 

  ```
  select attrelid
  from stv_locks sl, pg_attribute
  where sl.table_id=pg_attribute.attrelid and 1 not in
  (select 1 from pg_opclass where sl.lock_owner = opcowner);
  ```
+ Correlation references from within a subquery that contains a window function\. 

  ```
  select listid, qtysold
  from sales s
  where qtysold not in
  (select sum(numtickets) over() from listing l where s.listid=l.listid);
  ```
+ References in a GROUP BY column to the results of a correlated subquery\. For example: 

  ```
  select listing.listid,
  (select count (sales.listid) from sales where sales.listid=listing.listid) as list
  from listing
  group by list, listing.listid;
  ```
+ Correlation references from a subquery with an aggregate function and a GROUP BY clause, connected to the outer query by an IN predicate\. \(This restriction doesn't apply to MIN and MAX aggregate functions\.\) For example: 

  ```
  select * from listing where listid in
  (select sum(qtysold)
  from sales
  where numtickets>4
  group by salesid);
  ```