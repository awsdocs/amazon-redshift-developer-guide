# SVL\_DATASHARE\_USAGE\_CONSUMER<a name="r_SVL_DATASHARE_USAGE_CONSUMER"></a>

Records the activity and usage of datashares\. This view is only relevant on the consumer cluster\.

SVL\_DATASHARE\_USAGE\_CONSUMER is visible to all users\.

## Table columns<a name="r_SVL_DATASHARE_USAGE_CONSUMER-table-rows"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_DATASHARE_USAGE_CONSUMER.html)

## Sample queries<a name="r_SVL_DATASHARE_USAGE_CONSUMER-sample-queries"></a>

The following example shows a SVL\_DATASHARE\_USAGE\_CONSUMER view\.

```
SELECT request_type, status, trim(error) AS error
FROM svl_datashare_usage_consumer

  request_type  | status | error
----------------+--------+--------
 "GET RELATION" |   0    |
```