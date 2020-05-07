# Examples<a name="r_CREATE_TABLE_examples"></a>

The following examples demonstrate various column and table attributes in Amazon Redshift CREATE TABLE statements\.

## Create a table with a distribution key, a compound sort key, and compression<a name="r_CREATE_TABLE_examples-create-a-table-with-distribution-key"></a>

The following example creates a SALES table in the TICKIT database with compression defined for several columns\. LISTID is declared as the distribution key, and LISTID and SELLERID are declared as a multicolumn compound sort key\. Primary key and foreign key constraints are also defined for the table\. 

```
create table sales(
salesid integer not null,
listid integer not null,
sellerid integer not null,
buyerid integer not null,
eventid integer not null encode mostly16,
dateid smallint not null,
qtysold smallint not null encode mostly8,
pricepaid decimal(8,2) encode delta32k,
commission decimal(8,2) encode delta32k,
saletime timestamp,
primary key(salesid),
foreign key(listid) references listing(listid),
foreign key(sellerid) references users(userid),
foreign key(buyerid) references users(userid),
foreign key(dateid) references date(dateid))
distkey(listid)
compound sortkey(listid,sellerid);
```

The result is as follows: 

```
schemaname | tablename | column     | type                        | encoding | distkey | sortkey | notnull
-----------+-----------+------------+-----------------------------+----------+---------+---------+--------
public     | sales     | salesid    | integer                     | lzo      | false   |       0 | true   
public     | sales     | listid     | integer                     | none     | true    |       1 | true   
public     | sales     | sellerid   | integer                     | none     | false   |       2 | true   
public     | sales     | buyerid    | integer                     | lzo      | false   |       0 | true   
public     | sales     | eventid    | integer                     | mostly16 | false   |       0 | true   
public     | sales     | dateid     | smallint                    | lzo      | false   |       0 | true   
public     | sales     | qtysold    | smallint                    | mostly8  | false   |       0 | true   
public     | sales     | pricepaid  | numeric(8,2)                | delta32k | false   |       0 | false  
public     | sales     | commission | numeric(8,2)                | delta32k | false   |       0 | false  
public     | sales     | saletime   | timestamp without time zone | lzo      | false   |       0 | false
```

## Create a table using an interleaved sort key<a name="CREATE_TABLE_NEW-create-a-table-using-interleaved-sortkey"></a>

The following example creates the CUSTOMER table with an interleaved sort key\.

```
create table customer_interleaved (
  c_custkey     	integer        not null,
  c_name        	varchar(25)    not null,
  c_address     	varchar(25)    not null,
  c_city        	varchar(10)    not null,
  c_nation      	varchar(15)    not null,
  c_region      	varchar(12)    not null,
  c_phone       	varchar(15)    not null,
  c_mktsegment      varchar(10)    not null)
diststyle all
interleaved sortkey (c_custkey, c_city, c_mktsegment);
```

## Create a table using IF NOT EXISTS<a name="CREATE_TABLE_NEW-create-a-table-using-if-not-exists"></a>

 The following example either creates the CITIES table, or does nothing and returns a message if it already exists:

```
create table if not exists cities(
cityid integer not null,
city varchar(100) not null,
state char(2) not null);
```

## Create a table with ALL distribution<a name="CREATE_TABLE_NEW-create-a-table-with-all-distribution"></a>

 The following example creates the VENUE table with ALL distribution\. 

```
create table venue(
venueid smallint not null,
venuename varchar(100),
venuecity varchar(30),
venuestate char(2),
venueseats integer,
primary key(venueid))
diststyle all;
```

## Create a Table with EVEN distribution<a name="r_CREATE_TABLE_NEW-create-a-table-with-default-even-distribution"></a>

The following example creates a table called MYEVENT with three columns\. 

```
create table myevent(
eventid int,
eventname varchar(200),
eventcity varchar(30))
diststyle even;
```

The table is distributed evenly and isn't sorted\. The table has no declared DISTKEY or SORTKEY columns\. 

```
select "column", type, encoding, distkey, sortkey
from pg_table_def where tablename = 'myevent';

  column   |          type          | encoding | distkey | sortkey 
-----------+------------------------+----------+---------+---------
 eventid   | integer                | lzo      | f       |       0 
 eventname | character varying(200) | lzo      | f       |       0 
 eventcity | character varying(30)  | lzo      | f       |       0 
(3 rows)
```

## Create a temporary table that is LIKE another table<a name="r_CREATE_TABLE_NEW-create-a-temporary-table-that-is-like-another-table"></a>

The following example creates a temporary table called TEMPEVENT, which inherits its columns from the EVENT table\. 

```
create temp table tempevent(like event); 
```

This table also inherits the DISTKEY and SORTKEY attributes of its parent table: 

```
select "column", type, encoding, distkey, sortkey
 from pg_table_def where tablename = 'tempevent';
 
  column   |            type             | encoding | distkey | sortkey 
-----------+-----------------------------+----------+---------+---------
 eventid   | integer                     | none     | t       |       1 
 venueid   | smallint                    | none     | f       |       0 
 catid     | smallint                    | none     | f       |       0 
 dateid    | smallint                    | none     | f       |       0 
 eventname | character varying(200)      | lzo      | f       |       0 
 starttime | timestamp without time zone | bytedict | f       |       0 
(6 rows)
```

## Create a table with an IDENTITY column<a name="r_CREATE_TABLE_NEW-create-a-table-with-an-identity-column"></a>

The following example creates a table named VENUE\_IDENT, which has an IDENTITY column named VENUEID\. This column starts with 0 and increments by 1 for each record\. VENUEID is also declared as the primary key of the table\. 

```
create table venue_ident(venueid bigint identity(0, 1),
venuename varchar(100),
venuecity varchar(30),
venuestate char(2),
venueseats integer,
primary key(venueid));
```

## Create a table with a default IDENTITY column<a name="r_CREATE_TABLE_NEW-create-a-table-with-default-identity-column"></a>

The following example creates a table named `t1`\. This table has an IDENTITY column named `hist_id` and a default IDENTITY column named `base_id`\. 

```
CREATE TABLE t1(
  hist_id BIGINT IDENTITY NOT NULL, /* Cannot be overridden */
  base_id BIGINT GENERATED BY DEFAULT AS IDENTITY NOT NULL, /* Can be overridden */
  business_key varchar(10) ,
  some_field varchar(10) 
);
```

Inserting a row into the table shows that both `hist_id` and `base_id` values are generated\. 

```
INSERT INTO T1 (business_key, some_field) values ('A','MM');
            
SELECT * FROM t1;
 hist_id | base_id | business_key | some_field
---------+---------+--------------+------------
       1 |       1 | A            | MM
```

Inserting a second row shows that the default value for `base_id` is generated\.

```
INSERT INTO T1 (base_id, business_key, some_field) values (DEFAULT, 'B','MNOP');
               
SELECT * FROM t1;

 hist_id | base_id | business_key | some_field
---------+---------+--------------+------------
       1 |       1 | A            | MM
       2 |       2 | B            | MNOP
```

Inserting a third row shows that the value for `base_id` doesn't need to be unique\.

```
INSERT INTO T1 (base_id, business_key, some_field) values (2,'B','MNNN');
               
SELECT * FROM t1;

 hist_id | base_id | business_key | some_field
---------+---------+--------------+------------
       1 |       1 | A            | MM
       2 |       2 | B            | MNOP
       3 |       2 | B            | MNNN
```

## Create a table with DEFAULT column values<a name="r_CREATE_TABLE_NEW-create-a-table-with-default-column-values"></a>

The following example creates a CATEGORYDEF table that declares default values for each column: 

```
create table categorydef(
catid smallint not null default 0,
catgroup varchar(10) default 'Special',
catname varchar(10) default 'Other',
catdesc varchar(50) default 'Special events',
primary key(catid));

insert into categorydef values(default,default,default,default);

select * from categorydef;

catid | catgroup | catname |    catdesc
-------+----------+---------+----------------
0 | Special  | Other   | Special events
(1 row)
```

## DISTSTYLE, DISTKEY, and SORTKEY options<a name="r_CREATE_TABLE_NEW-diststyle-distkey-and-sortkey-options"></a>

The following example shows how the DISTKEY, SORTKEY, and DISTSTYLE options work\. In this example, COL1 is the distribution key; therefore, the distribution style must be either set to KEY or not set\. By default, the table has no sort key and so isn't sorted: 

```
create table t1(col1 int distkey, col2 int) diststyle key;
```

The result is as follows:

```
select "column", type, encoding, distkey, sortkey
from pg_table_def where tablename = 't1';

column |  type   | encoding | distkey | sortkey 
-------+---------+----------+---------+---------
col1   | integer | lzo      | t       | 0       
col2   | integer | lzo      | f       | 0
```

In the following example, the same column is defined as the distribution key and the sort key\. Again, the distribution style must be either set to KEY or not set\. 

```
create table t2(col1 int distkey sortkey, col2 int);
```

The result is as follows: 

```
select "column", type, encoding, distkey, sortkey
from pg_table_def where tablename = 't2';

column |  type   | encoding | distkey | sortkey 
-------+---------+----------+---------+---------
col1   | integer | none     | t       | 1       
col2   | integer | lzo      | f       | 0
```

In the following example, no column is set as the distribution key, COL2 is set as the sort key, and the distribution style is set to ALL: 

```
create table t3(col1 int, col2 int sortkey) diststyle all;
```

The result is as follows: 

```
select "column", type, encoding, distkey, sortkey
from pg_table_def where tablename = 't3';

Column |  Type   | Encoding | DistKey | SortKey 
-------+---------+----------+---------+--------
col1   | integer | lzo      | f       | 0       
col2   | integer | none     | f       | 1
```

In the following example, the distribution style is set to EVEN and no sort key is defined explicitly; therefore the table is distributed evenly but isn't sorted\. 

```
create table t4(col1 int, col2 int) diststyle even;
```

The result is as follows: 

```
select "column", type, encoding, distkey, sortkey
from pg_table_def where tablename = 't4';

column |  type   | encoding | distkey | sortkey 
--------+---------+---------+---------+--------
col1   | integer | lzo      | f       | 0       
col2   | integer | lzo      | f       | 0
```