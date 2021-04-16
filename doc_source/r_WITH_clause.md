# WITH clause<a name="r_WITH_clause"></a>

A WITH clause is an optional clause that precedes the SELECT list in a query\. The WITH clause defines one or more subqueries\. Each subquery defines a temporary table, similar to a view definition\. These temporary tables can be referenced in the FROM clause and are used only during the execution of the query to which they belong\. Each subquery in the WITH clause specifies a table name, an optional list of column names, and a query expression that evaluates to a table \(a SELECT statement\)\. 

WITH clause subqueries are an efficient way of defining tables that can be used throughout the execution of a single query\. In all cases, the same results can be achieved by using subqueries in the main body of the SELECT statement, but WITH clause subqueries may be simpler to write and read\. Where possible, WITH clause subqueries that are referenced multiple times are optimized as common subexpressions; that is, it may be possible to evaluate a WITH subquery once and reuse its results\. \(Note that common subexpressions aren't limited to those defined in the WITH clause\.\)

## Syntax<a name="r_WITH_clause-synopsis"></a>

```
[ WITH with_subquery [, ...] ]
```

where *with\_subquery* is: 

```
with_subquery_table_name [ ( column_name [, ...] ) ] AS ( query )
```

## Parameters<a name="r_WITH_clause-parameters"></a>

 *with\_subquery\_table\_name*   
A unique name for a temporary table that defines the results of a WITH clause subquery\. You can't use duplicate names within a single WITH clause\. Each subquery must be given a table name that can be referenced in the [FROM clause](r_FROM_clause30.md)\.

 *column\_name*   
 An optional list of output column names for the WITH clause subquery, separated by commas\. The number of column names specified must be equal to or less than the number of columns defined by the subquery\. 

 *query*   
 Any SELECT query that Amazon Redshift supports\. See [SELECT](r_SELECT_synopsis.md)\. 

## Usage notes<a name="r_WITH_clause-usage-notes"></a>

You can use a WITH clause in the following SQL statements: 
+ SELECT \(including subqueries within SELECT statements\)
+ SELECT INTO
+ CREATE TABLE AS
+ CREATE VIEW
+ DECLARE
+ EXPLAIN
+ INSERT INTO\.\.\.SELECT 
+ PREPARE
+ UPDATE \(within a WHERE clause subquery\)

If the FROM clause of a query that contains a WITH clause doesn't reference any of the tables defined by the WITH clause, the WITH clause is ignored and the query executes as normal\.

A table defined by a WITH clause subquery can be referenced only in the scope of the SELECT query that the WITH clause begins\. For example, you can reference such a table in the FROM clause of a subquery in the SELECT list, WHERE clause, or HAVING clause\. You can't use a WITH clause in a subquery and reference its table in the FROM clause of the main query or another subquery\. This query pattern results in an error message of the form `relation table_name doesn't exist` for the WITH clause table\.

You can't specify another WITH clause inside a WITH clause subquery\.

You can't make forward references to tables defined by WITH clause subqueries\. For example, the following query returns an error because of the forward reference to table W2 in the definition of table W1: 

```
with w1 as (select * from w2), w2 as (select * from w1)
select * from sales;
ERROR:  relation "w2" does not exist
```

A WITH clause subquery may not consist of a SELECT INTO statement; however, you can use a WITH clause in a SELECT INTO statement\.

## Examples<a name="r_WITH_clause-examples"></a>

The following example shows the simplest possible case of a query that contains a WITH clause\. The WITH query named VENUECOPY selects all of the rows from the VENUE table\. The main query in turn selects all of the rows from VENUECOPY\. The VENUECOPY table exists only for the duration of this query\. 

```
with venuecopy as (select * from venue)
select * from venuecopy order by 1 limit 10;
```

```
 venueid |         venuename          |    venuecity    | venuestate | venueseats
---------+----------------------------+-----------------+------------+------------
1 | Toyota Park                | Bridgeview      | IL         |          0
2 | Columbus Crew Stadium      | Columbus        | OH         |          0
3 | RFK Stadium                | Washington      | DC         |          0
4 | CommunityAmerica Ballpark  | Kansas City     | KS         |          0
5 | Gillette Stadium           | Foxborough      | MA         |      68756
6 | New York Giants Stadium    | East Rutherford | NJ         |      80242
7 | BMO Field                  | Toronto         | ON         |          0
8 | The Home Depot Center      | Carson          | CA         |          0
9 | Dick's Sporting Goods Park | Commerce City   | CO         |          0
v     10 | Pizza Hut Park             | Frisco          | TX         |          0
(10 rows)
```

The following example shows a WITH clause that produces two tables, named VENUE\_SALES and TOP\_VENUES\. The second WITH query table selects from the first\. In turn, the WHERE clause of the main query block contains a subquery that constrains the TOP\_VENUES table\. 

```
with venue_sales as 
(select venuename, venuecity, sum(pricepaid) as venuename_sales
from sales, venue, event
where venue.venueid=event.venueid and event.eventid=sales.eventid
group by venuename, venuecity),

top_venues as
(select venuename
from venue_sales
where venuename_sales > 800000)

select venuename, venuecity, venuestate,
sum(qtysold) as venue_qty,
sum(pricepaid) as venue_sales
from sales, venue, event
where venue.venueid=event.venueid and event.eventid=sales.eventid
and venuename in(select venuename from top_venues)
group by venuename, venuecity, venuestate
order by venuename;
```

```
        venuename       |   venuecity   | venuestate | venue_qty | venue_sales
------------------------+---------------+------------+-----------+-------------
August Wilson Theatre   | New York City | NY         |      3187 |  1032156.00
Biltmore Theatre        | New York City | NY         |      2629 |   828981.00
Charles Playhouse       | Boston        | MA         |      2502 |   857031.00
Ethel Barrymore Theatre | New York City | NY         |      2828 |   891172.00
Eugene O'Neill Theatre  | New York City | NY         |      2488 |   828950.00
Greek Theatre           | Los Angeles   | CA         |      2445 |   838918.00
Helen Hayes Theatre     | New York City | NY         |      2948 |   978765.00
Hilton Theatre          | New York City | NY         |      2999 |   885686.00
Imperial Theatre        | New York City | NY         |      2702 |   877993.00
Lunt-Fontanne Theatre   | New York City | NY         |      3326 |  1115182.00
Majestic Theatre        | New York City | NY         |      2549 |   894275.00
Nederlander Theatre     | New York City | NY         |      2934 |   936312.00
Pasadena Playhouse      | Pasadena      | CA         |      2739 |   820435.00
Winter Garden Theatre   | New York City | NY         |      2838 |   939257.00
(14 rows)
```

The following two examples demonstrate the rules for the scope of table references based on WITH clause subqueries\. The first query runs, but the second fails with an expected error\. The first query has WITH clause subquery inside the SELECT list of the main query\. The table defined by the WITH clause \(HOLIDAYS\) is referenced in the FROM clause of the subquery in the SELECT list: 

```
select caldate, sum(pricepaid) as daysales,
(with holidays as (select * from date where holiday ='t')
select sum(pricepaid)
from sales join holidays on sales.dateid=holidays.dateid
where caldate='2008-12-25') as dec25sales
from sales join date on sales.dateid=date.dateid
where caldate in('2008-12-25','2008-12-31')
group by caldate
order by caldate;

caldate   | daysales | dec25sales
-----------+----------+------------
2008-12-25 | 70402.00 |   70402.00
2008-12-31 | 12678.00 |   70402.00
(2 rows)
```

The second query fails because it attempts to reference the HOLIDAYS table in the main query as well as in the SELECT list subquery\. The main query references are out of scope\. 

```
select caldate, sum(pricepaid) as daysales,
(with holidays as (select * from date where holiday ='t')
select sum(pricepaid)
from sales join holidays on sales.dateid=holidays.dateid
where caldate='2008-12-25') as dec25sales
from sales join holidays on sales.dateid=holidays.dateid
where caldate in('2008-12-25','2008-12-31')
group by caldate
order by caldate;

ERROR:  relation "holidays" does not exist
```