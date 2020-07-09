# BIT\_AND function<a name="r_BIT_AND"></a>

The BIT\_AND function runs bit\-wise AND operations on all of the values in a single integer column or expression\. This function aggregates each bit of each binary value that corresponds to each integer value in the expression\.

The BIT\_AND function returns a result of `0` if none of the bits is set to 1 across all of the values\. If one or more bits is set to 1 across all values, the function returns an integer value\. This integer is the number that corresponds to the binary value for the those bits\.

For example, a table contains four integer values in a column: 3, 7, 10, and 22\. These integers are represented in binary form as follows:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_BIT_AND.html)

A BIT\_AND operation on this dataset finds that all bits are set to `1` in the second\-to\-last position only\. The result is a binary value of `00000010`, which represents the integer value `2`\. Therefore, the BIT\_AND function returns `2`\.

## Syntax<a name="r_BIT_AND-synopsis"></a>

```
BIT_AND ( [DISTINCT | ALL] expression )
```

## Arguments<a name="r_BIT_AND-arguments"></a>

 *expression *   
The target column or expression that the function operates on\. This expression must have an INT, INT2, or INT8 data type\. The function returns an equivalent INT, INT2, or INT8 data type\.

DISTINCT \| ALL  
With the argument DISTINCT, the function eliminates all duplicate values for the specified expression before calculating the result\. With the argument ALL, the function retains all duplicate values\. ALL is the default\. For more information, see [DISTINCT support for bit\-wise aggregations](c_bitwise_aggregate_functions.md#distinct-support-for-bit-wise-aggregations)\.

## Examples<a name="r_bit_end_example"></a>

Given that meaningful business information is stored in integer columns, you can use bit\-wise functions to extract and aggregate that information\. The following query applies the BIT\_AND function to the LIKES column in a table called USERLIKES and groups the results by the CITY column\. 

```
select city, bit_and(likes) from userlikes group by city 
order by city;
city          | bit_and
--------------+---------
Los Angeles   |       0
Sacramento    |       0
San Francisco |       0
San Jose      |      64
Santa Barbara |     192
(5 rows)
```

You can interpret these results as follows:
+ The integer value `192` for Santa Barbara translates to the binary value `11000000`\. In other words, all users in this city like sports and theatre, but not all users like any other type of event\.
+ The integer `64` translates to `01000000`\. So, for users in San Jose, the only type of event that they all like is theatre\.
+ The values of `0` for the other three cities indicate that no "likes" are shared by all users in those cities\.