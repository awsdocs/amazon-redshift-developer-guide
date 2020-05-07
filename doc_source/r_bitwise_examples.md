# Bit\-Wise function examples<a name="r_bitwise_examples"></a>

The USERS table in the TICKIT sample database contains several Boolean columns that indicate whether each user is known to like different types of events, such as sports, theatre, opera, and so on\. For example:

```
select userid, username, lastname, city, state, 
likesports, liketheatre
from users limit 10;

userid | username | lastname  |     city     | state | likesports | liketheatre
--------+----------+-----------+--------------+-------+------------+-------------
1 | JSG99FHE | Taylor    | Kent         | WA    | t          | t
9 | MSD36KVR | Watkins   | Port Orford  | MD    | t          | f
```

Assume that a new version of the USERS table is built in a different way, with a single integer column that defines \(in binary form\) eight types of events that each user likes or dislikes\. In this design, each bit position represents a type of event, and a user who likes all eight types has all eight bits set to 1 \(as in the first row of the following table\)\. A user who does not like any of these events has all eight bits set to 0 \(see second row\)\. A user who likes only sports and jazz is represented in the third row:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_bitwise_examples.html)

In the database table, these binary values could be stored in a single LIKES column as integers:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_bitwise_examples.html)

## BIT\_AND and BIT\_OR examples<a name="r_bitwise_examples-bit_and-and-bit_or-examples"></a>

Given that meaningful business information is stored in integer columns, you can use bit\-wise functions to extract and aggregate that information\. The following query applies the BIT\_AND function to the LIKES column in a table called USERLIKES and groups the results by the CITY column\. 

```
select city, bit_and(likes) from userlikes group by city 
order by city;
city      | bit_and
---------------+---------
Los Angeles   |       0
Sacramento    |       0
San Francisco |       0
San Jose      |      64
Santa Barbara |     192
(5 rows)
```

These results can be interpreted as follows:
+ The integer value `192` for Santa Barbara translates to the binary value `11000000`\. In other words, all users in this city like sports and theatre, but not all users like any other type of event\.
+ The integer `64` translates to `01000000`, so for users in San Jose, the only type of event that they all like is theatre\.
+ The values of `0` for the other three cities indicate that no "likes" are shared by all users in those cities\.

If you apply the BIT\_OR function to the same data, the results are as follows:

```
select city, bit_or(likes) from userlikes group by city
order by city;
city      | bit_or
---------------+--------
Los Angeles   |    127
Sacramento    |    255
San Francisco |    255
San Jose      |    255
Santa Barbara |    255
(5 rows)
```

For four of the cities listed, all of the event types are liked by at least one user \(`255=11111111`\)\. For Los Angeles, all of the event types except sports are liked by at least one user \(`127=01111111`\)\.

## BOOL\_AND and BOOL\_OR examples<a name="r_bitwise_examples-bool_and-and-bool_or-examples"></a>

You can use the Boolean functions against either Boolean expressions or integer expressions\. For example, the following query return results from the standard USERS table in the TICKIT database, which has several Boolean columns\.

The BOOL\_OR function returns `true` for all five rows\. At least one user in each of those states likes sports\. The BOOL\_AND function returns `false` for all five rows\. Not all users in each of those states likes sports\.

```
select state, bool_or(likesports), bool_and(likesports) from users 
group by state order by state limit 5;

state | bool_or | bool_and
-------+--------------------
AB    | t       | f
AK    | t       | f
AL    | t       | f
AZ    | t       | f
BC    | t       | f
(5 rows)
```