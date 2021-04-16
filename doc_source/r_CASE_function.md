# CASE expression<a name="r_CASE_function"></a>

## Syntax<a name="r_CASE_function-syntax"></a>

The CASE expression is a conditional expression, similar to if/then/else statements found in other languages\. CASE is used to specify a result when there are multiple conditions\.

There are two types of CASE expressions: simple and searched\.

 In simple CASE expressions, an expression is compared with a value\. When a match is found, the specified action in the THEN clause is applied\. If no match is found, the action in the ELSE clause is applied\.

In searched CASE expressions, each CASE is evaluated based on a Boolean expression, and the CASE statement returns the first matching CASE\. If no matching CASEs are found among the WHEN clauses, the action in the ELSE clause is returned\.

Simple CASE statement used to match conditions:

```
CASE expression
WHEN value THEN result
[WHEN...]
[ELSE result]
END
```

Searched CASE statement used to evaluate each condition:

```
CASE
WHEN boolean condition THEN result
[WHEN ...]
[ELSE result]
END
```

## Arguments<a name="r_CASE_function-arguments"></a>

 *expression*   
A column name or any valid expression\.

 *value*   
Value that the expression is compared with, such as a numeric constant or a character string\.

 *result*   
The target value or expression that is returned when an expression or Boolean condition is evaluated\.

 *Boolean condition*   
A Boolean condition is valid or true when the value is equal to the constant\. When true, the result specified following the THEN clause is returned\. If a condition is false, the result following the ELSE clause is returned\. If the ELSE clause is omitted and no condition matches, the result is null\.

## Examples<a name="r_CASE_function-examples"></a>

Use a simple CASE expression to replace `New York City` with `Big Apple` in a query against the VENUE table\. Replace all other city names with `other`\.

```
select venuecity,
case venuecity
when 'New York City'
then 'Big Apple' else 'other'
end from venue
order by venueid desc;

venuecity        |   case
-----------------+-----------
Los Angeles      | other
New York City    | Big Apple
San Francisco    | other
Baltimore        | other
...
(202 rows)
```

Use a searched CASE expression to assign group numbers based on the PRICEPAID value for individual ticket sales:

```
select pricepaid,
case when pricepaid <10000 then 'group 1'
when pricepaid >10000 then 'group 2'
else 'group 3'
end from sales
order by 1 desc;

pricepaid |  case
-----------+---------
12624.00 | group 2
10000.00 | group 3
10000.00 | group 3
9996.00 | group 1
9988.00 | group 1
...
(172456 rows)
```