# BIT\_OR function<a name="r_BIT_OR"></a>

The BIT\_OR function runs bit\-wise OR operations on all of the values in a single integer column or expression\. This function aggregates each bit of each binary value that corresponds to each integer value in the expression\.

For example, suppose that your table contains four integer values in a column: 3, 7, 10, and 22\. These integers are represented in binary form as follows\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_BIT_OR.html)

If you apply the BIT\_OR function to the set of integer values, the operation looks for any value in which a `1` is found in each position\. In this case, a `1` exists in the last five positions for at least one of the values, yielding a binary result of `00011111`; therefore, the function returns `31` \(or `16 + 8 + 4 + 2 + 1`\)\.

## Syntax<a name="r_BIT_OR-synopsis"></a>

```
BIT_OR ( [DISTINCT | ALL] expression )
```

## Arguments<a name="r_BIT_OR-arguments"></a>

 *expression *   
The target column or expression that the function operates on\. This expression must have an INT, INT2, or INT8 data type\. The function returns an equivalent INT, INT2, or INT8 data type\.

DISTINCT \| ALL  
With the argument DISTINCT, the function eliminates all duplicate values for the specified expression before calculating the result\. With the argument ALL, the function retains all duplicate values\. ALL is the default\. For more information, see [DISTINCT support for bit\-wise aggregations](c_bitwise_aggregate_functions.md#distinct-support-for-bit-wise-aggregations)\.

## Example<a name="r_bit_or_example"></a>

The following query applies the BIT\_OR function to the LIKES column in a table called USERLIKES and groups the results by the CITY column\.

```
select city, bit_or(likes) from userlikes group by city
order by city;
city          | bit_or
--------------+--------
Los Angeles   |    127
Sacramento    |    255
San Francisco |    255
San Jose      |    255
Santa Barbara |    255
(5 rows)
```

For four of the cities listed, all of the event types are liked by at least one user \(`255=11111111`\)\. For Los Angeles, all of the event types except sports are liked by at least one user \(`127=01111111`\)\.