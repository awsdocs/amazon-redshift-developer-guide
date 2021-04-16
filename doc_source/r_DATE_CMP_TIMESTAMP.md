# DATE\_CMP\_TIMESTAMP function<a name="r_DATE_CMP_TIMESTAMP"></a>

Compares a date to a timestamp and returns `0` if the values are identical, `1` if *date* is greater alphabetically and `-1` if *timestamp* is greater\.

## Syntax<a name="r_DATE_CMP_TIMESTAMP-synopsis"></a>

```
DATE_CMP_TIMESTAMP(date, timestamp)
```

## Arguments<a name="r_DATE_CMP_TIMESTAMP-arguments"></a>

 *date*   
A date column or an expression that implicitly converts to a date\.

 *timestamp*   
A timestamp column or an expression that implicitly converts to a timestamp\.

## Return type<a name="r_DATE_CMP_TIMESTAMP-return-type"></a>

INTEGER

## Examples<a name="r_DATE_CMP_TIMESTAMP-examples"></a>

The following example compares the date `2008-06-18` to LISTTIME\. Listings made before this date return `1`; listings made after this date return `-1`\. 

```
select listid, '2008-06-18', listtime,
date_cmp_timestamp('2008-06-18', listtime)
from listing
order by 1, 2, 3, 4
limit 10;

 listid |  ?column?  |      listtime       | date_cmp_timestamp
--------+------------+---------------------+--------------------
      1 | 2008-06-18 | 2008-01-24 06:43:29 |                  1
      2 | 2008-06-18 | 2008-03-05 12:25:29 |                  1
      3 | 2008-06-18 | 2008-11-01 07:35:33 |                 -1
      4 | 2008-06-18 | 2008-05-24 01:18:37 |                  1
      5 | 2008-06-18 | 2008-05-17 02:29:11 |                  1
      6 | 2008-06-18 | 2008-08-15 02:08:13 |                 -1
      7 | 2008-06-18 | 2008-11-15 09:38:15 |                 -1
      8 | 2008-06-18 | 2008-11-09 05:07:30 |                 -1
      9 | 2008-06-18 | 2008-09-09 08:03:36 |                 -1
     10 | 2008-06-18 | 2008-06-17 09:44:54 |                  1
(10 rows)
```