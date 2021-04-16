# PG\_STATISTIC\_INDICATOR<a name="r_PG_STATISTIC_INDICATOR"></a>

Stores information about the number of rows inserted or deleted since the last ANALYZE\. The PG\_STATISTIC\_INDICATOR table is updated frequently following DML operations, so statistics are approximate\.

PG\_STATISTIC\_INDICATOR is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_PG_STATISTIC_INDICATOR-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_PG_STATISTIC_INDICATOR.html)

## Example<a name="r_PG_STATISTIC_INDICATOR-example"></a>

The following example returns information for table changes since the last ANALYZE\. 

```
select * from pg_statistic_indicator;

stairelid | stairows | staiins | staidels
----------+----------+---------+---------
   108271 |       11 |       0 |        0
   108275 |      365 |       0 |        0
   108278 |     8798 |       0 |        0
   108280 |    91865 |       0 |   100632
   108267 |    89981 |   49990 |     9999
   108269 |      808 |     606 |      374
   108282 |   152220 |   76110 |   248566
```