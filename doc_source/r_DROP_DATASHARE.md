# DROP DATASHARE<a name="r_DROP_DATASHARE"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

Drops a data share\. This command isn't reversible\.

Only a superuser or the data share owner can drop a data share\.

## Syntax<a name="r_DROP_DATASHARE-synopsis"></a>

```
DROP DATASHARE datashare_name;
```

## Parameters<a name="r_DROP_DATASHARE-parameters"></a>

 *datashare\_name*   
The name of the data share to be dropped\.

## Examples<a name="r_DROP_DATASHARE-examples"></a>

The following example drops a data share named SalesShare\.

```
DROP DATASHARE SalesShare;
```