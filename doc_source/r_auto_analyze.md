# auto\_analyze<a name="r_auto_analyze"></a>

## Values \(Default in Bold\)<a name="t_auto_analyze-values"></a>

 **true**, false

## Description<a name="r_auto_analyze-description"></a>

If `auto_analyze` is set to `true`, Amazon Redshift continuously monitors your database and automatically performs analyze operations in the background\. To minimize impact to your system performance, automatic analyze runs during periods when workloads are light\.

## Example<a name="r_auto_analyze"></a>

```
set auto_analyze to true;
set auto_analyze to false
```