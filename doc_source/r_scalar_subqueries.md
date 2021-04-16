# Scalar subqueries<a name="r_scalar_subqueries"></a>

A scalar subquery is a regular SELECT query in parentheses that returns exactly one value: one row with one column\. The query is executed and the returned value is used in the outer query\. If the subquery returns zero rows, the value of the subquery expression is null\. If it returns more than one row, Amazon Redshift returns an error\. The subquery can refer to variables from the parent query, which will act as constants during any one invocation of the subquery\.

You can use scalar subqueries in most statements that call for an expression\. Scalar subqueries are not valid expressions in the following cases:
+ As default values for expressions
+ In GROUP BY and HAVING clauses

## Example<a name="r_scalar_subqueries-example"></a>

The following subquery computes the average price paid per sale across the entire year of 2008, then the outer query uses that value in the output to compare against the average price per sale per quarter:

```
select qtr, avg(pricepaid) as avg_saleprice_per_qtr,
(select avg(pricepaid)
from sales join date on sales.dateid=date.dateid
where year = 2008) as avg_saleprice_yearly
from sales join date on sales.dateid=date.dateid
where year = 2008
group by qtr
order by qtr;
qtr  | avg_saleprice_per_qtr | avg_saleprice_yearly
-------+-----------------------+----------------------
1     |                647.64 |               642.28
2     |                646.86 |               642.28
3     |                636.79 |               642.28
4     |                638.26 |               642.28
(4 rows)
```