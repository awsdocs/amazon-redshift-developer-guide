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
IS 'text' | NULL
```

## Parameters<a name="r_COMMENT-parameters"></a>

 *object\_name*   
Name of the database object being commented on\. You can add a comment to the following objects:  
+ TABLE
+ COLUMN \(also takes a *column\_name*\)\.
+ CONSTRAINT \(also takes a *constraint\_name* and *table\_name*\)\.
+ DATABASE
+ VIEW

IS '*text*' \| NULL  
The comment text that you want to add or replace for the specified object\. The *text* string is data type TEXT\. Enclose the comment in single quotation marks\. Set the value to NULL to remove the comment text\.

 *column\_name*   
Name of the column being commented on\. Parameter of COLUMN\. Follows a table specified in `object_name`\.

 *constraint\_name*   
Name of the constraint that is being commented on\. Parameter of CONSTRAINT\.

 *table\_name*   
Name of a table containing the constraint\. Parameter of CONSTRAINT\.

## Usage notes<a name="r_COMMENT-usage-notes"></a>

You must be a superuser or the owner of a database object to add or update a comment\.

Comments on databases may only be applied to the current database\. A warning message is displayed if you attempt to comment on a different database\. The same warning is displayed for comments on databases that don't exist\.

## Examples<a name="r_COMMENT-example"></a>

The following example adds a comment to the SALES table\. 

```
COMMENT ON TABLE sales IS 'This table stores tickets sales data';
```

The following example displays the comment on the SALES table\. 

```
select obj_description('public.sales'::regclass);

obj_description
-------------------------------------
This table stores tickets sales data
```

The following example removes a comment from the SALES table\. 

```
COMMENT ON TABLE sales IS NULL;
```

The following example adds a comment to the EVENTID column of the SALES table\. 

```
COMMENT ON COLUMN sales.eventid IS 'Foreign-key reference to the EVENT table.';
```

The following example displays a comment on the EVENTID column \(column number 5\) of the SALES table\. 

```
select col_description( 'public.sales'::regclass, 5::integer );

col_description
-----------------------------------------
Foreign-key reference to the EVENT table.
```

The following example adds a descriptive comment to the EVENT table\. 

```
comment on table event is 'Contains listings of individual events.';
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