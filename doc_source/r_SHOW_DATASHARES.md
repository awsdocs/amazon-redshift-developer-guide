# SHOW DATASHARES<a name="r_SHOW_DATASHARES"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

Displays the inbound and outbound shares in a cluster either from the same account or across accounts\. If you don't specify a data share name, then Amazon Redshift displays all data shares in all databases in the cluster\. Users who have the ALTER and SHARE privileges can see the shares that they have privileges for\. 

## Syntax<a name="r_SHOW_DATASHARES-synopsis"></a>

```
SHOW DATASHARES [ LIKE 'namepattern' ] 
```

## Parameters<a name="r_SHOW_DATASHARES-parameters"></a>

LIKE  
An optional clause that compares the specified name pattern to the description of the data share\. When this clause is used, Amazon Redshift displays only the data shares with names that match the specified name pattern\.

*namepattern*  
The name of the data share requested or part of the name to be matched using wildcard characters\.

## Examples<a name="r_SHOW_DATASHARES-examples"></a>

The following example displays the inbound and outbound shares in a cluster\. 

```
SHOW DATASHARES;
SHOW DATASHARES LIKE 'sales%';

share_name   | share_owner | source_database | consumer_database | share_type | createdate          | is_publicaccessible | share_acl | producer_account | producer_namespace 
-------------+-------------+-----------------+-------------------+------------+---------------------+---------------------+-----------+------------------+---------------------------------------
'salesshare' | 100         | dev             |                   | outbound   | 2020-12-09 01:22:54.| False               |           | '467896856988'   | 'f5a0b31c-f2c7-45d6-ba3e-0d53ad027e8b'
```