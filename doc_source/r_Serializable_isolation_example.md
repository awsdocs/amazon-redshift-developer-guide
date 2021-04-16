# Concurrent write examples<a name="r_Serializable_isolation_example"></a>

The following pseudo\-code examples demonstrate how transactions either proceed or wait when they are run concurrently\.

## Concurrent COPY operations into the same table<a name="r_Serializable_isolation_example-concurrent-copy-operations-into-the-same-table"></a>

Transaction 1 copies rows into the LISTING table: 

```
begin;
copy listing from ...;
end;
```

Transaction 2 starts concurrently in a separate session and attempts to copy more rows into the LISTING table\. Transaction 2 must wait until transaction 1 releases the write lock on the LISTING table, then it can proceed\. 

```
begin;
[waits]
copy listing from ;
end;
```

The same behavior would occur if one or both transactions contained an INSERT command instead of a COPY command\.

## Concurrent DELETE operations from the same table<a name="r_Serializable_isolation_example-concurrent-delete-operations-from-the-same-table"></a>

Transaction 1 deletes rows from a table: 

```
begin;
delete from listing where ...;
end;
```

Transaction 2 starts concurrently and attempts to delete rows from the same table\. It will succeed because it waits for transaction 1 to complete before attempting to delete rows\.

```
begin
[waits]
delete from listing where ;
end;
```

The same behavior would occur if one or both transactions contained an UPDATE command to the same table instead of a DELETE command\.

## Concurrent transactions with a mixture of read and write operations<a name="r_Serializable_isolation_example-concurrent-transactions"></a>

In this example, transaction 1 deletes rows from the USERS table, reloads the table, runs a COUNT\(\*\) query, and then ANALYZE, before committing: 

```
begin;
delete one row from USERS table;
copy ;
select count(*) from users;
analyze ;
end;
```

Meanwhile, transaction 2 starts\. This transaction attempts to copy additional rows into the USERS table, analyze the table, and then run the same COUNT\(\*\) query as the first transaction:

```
begin;
[waits]
copy users from ...;
select count(*) from users;
analyze;
end;
```

The second transaction will succeed because it must wait for the first to complete\. Its COUNT query will return the count based on the load it has completed\.