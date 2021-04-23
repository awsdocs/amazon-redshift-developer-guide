# SVL\_DATASHARE\_USAGE\_PRODUCER<a name="r_SVL_DATASHARE_USAGE_PRODUCER"></a>

Records the activity and usage of datashares\. This view is only relevant on the producer cluster\.

SVL\_DATASHARE\_USAGE\_PRODUCER is visible to all users\.

## Table columns<a name="r_SVL_DATASHARE_USAGE_PRODUCER-table-rows"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_DATASHARE_USAGE_PRODUCER.html)

## Sample queries<a name="r_SVL_DATASHARE_USAGE_PRODUCER-sample-queries"></a>

The following example shows a SVL\_DATASHARE\_USAGE\_PRODUCER view\.

```
SELECT DISTINCT request_type
FROM svl_datashare_usage_producer 
WHERE object_name LIKE 'tickit%';
   
   request_type
 ------------------   
   "GET RELATION"
```