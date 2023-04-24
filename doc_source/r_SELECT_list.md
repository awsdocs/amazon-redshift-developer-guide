# SELECT list<a name="r_SELECT_list"></a>

**Topics**
+ [Syntax](#r_SELECT_list-synopsis)
+ [Parameters](#r_SELECT_list-parameters)
+ [Usage notes](#r_SELECT_list_usage_notes)
+ [Examples](#r_SELECT_list-examples)

The SELECT list names the columns, functions, and expressions that you want the query to return\. The list represents the output of the query\. 

For more information about SQL functions, see [SQL functions reference](c_SQL_functions.md)\. For more information about expressions, see [Conditional expressions](c_conditional_expressions.md)\.

## Syntax<a name="r_SELECT_list-synopsis"></a>

```
SELECT
[ TOP number ]
[ ALL | DISTINCT ] * | expression [ AS column_alias ] [, ...]
```

## Parameters<a name="r_SELECT_list-parameters"></a>

TOP *number*   
TOP takes a positive integer as its argument, which defines the number of rows that are returned to the client\. The behavior with the TOP clause is the same as the behavior with the LIMIT clause\. The number of rows that is returned is fixed, but the set of rows isn't\. To return a consistent set of rows, use TOP or LIMIT in conjunction with an ORDER BY clause\. 

ALL   
A redundant keyword that defines the default behavior if you don't specify DISTINCT\. `SELECT ALL *` means the same as `SELECT *` \(select all rows for all columns and retain duplicates\)\. 

DISTINCT   
Option that eliminates duplicate rows from the result set, based on matching values in one or more columns\. 

\* \(asterisk\)   
Returns the entire contents of the table \(all columns and all rows\)\. 

 *expression*   
An expression formed from one or more columns that exist in the tables referenced by the query\. An expression can contain SQL functions\. For example:   

```
avg(datediff(day, listtime, saletime))
```

AS *column\_alias*   
A temporary name for the column that is used in the final result set\. The AS keyword is optional\. For example:   

```
avg(datediff(day, listtime, saletime)) as avgwait
```
If you don't specify an alias for an expression that isn't a simple column name, the result set applies a default name to that column\.   
The alias is recognized right after it is defined in the target list\. You can use an alias in other expressions defined after it in the same target list\. The following example illustrates this\.   

```
select clicks / impressions as probability, round(100 * probability, 1) as percentage from raw_data;
```
The benefit of the lateral alias reference is you don't need to repeat the aliased expression when building more complex expressions in the same target list\. When Amazon Redshift parses this type of reference, it just inlines the previously defined aliases\. If there is a column with the same name defined in the `FROM` clause as the previously aliased expression, the column in the `FROM` clause takes priority\. For example, in the above query if there is a column named 'probability' in table raw\_data, the 'probability' in the second expression in the target list refers to that column instead of the alias name 'probability'\. 

## Usage notes<a name="r_SELECT_list_usage_notes"></a>

TOP is a SQL extension; it provides an alternative to the LIMIT behavior\. You can't use TOP and LIMIT in the same query\.

## Examples<a name="r_SELECT_list-examples"></a>

The following example returns 10 rows from the SALES table\. Though the query uses the TOP clause, it still returns an unpredictable set of rows because no ORDER BY clause is specified,

```
select top 10 *
from sales;
```

The following query is functionally equivalent, but uses a LIMIT clause instead of a TOP clause:

```
select *
from sales
limit 10;
```

The following example returns the first 10 rows from the SALES table using the TOP clause, ordered by the QTYSOLD column in descending order\.

```
select top 10 qtysold, sellerid
from sales
order by qtysold desc, sellerid;

qtysold | sellerid
--------+----------
8 |      518
8 |      520
8 |      574
8 |      718
8 |      868
8 |     2663
8 |     3396
8 |     3726
8 |     5250
8 |     6216
(10 rows)
```

The following example returns the first two QTYSOLD and SELLERID values from the SALES table, ordered by the QTYSOLD column:

```
select top 2 qtysold, sellerid
from sales
order by qtysold desc, sellerid;

qtysold | sellerid
--------+----------
8 |      518
8 |      520
(2 rows)
```

The following example shows the list of distinct category groups from the CATEGORY table:

```
select distinct catgroup from category
order by 1;

catgroup
----------
Concerts
Shows
Sports
(3 rows)

--the same query, run without distinct
select catgroup from category
order by 1;

catgroup
----------
Concerts
Concerts
Concerts
Shows
Shows
Shows
Sports
Sports
Sports
Sports
Sports
(11 rows)
```

The following example returns the distinct set of week numbers for December 2008\. Without the DISTINCT clause, the statement would return 31 rows, or one for each day of the month\.

```
select distinct week, month, year
from date
where month='DEC' and year=2008
order by 1, 2, 3;

week | month | year
-----+-------+------
49 | DEC   | 2008
50 | DEC   | 2008
51 | DEC   | 2008
52 | DEC   | 2008
53 | DEC   | 2008
(5 rows)
```

