# Example UNION ALL query<a name="c_example_unionall_query"></a>

The following example uses a UNION ALL operator because duplicate rows, if found, need to be retained in the result\. For a specific series of event IDs, the query returns 0 or more rows for each sale associated with each event, and 0 or 1 row for each listing of that event\. Event IDs are unique to each row in the LISTING and EVENT tables, but there might be multiple sales for the same combination of event and listing IDs in the SALES table\.

The third column in the result set identifies the source of the row\. If it comes from the SALES table, it is marked "Yes" in the SALESROW column\. \(SALESROW is an alias for SALES\.LISTID\.\) If the row comes from the LISTING table, it is marked "No" in the SALESROW column\.

In this case, the result set consists of three sales rows for listing 500, event 7787\. In other words, three different transactions took place for this listing and event combination\. The other two listings, 501 and 502, did not produce any sales, so the only row that the query produces for these list IDs comes from the LISTING table \(SALESROW = 'No'\)\.

```
select eventid, listid, 'Yes' as salesrow
from sales
where listid in(500,501,502)
union all
select eventid, listid, 'No'
from listing
where listid in(500,501,502)
order by listid asc;

eventid | listid | salesrow
---------+--------+----------
7787 |    500 | No
7787 |    500 | Yes
7787 |    500 | Yes
7787 |    500 | Yes
6473 |    501 | No
5108 |    502 | No
(6 rows)
```

If you run the same query without the ALL keyword, the result retains only one of the sales transactions\. 

```
select eventid, listid, 'Yes' as salesrow
from sales
where listid in(500,501,502)
union
select eventid, listid, 'No'
from listing
where listid in(500,501,502)
order by listid asc;

eventid | listid | salesrow
---------+--------+----------
7787 |    500 | No
7787 |    500 | Yes
6473 |    501 | No
5108 |    502 | No
(4 rows)
```