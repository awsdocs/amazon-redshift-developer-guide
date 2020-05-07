# ALTER TABLE ADD and DROP COLUMN examples<a name="r_ALTER_TABLE_COL_ex-add-drop"></a>

The following examples demonstrate how to use ALTER TABLE to add and then drop a basic table column and also how to drop a column with a dependent object\. 

## ADD then DROP a basic column<a name="r_ALTER_TABLE_COL_ex-add-then-drop-a-basic-column"></a>

The following example adds a standalone FEEDBACK\_SCORE column to the USERS table\. This column simply contains an integer, and the default value for this column is NULL \(no feedback score\)\. 

First, query the PG\_TABLE\_DEF catalog table to view the USERS table: 

```
column        | type                   | encoding | distkey | sortkey
--------------+------------------------+----------+---------+--------
userid        | integer                | delta    | true    |       1
username      | character(8)           | lzo      | false   |       0
firstname     | character varying(30)  | text32k  | false   |       0
lastname      | character varying(30)  | text32k  | false   |       0
city          | character varying(30)  | text32k  | false   |       0
state         | character(2)           | bytedict | false   |       0
email         | character varying(100) | lzo      | false   |       0
phone         | character(14)          | lzo      | false   |       0
likesports    | boolean                | none     | false   |       0
liketheatre   | boolean                | none     | false   |       0
likeconcerts  | boolean                | none     | false   |       0
likejazz      | boolean                | none     | false   |       0
likeclassical | boolean                | none     | false   |       0
likeopera     | boolean                | none     | false   |       0
likerock      | boolean                | none     | false   |       0
likevegas     | boolean                | none     | false   |       0
likebroadway  | boolean                | none     | false   |       0
likemusicals  | boolean                | none     | false   |       0
```

Now add the feedback\_score column: 

```
alter table users
add column feedback_score int
default NULL;
```

Select the FEEDBACK\_SCORE column from USERS to verify that it was added: 

```
select feedback_score from users limit 5;

feedback_score
----------------

(5 rows)
```

Drop the column to reinstate the original DDL: 

```
alter table users drop column feedback_score;
```

## DROPPING a column with a dependent object<a name="r_ALTER_TABLE_COL_ex-dropping-a-column-with-a-dependent-object"></a>

This example drops a column that has a dependent object\. As a result, the dependent object is also dropped\. 

To start, add the FEEDBACK\_SCORE column to the USERS table again: 

```
alter table users
add column feedback_score int
default NULL;
```

Next, create a view from the USERS table called USERS\_VIEW: 

```
create view users_view as select * from users;
```

Now, try to drop the FEEDBACK\_SCORE column from the USERS table\. This DROP statement uses the default behavior \(RESTRICT\): 

```
alter table users drop column feedback_score;
```

Amazon Redshift displays an error message that the column can't be dropped because another object depends on it\. 

Try dropping the FEEDBACK\_SCORE column again, this time specifying CASCADE to drop all dependent objects: 

```
alter table users
drop column feedback_score cascade;
```