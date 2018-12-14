# ALTER TABLE Examples<a name="r_ALTER_TABLE_examples_basic"></a>

The following examples demonstrate basic usage of the ALTER TABLE command\. 

## Rename a Table<a name="r_ALTER_TABLE_examples_basic-rename-a-table"></a>

The following command renames the USERS table to USERS\_BKUP: 

```
alter table users
rename to users_bkup;
```

 You can also use this type of command to rename a view\. 

## Change the Owner of a Table or View<a name="r_ALTER_TABLE_examples_basic-change-the-owner-of-a-table-or-view"></a>

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

## Rename a Column<a name="r_ALTER_TABLE_examples_basic-rename-a-column"></a>

The following command renames the VENUESEATS column in the VENUE table to VENUESIZE: 

```
alter table venue
rename column venueseats to venuesize;
```

## Drop a Table Constraint<a name="r_ALTER_TABLE_examples_drop-constraint"></a>

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