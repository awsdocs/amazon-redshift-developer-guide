# Amazon Redshift best practices for designing queries<a name="c_designing-queries-best-practices"></a>

To maximize query performance, follow these recommendations when creating queries:
+ Design tables according to best practices to provide a solid foundation for query performance\. For more information, see [Amazon Redshift best practices for designing tables](c_designing-tables-best-practices.md)\.
+ Avoid using `select *`\. Include only the columns you specifically need\.
+ Use a [CASE expression](r_CASE_function.md) to perform complex aggregations instead of selecting from the same table multiple times\.
+ Don't use cross\-joins unless absolutely necessary\. These joins without a join condition result in the Cartesian product of two tables\. Cross\-joins are typically executed as nested\-loop joins, which are the slowest of the possible join types\. 
+ Use subqueries in cases where one table in the query is used only for predicate conditions and the subquery returns a small number of rows \(less than about 200\)\. The following example uses a subquery to avoid joining the LISTING table\.

  ```
  select sum(sales.qtysold)
  from sales
  where salesid in (select listid from listing where listtime > '2008-12-26');
  ```
+ Use predicates to restrict the dataset as much as possible\.
+ In the predicate, use the least expensive operators that you can\. [Comparison condition](r_comparison_condition.md) operators are preferable to [LIKE](r_patternmatching_condition_like.md) operators\. LIKE operators are still preferable to [SIMILAR TO](pattern-matching-conditions-similar-to.md) or [POSIX operators](pattern-matching-conditions-posix.md)\.
+ Avoid using functions in query predicates\. Using them can drive up the cost of the query by requiring large numbers of rows to resolve the intermediate steps of the query\.
+ If possible, use a WHERE clause to restrict the dataset\. The query planner can then use row order to help determine which records match the criteria, so it can skip scanning large numbers of disk blocks\. Without this, the query execution engine must scan participating columns entirely\.
+ Add predicates to filter tables that participate in joins, even if the predicates apply the same filters\. The query returns the same result set, but Amazon Redshift is able to filter the join tables before the scan step and can then efficiently skip scanning blocks from those tables\. Redundant filters aren't needed if you filter on a column that's used in the join condition\. 

  For example, suppose that you want to join `SALES` and `LISTING` to find ticket sales for tickets listed after December, grouped by seller\. Both tables are sorted by date\. The following query joins the tables on their common key and filters for `listing.listtime` values greater than December 1\.

  ```
  select listing.sellerid, sum(sales.qtysold)
  from sales, listing
  where sales.salesid = listing.listid
  and listing.listtime > '2008-12-01'
  group by 1 order by 1;
  ```

  The WHERE clause doesn't include a predicate for `sales.saletime`, so the execution engine is forced to scan the entire `SALES` table\. If you know the filter would result in fewer rows participating in the join, then add that filter as well\. The following example cuts execution time significantly\.

  ```
  select listing.sellerid, sum(sales.qtysold)
  from sales, listing
  where sales.salesid = listing.listid
  and listing.listtime > '2008-12-01'
  and sales.saletime > '2008-12-01'
  group by 1 order by 1;
  ```
+ Use sort keys in the GROUP BY clause so the query planner can use more efficient aggregation\. A query might qualify for one\-phase aggregation when its GROUP BY list contains only sort key columns, one of which is also the distribution key\. The sort key columns in the GROUP BY list must include the first sort key, then other sort keys that you want to use in sort key order\. For example, it is valid to use the first sort key, the first and second sort keys, the first, second, and third sort keys, and so on\. It is not valid to use the first and third sort keys\.

  You can confirm the use of one\-phase aggregation by running the [EXPLAIN](r_EXPLAIN.md) command and looking for `XN GroupAggregate` in the aggregation step of the query\.
+ If you use both GROUP BY and ORDER BY clauses, make sure that you put the columns in the same order in both\. That is, use the approach just following\.

  ```
  group by a, b, c
  order by a, b, c
  ```

  Don't use the following approach\.

  ```
  group by b, c, a
  order by a, b, c
  ```