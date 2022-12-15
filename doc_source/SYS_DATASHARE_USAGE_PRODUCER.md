# SYS\_DATASHARE\_USAGE\_PRODUCER<a name="SYS_DATASHARE_USAGE_PRODUCER"></a>

Records the activity and usage of datashares\. This view is only relevant on the producer cluster\.

SYS\_DATASHARE\_USAGE\_PRODUCER is visible to all users\.

## Table columns<a name="SYS_DATASHARE_USAGE_PRODUCER-table-rows"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/SYS_DATASHARE_USAGE_PRODUCER.html)

## Sample queries<a name="SYS_DATASHARE_USAGE_PRODUCER-sample-queries"></a>

The following example shows a SYS\_DATASHARE\_USAGE\_PRODUCER view\.

```
SELECT DISTINCT request_type
FROM sys_datashare_usage_producer 
WHERE object_name LIKE 'tickit%';
   
   request_type
 ------------------   
   "GET RELATION"
```