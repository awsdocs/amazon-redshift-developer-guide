# Query plan example<a name="t_explain_plan_example"></a>

This example shows how to evaluate a query plan to find opportunities to optimize the distribution\.

Run the following query with an EXPLAIN command to produce a query plan\.

```
explain
select lastname, catname, venuename, venuecity, venuestate, eventname, 
month, sum(pricepaid) as buyercost, max(totalprice) as maxtotalprice
from category join event on category.catid = event.catid
join venue on venue.venueid = event.venueid
join sales on sales.eventid = event.eventid
join listing on sales.listid = listing.listid
join date on sales.dateid = date.dateid
join users on users.userid = sales.buyerid
group by lastname, catname, venuename, venuecity, venuestate, eventname, month
having sum(pricepaid)>9999
order by catname, buyercost desc;
```

In the TICKIT database, SALES is a fact table and LISTING is its largest dimension\. In order to collocate the tables, SALES is distributed on the LISTID, which is the foreign key for LISTING, and LISTING is distributed on its primary key, LISTID\. The following example shows the CREATE TABLE commands for SALES and LISTID\.

```
create table sales(
	salesid integer not null,
	listid integer not null distkey,
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
        sortkey(listid,sellerid);

create table listing(
	listid integer not null distkey sortkey,
	sellerid integer not null,
	eventid integer not null encode mostly16,
	dateid smallint not null,
	numtickets smallint not null encode mostly8,
	priceperticket decimal(8,2) encode bytedict,
	totalprice decimal(8,2) encode mostly32,
	listtime timestamp,
	primary key(listid),
	foreign key(sellerid) references users(userid),
	foreign key(eventid) references event(eventid),
	foreign key(dateid) references date(dateid));
```

In the following query plan, the Merge Join step for the join on SALES and LISTING shows DS\_DIST\_NONE, which indicates that no redistribution is required for the step\. However, moving up the query plan, the other inner joins show DS\_BCAST\_INNER, which indicates that the inner table is broadcast as part of the query execution\. Because only one pair of tables can be collocated using key distribution, five tables need to be rebroadcast\.

```
QUERY PLAN
XN Merge  (cost=1015345167117.54..1015345167544.46 rows=1000 width=103)
  Merge Key: category.catname, sum(sales.pricepaid)
  ->  XN Network  (cost=1015345167117.54..1015345167544.46 rows=170771 width=103)
        Send to leader
        ->  XN Sort  (cost=1015345167117.54..1015345167544.46 rows=170771 width=103)
              Sort Key: category.catname, sum(sales.pricepaid)
              ->  XN HashAggregate  (cost=15345150568.37..15345152276.08 rows=170771 width=103)
                    Filter: (sum(pricepaid) > 9999.00)
	                    ->  XN Hash Join DS_BCAST_INNER  (cost=742.08..15345146299.10 rows=170771 width=103)
	                          Hash Cond: ("outer".catid = "inner".catid)
	                          ->  XN Hash Join DS_BCAST_INNER  (cost=741.94..15342942456.61 rows=170771 width=97)
	                                Hash Cond: ("outer".dateid = "inner".dateid)
	                                ->  XN Hash Join DS_BCAST_INNER  (cost=737.38..15269938609.81 rows=170766 width=90)
	                                      Hash Cond: ("outer".buyerid = "inner".userid)
	                                      ->  XN Hash Join DS_BCAST_INNER  (cost=112.50..3272334142.59 rows=170771 width=84)
	                                            Hash Cond: ("outer".venueid = "inner".venueid)
	                                            ->  XN Hash Join DS_BCAST_INNER  (cost=109.98..3167290276.71 rows=172456 width=47)
	                                                  Hash Cond: ("outer".eventid = "inner".eventid)
	                                                  ->  XN Merge Join DS_DIST_NONE  (cost=0.00..6286.47 rows=172456 width=30)
	                                                        Merge Cond: ("outer".listid = "inner".listid)
	                                                        ->  XN Seq Scan on listing  (cost=0.00..1924.97 rows=192497 width=14)
	                                                        ->  XN Seq Scan on sales  (cost=0.00..1724.56 rows=172456 width=24)
	                                                  ->  XN Hash  (cost=87.98..87.98 rows=8798 width=25)
	                                                        ->  XN Seq Scan on event  (cost=0.00..87.98 rows=8798 width=25)
	                                            ->  XN Hash  (cost=2.02..2.02 rows=202 width=41)
	                                                  ->  XN Seq Scan on venue  (cost=0.00..2.02 rows=202 width=41)
	                                      ->  XN Hash  (cost=499.90..499.90 rows=49990 width=14)
	                                            ->  XN Seq Scan on users  (cost=0.00..499.90 rows=49990 width=14)
	                                ->  XN Hash  (cost=3.65..3.65 rows=365 width=11)
	                                      ->  XN Seq Scan on date  (cost=0.00..3.65 rows=365 width=11)
	                          ->  XN Hash  (cost=0.11..0.11 rows=11 width=10)
	                                ->  XN Seq Scan on category  (cost=0.00..0.11 rows=11 width=10)
```

One solution is to recreate the tables with DISTSTYLE ALL\. You cannot change a table's distribution style after it is created\. To recreate tables with a different distribution style, use a deep copy\.

First, rename the tables\.

```
alter table users rename to userscopy;
alter table venue rename to venuecopy;
alter table category rename to categorycopy;
alter table date rename to datecopy;
alter table event rename to eventcopy;
```

Run the following script to recreate USERS, VENUE, CATEGORY, DATE, EVENT\. Don't make any changes to SALES and LISTING\.

```
create table users(
        userid integer not null sortkey,
        username char(8),
        firstname varchar(30),
        lastname varchar(30),
        city varchar(30),
        state char(2),
        email varchar(100),
        phone char(14),
        likesports boolean,
        liketheatre boolean,
        likeconcerts boolean,
        likejazz boolean,
        likeclassical boolean,
        likeopera boolean,
        likerock boolean,
        likevegas boolean,
        likebroadway boolean,
        likemusicals boolean,
        primary key(userid)) diststyle all;

create table venue(
        venueid smallint not null sortkey,
        venuename varchar(100),
        venuecity varchar(30),
        venuestate char(2),
        venueseats integer,
        primary key(venueid)) diststyle all;

create table category(
        catid smallint not null,
        catgroup varchar(10),
        catname varchar(10),
        catdesc varchar(50),
        primary key(catid)) diststyle all;

create table date(
        dateid smallint not null sortkey,
        caldate date not null,
        day character(3) not null,
        week smallint not null,
        month character(5) not null,
        qtr character(5) not null,
        year smallint not null,
        holiday boolean default('N'),
        primary key (dateid)) diststyle all;

create table event(
        eventid integer not null sortkey,
        venueid smallint not null,
        catid smallint not null,
        dateid smallint not null,
        eventname varchar(200),
        starttime timestamp,
        primary key(eventid),
        foreign key(venueid) references venue(venueid),
        foreign key(catid) references category(catid),
        foreign key(dateid) references date(dateid)) diststyle all;
```

Insert the data back into the tables and run an ANALYZE command to update the statistics\.

```
insert into users select * from userscopy;
insert into venue select * from venuecopy;
insert into category select * from categorycopy;
insert into date select * from datecopy;
insert into event select * from eventcopy;

analyze;
```

Finally, drop the copies\.

```
drop table userscopy;
drop table venuecopy;
drop table categorycopy;
drop table datecopy;
drop table eventcopy;
```

Run the same query with EXPLAIN again, and examine the new query plan\. The joins now show DS\_DIST\_ALL\_NONE, indicating that no redistribution is required because the data was distributed to every node using DISTSTYLE ALL\.

```
QUERY PLAN
XN Merge  (cost=1000000047117.54..1000000047544.46 rows=1000 width=103)
  Merge Key: category.catname, sum(sales.pricepaid)
  ->  XN Network  (cost=1000000047117.54..1000000047544.46 rows=170771 width=103)
        Send to leader
        ->  XN Sort  (cost=1000000047117.54..1000000047544.46 rows=170771 width=103)
              Sort Key: category.catname, sum(sales.pricepaid)
              ->  XN HashAggregate  (cost=30568.37..32276.08 rows=170771 width=103)
                    Filter: (sum(pricepaid) > 9999.00)
                    ->  XN Hash Join DS_DIST_ALL_NONE  (cost=742.08..26299.10 rows=170771 width=103)
                          Hash Cond: ("outer".buyerid = "inner".userid)
                          ->  XN Hash Join DS_DIST_ALL_NONE  (cost=117.20..21831.99 rows=170766 width=97)
                                Hash Cond: ("outer".dateid = "inner".dateid)
                                ->  XN Hash Join DS_DIST_ALL_NONE  (cost=112.64..17985.08 rows=170771 width=90)
                                      Hash Cond: ("outer".catid = "inner".catid)
                                      ->  XN Hash Join DS_DIST_ALL_NONE  (cost=112.50..14142.59 rows=170771 width=84)
                                            Hash Cond: ("outer".venueid = "inner".venueid)
                                            ->  XN Hash Join DS_DIST_ALL_NONE  (cost=109.98..10276.71 rows=172456 width=47)
                                                  Hash Cond: ("outer".eventid = "inner".eventid)
                                                  ->  XN Merge Join DS_DIST_NONE  (cost=0.00..6286.47 rows=172456 width=30)
                                                        Merge Cond: ("outer".listid = "inner".listid)
                                                        ->  XN Seq Scan on listing  (cost=0.00..1924.97 rows=192497 width=14)
                                                        ->  XN Seq Scan on sales  (cost=0.00..1724.56 rows=172456 width=24)
                                                  ->  XN Hash  (cost=87.98..87.98 rows=8798 width=25)
                                                        ->  XN Seq Scan on event  (cost=0.00..87.98 rows=8798 width=25)
                                            ->  XN Hash  (cost=2.02..2.02 rows=202 width=41)
                                                  ->  XN Seq Scan on venue  (cost=0.00..2.02 rows=202 width=41)
                                      ->  XN Hash  (cost=0.11..0.11 rows=11 width=10)
                                            ->  XN Seq Scan on category  (cost=0.00..0.11 rows=11 width=10)
                                ->  XN Hash  (cost=3.65..3.65 rows=365 width=11)
                                      ->  XN Seq Scan on date  (cost=0.00..3.65 rows=365 width=11)
                          ->  XN Hash  (cost=499.90..499.90 rows=49990 width=14)
                                ->  XN Seq Scan on users  (cost=0.00..499.90 rows=49990 width=14)
```