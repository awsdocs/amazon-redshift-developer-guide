# ALTER DATASHARE<a name="r_ALTER_DATASHARE"></a>


|  | 
| --- |
| This is prerelease documentation for the Amazon Redshift data sharing feature, which is in preview release\. The documentation and the feature are both subject to change\. We recommend that you use this feature only with test clusters, and not in production environments\. For preview terms and conditions, see Beta Service Participation in [AWS Service Terms](https://aws.amazon.com/service-terms/)\. Send feedback on this feature to redshift\-datasharing@amazon\.com\.   | 

Changes the definition of a data share\. You can add objects or remove objects using ALTER DATASHARE\.

## Syntax<a name="r_ALTER_DATASHARE-synopsis"></a>

```
ALTER DATASHARE datashare_name { ADD | REMOVE } { 
TABLE schema.table [, ...] 
| SCHEMA schema [, ...] 
| FUNCTION schema.sql_udf [, ...] 
| ALL TABLES IN SCHEMA schema [, ...]  
| ALL FUNCTIONS IN SCHEMA schema [, ...] }
[ SET PUBLICACCESSIBLE [=] TRUE | FALSE ]
```

## Parameters<a name="r_ALTER_DATASHARE-parameters"></a>

*datashare\_name*  
The name of the data share to be altered\. 

ADD \| REMOVE  
A clause that specifies whether to add objects to or remove objects from the data share\.

TABLE *schema*\.*table* \[, \.\.\.\]  
The name of the table or view in the specified schema to add to the data share\.

SCHEMA *schema* \[, \.\.\.\]   
The name of the schema to add to the data share\.

FUNCTION *schema*\.*sql\_udf* \[, \.\.\.\]  
The name of the user\-defined SQL function to add to the data share\.

ALL TABLES IN SCHEMA *schema* \[, \.\.\.\]  
A clause that specifies adding all tables in the specified schema to the data share\.

ALL FUNCTIONS IN SCHEMA *schema* \[, \.\.\.\] \}  
A clause that specifies adding all functions in the specified schema to the data share\.

\[ SET PUBLICACCESSIBLE \[=\] TRUE \| FALSE \]  
A clause that specifies whether a data share can be shared to clusters that are publicly accessible\.  
The default value for `SET PUBLICACCESSIBLE` is `TRUE`\.

## Usage notes<a name="r_ALTER_DATASHARE_usage"></a>

The following users can alter a data share:
+ A superuser
+ The owner of the data share
+ Users who have SELECT, USAGE, or ALL privileges on the objects in the data share

You can share tables, regular views, late\-binding views, materialized views, SQL user\-defined functions \(UDFs\), and entire schemas\. When you add a schema, Amazon Redshift doesn't add all the objects under it\. You must add them explicitly\.

## Examples<a name="r_ALTER_DATASHARE_examples"></a>

The following example adds the public\.tickit\_venue\_redshift table to the data share SalesShare\.

```
ALTER DATASHARE SalesShare ADD TABLE public.tickit_venue_redshift;
```

The following example adds all tables to the data share SalesShare\.

```
ALTER DATASHARE SalesShare ADD ALL TABLES IN SCHEMA PUBLIC;
```

The following example removes the public\.tickit\_venue\_redshift table from the data share SalesShare\.

```
ALTER DATASHARE SalesShare REMOVE TABLE public.tickit_venue_redshift;
```