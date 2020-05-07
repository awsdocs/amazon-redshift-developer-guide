# Join examples<a name="r_Join_examples"></a>

The following query is an outer join\. Left and right outer joins retain values from one of the joined tables when no match is found in the other table\. The left and right tables are the first and second tables listed in the syntax\. NULL values are used to fill the "gaps" in the result set\.

This query matches LISTID column values in LISTING \(the left table\) and SALES \(the right table\)\. The results show that listings 2, 3, and 5 did not result in any sales\.

```
select listing.listid, sum(pricepaid) as price, sum(commission) as comm
from listing left outer join sales on sales.listid = listing.listid
where listing.listid between 1 and 5
group by 1
order by 1;

listid | price  |  comm
--------+--------+--------
1 | 728.00 | 109.20
2 |        |
3 |        |
4 |  76.00 |  11.40
5 | 525.00 |  78.75
(5 rows)
```

The following query is an inner join of two subqueries in the FROM clause\. The query finds the number of sold and unsold tickets for different categories of events \(concerts and shows\):

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
-----------+--------+--------
Concerts  | 195444 |1067199
Shows     | 149905 | 817736
(2 rows)
```

These FROM clause subqueries are *table* subqueries; they can return multiple columns and rows\.