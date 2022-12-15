# SHOW DATABASES<a name="r_SHOW_DATABASE"></a>

Displays databases from a specified account ID\.

## Syntax<a name="r_SHOW_DATABASE-syntax"></a>

```
SHOW DATABASES [ FROM ] 
[ DATA CATALOG ACCOUNT [ '<id1>', '<id>' ] ]
[ LIKE '<expression>' ]
```

## Parameters<a name="r_SHOW_DATABASE-parameters"></a>

DATA CATALOG \[ ACCOUNT '<id1>', '<id2>'\]  
The AWS Glue Data Catalog accounts from which to list databases\.

LIKE '<expression>'  
Filters the list of databases to those that match the expression that you specify\. This parameter supports patterns that use the wildcard characters % \(percent\) and \_ \(underscore\)\.

## Examples<a name="r_SHOW_DATABASE-examples"></a>

The following example displays all of the Data Catalog databases from the account ID 1234567890\.

```
SHOW DATABASES FROM DATA CATALOG [ACCOUNT '1234567890']              
  catalog_id  |     database_name     |     type     | target_database |                        location            
--------------+-----------------------+--------------+-----------------+-------------------------------------------------------
 123456789012 |      database1        | Data Catalog |     sample1     | arn:aws:glue:us-east-1:123456789012:database/database1
 098765432102 |      database2        | Data Catalog |     sample2     | arn:aws:glue:us-east-1:098765432102:database/database2
 120695041343 |      database3        | Data Catalog |     sample3     | arn:aws:glue:us-east-1:120695041343:database/database3
```