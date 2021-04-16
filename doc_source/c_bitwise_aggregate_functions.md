# Bit\-wise aggregate functions<a name="c_bitwise_aggregate_functions"></a>

Bit\-wise aggregate functions compute bit operations to perform aggregation of integer columns and columns that can be converted or rounded to integer values\.

**Topics**
+ [Using NULLs in bit\-wise aggregations](#c_bitwise_aggregate_functions-nulls-in-bit-wise-aggregations)
+ [DISTINCT support for bit\-wise aggregations](#distinct-support-for-bit-wise-aggregations)
+ [Overview examples for bit\-wise functions](#r_bitwise_example)
+ [BIT\_AND function](r_BIT_AND.md)
+ [BIT\_OR function](r_BIT_OR.md)
+ [BOOL\_AND function](r_BOOL_AND.md)
+ [BOOL\_OR function](r_BOOL_OR.md)

## Using NULLs in bit\-wise aggregations<a name="c_bitwise_aggregate_functions-nulls-in-bit-wise-aggregations"></a>

When you apply a bit\-wise function to a column that is nullable, any NULL values are eliminated before the function result is calculated\. If no rows qualify for aggregation, the bit\-wise function returns NULL\. The same behavior applies to regular aggregate functions\. Following is an example\.

```
select sum(venueseats), bit_and(venueseats) from venue
where venueseats is null;

sum  | bit_and
------+---------
null |    null
(1 row)
```

## DISTINCT support for bit\-wise aggregations<a name="distinct-support-for-bit-wise-aggregations"></a>

As other aggregate functions do, bit\-wise functions support the DISTINCT keyword\. 

However, using DISTINCT with these functions has no impact on the results\. The first instance of a value is sufficient to satisfy bit\-wise AND or OR operations\. It makes no difference if duplicate values are present in the expression being evaluated\. 

Because the DISTINCT processing is likely to incur some query execution overhead, we recommend that you don't use DISTINCT with bit\-wise functions\.

## Overview examples for bit\-wise functions<a name="r_bitwise_example"></a>

Following, you can find some overview examples demonstrating how to work with the bit\-wise functions\. You can also find specific code examples with each function description\.

Examples for the bit\-wise functions are based on the TICKIT sample database\. The USERS table in the TICKIT sample database contains several Boolean columns that indicate whether each user is known to like different types of events, such as sports, theatre, opera, and so on\. An example follows\.

```
select userid, username, lastname, city, state, 
likesports, liketheatre
from users limit 10;

userid | username | lastname  |     city     | state | likesports | liketheatre
-------+----------+-----------+--------------+-------+------------+-------------
1 | JSG99FHE | Taylor    | Kent         | WA    | t          | t
9 | MSD36KVR | Watkins   | Port Orford  | MD    | t          | f
```

Assume that a new version of the USERS table is built in a different way\. In this new version, a single integer column that defines \(in binary form\) eight types of events that each user likes or dislikes\. In this design, each bit position represents a type of event\. A user who likes all eight types has all eight bits set to 1 \(as in the first row of the following table\)\. A user who doesn't like any of these events has all eight bits set to 0 \(see the second row\)\. A user who likes only sports and jazz is represented in the third row following\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/c_bitwise_aggregate_functions.html)

In the database table, these binary values can be stored in a single LIKES column as integers, as shown following\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/c_bitwise_aggregate_functions.html)