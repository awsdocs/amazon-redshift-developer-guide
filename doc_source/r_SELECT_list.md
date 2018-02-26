# SELECT List<a name="r_SELECT_list"></a>


+ [Syntax](#r_SELECT_list-synopsis)
+ [Parameters](#r_SELECT_list-parameters)
+ [Usage Notes](#r_SELECT_list_usage_notes)
+ [Examples with TOP](r_Examples_with_TOP.md)
+ [SELECT DISTINCT Examples](r_DISTINCT_examples.md)

The SELECT list names the columns, functions, and expressions that you want the query to return\. The list represents the output of the query\. 

## Syntax<a name="r_SELECT_list-synopsis"></a>

```
SELECT
[ TOP number ]
[ ALL | DISTINCT ] * | expression [ AS column_alias ] [, ...]
```

## Parameters<a name="r_SELECT_list-parameters"></a>

TOP *number*   
TOP takes a positive integer as its argument, which defines the number of rows that are returned to the client\. The behavior with the TOP clause is the same as the behavior with the LIMIT clause\. The number of rows that is returned is fixed, but the set of rows is not; to return a consistent set of rows, use TOP or LIMIT in conjunction with an ORDER BY clause\. 

ALL   
A redundant keyword that defines the default behavior if you do not specify DISTINCT\. `SELECT ALL *` means the same as `SELECT *` \(select all rows for all columns and retain duplicates\)\. 

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
A temporary name for the column that will be used in the final result set\. The AS keyword is optional\. For example:   

```
avg(datediff(day, listtime, saletime)) as avgwait
```
If you do not specify an alias for an expression that is not a simple column name, the result set applies a default name to that column\.   
The alias is not recognized until the entire target list has been parsed, which means that you cannot refer to the alias elsewhere within the target list\. For example, the following statement will fail:   

```
select (qtysold + 1) as q, sum(q) from sales group by 1;
ERROR:  column "q" does not exist
```
You must use the same expression that was aliased to `q`:   

```
select (qtysold + 1) as q, sum(qtysold + 1) from sales group by 1;
q |  sum
---+--------
8 |    368
...
```

## Usage Notes<a name="r_SELECT_list_usage_notes"></a>

TOP is a SQL extension; it provides an alternative to the LIMIT behavior\. You cannot use TOP and LIMIT in the same query\.