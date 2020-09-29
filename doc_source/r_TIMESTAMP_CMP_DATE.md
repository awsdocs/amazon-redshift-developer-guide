# TIMESTAMP\_CMP\_DATE function<a name="r_TIMESTAMP_CMP_DATE"></a>

TIMESTAMP\_CMP\_DATE compares the value of a timestamp and a date\. If the timestamp and date values are identical, the function returns 0\. If the timestamp is greater alphabetically, the function returns 1\. If the date is greater, the function returns –1\.

## Syntax<a name="r_TIMESTAMP_CMP_DATE-syntax"></a>

```
TIMESTAMP_CMP_DATE(timestamp, date)
```

## Arguments<a name="r_TIMESTAMP_CMP_DATE-arguments"></a>

 *timestamp*   
A timestamp column or an expression that implicitly converts to a timestamp\.

 *date*   
A date column or an expression that implicitly converts to a date\.

## Return type<a name="r_TIMESTAMP_CMP_DATE-return-type"></a>

INTEGER

## Examples<a name="r_TIMESTAMP_CMP_DATE-examples"></a>

The following example compares LISTTIME to the date `2008-06-18`\. Listings made after this date return `1`; listings made before this date return `-1`\. 

```
select listid, listtime,
timestamp_cmp_date(listtime, '2008-06-18')
from listing
order by 1, 2, 3
limit 10;


 listid |      listtime       | timestamp_cmp_date
--------+---------------------+--------------------
      1 | 2008-01-24 06:43:29 |              -1
      2 | 2008-03-05 12:25:29 |              -1
      3 | 2008-11-01 07:35:33 |               1
      4 | 2008-05-24 01:18:37 |              -1
      5 | 2008-05-17 02:29:11 |              -1
      6 | 2008-08-15 02:08:13 |               1
      7 | 2008-11-15 09:38:15 |               1
      8 | 2008-11-09 05:07:30 |               1
      9 | 2008-09-09 08:03:36 |               1
     10 | 2008-06-17 09:44:54 |              -1
(10 rows)
```