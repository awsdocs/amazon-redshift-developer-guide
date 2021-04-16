# COMMENT<a name="r_COMMENT"></a>

Creates or changes a comment about a database object\.

## Syntax<a name="r_COMMENT-synopsis"></a>

```
COMMENT ON
{
TABLE object_name |
COLUMN object_name.column_name |
CONSTRAINT constraint_name ON table_name |
DATABASE object_name |
VIEW object_name
}
IS 'text'
```

## Parameters<a name="r_COMMENT-parameters"></a>

 *object\_name*   
Name of the database object being commented on\. You can add a comment to the following objects:  
+ TABLE
+ COLUMN \(also takes a *column\_name*\)\.
+ CONSTRAINT \(also takes a *constraint\_name* and *table\_name*\)\.
+ DATABASE
+ VIEW

IS '*text'*'   
The text of the comment that you want to apply to the specified object\. Enclose the comment in single quotation marks\.

 *column\_name*   
Name of the column being commented on\. Parameter of COLUMN\. Follows a table specified in `object_name`\.

 *constraint\_name*   
Name of the constraint that is being commented on\. Parameter of CONSTRAINT\.

 *table\_name*   
Name of a table containing the constraint\. Parameter of CONSTRAINT\.

 *arg1\_type, arg2\_type, \.\.\.*   
Data types of the arguments for a function\. Parameter of FUNCTION\. 

## Usage notes<a name="r_COMMENT-usage-notes"></a>

Comments on databases may only be applied to the current database\. A warning message is displayed if you attempt to comment on a different database\. The same warning is displayed for comments on databases that don't exist\.

## Example<a name="r_COMMENT-example"></a>

The following example adds a descriptive comment to the EVENT table: 

```
comment on table
event is 'Contains listings of individual events.';
```

To view comments, query the PG\_DESCRIPTION system catalog\. The following example returns the description for the EVENT table\.

```
select * from pg_catalog.pg_description 
where objoid =
(select oid from pg_class where relname = 'event' 
and relnamespace =
(select oid from pg_catalog.pg_namespace where nspname = 'public') );

objoid | classoid | objsubid | description                            
-------+----------+----------+----------------------------------------
116658 |     1259 |        0 | Contains listings of individual events.
```

The following example uses the psql `\dd` command to view the comments\. Amazon Redshift doesn't support psql directly\. You must execute psql commands from the PostgreSQL psql client\. 

**Note**  
The `\dd` command returns comments only with the psql 8\.x versions\. 

```
\dd event

Object descriptions
schema | name | object | description
--------+-------+--------+-----------------------------------------
public | event | table | Contains listings of individual events.
(1 row)
```