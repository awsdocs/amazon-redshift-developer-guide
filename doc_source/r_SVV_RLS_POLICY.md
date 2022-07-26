# SVV\_RLS\_POLICY<a name="r_SVV_RLS_POLICY"></a>

Use SVV\_RLS\_POLICY to view a list of all row\-level security policies created on the Amazon Redshift cluster\.

Only users with the sys:secadmin role can query this view\.

## Table columns<a name="r_SVV_RLS_POLICY-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVV_RLS_POLICY.html)

## Sample query<a name="r_SVV_RLS_POLICY-sample-query"></a>

The following example displays the result of the SVV\_RLS\_POLICY\.

```
-- Create some policies.
CREATE RLS POLICY pol1 WITH (a int) AS t USING ( t.a IS NOT NULL );
CREATE RLS POLICY pol2 WITH (c varchar(10)) AS t USING ( c LIKE '%public%');

-- Inspect the policy in SVV_RLS_POLICY
SELECT * FROM svv_rls_policy;

 poldb | polname | polalias |                     polatts                      |                polqual                | polenabled | polmodifiedby |   polmodifiedtime   
-------+---------+----------+--------------------------------------------------+---------------------------------------+------------+---------------+---------------------
 my_db | pol1    | t        | [{"colname":"a","type":"integer"}]               | "t"."a" IS NOT NULL                   | t          | policy_admin  | 2022-02-11 14:40:49
 my_db | pol2    | t        | [{"colname":"c","type":"character varying(10)"}] | "t"."c" LIKE CAST('%public%' AS TEXT) | t          | policy_admin  | 2022-02-11 14:41:28
```