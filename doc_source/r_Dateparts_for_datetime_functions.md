# Date parts for date or timestamp functions<a name="r_Dateparts_for_datetime_functions"></a>

The following table identifies the date part and time part names and abbreviations that are accepted as arguments to the following functions: 
+ DATEADD 
+ DATEDIFF 
+ DATE\_PART 
+ DATE\_TRUNC 
+ EXTRACT 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_Dateparts_for_datetime_functions.html)

## Variations in results with seconds, milliseconds, and microseconds<a name="r_Dateparts_for_datetime_functions-variations-in-results"></a>

Minor differences in query results occur when different date functions specify seconds, milliseconds, or microseconds as date parts: 
+ The EXTRACT function return integers for the specified date part only, ignoring higher\- and lower\-level date parts\. If the specified date part is seconds, milliseconds and microseconds are not included in the result\. If the specified date part is milliseconds, seconds and microseconds are not included\. If the specified date part is microseconds, seconds and milliseconds are not included\. 
+ The DATE\_PART function returns the complete seconds portion of the timestamp, regardless of the specified date part, returning either a decimal value or an integer as required\. 

For example, compare the results of the following queries: 

```
create table seconds(micro timestamp);

insert into seconds values('2009-09-21 11:10:03.189717');

select extract(sec from micro) from seconds;
date_part
-----------
3
(1 row)

select date_part(sec, micro) from seconds;
pgdate_part
-------------
3.189717
(1 row)
```

## CENTURY, EPOCH, DECADE, and MIL notes<a name="r_Dateparts_for_datetime_functions-century"></a>

CENTURY or CENTURIES   
Amazon Redshift interprets a CENTURY to start with year *\#\#\#1* and end with year `###0`:   

```
select extract (century from timestamp '2000-12-16 12:21:13');
date_part
-----------
20
(1 row)

select extract (century from timestamp '2001-12-16 12:21:13');
date_part
-----------
21
(1 row)
```

EPOCH   
The Amazon Redshift implementation of EPOCH is relative to 1970\-01\-01 00:00:00\.000000 independent of the time zone where the cluster resides\. You might need to offset the results by the difference in hours depending on the time zone where the cluster is located\.   
 The following example demonstrates the following:   

1.  Creates a table called EVENT\_EXAMPLE based on the EVENT table\. This CREATE AS command uses the DATE\_PART function to create a date column \(called PGDATE\_PART by default\) to store the epoch value for each event\. 

1.  Selects the column and data type of EVENT\_EXAMPLE from PG\_TABLE\_DEF\. 

1.  Selects EVENTNAME, STARTTIME, and PGDATE\_PART from the EVENT\_EXAMPLE table to view the different date and time formats\. 

1.  Selects EVENTNAME and STARTTIME from EVENT EXAMPLE as is\. Converts epoch values in PGDATE\_PART using a 1 second interval to a timestamp without time zone, and returns the results in a column called CONVERTED\_TIMESTAMP\. 

```
create table event_example
as select eventname, starttime, date_part(epoch, starttime) from event;

select "column", type from pg_table_def where tablename='event_example';

     column    |            type
---------------+-----------------------------
 eventname     | character varying(200)
 starttime     | timestamp without time zone
 pgdate_part   | double precision
(3 rows)
```

```
select eventname, starttime, pgdate_part from event_example;

   eventname          |      starttime      | pgdate_part
----------------------+---------------------+-------------
 Mamma Mia!           | 2008-01-01 20:00:00 |  1199217600
 Spring Awakening     | 2008-01-01 15:00:00 |  1199199600
 Nas                  | 2008-01-01 14:30:00 |  1199197800
 Hannah Montana       | 2008-01-01 19:30:00 |  1199215800
 K.D. Lang            | 2008-01-01 15:00:00 |  1199199600
 Spamalot             | 2008-01-02 20:00:00 |  1199304000
 Macbeth              | 2008-01-02 15:00:00 |  1199286000
 The Cherry Orchard   | 2008-01-02 14:30:00 |  1199284200
 Macbeth              | 2008-01-02 19:30:00 |  1199302200
 Demi Lovato          | 2008-01-02 19:30:00 |  1199302200

   
select eventname, 
starttime, 
timestamp with time zone 'epoch' + pgdate_part * interval '1 second' AS converted_timestamp 
from event_example;

       eventname      |      starttime      | converted_timestamp
----------------------+---------------------+---------------------
 Mamma Mia!           | 2008-01-01 20:00:00 | 2008-01-01 20:00:00
 Spring Awakening     | 2008-01-01 15:00:00 | 2008-01-01 15:00:00
 Nas                  | 2008-01-01 14:30:00 | 2008-01-01 14:30:00
 Hannah Montana       | 2008-01-01 19:30:00 | 2008-01-01 19:30:00
 K.D. Lang            | 2008-01-01 15:00:00 | 2008-01-01 15:00:00
 Spamalot             | 2008-01-02 20:00:00 | 2008-01-02 20:00:00
 Macbeth              | 2008-01-02 15:00:00 | 2008-01-02 15:00:00
 The Cherry Orchard   | 2008-01-02 14:30:00 | 2008-01-02 14:30:00
 Macbeth              | 2008-01-02 19:30:00 | 2008-01-02 19:30:00
 Demi Lovato          | 2008-01-02 19:30:00 | 2008-01-02 19:30:00
 ...
```

DECADE or DECADES   
Amazon Redshift interprets the DECADE or DECADES DATEPART based on the common calendar\. For example, because the common calendar starts from the year 1, the first decade \(decade 1\) is 0001\-01\-01 through 0009\-12\-31, and the second decade \(decade 2\) is 0010\-01\-01 through 0019\-12\-31\. For example, decade 201 spans from 2000\-01\-01 to 2009\-12\-31:   

```
select extract(decade from timestamp '1999-02-16 20:38:40');
date_part
-----------
200
(1 row)

select extract(decade from timestamp '2000-02-16 20:38:40');
date_part
-----------
201
(1 row)

select extract(decade from timestamp '2010-02-16 20:38:40');
date_part
-----------
202
(1 row)
```

MIL or MILS   
Amazon Redshift interprets a MIL to start with the first day of year *\#001* and end with the last day of year `#000`:   

```
select extract (mil from timestamp '2000-12-16 12:21:13');
date_part
-----------
2
(1 row)

select extract (mil from timestamp '2001-12-16 12:21:13');
date_part
-----------
3
(1 row)
```