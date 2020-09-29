# ORDER BY clause<a name="r_ORDER_BY_clause"></a>

**Topics**
+ [Syntax](#r_ORDER_BY_clause-synopsis)
+ [Parameters](#r_ORDER_BY_clause-parameters)
+ [Usage notes](#r_ORDER_BY_usage_notes)
+ [Examples with ORDER BY](r_Examples_with_ORDER_BY.md)

The ORDER BY clause sorts the result set of a query\.

## Syntax<a name="r_ORDER_BY_clause-synopsis"></a>

```
[ ORDER BY expression [ ASC | DESC ] ]
[ NULLS FIRST | NULLS LAST ]  
[ LIMIT { count | ALL } ]
[ OFFSET start ]
```

## Parameters<a name="r_ORDER_BY_clause-parameters"></a>

 *expression*   
Expression that defines the sort order of the query result set, typically by specifying one or more columns in the select list\. Results are returned based on binary UTF\-8 ordering\. You can also specify the following:  
+ Columns that aren't in the select list
+ Expressions formed from one or more columns that exist in the tables referenced by the query
+ Ordinal numbers that represent the position of select list entries \(or the position of columns in the table if no select list exists\)
+ Aliases that define select list entries
When the ORDER BY clause contains multiple expressions, the result set is sorted according to the first expression, then the second expression is applied to rows that have matching values from the first expression, and so on\.

ASC \| DESC   
Option that defines the sort order for the expression, as follows:   
+ ASC: ascending \(for example, low to high for numeric values and 'A' to 'Z' for character strings\)\. If no option is specified, data is sorted in ascending order by default\. 
+ DESC: descending \(high to low for numeric values; 'Z' to 'A' for strings\)\. 

NULLS FIRST \| NULLS LAST  
Option that specifies whether NULL values should be ordered first, before non\-null values, or last, after non\-null values\. By default, NULL values are sorted and ranked last in ASC ordering, and sorted and ranked first in DESC ordering\.

LIMIT *number* \| ALL   <a name="order-by-clause-limit"></a>
Option that controls the number of sorted rows that the query returns\. The LIMIT number must be a positive integer; the maximum value is `2147483647`\.   
LIMIT 0 returns no rows\. You can use this syntax for testing purposes: to check that a query runs \(without displaying any rows\) or to return a column list from a table\. An ORDER BY clause is redundant if you are using LIMIT 0 to return a column list\. The default is LIMIT ALL\. 

OFFSET *start*   <a name="order-by-clause-offset"></a>
Option that specifies to skip the number of rows before *start* before beginning to return rows\. The OFFSET number must be a positive integer; the maximum value is `2147483647`\. When used with the LIMIT option, OFFSET rows are skipped before starting to count the LIMIT rows that are returned\. If the LIMIT option isn't used, the number of rows in the result set is reduced by the number of rows that are skipped\. The rows skipped by an OFFSET clause still have to be scanned, so it might be inefficient to use a large OFFSET value\.

## Usage notes<a name="r_ORDER_BY_usage_notes"></a>

 Note the following expected behavior with ORDER BY clauses: 
+ NULL values are considered "higher" than all other values\. With the default ascending sort order, NULL values sort at the end\. To change this behavior, use the NULLS FIRST option\.
+ When a query doesn't contain an ORDER BY clause, the system returns result sets with no predictable ordering of the rows\. The same query executed twice might return the result set in a different order\. 
+ The LIMIT and OFFSET options can be used without an ORDER BY clause; however, to return a consistent set of rows, use these options in conjunction with ORDER BY\. 
+ In any parallel system like Amazon Redshift, when ORDER BY doesn't produce a unique ordering, the order of the rows is nondeterministic\. That is, if the ORDER BY expression produces duplicate values, the return order of those rows might vary from other systems or from one run of Amazon Redshift to the next\. 
+ Amazon Redshift doesn't support string literals in ORDER BY clauses\.