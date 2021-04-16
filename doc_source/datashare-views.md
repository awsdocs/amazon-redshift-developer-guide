# Working with views in data sharing<a name="datashare-views"></a>

A producer cluster can share regular, late\-binding, and materialized views\. When sharing regular or late\-binding views, you don't have to share the base tables\. The following table shows how views are supported with data sharing\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/datashare-views.html)

You can maintain common tables across all tenants in a producer cluster and share subsets of data filtered by dimension columns, such as tenant\_id \(account\_id or namespace\_id\) to consumer clusters\. To do this, you can define a view on the base table with a filter on these ID columns, for example: current\_aws\_account = tenant\_id\. On the consumer side, when you run the view, you see only the rows that qualify for your account\. Amazon Redshift provides context functions cuurent\_aws\_account, current\_namespace to enable this\.

The following query returns the account ID in which the current Amazon Redshift cluster resides\. You can run this query if you are connected to Amazon Redshift\.

```
select current_user,current_aws_account;

current_user | current_aws_account
-------------+--------------------
dwuser       | 111111111111
(1row)
```

The following query returns the namespace of the current Amazon Redshift cluster\. You can run this query if you are connected to the database\.

```
select current_user, current_namespace; 

current_user | current_namespace
-------------+--------------------------------------
dwuser       | 86b5169f-01dc-4a6f-9fbb-e2e24359e9a8
(1 row)
```