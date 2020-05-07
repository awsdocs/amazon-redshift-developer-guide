# DECODE expression<a name="r_DECODE_expression"></a>

A DECODE expression replaces a specific value with either another specific value or a default value, depending on the result of an equality condition\. This operation is equivalent to the operation of a simple CASE expression or an IF\-THEN\-ELSE statement\.

## Syntax<a name="r_DECODE_expression-synopsis"></a>

```
DECODE ( expression, search, result [, search, result ]... [ ,default ] )
```

This type of expression is useful for replacing abbreviations or codes that are stored in tables with meaningful business values that are needed for reports\.

## Parameters<a name="r_DECODE_expression-parameters"></a>

 *expression*   
The source of the value that you want to compare, such as a column in a table\.

 *search*   
The target value that is compared against the source expression, such as a numeric value or a character string\. The search expression must evaluate to a single fixed value\. You cannot specify an expression that evaluates to a range of values, such as `age between 20 and 29`; you need to specify separate search/result pairs for each value that you want to replace\.  
The data type of all instances of the search expression must be the same or compatible\. The *expression* and *search* parameters must also be compatible\.

 *result*   
The replacement value that query returns when the expression matches the search value\. You must include at least one search/result pair in the DECODE expression\.  
The data types of all instances of the result expression must be the same or compatible\. The *result* and *default* parameters must also be compatible\.

 *default*   
An optional default value that is used for cases when the search condition fails\. If you do not specify a default value, the DECODE expression returns NULL\.

## Usage notes<a name="decode-expression-usage-notes"></a>

If the *expression* value and the *search* value are both NULL, the DECODE result is the corresponding *result* value\. For an illustration of this use of the function, see the Examples section\.

When used this way, DECODE is similar to [NVL2 expression](r_NVL2.md), but there are some differences\. For a description of these differences, see the NVL2 usage notes\.

## Examples<a name="r_DECODE_expression-examples"></a>

When the value `2008-06-01` exists in the START\_DATE column of DATETABLE, the following example replaces it with `June 1st, 2008`\. The example replaces all other START\_DATE values with NULL\. 

```
select decode(caldate, '2008-06-01', 'June 1st, 2008')
from date where month='JUN' order by caldate;

case
----------------
June 1st, 2008

...
(30 rows)
```

The following example uses a DECODE expression to convert the five abbreviated CATNAME columns in the CATEGORY table to full names and convert other values in the column to `Unknown`\. 

```
select catid, decode(catname,
'NHL', 'National Hockey League',
'MLB', 'Major League Baseball',
'MLS', 'Major League Soccer',
'NFL', 'National Football League',
'NBA', 'National Basketball Association',
'Unknown')
from category
order by catid;

catid  |	case
-------+---------------------------------
1      | Major League Baseball
2      | National Hockey League
3      | National Football League
4      | National Basketball Association
5      | Major League Soccer
6      | Unknown
7      | Unknown
8      | Unknown
9      | Unknown
10     | Unknown
11     | Unknown
(11 rows)
```

Use a DECODE expression to find venues in Colorado and Nevada with NULL in the VENUESEATS column; convert the NULLs to zeroes\. If the VENUESEATS column is not NULL, return 1 as the result\. 

```
select venuename, venuestate, decode(venueseats,null,0,1)
from venue
where venuestate in('NV','CO')
order by 2,3,1;

venuename	              | venuestate     | case
------------------------------+----------------+-----------
Coors Field                   |	CO	       |   1
Dick's Sporting Goods Park    |	CO	       |   1
Ellie Caulkins Opera House    |	CO	       |   1
INVESCO Field		      |	CO	       |   1
Pepsi Center		      |	CO	       |   1
Ballys Hotel		      |	NV	       |   0
Bellagio Hotel                |	NV	       |   0
Caesars Palace                |	NV	       |   0
Harrahs Hotel                 |	NV	       |   0
Hilton Hotel                  |	NV	       |   0
...						
(20 rows)
```