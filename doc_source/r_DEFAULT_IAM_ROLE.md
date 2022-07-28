# DEFAULT\_IAM\_ROLE<a name="r_DEFAULT_IAM_ROLE"></a>

Returns the default IAM role currently associated with the Amazon Redshift cluster\. The function returns none if there isn't any default IAM role associated\. 

## Syntax<a name="r_DEFAULT_IAM_ROLE-synopsis"></a>

```
select default_iam_role();
```

## Return type<a name="r_DEFAULT_IAM_ROLE-return-type"></a>

Returns a VARCHAR string\. 

## Example<a name="r_DEFAULT_IAM_ROLE-example"></a>

The following example returns the default IAM role currently associated with the specified Amazon Redshift cluster, 

```
select default_iam_role();
              default_iam_role
-----------------------------------------------
 arn:aws:iam::123456789012:role/myRedshiftRole
(1 row)
```