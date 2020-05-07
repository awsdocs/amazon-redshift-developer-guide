# ROLLBACK<a name="r_ROLLBACK"></a>

Aborts the current transaction and discards all updates made by that transaction\.

This command performs the same function as the [ABORT](r_ABORT.md) command\.

## Syntax<a name="r_ROLLBACK-synopsis"></a>

```
ROLLBACK [ WORK | TRANSACTION ]
```

## Parameters<a name="r_ROLLBACK-parameters"></a>

WORK  
Optional keyword\. This keyword isn't supported within a stored procedure\. 

TRANSACTION  
Optional keyword\. WORK and TRANSACTION are synonyms\. Neither is supported within a stored procedure\. 

For information about using ROLLBACK within a stored procedure, see [Managing transactions](stored-procedure-transaction-management.md)\. 

## Example<a name="r_ROLLBACK-example"></a>

The following example creates a table then starts a transaction where data is inserted into the table\. The ROLLBACK command then rolls back the data insertion to leave the table empty\.

The following command creates an example table called MOVIE\_GROSS:

```
create table movie_gross( name varchar(30), gross bigint );
```

The next set of commands starts a transaction that inserts two data rows into the table:

```
begin;

insert into movie_gross values ( 'Raiders of the Lost Ark', 23400000);

insert into movie_gross values ( 'Star Wars', 10000000 );
```

Next, the following command selects the data from the table to show that it was successfully inserted:

```
select * from movie_gross;
```

The command output shows that both rows successfully inserted:

```
name           |  gross
-------------------------+----------
Raiders of the Lost Ark | 23400000
Star Wars               | 10000000
(2 rows)
```

This command now rolls back the data changes to where the transaction began:

```
rollback;
```

Selecting data from the table now shows an empty table:

```
select * from movie_gross;

name | gross
------+-------
(0 rows)
```