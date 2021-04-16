# ALTER TABLE examples<a name="r_ALTER_TABLE_examples_basic"></a>

The following examples demonstrate basic usage of the ALTER TABLE command\. 

## Rename a table<a name="r_ALTER_TABLE_examples_basic-rename-a-table"></a>

The following command renames the USERS table to USERS\_BKUP: 

```
alter table users
rename to users_bkup;
```

 You can also use this type of command to rename a view\. 

## Change the owner of a table or view<a name="r_ALTER_TABLE_examples_basic-change-the-owner-of-a-table-or-view"></a>

The following command changes the VENUE table owner to the user DWUSER: 

```
alter table venue
owner to dwuser;
```

The following commands create a view, then change its owner: 

```
create view vdate as select * from date;
alter table vdate owner to vuser;
```

## Rename a column<a name="r_ALTER_TABLE_examples_basic-rename-a-column"></a>

The following command renames the VENUESEATS column in the VENUE table to VENUESIZE: 

```
alter table venue
rename column venueseats to venuesize;
```

## Drop a table constraint<a name="r_ALTER_TABLE_examples_drop-constraint"></a>

To drop a table constraint, such as a primary key, foreign key, or unique constraint, first find the internal name of the constraint\. Then specify the constraint name in the ALTER TABLE command\. The following example finds the constraints for the CATEGORY table, then drops the primary key with the name `category_pkey`\. 

```
select constraint_name, constraint_type 
from information_schema.table_constraints 
where constraint_schema ='public'
and table_name = 'category';

constraint_name | constraint_type
----------------+----------------
category_pkey   | PRIMARY KEY    

alter table category
drop constraint category_pkey;
```

## Alter a VARCHAR column<a name="r_ALTER_TABLE_examples_alter-column"></a>

To conserve storage, you can define a table initially with VARCHAR columns with the minimum size needed for your current data requirements\. Later, to accommodate longer strings, you can alter the table to increase the size of the column\. 

The following example increases the size of the EVENTNAME column to VARCHAR\(300\)\. 

```
alter table event alter column eventname type varchar(300);
```

## Alter the compression encoding for a column<a name="r_ALTER_TABLE_examples_alter-column-encoding"></a>

You can alter the compression encoding of a column\. Below, you can find a set of examples demonstrating this approach\. The table definition for these examples is as follows\.

```
create table t1(c0 int encode lzo, c1 bigint encode zstd, c2 varchar(16) encode lzo, c3 varchar(32) encode zstd);
```

The following statement alters the compression encoding for column c0 from LZO encoding to AZ64 encoding\. 

```
alter table t1 alter column c0 encode az64;
```

The following statement alters the compression encoding for column c1 from Zstandard encoding to AZ64 encoding\. 

```
alter table t1 alter column c1 encode az64;
```

The following statement alters the compression encoding for column c2 from LZO encoding to Byte\-dictionary encoding\. 

```
alter table t1 alter column c2 encode bytedict;
```

The following statement alters the compression encoding for column c3 from Zstandard encoding to Runlength encoding\. 

```
alter table t1 alter column c3 encode runlength;
```

## Alter a DISTSTYLE KEY DISTKEY column<a name="r_ALTER_TABLE_examples_alter-distkey"></a>

The following examples show how to change the DISTSTYLE and DISTKEY of a table\.

Create a table with an EVEN distribution style\. The SVV\_TABLE\_INFO view shows that the DISTSTYLE is EVEN\. 

```
create table inventory(
  inv_date_sk int4 not null , 
  inv_item_sk int4 not null ,
  inv_warehouse_sk int4 not null ,
  inv_quantity_on_hand int4
) diststyle even;

Insert into inventory values(1,1,1,1);

select "table", "diststyle" from svv_table_info;
   table   |   diststyle
-----------+----------------
 inventory |     EVEN
```

Alter the table DISTKEY to `inv_warehouse_sk`\. The SVV\_TABLE\_INFO view shows the `inv_warehouse_sk` column as the resulting distribution key\. 

```
alter table inventory alter diststyle key distkey inv_warehouse_sk;

select "table", "diststyle" from svv_table_info;
   table   |       diststyle
-----------+-----------------------
 inventory | KEY(inv_warehouse_sk)
```

Alter the table DISTKEY to `inv_item_sk`\. The SVV\_TABLE\_INFO view shows the `inv_item_sk` column as the resulting distribution key\. 

```
alter table inventory alter distkey inv_item_sk;

select "table", "diststyle" from svv_table_info;
   table   |       diststyle
-----------+-----------------------
 inventory | KEY(inv_item_sk)
```

## Alter a table to DISTSTYLE ALL<a name="r_ALTER_TABLE_examples_alter-diststyle-all"></a>

The following examples show how to change a table to DISTSTYLE ALL\.

Create a table with an EVEN distribution style\. The SVV\_TABLE\_INFO view shows that the DISTSTYLE is EVEN\. 

```
create table inventory(
  inv_date_sk int4 not null , 
  inv_item_sk int4 not null ,
  inv_warehouse_sk int4 not null ,
  inv_quantity_on_hand int4
) diststyle even;

Insert into inventory values(1,1,1,1);

select "table", "diststyle" from svv_table_info;
   table   |   diststyle
-----------+----------------
 inventory |     EVEN
```

Alter the table DISTSTYLE to ALL\. The SVV\_TABLE\_INFO view shows the changed DISTSYTLE\. 

```
alter table inventory alter diststyle all;

select "table", "diststyle" from svv_table_info;
   table   |   diststyle
-----------+----------------
 inventory |     ALL
```