# Expression lists<a name="r_expression_lists"></a>

An expression list is a combination of expressions, and can appear in membership and comparison conditions \(WHERE clauses\) and in GROUP BY clauses\.

## Syntax<a name="r_expression_lists-synopsis"></a>

```
expression , expression , ... | (expression, expression, ...)
```

## Arguments<a name="r_expression_lists-arguments"></a>

 *expression*   
A simple expression that evaluates to a value\. An expression list can contain one or more comma\-separated expressions or one or more sets of comma\-separated expressions\. When there are multiple sets of expressions, each set must contain the same number of expressions, and be separated by parentheses\. The number of expressions in each set must match the number of expressions before the operator in the condition\.

## Examples<a name="r_expression_lists-examples"></a>

The following are examples of expression lists in conditions:

```
(1, 5, 10)
('THESE', 'ARE', 'STRINGS')
(('one', 'two', 'three'), ('blue', 'yellow', 'green'))
```

The number of expressions in each set must match the number in the first part of the statement:

```
select * from venue
where (venuecity, venuestate) in (('Miami', 'FL'), ('Tampa', 'FL'))
order by venueid;

venueid |        venuename        | venuecity | venuestate | venueseats
---------+-------------------------+-----------+------------+------------
28 | American Airlines Arena | Miami     | FL         |          0
54 | St. Pete Times Forum    | Tampa     | FL         |          0
91 | Raymond James Stadium   | Tampa     | FL         |      65647
(3 rows)
```