# CTAS examples<a name="r_CTAS_examples"></a>

The following example creates a table called EVENT\_BACKUP for the EVENT table:

```
create table event_backup as select * from event;
```

The resulting table inherits the distribution and sort keys from the EVENT table\. 

```
select "column", type, encoding, distkey, sortkey
from pg_table_def where tablename = 'event_backup';

column    | type                        | encoding | distkey | sortkey
----------+-----------------------------+----------+---------+--------
catid     | smallint                    | none     | false   |       0
dateid    | smallint                    | none     | false   |       1
eventid   | integer                     | none     | true    |       0
eventname | character varying(200)      | none     | false   |       0
starttime | timestamp without time zone | none     | false   |       0
venueid   | smallint                    | none     | false   |       0
```

The following command creates a new table called EVENTDISTSORT by selecting four columns from the EVENT table\. The new table is distributed by EVENTID and sorted by EVENTID and DATEID: 

```
create table eventdistsort
distkey (1)
sortkey (1,3)
as
select eventid, venueid, dateid, eventname
from event;
```

The result is as follows:

```
select "column", type, encoding, distkey, sortkey
from pg_table_def where tablename = 'eventdistsort';

column   |          type          | encoding | distkey | sortkey 
---------+------------------------+----------+---------+-------
eventid   | integer               | none     | t       | 1      
venueid   | smallint              | none     | f       | 0      
dateid    | smallint              | none     | f       | 2      
eventname | character varying(200)| none     | f       | 0
```

You could create exactly the same table by using column names for the distribution and sort keys\. For example:

```
create table eventdistsort1
distkey (eventid)
sortkey (eventid, dateid)
as
select eventid, venueid, dateid, eventname
from event;
```

The following statement applies even distribution to the table but doesn't define an explicit sort key\. 

```
create table eventdisteven
diststyle even
as
select eventid, venueid, dateid, eventname
from event;
```

The table doesn't inherit the sort key from the EVENT table \(EVENTID\) because EVEN distribution is specified for the new table\. The new table has no sort key and no distribution key\. 

```
select "column", type, encoding, distkey, sortkey
from pg_table_def where tablename = 'eventdisteven';

column    |          type          | encoding | distkey | sortkey 
----------+------------------------+----------+---------+---------
eventid   | integer                | none     | f       | 0       
venueid   | smallint               | none     | f       | 0       
dateid    | smallint               | none     | f       | 0       
eventname | character varying(200) | none     | f       | 0
```

The following statement applies even distribution and defines a sort key: 

```
create table eventdistevensort diststyle even sortkey (venueid)
as select eventid, venueid, dateid, eventname from event;
```

 The resulting table has a sort key but no distribution key\. 

```
select "column", type, encoding, distkey, sortkey
from pg_table_def where tablename = 'eventdistevensort';

column    |          type          | encoding | distkey | sortkey 
----------+------------------------+----------+---------+-------
eventid   | integer                | none     | f       | 0      
venueid   | smallint               | none     | f       | 1      
dateid    | smallint               | none     | f       | 0      
eventname | character varying(200) | none     | f       | 0
```

The following statement redistributes the EVENT table on a different key column from the incoming data, which is sorted on the EVENTID column, and defines no SORTKEY column; therefore the table isn't sorted\. 

```
create table venuedistevent distkey(venueid)
as select * from event;
```

The result is as follows: 

```
select "column", type, encoding, distkey, sortkey
from pg_table_def where tablename = 'venuedistevent';

 column   |            type             | encoding | distkey | sortkey 
----------+-----------------------------+----------+---------+-------
eventid   | integer                     | none     | f       | 0      
venueid   | smallint                    | none     | t       | 0      
catid     | smallint                    | none     | f       | 0      
dateid    | smallint                    | none     | f       | 0      
eventname | character varying(200)      | none     | f       | 0      
starttime | timestamp without time zone | none     | f       | 0
```