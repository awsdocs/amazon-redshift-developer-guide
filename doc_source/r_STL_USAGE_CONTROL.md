# STL\_USAGE\_CONTROL<a name="r_STL_USAGE_CONTROL"></a>

The STL\_USAGE\_CONTROL view contains information that is logged when a usage limit is reached\. For more information about usage limits, see [Managing Usage Limits](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-cluster-usage-limits.html) in the *Amazon Redshift Cluster Management Guide*\. 

STL\_USAGE\_CONTROL is visible only to superusers\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_USAGE_CONTROL-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_USAGE_CONTROL.html)

## Sample query<a name="r_STL_USAGE_CONTROL-sample-queries"></a>

The following SQL example returns some of the information logged when a usage limit is reached\. 

```
select query, pid, eventtime, feature_type
from stl_usage_control
order by eventtime desc
limit 5;
```