# SVV\_RLS\_APPLIED\_POLICY<a name="r_SVV_RLS_APPLIED_POLICY"></a>

Use SVV\_RLS\_APPLIED\_POLICY to trace the application of RLS policies on queries that reference RLS\-protected relations\.

Only superuser, sys:operator, or users with system privilege ACCESS SYSTEM TABLE can query this view\. Note that sys:secadmin isn't granted this system permission\.

## Table columns<a name="r_SVV_RLS_APPLIED_POLICY-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_RLS_APPLIED_POLICY.html)

## Sample query<a name="r_SVV_RLS_APPLIED_POLICY-sample-query"></a>

The following example displays the result of the SVV\_RLS\_APPLIED\_POLICY\. To query the SVV\_RLS\_APPLIED\_POLICY, you must have the ACCESS SYSTEM TABLE permission\.

```
-- Check what RLS policies were applied to the run query.
SELECT username, command, datname, relschema, relname, polname, poldefault
FROM svv_rls_applied_policy
WHERE datname = CURRENT_DATABASE() AND query = PG_LAST_QUERY_ID();

 username | command |  datname  | relschema |          relname         |      polname    | poldefault 
----------+---------+-----------+-----------+--------------------------+-----------------+------------
   molly  |    s    | tickit_db |   public  | tickit_category_redshift | policy_concerts |
```