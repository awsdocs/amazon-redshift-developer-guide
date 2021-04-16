# CURRENT\_AWS\_ACCOUNT<a name="r_CURRENT_AWS_ACCOUNT"></a>

Returns the AWS account associated withe the Amazon Redshift cluster that submitted a query\.

## Syntax<a name="r_CURRENT_AWS_ACCOUNT-synopsis"></a>

```
current_aws_account
```

## Return type<a name="r_CURRENT_AWS_ACCOUNT-return-type"></a>

Returns an integer\. 

## Example<a name="r_CURRENT_AWS_ACCOUNT-example"></a>

The following query returns the name of the current database\. 

```
select user, current_aws_account; 
current_user | current_account
-------------+--------------- 
dwuser       | 987654321

(1 row)
```