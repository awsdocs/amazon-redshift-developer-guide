# DATE\_PART\_YEAR function<a name="r_DATE_PART_YEAR"></a>

The DATE\_PART\_YEAR function extracts the year from a date\. 

## Syntax<a name="r_DATE_PART_YEAR-synopsis"></a>

```
DATE_PART_YEAR(date)
```

## Argument<a name="r_DATE_PART_YEAR-argument"></a>

 *date*   
A date column or an expression that implicitly converts to a date\.

## Return type<a name="r_DATE_PART_YEAR-return-type"></a>

INTEGER

## Examples<a name="r_DATE_PART_YEAR-examples"></a>

The following example extracts the year from the CALDATE column\.

```
select caldate, date_part_year(caldate)
from date
order by
dateid limit 10;

 caldate   | date_part_year
-----------+----------------
2008-01-01 |           2008
2008-01-02 |           2008
2008-01-03 |           2008
2008-01-04 |           2008
2008-01-05 |           2008
2008-01-06 |           2008
2008-01-07 |           2008
2008-01-08 |           2008
2008-01-09 |           2008
2008-01-10 |           2008
(10 rows)
```