# Examples<a name="r_REVOKE-examples"></a>

The following example revokes INSERT privileges on the SALES table from the GUESTS user group\. This command prevents members of GUESTS from being able to load data into the SALES table by using the INSERT command\. 

```
revoke insert on table sales from group guests;
```

The following example revokes the SELECT privilege on all tables in the QA\_TICKIT schema from the user `fred`\.

```
revoke select on all tables in schema qa_tickit from fred;
```

The following example revokes the privilege to select from a view for user `bobr`\.

```
revoke select on table eventview from bobr;
```

The following example revokes the privilege to create temporary tables in the TICKIT database from all users\.

```
revoke temporary on database tickit from public;
```

The following example revokes SELECT privilege on the `cust_name` and `cust_phone` columns of the `cust_profile` table from the user `user1`\. 

```
revoke select(cust_name, cust_phone) on cust_profile from user1;
```

The following example revokes SELECT privilege on the `cust_name` and `cust_phone` columns and UPDATE privilege on the `cust_contact_preference` column of the `cust_profile` table from the `sales_group` group\. 

```
revoke select(cust_name, cust_phone), update(cust_contact_preference) on cust_profile from group sales_group;
```

The following example shows the usage of the ALL keyword to revoke both SELECT and UPDATE privileges on three columns of the table `cust_profile` from the `sales_admin` group\. 

```
revoke ALL(cust_name, cust_phone,cust_contact_preference) on cust_profile from group sales_admin;
```

The following example revokes the SELECT privilege on the `cust_name` column of the `cust_profile_vw` view from the `user2` user\. 

```
revoke select(cust_name) on cust_profile_vw from user2;
```

## Examples of revoking the USAGE privilege for data shares<a name="r_REVOKE-examples-datashare"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

The following example revokes access to the SalesShare data share for the Marketing namespace\.

```
REVOKE USAGE ON DATASHARE SalesShare FROM NAMESPACE 'f5a0b31c-f2c7-45d6-ba3e-0d53ad027e8b';
```

The following example revokes the USAGE privilege on the `Sales_db` to Bob\.

```
REVOKE USAGE ON DATABASE Sales_db FROM Bob;
```

The following example REVOKE USAGE privilege on the `Sales_schema` to the `Analyst_group`\.

```
REVOKE USAGE ON SCHEMA Sales_schema FROM GROUP Analyst_group;
```

## Examples of revoking the ASSUMEROLE privilege<a name="r_REVOKE-examples-assumerole"></a>

The following are examples of revoking the ASSUMEROLE privilege\. 

### Enable the use of the ASSUMEROLE privilege<a name="r_REVOKE-usage-notes-assumerole-enable"></a>

A superuser must enable the use of the ASSUMEROLE privilege for users and groups by running the following statement once on the cluster: 

```
revoke assumerole on all from public for all;          
```

### Revoking the ASSUMEROLE privilege from a user<a name="r_REVOKE-examples-assumerole-user"></a>

The following statement revokes the ASSUMEROLE privilege from user reg\_user1 on all roles for all operations\. 

```
revoke assumerole on all from reg_user1 for all;
```