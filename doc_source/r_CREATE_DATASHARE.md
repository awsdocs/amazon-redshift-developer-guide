# CREATE DATASHARE<a name="r_CREATE_DATASHARE"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

Creates a new data share in the current database\. The owner of this data share is the issuer of the CREATE DATASHARE command\.

Amazon Redshift associates one data share with a single Amazon Redshift database\. Superusers and database owners can create data shares\. You can only add objects from that database to the associated data share\. You can create multiple database shares on the same Amazon Redshift database\.

To view information about the data shares, use [SHOW DATASHARES](r_SHOW_DATASHARES.md)\.

## Syntax<a name="r_CREATE_DATASHARE-synopsis"></a>

```
CREATE DATASHARE datashare_name
[[SET] PUBLICACCESSIBLE [=] TRUE | FALSE ];
```

## Parameters<a name="r_CREATE_DATASHARE-parameters"></a>

*datashare\_name*  
The name of the data share\. The data share name must be unique in the cluster namespace\.

\[\[SET\] PUBLICACCESSIBLE\]  
A clause that specifies whether a data share can be shared to clusters that are publicly accessible\.  
The default value for `SET PUBLICACCESSIBLE` is `FALSE`\.

## Usage notes<a name="r_CREATE_DATASHARE_usage"></a>

By default, the owner of the data share only owns the share but not objects of the share\.

Only superuser and the database owner can use CREATE DATASHARE and delegate ALTER privileges to other users or groups\. 

## Examples<a name="r_CREATE_DATASHARE_examples"></a>

The following example creates the data share SalesShare\.

```
create datashare SalesShare;
create datashare SalesShare set publicaccessible = true;
create datashare SalesShare set publicaccessible false;
create datashare SalesShare publicaccessible = false;
create datashare SalesShare publicaccessible true;
```