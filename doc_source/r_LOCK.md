# LOCK<a name="r_LOCK"></a>

Restricts access to a database table\. This command is only meaningful when it is run inside a transaction block\.

The LOCK command obtains a table\-level lock in "ACCESS EXCLUSIVE" mode, waiting if necessary for any conflicting locks to be released\. Explicitly locking a table in this way causes reads and writes on the table to wait when they are attempted from other transactions or sessions\. An explicit table lock created by one user temporarily prevents another user from selecting data from that table or loading data into it\. The lock is released when the transaction that contains the LOCK command completes\.

Less restrictive table locks are acquired implicitly by commands that refer to tables, such as write operations\. For example, if a user tries to read data from a table while another user is updating the table, the data that is read will be a snapshot of the data that has already been committed\. \(In some cases, queries will abort if they violate serializable isolation rules\.\) See [Managing concurrent write operations](c_Concurrent_writes.md)\.

Some DDL operations, such as DROP TABLE and TRUNCATE, create exclusive locks\. These operations prevent data reads\.

If a lock conflict occurs, Amazon Redshift displays an error message to alert the user who started the transaction in conflict\. The transaction that received the lock conflict is aborted\. Every time a lock conflict occurs, Amazon Redshift writes an entry to the [STL\_TR\_CONFLICT](r_STL_TR_CONFLICT.md) table\.

## Syntax<a name="section_r_LOCK-synopsis"></a>

```
LOCK [ TABLE ] table_name [, ...]
```

## Parameters<a name="parameters"></a>

TABLE   
Optional keyword\.

 *table\_name*   
Name of the table to lock\. You can lock more than one table by using a comma\-delimited list of table names\. You can't lock views\. 

## Example<a name="example2"></a>

```
begin;

lock event, sales;

...
```