# Operators and functions<a name="operators-functions"></a>

Amazon Redshift provides the following function support of SUPER operators and functions\.

## Arithmetic operators<a name="arithmetic-opertors"></a>

SUPER values support all basic arithmetic operators \+, \-, \*, /, % using dynamic typing\. The resultant type of the operation remains as SUPER\. For all operators, except for the binary operator \+, the input operands must be numbers\. Otherwise, Amazon Redshift returns null\. The distinction between decimals and floating\-point values is retained when Amazon Redshift runs these operators and the dynamic type doesn't change\. However, decimal scale changes when you use multiplications and divisions\. Arithmetic overflows still cause query errors, they aren't changed to null\. Binary operator \+ performs addition if the inputs are numbers or concatenation if the inputs are string\. If one operand is a string and the other operand is a number, the result is null\. Unary prefix operators \+ and \- returns null if the SUPER value is not a number as shown in the following example:

```
SELECT (c_orders[0]. o_orderkey + 0.5) * c_orders[0]. o_orderkey / 10 AS math FROM customer_orders_lineitem;
            math
----------------------------
 1757958232200.1500
(1 row)
```

Dynamic typing allows decimal values in SUPER to have different scales\. Amazon Redshift treats decimal values as if they are different static types and allows all mathematical operations\. Amazon Redshift computes the resulting scale dynamically based on the scales of the operands\. If one of the operands is a floating\-point number, then Amazon Redshift promotes the other operand to a floating\-point number and generates the result as a floating\-point number\.

## Arithmetic functions<a name="arithmetic-functions"></a>

Amazon Redshift supports the following arithmetic functions for SUPER columns\. They return null if the input isn't a number:
+ FLOOR\. For more information, see [FLOOR function](r_FLOOR.md)\.
+ CEIL and CEILING\. For more information, see [CEILING \(or CEIL\) function](r_CEILING_FLOOR.md)\.
+ ROUND\. For more information, see [ROUND function](r_ROUND.md)\.
+ ABS\. For more information, see [ABS function](r_ABS.md)\.

The following example uses arithmetic functions to query data:

```
SELECT x, FLOOR(x), CEIL(x), ROUND(x)
FROM (
    SELECT (c_orders[0]. o_orderkey + 0.5) * c_orders[0].o_orderkey / 10 AS x
    FROM customer_orders_lineitem
    );

         x          |     floor     |     ceil      |     round
--------------------+---------------+---------------+---------------
 1389636795898.0500  | 1389636795898  | 1389636795899  | 1389636795898
```

The ABS function retains the scale of the input decimal while FLOOR, CEIL\. The ROUND eliminates the scale of the input decimal\.

## Array functions<a name="array-functions"></a>

Amazon Redshift supports the following array composition and utility functions array, array\_concat, subarray, array\_flatten, get\_array\_length, and split\_to\_array\.

You can construct SUPER arrays from values in Amazon Redshift data types using the ARRAY function, including other SUPER values\. The following example uses the variadic function ARRAY: 

```
SELECT ARRAY(1, c.c_custkey, NULL, c.c_name, 'abc') FROM customer_orders_lineitem c; 
                               array
 -------------------------------------------------------
[1,8401,null,""Customer#000008401"",""abc""]
[1,9452,null,""Customer#000009452"",""abc""]
[1,9451,null,""Customer#000009451"",""abc""]
[1,8251,null,""Customer#000008251"",""abc""]
[1,5851,null,""Customer#000005851"",""abc""] 
(5 rows)
```

The following example uses array concatenation with the ARRAY\_CONCAT function:

```
SELECT ARRAY_CONCAT(JSON_PARSE('[10001,10002]'),JSON_PARSE('[10003,10004]'));

             array_concat
------------------------------------
 [10001,10002,10003,10004]
(1 row)
```

The following example uses array manipulation with the SUBARRAY function which returns a subset of the input array\.

```
SELECT SUBARRAY(ARRAY('a', 'b', 'c', 'd', 'e', 'f'), 2, 3);

   subarray
---------------
 ["c","d","e"]
(1 row))
```

The following example merges multiple levels of arrays into a single array using ARRAY\_FLATTEN:

```
SELECT x, ARRAY_FLATTEN(x) FROM (SELECT ARRAY(1, ARRAY(2, ARRAY(3, ARRAY()))) AS x);

     x           | array_flatten
 ----------------+---------------
 [1,[2,[3,[]]]]  | [1,2,3]
(1 row)
```

Array functions ARRAY\_CONCAT and ARRAY\_FLATTEN use dynamic typing rules\. They return a null instead of an error if the input isn't an array\. The GET\_ARRAY\_LENGTH function returns the length of a SUPER array given an object or array path\. 

```
SELECT c_name
FROM customer_orders_lineitem
WHERE GET_ARRAY_LENGTH(c_orders) = (
    SELECT MAX(GET_ARRAY_LENGTH(c_orders))
    FROM customer_orders_lineitem
    );
```

The following example splits a string to an array of strings using SPLIT\_TO\_ARRAY\. The function uses a delimiter as an optional parameter\. If no delimiter is absent, then the default is a comma\.

```
SELECT SPLIT_TO_ARRAY('12|345|6789', '|');

   split_to_array
---------------------
 ["12","345","6789"]
(1 row)
```