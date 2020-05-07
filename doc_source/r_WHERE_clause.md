# WHERE clause<a name="r_WHERE_clause"></a>

The WHERE clause contains conditions that either join tables or apply predicates to columns in tables\. Tables can be inner\-joined by using appropriate syntax in either the WHERE clause or the FROM clause\. Outer join criteria must be specified in the FROM clause\. 

## Syntax<a name="r_WHERE_clause-synopsis"></a>

```
[ WHERE condition ]
```

## *condition*<a name="r_WHERE_clause-synopsis-condition"></a>

Any search condition with a Boolean result, such as a join condition or a predicate on a table column\. The following examples are valid join conditions: 

```
sales.listid=listing.listid
sales.listid<>listing.listid
```

The following examples are valid conditions on columns in tables: 

```
catgroup like 'S%'
venueseats between 20000 and 50000
eventname in('Jersey Boys','Spamalot')
year=2008
length(catdesc)>25
date_part(month, caldate)=6
```

Conditions can be simple or complex; for complex conditions, you can use parentheses to isolate logical units\. In the following example, the join condition is enclosed by parentheses\. 

```
where (category.catid=event.catid) and category.catid in(6,7,8)
```

## Usage notes<a name="r_WHERE_clause_usage_notes"></a>

You can use aliases in the WHERE clause to reference select list expressions\. 

You can't restrict the results of aggregate functions in the WHERE clause; use the HAVING clause for this purpose\. 

Columns that are restricted in the WHERE clause must derive from table references in the FROM clause\. 

## Example<a name="r_SELECT_synopsis-example"></a>

The following query uses a combination of different WHERE clause restrictions, including a join condition for the SALES and EVENT tables, a predicate on the EVENTNAME column, and two predicates on the STARTTIME column\. 

```
select eventname, starttime, pricepaid/qtysold as costperticket, qtysold
from sales, event
where sales.eventid = event.eventid
and eventname='Hannah Montana'
and date_part(quarter, starttime) in(1,2)
and date_part(year, starttime) = 2008
order by 3 desc, 4, 2, 1 limit 10;

eventname    |      starttime      |   costperticket   | qtysold
----------------+---------------------+-------------------+---------
Hannah Montana | 2008-06-07 14:00:00 |     1706.00000000 |       2
Hannah Montana | 2008-05-01 19:00:00 |     1658.00000000 |       2
Hannah Montana | 2008-06-07 14:00:00 |     1479.00000000 |       1
Hannah Montana | 2008-06-07 14:00:00 |     1479.00000000 |       3
Hannah Montana | 2008-06-07 14:00:00 |     1163.00000000 |       1
Hannah Montana | 2008-06-07 14:00:00 |     1163.00000000 |       2
Hannah Montana | 2008-06-07 14:00:00 |     1163.00000000 |       4
Hannah Montana | 2008-05-01 19:00:00 |      497.00000000 |       1
Hannah Montana | 2008-05-01 19:00:00 |      497.00000000 |       2
Hannah Montana | 2008-05-01 19:00:00 |      497.00000000 |       4
(10 rows)
```