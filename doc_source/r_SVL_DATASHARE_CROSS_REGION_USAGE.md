# SVL\_DATASHARE\_CROSS\_REGION\_USAGE<a name="r_SVL_DATASHARE_CROSS_REGION_USAGE"></a>

Use the SVL\_DATASHARE\_CROSS\_REGION\_USAGE view to get a summary of cross\-Region data transferred usage caused by cross\-Region datasharing query\. SVL\_DATASHARE\_CROSS\_REGION\_USAGE aggregates details at the segment level\.

SVL\_DATASHARE\_CROSS\_REGION\_USAGE is visible only to superusers\.

## Table columns<a name="r_SVL_DATASHARE_CROSS_REGION_USAGE-table-rows"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_DATASHARE_CROSS_REGION_USAGE.html)

## Sample queries<a name="r_SVL_DATASHARE_CROSS_REGION_USAGE-sample-queries"></a>

The following example shows a SVL\_DATASHARE\_CROSS\_REGION\_USAGE view\.

```
SELECT query, segment, transferred_data, source_region
from svl_datashare_cross_region_usage
where query = pg_last_query_id()
order by query,segment;

  query | segment | transferred_data | source_region 
--------+---------+------------------+---------------
 200048 |       2 |          4194304 |    us-west-1  
 200048 |       2 |          4194304 |    us-east-2
```