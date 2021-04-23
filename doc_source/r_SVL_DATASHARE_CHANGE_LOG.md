# SVL\_DATASHARE\_CHANGE\_LOG<a name="r_SVL_DATASHARE_CHANGE_LOG"></a>

Records the consolidated view for tracking changes to datashares on both producer and consumer clusters\.

Superusers can see all rows; regular users can see only their own data\.

## Table columns<a name="r_SVL_DATASHARE_CHANGE_LOG-table-rows"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_DATASHARE_CHANGE_LOG.html)

## Sample queries<a name="r_SVL_DATASHARE_CHANGE_LOG-sample-queries"></a>

The following example shows a SVL\_DATASHARE\_CHANGE\_LOG view\.

```
SELECT DISTINCT action
FROM svl_datashare_change_log
WHERE share_object_name LIKE 'tickit%';

         action
 -----------------------
  "ALTER DATASHARE ADD"
```