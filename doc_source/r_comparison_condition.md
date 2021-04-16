# Comparison condition<a name="r_comparison_condition"></a>

Comparison conditions state logical relationships between two values\. All comparison conditions are binary operators with a Boolean return type\. Amazon Redshift supports the comparison operators described in the following table:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_comparison_condition.html)

## Usage notes<a name="r_comparison_condition_usage_notes"></a>

= ANY \| SOME   
The ANY and SOME keywords are synonymous with the *IN* condition, and return true if the comparison is true for at least one value returned by a subquery that returns one or more values\. Amazon Redshift supports only the = \(equals\) condition for ANY and SOME\. Inequality conditions are not supported\.  
The ALL predicate is not supported\.

<> ALL  
The ALL keyword is synonymous with NOT IN \(see [IN condition](r_in_condition.md) condition\) and returns true if the expression is not included in the results of the subquery\. Amazon Redshift supports only the <> or \!= \(not equals\) condition for ALL\. Other comparison conditions are not supported\.

IS TRUE/FALSE/UNKNOWN  
Non\-zero values equate to TRUE, 0 equates to FALSE, and null equates to UNKNOWN\. See the [Boolean typeHLLSKETCH type](r_Boolean_type.md) data type\.

## Examples<a name="r_comparison_condition-examples"></a>

Here are some simple examples of comparison conditions: 

```
a = 5
a < b
min(x) >= 5
qtysold = any (select qtysold from sales where dateid = 1882
```

The following query returns venues with more than 10000 seats from the VENUE table: 

```
select venueid, venuename, venueseats from venue
where venueseats > 10000
order by venueseats desc;

venueid |           venuename            | venueseats
---------+--------------------------------+------------
83 | FedExField                     |      91704
 6 | New York Giants Stadium        |      80242
79 | Arrowhead Stadium              |      79451
78 | INVESCO Field                  |      76125
69 | Dolphin Stadium                |      74916
67 | Ralph Wilson Stadium           |      73967
76 | Jacksonville Municipal Stadium |      73800
89 | Bank of America Stadium        |      73298
72 | Cleveland Browns Stadium       |      73200
86 | Lambeau Field                  |      72922
...
(57 rows)
```

This example selects the users \(USERID\) from the USERS table who like rock music:

```
select userid from users where likerock = 't' order by 1 limit 5;

userid
--------
3
5
6
13
16
(5 rows)
```

This example selects the users \(USERID\) from the USERS table where it is unknown whether they like rock music:

```
select firstname, lastname, likerock
from users
where likerock is unknown
order by userid limit 10;

firstname | lastname | likerock
----------+----------+----------
Rafael    | Taylor   |
Vladimir  | Humphrey |
Barry     | Roy      |
Tamekah   | Juarez   |
Mufutau   | Watkins  |
Naida     | Calderon |
Anika     | Huff     |
Bruce     | Beck     |
Mallory   | Farrell  |
Scarlett  | Mayer    |
(10 rows
```

## Examples with a TIME column<a name="r_comparison_condition-examples-time"></a>

The following example table TIME\_TEST has a column TIME\_VAL \(type TIME\) with three values inserted\. 

```
select time_val from time_test;
            
time_val
---------------------
20:00:00
00:00:00.5550
00:58:00
```

The following example extracts the hours from each timetz\_val\.

```
select time_val from time_test where time_val < '3:00';
   time_val
---------------
 00:00:00.5550
 00:58:00
```

The following example compares two time literals\. 

```
select time '18:25:33.123456' = time '18:25:33.123456';
 ?column?
----------
 t
```

## Examples with a TIMETZ column<a name="r_comparison_condition-examples-timetz"></a>

The following example table TIMETZ\_TEST has a column TIMETZ\_VAL \(type TIMETZ\) with three values inserted\.

```
select timetz_val from timetz_test;
            
timetz_val
------------------
04:00:00+00
00:00:00.5550+00
05:58:00+00
```

The following example selects only the TIMETZ values less than `3:00:00 UTC`\. The comparison is made after converting the value to UTC\.

```
select timetz_val from timetz_test where timetz_val < '3:00:00 UTC';
                  
   timetz_val
---------------
 00:00:00.5550+00
```

The following example compares two TIMETZ literals\. The time zone is ignored for the comparison\. 

```
select time '18:25:33.123456 PST' < time '19:25:33.123456 EST';
                  
 ?column?
----------
 t
```