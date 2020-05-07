# Bit\-wise aggregate functions<a name="c_bitwise_aggregate_functions"></a>

**Topics**
+ [BIT\_AND and BIT\_OR](#c_bitwise_aggregate_functions-bit_and-and-bit_or)
+ [BOOL\_AND and BOOL\_OR](#c_bitwise_aggregate_functions-bool_and-and-bool_or)
+ [NULLs in bit\-wise aggregations](#c_bitwise_aggregate_functions-nulls-in-bit-wise-aggregations)
+ [DISTINCT support for bit\-wise aggregations](#distinct-support-for-bit-wise-aggregations)
+ [BIT\_AND function](r_BIT_AND.md)
+ [BIT\_OR function](r_BIT_OR.md)
+ [BOOL\_AND function](r_BOOL_AND.md)
+ [BOOL\_OR function](r_BOOL_OR.md)
+ [Bit\-Wise function examples](r_bitwise_examples.md)

Amazon Redshift supports the following bit\-wise aggregate functions:
+ BIT\_AND
+ BIT\_OR
+ BOOL\_AND
+ BOOL\_OR

## BIT\_AND and BIT\_OR<a name="c_bitwise_aggregate_functions-bit_and-and-bit_or"></a>

The BIT\_AND and BIT\_OR functions run bit\-wise AND and OR operations on all of the values in a single integer column or expression\. These functions aggregate each bit of each binary value that corresponds to each integer value in the expression\.

The BIT\_AND function returns a result of `0` if none of the bits is set to 1 across all of the values\. If one or more bits is set to 1 across all values, the function returns an integer value\. This integer is the number that corresponds to the binary value for the those bits\.

For example, a table contains four integer values in a column: 3, 7, 10, and 22\. These integers are represented in binary form as follows:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/c_bitwise_aggregate_functions.html)

A BIT\_AND operation on this dataset finds that all bits are set to `1` in the second\-to\-last position only\. The result is a binary value of `00000010`, which represents the integer value `2`; therefore, the BIT\_AND function returns `2`\.

If you apply the BIT\_OR function to the same set of integer values, the operation looks for *any* value in which a `1` is found in each position\. In this case, a `1` exists in the last five positions for at least one of the values, yielding a binary result of `00011111`; therefore, the function returns `31` \(or `16 + 8 + 4 + 2 + 1`\)\.

## BOOL\_AND and BOOL\_OR<a name="c_bitwise_aggregate_functions-bool_and-and-bool_or"></a>

The BOOL\_AND and BOOL\_OR functions operate on a single Boolean or integer column or expression\. These functions apply similar logic to the BIT\_AND and BIT\_OR functions\. For these functions, the return type is a Boolean value \(`true` or `false`\):
+ If all values in a set are true, the BOOL\_AND function returns `true` \(`t`\)\. If any value is false, the function returns `false` \(`f`\)\.
+ If any value in a set is `true`, the BOOL\_OR function returns `true` \(`t`\)\. If no value in a set is `true`, the function returns `false` \(`f`\)\.

## NULLs in bit\-wise aggregations<a name="c_bitwise_aggregate_functions-nulls-in-bit-wise-aggregations"></a>

When a bit\-wise function is applied to a column that is nullable, any NULL values are eliminated before the function result is calculated\. If no rows qualify for aggregation, the bit\-wise function returns NULL\. The same behavior applies to regular aggregate functions\. For example:

```
select sum(venueseats), bit_and(venueseats) from venue
where venueseats is null;

sum  | bit_and
------+---------
null |    null
(1 row)
```

## DISTINCT support for bit\-wise aggregations<a name="distinct-support-for-bit-wise-aggregations"></a>

Like other aggregate functions, bit\-wise functions support the DISTINCT keyword\. However, using DISTINCT with these functions has no impact on the results\. The first instance of a value is sufficient to satisfy bitwise AND or OR operations, and it makes no difference if duplicate values are present in the expression being evaluated\. Because the DISTINCT processing is likely to incur some query execution overhead, do not use DISTINCT with these functions\.