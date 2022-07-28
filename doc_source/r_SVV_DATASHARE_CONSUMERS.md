# SVV\_DATASHARE\_CONSUMERS<a name="r_SVV_DATASHARE_CONSUMERS"></a>

Use SVV\_DATASHARE\_CONSUMERS to view a list of consumers for a datashare created on a cluster\. 

## Table columns<a name="r_SVV_DATASHARE_CONSUMERS-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_DATASHARE_CONSUMERS.html)

## Sample query<a name="r_SVV_DATASHARE_CONSUMERS-sample-query"></a>

The following example returns the output for SVV\_DATASHARE\_CONSUMERS\.

```
SELECT count(*)
FROM svv_datashare_consumers
WHERE share_name LIKE 'tickit_datashare%';

1
```