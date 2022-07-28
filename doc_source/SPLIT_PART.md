# SPLIT\_PART function<a name="SPLIT_PART"></a>

Splits a string on the specified delimiter and returns the part at the specified position\.

## Syntax<a name="SPLIT_PART-synopsis"></a>

```
SPLIT_PART(string, delimiter, part)
```

## Arguments<a name="SPLIT_PART-arguments"></a>

 *string*   
The string to be split\. The string can be CHAR or VARCHAR\.

 *delimiter*   
The delimiter string\.   
If *delimiter* is a literal, enclose it in single quotation marks\. 

 *part*   
Position of the portion to return \(counting from 1\)\. Must be an integer greater than 0\. If *part* is larger than the number of string portions, SPLIT\_PART returns an empty string\. If *delimiter* is not found in *string*, then the returned value contains the contents of the specified part, which might be the entire *string* or an empty value\.

## Return type<a name="SPLIT_PART-return-type"></a>

A CHAR or VARCHAR string, the same as the string parameter\.

## Examples<a name="SPLIT_PART-examples"></a>

The following example splits the timestamp field LISTTIME into year, month, and day components\.

```
select listtime, split_part(listtime,'-',1) as year,
split_part(listtime,'-',2) as month, 
split_part(split_part(listtime,'-',3),' ',1) as day 
from listing limit 5;

      listtime       | year | month | day
---------------------+------+-------+------
 2008-03-05 12:25:29 | 2008 | 03    | 05
 2008-09-09 08:03:36 | 2008 | 09    | 09
 2008-09-26 05:43:12 | 2008 | 09    | 26
 2008-10-04 02:00:30 | 2008 | 10    | 04
 2008-01-06 08:33:11 | 2008 | 01    | 06
(5 rows)
```

The following example selects the LISTTIME timestamp field and splits it on the `'-'` character to get the month \(the second part of the LISTTIME string\), then counts the number of entries for each month:

```
select split_part(listtime,'-',2) as month, count(*)
from listing
group by split_part(listtime,'-',2)
order by 1, 2;

 month | count
-------+-------
    01 | 18543
    02 | 16620
    03 | 17594
    04 | 16822
    05 | 17618
    06 | 17158
    07 | 17626
    08 | 17881
    09 | 17378
    10 | 17756
    11 | 12912
    12 | 4589
(12 rows)
```