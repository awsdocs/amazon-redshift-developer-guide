# Expressions<a name="r_expressions"></a>

**Topics**
+ [Simple Expressions](#r_expressions-simple-expressions)
+ [Compound Expressions](r_compound_expressions.md)
+ [Expression Lists](r_expression_lists.md)
+ [Scalar Subqueries](r_scalar_subqueries.md)
+ [Function Expressions](r_function_expressions.md)

An expression is a combination of one or more values, operators, or functions that evaluate to a value\. The data type of an expression is generally that of its components\. 

## Simple Expressions<a name="r_expressions-simple-expressions"></a>

A simple expression is one of the following: 
+ A constant or literal value 
+ A column name or column reference 
+ A scalar function 
+ An aggregate \(set\) function 
+ A window function 
+ A scalar subquery 

Examples of simple expressions include: 

```
5+12
dateid
sales.qtysold * 100
sqrt (4)
max (qtysold)
(select max (qtysold) from sales)
```