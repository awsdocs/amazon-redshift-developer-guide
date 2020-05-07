# TRUNCATE<a name="r_TRUNCATE"></a>

Deletes all of the rows from a table without doing a table scan: this operation is a faster alternative to an unqualified DELETE operation\. To execute a TRUNCATE command, you must be the owner of the table or a superuser\.

TRUNCATE is much more efficient than DELETE and doesn't require a VACUUM and ANALYZE\. However, be aware that TRUNCATE commits the transaction in which it is run\.

## Syntax<a name="r_TRUNCATE-synopsis"></a>

```
TRUNCATE [ TABLE ] table_name
```

## Parameters<a name="r_TRUNCATE-parameters"></a>

TABLE   
Optional keyword\. 

 *table\_name*   
A temporary or persistent table\. Only the owner of the table or a superuser may truncate it\.   
You can truncate any table, including tables that are referenced in foreign\-key constraints\.   
You don't need to vacuum a table after truncating it\. 

## Usage notes<a name="r_TRUNCATE_usage_notes"></a>

The TRUNCATE command commits the transaction in which it is run; therefore, you can't roll back a TRUNCATE operation, and a TRUNCATE command may commit other operations when it commits itself\. 

## Examples<a name="r_TRUNCATE-examples"></a>

Use the TRUNCATE command to delete all of the rows from the CATEGORY table: 

```
truncate category;
```

Attempt to roll back a TRUNCATE operation: 

```
begin;

truncate date;

rollback;

select count(*) from date;
count
-------
0
(1 row)
```

The DATE table remains empty after the ROLLBACK command because the TRUNCATE command committed automatically\. 