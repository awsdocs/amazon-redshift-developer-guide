# CREATE RLS POLICY<a name="r_CREATE_RLS_POLICY"></a>

Creates a new row\-level security policy to provide granular access to database objects\.

Superusers and users or roles that have the sys:secadmin role can create a policy\.

## Syntax<a name="r_CREATE_RLS_POLICY-synopsis"></a>

```
CREATE RLS POLICY policy_name
[ WITH (column_name data_type [, ...]) [ [AS] relation_alias ] ]
USING ( using_predicate_exp )
```

## Parameters<a name="r_CREATE_RLS_POLICY-parameters"></a>

 *policy\_name*   
The name of the policy\.

WITH \(*column\_name data\_type \[, \.\.\.\]*\)   
Specifies the *column\_name* and *data\_type* referenced to the columns of tables to which the policy is attached\.   
You can omit the WITH clause only when the RLS policy doesn't reference any columns of tables to which the policy is attached\.

AS *relation\_alias*  
Specifies an optional alias for the table that the RLS policy will be attached to\.

USING \(* using\_predicate\_exp *\)  
Specifies a filter that is applied to the WHERE clause of the query\. Amazon Redshift applies a policy predicate before the query\-level user predicates\. For example, **current\_user = ‘joe’ and price > 10** limits Joe to see only records with the price greater than $10\.

## Usage notes<a name="r_CREATE_RLS_POLICY-usage"></a>

When working with the CREATE RLS POLICY statement, observe the following:
+ Amazon Redshift supports filters that can be part of a WHERE clause of a query\.
+ All policies being attached to a table must have been created with the same table alias\.
+ You don't require SELECT permission on lookup tables\. When you create a policy, Amazon Redshift grants the SELECT permission on the lookup table for the respective policy\. A lookup table is a table object used inside a policy definition\. 
+ Amazon Redshift row\-level security doesn't support the following object types inside a policy definition: catalog tables, cross\-database relations, external tables, regular views, late\-binding views, tables with RLS policies turned on, and temporary tables\.

## Examples<a name="r_CREATE_RLS_POLICY-examples"></a>

The following SQL statements create the tables, users, and roles for the CREATE RLS POLICY example\.

```
-- Create users and roles reference in the policy statements.
CREATE ROLE analyst;

CREATE ROLE consumer;

CREATE USER bob WITH PASSWORD 'Name_is_bob_1';

CREATE USER alice WITH PASSWORD 'Name_is_alice_1';

CREATE USER joe WITH PASSWORD 'Name_is_joe_1';

GRANT ROLE sys:secadmin TO bob;

GRANT ROLE analyst TO alice;

GRANT ROLE consumer TO joe;

GRANT ALL ON TABLE tickit_category_redshift TO PUBLIC;
```

The following example creates a policy called policy\_concerts\.

```
CREATE RLS POLICY policy_concerts
WITH (catgroup VARCHAR(10))
USING (catgroup = 'Concerts');
```