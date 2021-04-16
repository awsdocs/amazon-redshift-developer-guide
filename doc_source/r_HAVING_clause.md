# HAVING clause<a name="r_HAVING_clause"></a>

The HAVING clause applies a condition to the intermediate grouped result set that a query returns\.

## Syntax<a name="r_HAVING_clause-synopsis"></a>

```
[ HAVING condition ]
```

For example, you can restrict the results of a SUM function:

```
having sum(pricepaid) >10000
```

The HAVING condition is applied after all WHERE clause conditions are applied and GROUP BY operations are completed\.

The condition itself takes the same form as any WHERE clause condition\.

## Usage notes<a name="r_HAVING_clause_usage_notes"></a>
+ Any column that is referenced in a HAVING clause condition must be either a grouping column or a column that refers to the result of an aggregate function\.
+ In a HAVING clause, you can't specify:
  + An alias that was defined in the select list\. You must repeat the original, unaliased expression\.
  + An ordinal number that refers to a select list item\. Only the GROUP BY and ORDER BY clauses accept ordinal numbers\.

## Examples<a name="r_HAVING_clause-examples"></a>

The following query calculates total ticket sales for all events by name, then eliminates events where the total sales were less than $800,000\. The HAVING condition is applied to the results of the aggregate function in the select list: `sum(pricepaid)`\.

```
select eventname, sum(pricepaid)
from sales join event on sales.eventid = event.eventid
group by 1
having sum(pricepaid) > 800000
order by 2 desc, 1;

eventname     |    sum
------------------+-----------
Mamma Mia!       | 1135454.00
Spring Awakening |  972855.00
The Country Girl |  910563.00
Macbeth          |  862580.00
Jersey Boys      |  811877.00
Legally Blonde   |  804583.00
(6 rows)
```

The following query calculates a similar result set\. In this case, however, the HAVING condition is applied to an aggregate that isn't specified in the select list: `sum(qtysold)`\. Events that did not sell more than 2,000 tickets are eliminated from the final result\.

```
select eventname, sum(pricepaid)
from sales join event on sales.eventid = event.eventid
group by 1
having sum(qtysold) >2000
order by 2 desc, 1;

eventname     |    sum
------------------+-----------
Mamma Mia!       | 1135454.00
Spring Awakening |  972855.00
The Country Girl |  910563.00
Macbeth          |  862580.00
Jersey Boys      |  811877.00
Legally Blonde   |  804583.00
Chicago          |  790993.00
Spamalot         |  714307.00
(8 rows)
```