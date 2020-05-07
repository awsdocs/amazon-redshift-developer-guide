# Examples of UPDATE statements<a name="c_Examples_of_UPDATE_statements"></a>

For more information about the tables used in the following examples, see [Sample database](c_sampledb.md)\.

The CATEGORY table in the TICKIT database contains the following rows: 

```
 catid | catgroup |  catname  |                  catdesc
-------+----------+-----------+-----------------------------------------
1 | Sports   | MLB       | Major League Baseball
2 | Sports   | NHL       | National Hockey League
3 | Sports   | NFL       | National Football League
4 | Sports   | NBA       | National Basketball Association
5 | Sports   | MLS       | Major League Soccer
6 | Shows    | Musicals  | Musical theatre
7 | Shows    | Plays     | All non-musical theatre
8 | Shows    | Opera     | All opera and light opera
9 | Concerts | Pop       | All rock and pop music concerts
10 | Concerts | Jazz      | All jazz singers and bands
11 | Concerts | Classical | All symphony, concerto, and choir concerts
(11 rows)
```

 **Updating a table based on a range of values** 

Update the CATGROUP column based on a range of values in the CATID column\. 

```
update category
set catgroup='Theatre'
where catid between 6 and 8;
```

```
select * from category
where catid between 6 and 8;

catid | catgroup |  catname  |                  catdesc
-------+----------+-----------+--------------------------------------------
6 | Theatre  | Musicals  | Musical theatre
7 | Theatre  | Plays     | All non-musical theatre
8 | Theatre  | Opera     | All opera and light opera
(3 rows)
```

 **Updating a table based on a current value** 

Update the CATNAME and CATDESC columns based on their current CATGROUP value: 

```
update category
set catdesc=default, catname='Shows'
where catgroup='Theatre';
```

```
select * from category
where catname='Shows';

catid | catgroup |  catname  |                  catdesc
-------+----------+-----------+--------------------------------------------
6 | Theatre  | Shows     |
7 | Theatre  | Shows     |
8 | Theatre  | Shows     |
(3 rows)
```

In this case, the CATDESC column was set to null because no default value was defined when the table was created\.

Run the following commands to set the CATEGORY table data back to the original values:

```
truncate category;

copy category from
's3://mybucket/data/category_pipe.txt'
iam_role 'arn:aws:iam::0123456789012:role/MyRedshiftRole'
delimiter '|';
```

 **Updating a table based on the result of a WHERE clause subquery** 

Update the CATEGORY table based on the result of a subquery in the WHERE clause: 

```
update category
set catdesc='Broadway Musical'
where category.catid in
(select category.catid from category
join event on category.catid = event.catid
join venue on venue.venueid = event.venueid
join sales on sales.eventid = event.eventid
where venuecity='New York City' and catname='Musicals');
```

View the updated table: 

```
select * from category order by 1;

catid | catgroup |  catname  |                  catdesc
-------+----------+-----------+--------------------------------------------
1 | Sports   | MLB       | Major League Baseball
2 | Sports   | NHL       | National Hockey League
3 | Sports   | NFL       | National Football League
4 | Sports   | NBA       | National Basketball Association
5 | Sports   | MLS       | Major League Soccer
6 | Shows    | Musicals  | Broadway Musical
7 | Shows    | Plays     | All non-musical theatre
8 | Shows    | Opera     | All opera and light opera
9 | Concerts | Pop       | All rock and pop music concerts
10 | Concerts | Jazz      | All jazz singers and bands
11 | Concerts | Classical | All symphony, concerto, and choir concerts
(11 rows)
```

## Updating a table based on the result of a join condition<a name="c_Examples_of_UPDATE_statements-updating-a-table-based-on-the-result-of-a-join-condition"></a>

Update the original 11 rows in the CATEGORY table based on matching CATID rows in the EVENT table: 

```
update category set catid=100
from event
where event.catid=category.catid;

select * from category order by 1;
catid | catgroup |  catname  |                  catdesc
-------+----------+-----------+--------------------------------------------
1 | Sports   | MLB       | Major League Baseball
2 | Sports   | NHL       | National Hockey League
3 | Sports   | NFL       | National Football League
4 | Sports   | NBA       | National Basketball Association
5 | Sports   | MLS       | Major League Soccer
10 | Concerts | Jazz      | All jazz singers and bands
11 | Concerts | Classical | All symphony, concerto, and choir concerts
100 | Shows    | Opera     | All opera and light opera
100 | Shows    | Musicals  | Musical theatre
100 | Concerts | Pop       | All rock and pop music concerts
100 | Shows    | Plays     | All non-musical theatre
(11 rows)
```

 Note that the EVENT table is listed in the FROM clause and the join condition to the target table is defined in the WHERE clause\. Only four rows qualified for the update\. These four rows are the rows whose CATID values were originally 6, 7, 8, and 9; only those four categories are represented in the EVENT table: 

```
select distinct catid from event;
catid
-------
9
8
6
7
(4 rows)
```

Update the original 11 rows in the CATEGORY table by extending the previous example and adding another condition to the WHERE clause\. Because of the restriction on the CATGROUP column, only one row qualifies for the update \(although four rows qualify for the join\)\. 

```
update category set catid=100
from event
where event.catid=category.catid
and catgroup='Concerts';

select * from category where catid=100;

catid | catgroup | catname |             catdesc
-------+----------+---------+---------------------------------
100 | Concerts | Pop     | All rock and pop music concerts
(1 row)
```

An alternative way to write this example is as follows: 

```
update category set catid=100
from event join category cat on event.catid=cat.catid
where cat.catgroup='Concerts';
```

The advantage to this approach is that the join criteria are clearly separated from any other criteria that qualify rows for the update\. Note the use of the alias CAT for the CATEGORY table in the FROM clause\.

## Updates with outer joins in the FROM clause<a name="c_Examples_of_UPDATE_statements-updates-with-outer-joins-in-the-from-clause"></a>

The previous example showed an inner join specified in the FROM clause of an UPDATE statement\. The following example returns an error because the FROM clause does not support outer joins to the target table: 

```
update category set catid=100
from event left join category cat on event.catid=cat.catid
where cat.catgroup='Concerts';
ERROR:  Target table must be part of an equijoin predicate
```

If the outer join is required for the UPDATE statement, you can move the outer join syntax into a subquery: 

```
update category set catid=100
from
(select event.catid from event left join category cat on event.catid=cat.catid) eventcat
where category.catid=eventcat.catid
and catgroup='Concerts';
```