# ALTER DATASHARE<a name="r_ALTER_DATASHARE"></a>

Changes the definition of a datashare\. You can add objects or remove objects using ALTER DATASHARE\. You can only change a datashare in the current database\. Add or remove objects from the associated database to a datashare\. The owner of the datashare with the required permissions on the datashare objects to be added or removed can alter the datashare\.

## Required privileges<a name="r_ALTER_DATASHARE-privileges"></a>

Following are required privileges for ALTER DATASHARE:
+ Superuser\.
+ User with the ALTER DATASHARE privilege\.
+ Users who have the ALTER or ALL privilege on the datashare\.
+ To add specific objects to a datashare, users must have the privilege on the objects\. For this case, users should be the owners of objects or have SELECT, USAGE, or ALL privileges on the objects\.  

## Syntax<a name="r_ALTER_DATASHARE-synopsis"></a>

The following syntax illustrates how to add or remove objects to the datashare\.

```
ALTER DATASHARE datashare_name { ADD | REMOVE } {
TABLE schema.table [, ...]
| SCHEMA schema [, ...]
| FUNCTION schema.sql_udf (argtype,...) [, ...]
| ALL TABLES IN SCHEMA schema [, ...]
| ALL FUNCTIONS IN SCHEMA schema [, ...] }
```

The following syntax illustrates how to configure the properties of the datashare\.

```
ALTER DATASHARE datashare_name {
[ SET PUBLICACCESSIBLE [=] TRUE | FALSE ]
[ SET INCLUDENEW [=] TRUE | FALSE FOR SCHEMA schema ] }
```

## Parameters<a name="r_ALTER_DATASHARE-parameters"></a>

*datashare\_name*  
The name of the datashare to be altered\. 

ADD \| REMOVE  
A clause that specifies whether to add objects to or remove objects from the datashare\.

TABLE *schema*\.*table* \[, \.\.\.\]  
The name of the table or view in the specified schema to add to the datashare\.

SCHEMA *schema* \[, \.\.\.\]   
The name of the schema to add to the datashare\.

FUNCTION *schema*\.*sql\_udf* \(argtype,\.\.\.\) \[, \.\.\.\]  
The name of the user\-defined SQL function with argument types to add to the datashare\.

ALL TABLES IN SCHEMA *schema*  \[, \.\.\.\]   
A clause that specifies whether to add all tables and views in the specified schema to the datashare\.

ALL FUNCTIONS IN SCHEMA *schema* \[, \.\.\.\] \}  
A clause that specifies adding all functions in the specified schema to the datashare\.

\[ SET PUBLICACCESSIBLE \[=\] TRUE \| FALSE \]  
A clause that specifies whether a datashare can be shared to clusters that are publicly accessible\.

\[ SET INCLUDENEW \[=\] TRUE \| FALSE FOR SCHEMA *schema* \]  
A clause that specifies whether to add any future tables, views, or SQL user\-defined functions \(UDFs\) created in the specified schema to the datashare\. Current tables, views, or SQL UDFs in the specified schema aren't added to the datashare\. Only superusers can change this property for each datashare\-schema pair\. By default, the INCLUDENEW clause is false\. 

## ALTER DATASHARE usage notes<a name="r_ALTER_DATASHARE_usage"></a>
+ The following users can alter a datashare:
  + A superuser
  + The owner of the datashare
  + Users that have ALTER or ALL privilege on the datashare
+ To add specific objects to a datashare, users must have the correct privileges on the objects\. Users should be the owners of objects or have SELECT, USAGE, or ALL privileges on the objects\.
+ You can share schemas, tables, regular views, late\-binding views, materialized views, and SQL user\-defined functions \(UDFs\)\. Add a schema to a datashare first before adding objects in the schema\. 

  When you add a schema, Amazon Redshift doesn't add all the objects under it\. You must add them explicitly\. 
+ We recommend that you create AWS Data Exchange datashares with the publicly accessible setting turned on\.
+ In general, we recommend that you don't alter an AWS Data Exchange datashare to turn off public accessibility using the ALTER DATASHARE statement\. If you do, the AWS accounts that have access to the datashare lose access if their clusters are publicly accessible\. Performing this type of alteration can breach data product terms in AWS Data Exchange\. For an exception to this recommendation, see following\.

  The following example shows an error when an AWS Data Exchange datashare is created with the setting turned off\.

  ```
  ALTER DATASHARE salesshare SET PUBLICACCESSIBLE FALSE;
  ERROR:  Alter of ADX-managed datashare salesshare requires session variable datashare_break_glass_session_var to be set to value 'c670ba4db22f4b'
  ```

  To allow altering an AWS Data Exchange datashare to turn off the publicly accessible setting, set the following variable and run the ALTER DATASHARE statement again\.

  ```
  SET datashare_break_glass_session_var to 'c670ba4db22f4b';
  ```

  ```
  ALTER DATASHARE salesshare SET PUBLICACCESSIBLE FALSE;
  ```

  In this case, Amazon Redshift generates a random one\-time value to set the session variable to allow ALTER DATASHARE SET PUBLICACCESSIBLE FALSE for an AWS Data Exchange datashare\.

## Examples<a name="r_ALTER_DATASHARE_examples"></a>

The following example adds the `public.tickit_sales_redshift` table to the datashare `salesshare`\.

```
ALTER DATASHARE salesshare ADD TABLE public.tickit_sales_redshift;
```

The following example adds all tables to the datashare `salesshare`\.

```
ALTER DATASHARE salesshare ADD ALL TABLES IN SCHEMA PUBLIC;
```

The following example removes the `public.tickit_sales_redshift` table from the datashare `salesshare`\.

```
ALTER DATASHARE salesshare REMOVE TABLE public.tickit_sales_redshift;
```