# Managing permissions for datashares in Amazon Redshift<a name="permissions"></a>

As a producer cluster administrator, you retain control for the datasets you are sharing\. You can add new objects to or remove them from the datashare\. You can also grant or revoke access to datashares as a whole for the consumer clusters and AWS accounts \. When permissions are revoked, consumer clusters immediately lose access to the shared objects and stop seeing them in list of INBOUND datashare in SVV\_DATASHARES\.

The following example creates a datashare SalesShare, adds a table public\.tickit\_sales\_redshift to SalesShare, and grants usage privileges on SalesShare to a cluster namespace\.

```
CREATE DATASHARE SalesShare;

ALTER DATASHARE SalesShare ADD TABLE public.tickit_sales_redshift; 

GRANT USAGE ON DATASHARE SalesShare TO NAMESPACE '13b8833d-17c6-4f16-8fe4-1a018f5ed00d';
```

For CREATE DATASHARE, superusers and database owners can create datashares\. For more information, see [CREATE DATASHARE](r_CREATE_DATASHARE.md)\. For ALTER DATASHARE, the owner of the datashare with the required permissions on the datashare objects to be added or removed can alter the datashare\. For information, see [ALTER DATASHARE](r_ALTER_DATASHARE.md)\. 

As a producer administrator, when you drop a datashare, it stops being listed on consumer clusters\. The databases and schema references created on the consumer cluster from the dropped datashare continue to exist with no objects in them\. The consumer cluster administrator needs to delete these databases manually\.

On the consumer side, a consumer cluster administrator can determine which users and groups should get access to the shared data\. An administrator can control access at the database or schema level\.

The following example grants privileges to access a shared table at the database level and schema level\.

```
GRANT USAGE ON DATABASE Sales_db TO Bob;

GRANT USAGE ON SCHEMA Sales_schema TO GROUP Analyst_group;
```

To further restrict access, you can create views on top of shared objects, exposing only the necessary data\. You can then use these views to give access to the users and groups\.