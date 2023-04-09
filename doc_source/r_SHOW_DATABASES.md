# SHOW DATABASES<a name="r_SHOW_DATABASES"></a>

Displays databases from a specified account ID\.

## Syntax<a name="r_SHOW_DATABASES-syntax"></a>

```
SHOW DATABASES FROM
DATA CATALOG [ ACCOUNT  '<id1>', '<id2>', ... ]
[ LIKE '<expression>' ]
```

## Parameters<a name="r_SHOW_DATABASES-parameters"></a>

ACCOUNT '<id1>', '<id2>', \.\.\.   
The AWS Glue Data Catalog accounts from which to list databases\. Omitting this parameter indicates that Amazon Redshift should show the databases from the account that owns the cluster\.

LIKE '<expression>'  
Filters the list of databases to those that match the expression that you specify\. This parameter supports patterns that use the wildcard characters % \(percent\) and \_ \(underscore\)\.

## Examples<a name="r_SHOW_DATABASES-examples"></a>

The following example displays all of the Data Catalog databases from the account ID 123456789012\.

```
SHOW DATABASES FROM DATA CATALOG ACCOUNT '123456789012'
  catalog_id  | database_name |                        database_arn                    |     type     |                                             target_database                                      | location | parameters
--------------+---------------+--------------------------------------------------------+--------------+--------------------------------------------------------------------------------------------------+----------+------------
 123456789012 |   database1   | arn:aws:glue:us-east-1:123456789012:database/database1 | Data Catalog |                                                                                                  |          |
 123456789012 |   database2   | arn:aws:glue:us-east-1:123456789012:database/database2 | Data Catalog | arn:aws:redshift:us-east-1:123456789012:datashare:035c45ea-61ce-86f0-8b75-19ac6102c3b7/database2 |          |
```