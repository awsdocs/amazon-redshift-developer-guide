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

## Examples of revoking the USAGE privilege for datashares<a name="r_REVOKE-examples-datashare"></a>

The following example revokes access to the SalesShare datashare for the Marketing namespace\.

```
REVOKE USAGE ON DATASHARE SalesShare FROM NAMESPACE '13b8833d-17c6-4f16-8fe4-1a018f5ed00d';
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

A superuser must enable the use of the ASSUMEROLE privilege for users and groups by running the following statement once on the cluster: 

```
revoke assumerole on all from public for all;          
```

The following statement revokes the ASSUMEROLE privilege from user reg\_user1 on all roles for all operations\. 

```
revoke assumerole on all from reg_user1 for all;
```