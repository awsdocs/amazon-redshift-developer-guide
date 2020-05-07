# DROP TABLE<a name="r_DROP_TABLE"></a>

Removes a table from a database\. Only the owner of the table, the schema owner, or a superuser can drop a table\. 

If you are trying to empty a table of rows, without removing the table, use the DELETE or TRUNCATE command\. 

DROP TABLE removes constraints that exist on the target table\. Multiple tables can be removed with a single DROP TABLE command\. 

DROP TABLE with an external table can't be run inside a transaction \(BEGIN … END\)\. For more information about transactions, see [Serializable isolation](c_serial_isolation.md)\. 

## Syntax<a name="r_DROP_TABLE-synopsis"></a>

```
DROP TABLE [ IF EXISTS ] name [, ...] [ CASCADE | RESTRICT ]
```

## Parameters<a name="r_DROP_TABLE-parameters"></a>

IF EXISTS  
Clause that indicates that if the specified table doesn’t exist, the command should make no changes and return a message that the table doesn't exist, rather than terminating with an error\.  
This clause is useful when scripting, so the script doesn’t fail if DROP TABLE runs against a nonexistent table\.

 *name*   
Name of the table to drop\. 

CASCADE   
Clause that indicates to automatically drop objects that depend on the table, such as views\.   
To create a view that isn't dependent on a table referenced by the view, include the WITH NO SCHEMA BINDING clause in the view definition\. For more information, see [CREATE VIEW](r_CREATE_VIEW.md)\.

RESTRICT   
Clause that indicates not to drop the table if any objects depend on it\. This action is the default\.

## Examples<a name="r_DROP_TABLE-examples"></a>

 **Dropping a table with no dependencies** 

The following example creates and drops a table called FEEDBACK that has no dependencies: 

```
create table feedback(a int);

drop table feedback;
```

 If a table contains columns that are referenced by views or other tables, Amazon Redshift displays a message such as the following\. 

```
Invalid operation: cannot drop table feedback because other objects depend on it
```

 **Dropping two tables simultaneously** 

The following command set creates a FEEDBACK table and a BUYERS table and then drops both tables with a single command: 

```
create table feedback(a int);

create table buyers(a int);

drop table feedback, buyers;
```

 **Dropping a table with a dependency** 

The following steps show how to drop a table called FEEDBACK using the CASCADE switch\. 

First, create a simple table called FEEDBACK using the CREATE TABLE command: 

```
create table feedback(a int);
```

 Next, use the CREATE VIEW command to create a view called FEEDBACK\_VIEW that relies on the table FEEDBACK: 

```
create view feedback_view as select * from feedback;
```

 The following example drops the table FEEDBACK and also drops the view FEEDBACK\_VIEW, because FEEDBACK\_VIEW is dependent on the table FEEDBACK: 

```
drop table feedback cascade;
```

 **Viewing the dependencies for a table** 

You can create a view that holds the dependency information for all of the tables in a database\. Before dropping a given table, query this view to determine if the table has dependencies\. 

Type the following command to create a FIND\_DEPEND view, which joins dependencies with object references: 

```
create view find_depend as
select distinct c_p.oid as tbloid,
n_p.nspname as schemaname, c_p.relname as name,
n_c.nspname as refbyschemaname, c_c.relname as refbyname,
c_c.oid as viewoid
from pg_catalog.pg_class c_p
join pg_catalog.pg_depend d_p
on c_p.relfilenode = d_p.refobjid
join pg_catalog.pg_depend d_c
on d_p.objid = d_c.objid
join pg_catalog.pg_class c_c
on d_c.refobjid = c_c.relfilenode
left outer join pg_namespace n_p
on c_p.relnamespace = n_p.oid
left outer join pg_namespace n_c
on c_c.relnamespace = n_c.oid
where d_c.deptype = 'i'::"char"
and c_c.relkind = 'v'::"char";
```

Now create a SALES\_VIEW from the SALES table: 

```
create view sales_view as select * from sales;
```

Now query the FIND\_DEPEND view to view dependencies in the database\. Limit the scope of the query to the PUBLIC schema, as shown in the following code: 

```
select * from find_depend
where refbyschemaname='public'
order by name;
```

This query returns the following dependencies, showing that the SALES\_VIEW view is also dropped by using the CASCADE option when dropping the SALES table: 

```
 tbloid | schemaname |    name     | viewoid | refbyschemaname |  refbyname
--------+------------+-------------+---------+-----------------+-------------
100241 | public     | find_depend |  100241 | public          | find_depend
100203 | public     | sales       |  100245 | public          | sales_view
100245 | public     | sales_view  |  100245 | public          | sales_view
(3 rows)
```

 **Dropping a table Using IF EXISTS** 

The following example either drops the FEEDBACK table if it exists, or does nothing and returns a message if it doesn't: 

```
drop table if exists feedback;
```