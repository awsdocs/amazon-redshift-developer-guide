# DROP MATERIALIZED VIEW<a name="mv-drop"></a>


|  | 
| --- |
| This is prerelease documentation for the materialized view feature for Amazon Redshift, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\.   | 

## Syntax<a name="mv_DROP_MATERIALIZED_VIEW-synopsis"></a>

```
DROP MATERIALIZED VIEW [ IF EXISTS ]  mv_name
```

## Parameters<a name="mv_DROP_MATERIALIZED_VIEW-parameters"></a>

IF EXISTS  
If the materialized view doesn't exist, then `DROP MATERIALIZED VIEW` returns an error message\. This clause is useful when scripting, to keep the script from failing if you drop a nonexistent materialized view\.

*mv\_name*  
The name of the materialized view to be dropped\.

## Usage Notes<a name="mv_CREATE_EXTERNAL_SCHEMA_usage"></a>

Only the owner of a materialized view can `DROP` it\.

## Example<a name="mv_CREATE_EXTERNAL_SCHEMA_examples"></a>

The following example drops the `tickets_mv` materialized view\.

```
DROP MATERIALIZED VIEW tickets_mv;
```