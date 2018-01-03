# ABORT<a name="r_ABORT"></a>

Aborts the currently running transaction and discards all updates made by that transaction\. ABORT has no effect on already completed transactions\.

This command performs the same function as the ROLLBACK command\. See [ROLLBACK](r_ROLLBACK.md) for more detailed documentation\.

## Syntax<a name="r_ABORT-synopsis"></a>

```
ABORT [ WORK | TRANSACTION ]
```

## Parameters<a name="r_ABORT-parameters"></a>

WORK  
Optional keyword\.

TRANSACTION  
Optional keyword; WORK and TRANSACTION are synonyms\.

## Example<a name="r_ABORT-example"></a>

The following example creates a table then starts a transaction where data is inserted into the table\. The ABORT command then rolls back the data insertion to leave the table empty\.

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

The command output shows that both rows are successfully inserted:

```
         name           |  gross
------------------------+----------
Raiders of the Lost Ark | 23400000
Star Wars               | 10000000
(2 rows)
```

This command now rolls back the data changes to where the transaction began:

```
abort;
```

Selecting data from the table now shows an empty table:

```
select * from movie_gross;

 name | gross
------+-------
(0 rows)
```