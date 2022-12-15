# SYS\_DATASHARE\_USAGE\_CONSUMER<a name="SYS_DATASHARE_USAGE_CONSUMER"></a>

Records the activity and usage of datashares\. This view is only relevant on the consumer cluster\.

SYS\_DATASHARE\_USAGE\_CONSUMER is visible to all users\.

## Table columns<a name="SYS_DATASHARE_USAGE_CONSUMER-table-rows"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/SYS_DATASHARE_USAGE_CONSUMER.html)

## Sample queries<a name="SYS_DATASHARE_USAGE_CONSUMER-sample-queries"></a>

The following example shows a SYS\_DATASHARE\_USAGE\_CONSUMER view\.

```
SELECT request_type, status, trim(error) AS error
FROM sys_datashare_usage_consumer

  request_type  | status | error
----------------+--------+--------
 "GET RELATION" |   0    |
```