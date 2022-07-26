# JOIN examples<a name="r_Join_examples"></a>

A SQL JOIN clause is used to combine the data from two or more tables based on common fields\. The results might or might not change depending on the join method specified\. For more information about the syntax of a JOIN clause, see [Parameters](r_FROM_clause30.md#r_FROM_clause30-parameters)\. 

The following examples use data from the `TICKIT` sample data\. For more information about the database schema, see [Sample database](c_sampledb.md)\. To learn how to load sample data, see [Using a sample dataset](https://docs.aws.amazon.com/redshift/gsg/sample-data-load.html) in the *Amazon Redshift Getting Started Guide*\.

The following query is an inner join \(without the JOIN keyword\) between the LISTING table and SALES table, where the LISTID from the LISTING table is between 1 and 5\. This query matches LISTID column values in the LISTING table \(the left table\) and SALES table \(the right table\)\. The results show that LISTID 1, 4, and 5 match the criteria\.

```
select listing.listid, sum(pricepaid) as price, sum(commission) as comm
from listing, sales
where listing.listid = sales.listid
and listing.listid between 1 and 5
group by 1
order by 1;

listid | price  |  comm
-------+--------+--------
     1 | 728.00 | 109.20
     4 |  76.00 |  11.40
     5 | 525.00 |  78.75
```

The following query is a left outer join\. Left and right outer joins retain values from one of the joined tables when no match is found in the other table\. The left and right tables are the first and second tables listed in the syntax\. NULL values are used to fill the "gaps" in the result set\. This query matches LISTID column values in the LISTING table \(the left table\) and the SALES table \(the right table\)\. The results show that LISTIDs 2 and 3 did not result in any sales\.

```
select listing.listid, sum(pricepaid) as price, sum(commission) as comm
from listing left outer join sales on sales.listid = listing.listid
where listing.listid between 1 and 5
group by 1
order by 1;

listid | price  |  comm
-------+--------+--------
     1 | 728.00 | 109.20
     2 | NULL   | NULL
     3 | NULL   | NULL
     4 |  76.00 |  11.40
     5 | 525.00 |  78.75
```

The following query is a right outer join\. This query matches LISTID column values in the LISTING table \(the left table\) and the SALES table \(the right table\)\. The results show that LISTIDs 1, 4, and 5 match the criteria\.

```
select listing.listid, sum(pricepaid) as price, sum(commission) as comm
from listing right outer join sales on sales.listid = listing.listid
where listing.listid between 1 and 5
group by 1
order by 1;

listid | price  |  comm
-------+--------+--------
     1 | 728.00 | 109.20
     4 |  76.00 |  11.40
     5 | 525.00 |  78.75
```

The following query is a full join\. Full joins retain values from the joined tables when no match is found in the other table\. The left and right tables are the first and second tables listed in the syntax\. NULL values are used to fill the "gaps" in the result set\. This query matches LISTID column values in the LISTING table \(the left table\) and the SALES table \(the right table\)\. The results show that LISTIDs 2 and 3 did not result in any sales\.

```
select listing.listid, sum(pricepaid) as price, sum(commission) as comm
from listing full join sales on sales.listid = listing.listid
where listing.listid between 1 and 5
group by 1
order by 1;

listid | price  |  comm
-------+--------+--------
     1 | 728.00 | 109.20
     2 | NULL   | NULL
     3 | NULL   | NULL
     4 |  76.00 |  11.40
     5 | 525.00 |  78.75
```

The following query is a full join\. This query matches LISTID column values in the LISTING table \(the left table\) and the SALES table \(the right table\)\. Only rows that do not result in any sales \(LISTIDs 2 and 3\) are in the results\.

```
select listing.listid, sum(pricepaid) as price, sum(commission) as comm
from listing full join sales on sales.listid = listing.listid
where listing.listid between 1 and 5
and (listing.listid IS NULL or sales.listid IS NULL)
group by 1
order by 1;

listid | price  |  comm
-------+--------+--------
     2 | NULL   | NULL
     3 | NULL   | NULL
```

The following example is an inner join with the ON clause\. In this case, NULL rows are not returned\.

```
select listing.listid, sum(pricepaid) as price, sum(commission) as comm
from sales join listing
on sales.listid=listing.listid and sales.eventid=listing.eventid
where listing.listid between 1 and 5
group by 1
order by 1;

listid | price  |  comm
-------+--------+--------
     1 | 728.00 | 109.20
     4 |  76.00 |  11.40
     5 | 525.00 |  78.75
```

The following query is a cross join or Cartesian join of the LISTING table and the SALES table with a predicate to limit the results\. This query matches LISTID column values in the SALES table and the LISTING table for LISTIDs 1, 2, 3, 4, and 5 in both tables\. The results show that 20 rows match the criteria\.

```
select sales.listid as sales_listid, listing.listid as listing_listid
from sales cross join listing
where sales.listid between 1 and 5
and listing.listid between 1 and 5
order by 1,2;

sales_listid | listing_listid
-------------+---------------
1            | 1	
1            | 2	
1            | 3	
1            | 4	
1            | 5	
4            | 1	
4            | 2	
4            | 3	
4            | 4	
4            | 5	
5            | 1	
5            | 1	
5            | 2	
5            | 2	
5            | 3	
5            | 3	
5            | 4	
5            | 4	
5            | 5	
5            | 5
```

The following example is a natural join between two tables\. In this case, the columns listid, sellerid, eventid, and dateid have identical names and data types in both tables and so are used as the join columns\. The results are limited to five rows\.

```
select listid, sellerid, eventid, dateid, numtickets
from listing natural join sales
order by 1
limit 5;

listid | sellerid  | eventid | dateid | numtickets
-------+-----------+---------+--------+-----------
113    | 29704     | 4699    | 2075   | 22	
115    | 39115     | 3513    | 2062   | 14	
116    | 43314     | 8675    | 1910   | 28	
118    | 6079      | 1611    | 1862   | 9	
163    | 24880     | 8253    | 1888   | 14
```

The following example is a join between two tables with the USING clause\. In this case, the columns listid and eventid are used as the join columns\. The results are limited to five rows\.

```
select listid, listing.sellerid, eventid, listing.dateid, numtickets
from listing join sales
using (listid, eventid)
order by 1
limit 5;

listid | sellerid | eventid | dateid | numtickets
-------+----------+---------+--------+-----------
1      | 36861    | 7872    | 1850   | 10	
4      | 8117     | 4337    | 1970   | 8	
5      | 1616     | 8647    | 1963   | 4	
5      | 1616     | 8647    | 1963   | 4	
6      | 47402    | 8240    | 2053   | 18
```

The following query is an inner join of two subqueries in the FROM clause\. The query finds the number of sold and unsold tickets for different categories of events \(concerts and shows\)\. The FROM clause subqueries are *table* subqueries; they can return multiple columns and rows\.

```
select catgroup1, sold, unsold
from
(select catgroup, sum(qtysold) as sold
from category c, event e, sales s
where c.catid = e.catid and e.eventid = s.eventid
group by catgroup) as a(catgroup1, sold)
join
(select catgroup, sum(numtickets)-sum(qtysold) as unsold
from category c, event e, sales s, listing l
where c.catid = e.catid and e.eventid = s.eventid
and s.listid = l.listid
group by catgroup) as b(catgroup2, unsold)

on a.catgroup1 = b.catgroup2
order by 1;

catgroup1 |  sold  | unsold
----------+--------+--------
Concerts  | 195444 |1067199
Shows     | 149905 | 817736
```