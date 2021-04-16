# Subquery examples<a name="r_Subquery_examples"></a>

The following examples show different ways in which subqueries fit into SELECT queries\. See [Join examples](r_Join_examples.md) for another example of the use of subqueries\. 

## SELECT list subquery<a name="r_Subquery_examples-select-list-subquery"></a>

The following example contains a subquery in the SELECT list\. This subquery is *scalar*: it returns only one column and one value, which is repeated in the result for each row that is returned from the outer query\. The query compares the Q1SALES value that the subquery computes with sales values for two other quarters \(2 and 3\) in 2008, as defined by the outer query\. 

```
select qtr, sum(pricepaid) as qtrsales,
(select sum(pricepaid)
from sales join date on sales.dateid=date.dateid
where qtr='1' and year=2008) as q1sales
from sales join date on sales.dateid=date.dateid
where qtr in('2','3') and year=2008
group by qtr
order by qtr;

qtr  |  qtrsales   |   q1sales
-------+-------------+-------------
2     | 30560050.00 | 24742065.00
3     | 31170237.00 | 24742065.00
(2 rows)
```

## WHERE clause subquery<a name="r_Subquery_examples-where-clause-subquery"></a>

The following example contains a table subquery in the WHERE clause\. This subquery produces multiple rows\. In this case, the rows contain only one column, but table subqueries can contain multiple columns and rows, just like any other table\. 

The query finds the top 10 sellers in terms of maximum tickets sold\. The top 10 list is restricted by the subquery, which removes users who live in cities where there are ticket venues\. This query can be written in different ways; for example, the subquery could be rewritten as a join within the main query\. 

```
select firstname, lastname, city, max(qtysold) as maxsold
from users join sales on users.userid=sales.sellerid
where users.city not in(select venuecity from venue)
group by firstname, lastname, city
order by maxsold desc, city desc
limit 10;

firstname | lastname  |      city      | maxsold
-----------+-----------+----------------+---------
Noah       | Guerrero | Worcester      |       8
Isadora    | Moss     | Winooski       |       8
Kieran     | Harrison | Westminster    |       8
Heidi      | Davis    | Warwick        |       8
Sara       | Anthony  | Waco           |       8
Bree       | Buck     | Valdez         |       8
Evangeline | Sampson  | Trenton        |       8
Kendall    | Keith    | Stillwater     |       8
Bertha     | Bishop   | Stevens Point  |       8
Patricia   | Anderson | South Portland |       8
(10 rows)
```

## WITH clause subqueries<a name="r_Subquery_examples-with-clause-subqueries"></a>

See [WITH clause](r_WITH_clause.md)\. 