# Example: Performing correlated subqueries in Redshift Spectrum<a name="c_performing-correlated-subqueries-spectrum"></a>

You can perform correlated subqueries in Redshift Spectrum\. The `$spectrum_oid` pseudocolumn provides the ability to perform correlated queries with Redshift Spectrum\. To perform a correlated subquery, the pseudocolumn `$spectrum_oid` must be enabled but doesn't appear in the SQL statement\. For more information, see [Pseudocolumns](c-spectrum-external-tables.md#c-spectrum-external-tables-pseudocolumns)\.

To create the external schema and external tables for this example, see [Getting started with Amazon Redshift Spectrum](c-getting-started-using-spectrum.md)\.

Following is an example of a correlated subquery in Redshift Spectrum\. 

```
select *
from myspectrum_schema.sales s
where exists
( select *
from myspectrum_schema.listing l
where l.listid = s.listid )
order by salesid
limit 5;
```

```
salesid  listid   sellerid   buyerid   eventid   dateid  qtysold  pricepaid   commission   saletime
1        1        36861      21191     7872      1875    4        728         109.2        2008-02-18 02:36:48
2        4        8117       11498     4337      1983    2        76          11.4         2008-06-06 05:00:16	
3        5        1616       17433     8647      1983    2        350         52.5         2008-06-06 08:26:17	
4        5        1616       19715     8647      1986    1        175         26.25        2008-06-09 08:38:52	
5        6        47402      14115     8240      2069    2        154         23.1         2008-08-31 09:17:02
```