# SHOW DATASHARES<a name="r_SHOW_DATASHARES"></a>

Displays the inbound and outbound shares in a cluster either from the same account or across accounts\. If you don't specify a datashare name, then Amazon Redshift displays all datashares in all databases in the cluster\. Users who have the ALTER and SHARE privileges can see the shares that they have privileges for\. 

## Syntax<a name="r_SHOW_DATASHARES-synopsis"></a>

```
SHOW DATASHARES [ LIKE 'namepattern' ] 
```

## Parameters<a name="r_SHOW_DATASHARES-parameters"></a>

LIKE  
An optional clause that compares the specified name pattern to the description of the datashare\. When this clause is used, Amazon Redshift displays only the datashares with names that match the specified name pattern\.

*namepattern*  
The name of the datashare requested or part of the name to be matched using wildcard characters\.

## Examples<a name="r_SHOW_DATASHARES-examples"></a>

The following example displays the inbound and outbound shares in a cluster\. 

```
SHOW DATASHARES;
SHOW DATASHARES LIKE 'sales%';

share_name   | share_owner | source_database | consumer_database | share_type | createdate          | is_publicaccessible | share_acl | producer_account |           producer_namespace 
-------------+-------------+-----------------+-------------------+------------+---------------------+---------------------+-----------+------------------+---------------------------------------
'salesshare' | 100         | dev             |                   | outbound   | 2020-12-09 01:22:54.| False               |           |   123456789012   | 13b8833d-17c6-4f16-8fe4-1a018f5ed00d
```