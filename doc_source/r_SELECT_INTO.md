# SELECT INTO<a name="r_SELECT_INTO"></a>

Selects rows defined by any query and inserts them into a new table\. You can specify whether to create a temporary or a persistent table\. 

## Syntax<a name="r_SELECT_INTO-synopsis"></a>

```
[ WITH with_subquery [, ...] ]
SELECT
[ TOP number ] [ ALL | DISTINCT ]
* | expression [ AS output_name ] [, ...]
INTO [ TEMPORARY | TEMP ] [ TABLE ] new_table
[ FROM table_reference [, ...] ]
[ WHERE condition ]
[ GROUP BY expression [, ...] ]
[ HAVING condition [, ...] ]
[ { UNION | INTERSECT | { EXCEPT | MINUS } } [ ALL ] query ]
[ ORDER BY expression
[ ASC | DESC ]
[ LIMIT { number | ALL } ]
[ OFFSET start ]
```

 For details about the parameters of this command, see [SELECT](r_SELECT_synopsis.md)\. 

## Examples<a name="r_SELECT_INTO-examples"></a>

Select all of the rows from the EVENT table and create a NEWEVENT table: 

```
select * into newevent from event;
```

Select the result of an aggregate query into a temporary table called PROFITS: 

```
select username, lastname, sum(pricepaid-commission) as profit
into temp table profits
from sales, users
where sales.sellerid=users.userid
group by 1, 2
order by 3 desc;
```