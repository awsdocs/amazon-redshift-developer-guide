# UNION, INTERSECT, and EXCEPT<a name="r_UNION"></a>

**Topics**
+ [Syntax](#r_UNION-synopsis)
+ [Parameters](#r_UNION-parameters)
+ [Order of evaluation for set operators](#r_UNION-order-of-evaluation-for-set-operators)
+ [Usage notes](#r_UNION-usage-notes)
+ [Example UNION queries](c_example_union_query.md)
+ [Example UNION ALL query](c_example_unionall_query.md)
+ [Example INTERSECT queries](c_example_intersect_query.md)
+ [Example EXCEPT query](c_Example_MINUS_query.md)

The UNION, INTERSECT, and EXCEPT *set operators* are used to compare and merge the results of two separate query expressions\. For example, if you want to know which users of a website are both buyers and sellers but their user names are stored in separate columns or tables, you can find the *intersection* of these two types of users\. If you want to know which website users are buyers but not sellers, you can use the EXCEPT operator to find the *difference* between the two lists of users\. If you want to build a list of all users, regardless of role, you can use the UNION operator\.

## Syntax<a name="r_UNION-synopsis"></a>

```
query
{ UNION [ ALL ] | INTERSECT | EXCEPT | MINUS }
query
```

## Parameters<a name="r_UNION-parameters"></a>

 *query*   
A query expression that corresponds, in the form of its select list, to a second query expression that follows the UNION, INTERSECT, or EXCEPT operator\. The two expressions must contain the same number of output columns with compatible data types; otherwise, the two result sets can't be compared and merged\. Set operations don't allow implicit conversion between different categories of data types; for more information, see [Type compatibility and conversion](c_Supported_data_types.md#r_Type_conversion)\.  
You can build queries that contain an unlimited number of query expressions and link them with UNION, INTERSECT, and EXCEPT operators in any combination\. For example, the following query structure is valid, assuming that the tables T1, T2, and T3 contain compatible sets of columns:   

```
select * from t1
union
select * from t2
except
select * from t3
order by c1;
```

UNION   
Set operation that returns rows from two query expressions, regardless of whether the rows derive from one or both expressions\.

INTERSECT   
Set operation that returns rows that derive from two query expressions\. Rows that aren't returned by both expressions are discarded\.

EXCEPT \| MINUS   
Set operation that returns rows that derive from one of two query expressions\. To qualify for the result, rows must exist in the first result table but not the second\. MINUS and EXCEPT are exact synonyms\. 

ALL   
The ALL keyword retains any duplicate rows that are produced by UNION\. The default behavior when the ALL keyword isn't used is to discard these duplicates\. INTERSECT ALL, EXCEPT ALL, and MINUS ALL aren't supported\.

## Order of evaluation for set operators<a name="r_UNION-order-of-evaluation-for-set-operators"></a>

The UNION and EXCEPT set operators are left\-associative\. If parentheses aren't specified to influence the order of precedence, a combination of these set operators is evaluated from left to right\. For example, in the following query, the UNION of T1 and T2 is evaluated first, then the EXCEPT operation is performed on the UNION result: 

```
select * from t1
union
select * from t2
except
select * from t3
order by c1;
```

The INTERSECT operator takes precedence over the UNION and EXCEPT operators when a combination of operators is used in the same query\. For example, the following query evaluates the intersection of T2 and T3, then union the result with T1: 

```
select * from t1
union
select * from t2
intersect
select * from t3
order by c1;
```

By adding parentheses, you can enforce a different order of evaluation\. In the following case, the result of the union of T1 and T2 is intersected with T3, and the query is likely to produce a different result\. 

```
(select * from t1
union
select * from t2)
intersect
(select * from t3)
order by c1;
```

## Usage notes<a name="r_UNION-usage-notes"></a>
+ The column names returned in the result of a set operation query are the column names \(or aliases\) from the tables in the first query expression\. Because these column names are potentially misleading, in that the values in the column derive from tables on either side of the set operator, you might want to provide meaningful aliases for the result set\.
+ A query expression that precedes a set operator should not contain an ORDER BY clause\. An ORDER BY clause produces meaningful sorted results only when it is used at the end of a query that contains set operators\. In this case, the ORDER BY clause applies to the final results of all of the set operations\. The outermost query can also contain standard LIMIT and OFFSET clauses\. 
+ The LIMIT and OFFSET clauses aren't supported as a means of restricting the number of rows returned by an intermediate result of a set operation\. For example, the following query returns an error:

  ```
  (select listid from listing
  limit 10)
  intersect
  select listid from sales;
  ERROR:  LIMIT may not be used within input to set operations.
  ```
+ When set operator queries return decimal results, the corresponding result columns are promoted to return the same precision and scale\. For example, in the following query, where T1\.REVENUE is a DECIMAL\(10,2\) column and T2\.REVENUE is a DECIMAL\(8,4\) column, the decimal result is promoted to DECIMAL\(12,4\): 

  ```
  select t1.revenue union select t2.revenue;
  ```

  The scale is `4` because that is the maximum scale of the two columns\. The precision is `12` because T1\.REVENUE requires 8 digits to the left of the decimal point \(12 \- 4 = 8\)\. This type promotion ensures that all values from both sides of the UNION fit in the result\. For 64\-bit values, the maximum result precision is 19 and the maximum result scale is 18\. For 128\-bit values, the maximum result precision is 38 and the maximum result scale is 37\.

  If the resulting data type exceeds Amazon Redshift precision and scale limits, the query returns an error\.
+ For set operations, two rows are treated as identical if, for each corresponding pair of columns, the two data values are either *equal* or *both NULL*\. For example, if tables T1 and T2 both contain one column and one row, and that row is NULL in both tables, an INTERSECT operation over those tables returns that row\.