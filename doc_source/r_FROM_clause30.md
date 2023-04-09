# FROM clause<a name="r_FROM_clause30"></a>

The FROM clause in a query lists the table references \(tables, views, and subqueries\) that data is selected from\. If multiple table references are listed, the tables must be joined, using appropriate syntax in either the FROM clause or the WHERE clause\. If no join criteria are specified, the system processes the query as a cross\-join \(Cartesian product\)\. 

**Topics**
+ [Syntax](#r_FROM_clause30-synopsis)
+ [Parameters](#r_FROM_clause30-parameters)
+ [Usage notes](#r_FROM_clause_usage_notes)
+ [PIVOT and UNPIVOT examples](r_FROM_clause-pivot-unpivot-examples.md)
+ [JOIN examples](r_Join_examples.md)

## Syntax<a name="r_FROM_clause30-synopsis"></a>

```
FROM table_reference [, ...]
```

where *table\_reference* is one of the following: 

```
with_subquery_table_name [ [ AS ] alias [ ( column_alias [, ...] ) ] ]
table_name [ * ] [ [ AS ] alias [ ( column_alias [, ...] ) ] ]
( subquery ) [ AS ] alias [ ( column_alias [, ...] ) ]
table_reference [ NATURAL ] join_type table_reference
[ ON join_condition | USING ( join_column [, ...] ) ]
table_reference [ PIVOT ] ( aggregate(expr) [ [ AS ] aggregate_alias ]
 FOR column_name IN ( expression [ [ AS ] in_alias [, ...]
   )
) [ [ AS ] in_alias [, ...] ) ] ]
table_reference [ UNPIVOT [INCLUDE NULLS | EXCLUDE NULLS] ] ( value_column_name FOR name_column_name
               IN ( column_reference [ [ AS ] in_alias [, ...]
   )
) [ [ AS ] alias [(column_alias, ...] ) ] ]
```

## Parameters<a name="r_FROM_clause30-parameters"></a>

 *with\_subquery\_table\_name*   
A table defined by a subquery in the [WITH clause](r_WITH_clause.md)\. 

 *table\_name*   
Name of a table or view\. 

 *alias*   
Temporary alternative name for a table or view\. An alias must be supplied for a table derived from a subquery\. In other table references, aliases are optional\. The AS keyword is always optional\. Table aliases provide a convenient shortcut for identifying tables in other parts of a query, such as the WHERE clause\. For example:   

```
select * from sales s, listing l
where s.listid=l.listid
```

 *column\_alias*   
Temporary alternative name for a column in a table or view\. 

 *subquery*   
A query expression that evaluates to a table\. The table exists only for the duration of the query and is typically given a name or *alias*\. However, an alias isn't required\. You can also define column names for tables that derive from subqueries\. Naming column aliases is important when you want to join the results of subqueries to other tables and when you want to select or constrain those columns elsewhere in the query\.   
A subquery may contain an ORDER BY clause, but this clause may have no effect if a LIMIT or OFFSET clause isn't also specified\. 

PIVOT  
Rotates output from rows to columns, for the purpose of representing tabular data in a format that is easy to read\. Output is represented horizontally across multiple columns\. PIVOT is similar to a GROUP BY query with an aggregation, using an aggregate expression to specify an output format\. However, in contrast to GROUP BY, the results are returned in columns instead of rows\.  
For examples that show how to query with PIVOT and UNPIVOT, see [PIVOT and UNPIVOT examples](r_FROM_clause-pivot-unpivot-examples.md)\.

UNPIVOT  
The UNPIVOT operator transforms result columns, from an input table or query results, into rows, to make the output easier to read\. UNPIVOT combines the data of its input columns into two result columns: a name column and a value column\. The name column contains column names from the input, as row entries\. The value column contains values from the input columns, such as results of an aggregation\. For example, the counts of items in various categories\.  
For examples that show how to query semistructured data, such as JSON\-formatted data, using UNPIVOT, see [Object unpivoting](query-super.md#unpivoting)\.

NATURAL   
Defines a join that automatically uses all pairs of identically named columns in the two tables as the joining columns\. No explicit join condition is required\. For example, if the CATEGORY and EVENT tables both have columns named CATID, a natural join of those tables is a join over their CATID columns\.   
If a NATURAL join is specified but no identically named pairs of columns exist in the tables to be joined, the query defaults to a cross\-join\. 

 *join\_type*   
Specify one of the following types of join:   
+ \[INNER\] JOIN 
+ LEFT \[OUTER\] JOIN 
+ RIGHT \[OUTER\] JOIN 
+ FULL \[OUTER\] JOIN 
+ CROSS JOIN 
Cross\-joins are unqualified joins; they return the Cartesian product of the two tables\.   
Inner and outer joins are qualified joins\. They are qualified either implicitly \(in natural joins\); with the ON or USING syntax in the FROM clause; or with a WHERE clause condition\.   
An inner join returns matching rows only, based on the join condition or list of joining columns\. An outer join returns all of the rows that the equivalent inner join would return plus non\-matching rows from the "left" table, "right" table, or both tables\. The left table is the first\-listed table, and the right table is the second\-listed table\. The non\-matching rows contain NULL values to fill the gaps in the output columns\. 

ON *join\_condition*   
Type of join specification where the joining columns are stated as a condition that follows the ON keyword\. For example:   

```
sales join listing
on sales.listid=listing.listid and sales.eventid=listing.eventid
```

USING \( *join\_column* \[, \.\.\.\] \)   
Type of join specification where the joining columns are listed in parentheses\. If multiple joining columns are specified, they are delimited by commas\. The USING keyword must precede the list\. For example:   

```
sales join listing
using (listid,eventid)
```

## Usage notes<a name="r_FROM_clause_usage_notes"></a>

Joining columns must have comparable data types\. 

A NATURAL or USING join retains only one of each pair of joining columns in the intermediate result set\. 

A join with the ON syntax retains both joining columns in its intermediate result set\. 

See also [WITH clause](r_WITH_clause.md)\. 