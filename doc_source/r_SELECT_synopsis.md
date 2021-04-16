# SELECT<a name="r_SELECT_synopsis"></a>

**Topics**
+ [Syntax](#r_SELECT_synopsis-synopsis)
+ [WITH clause](r_WITH_clause.md)
+ [SELECT list](r_SELECT_list.md)
+ [FROM clause](r_FROM_clause30.md)
+ [WHERE clause](r_WHERE_clause.md)
+ [GROUP BY clause](r_GROUP_BY_clause.md)
+ [HAVING clause](r_HAVING_clause.md)
+ [UNION, INTERSECT, and EXCEPT](r_UNION.md)
+ [ORDER BY clause](r_ORDER_BY_clause.md)
+ [Join examples](r_Join_examples.md)
+ [Subquery examples](r_Subquery_examples.md)
+ [Correlated subqueries](r_correlated_subqueries.md)

Returns rows from tables, views, and user\-defined functions\. 

**Note**  
The maximum size for a single SQL statement is 16 MB\.

## Syntax<a name="r_SELECT_synopsis-synopsis"></a>

```
[ WITH with_subquery [, ...] ]
SELECT
[ TOP number | [ ALL | DISTINCT ]
* | expression [ AS output_name ] [, ...] ]
[ FROM table_reference [, ...] ]
[ WHERE condition ]
[ GROUP BY expression [, ...] ]
[ HAVING condition ]
[ { UNION | ALL | INTERSECT | EXCEPT | MINUS } query ]
[ ORDER BY expression [ ASC | DESC ] ]
[ LIMIT { number | ALL } ]
[ OFFSET start ]
```