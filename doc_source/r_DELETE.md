# DELETE<a name="r_DELETE"></a>

Deletes rows from tables\.

**Note**  
The maximum size for a single SQL statement is 16 MB\.

## Syntax<a name="r_DELETE-synopsis"></a>

```
DELETE [ FROM ] table_name
[ {USING } table_name, ... ]
[ WHERE condition ]
```

## Parameters<a name="r_DELETE-parameters"></a>

FROM  
The FROM keyword is optional, except when the USING clause is specified\. The statements `delete from event;` and `delete event;` are equivalent operations that remove all of the rows from the EVENT table\.  
To delete all the rows from a table, [TRUNCATE](r_TRUNCATE.md) the table\. TRUNCATE is much more efficient than DELETE and doesn't require a VACUUM and ANALYZE\. However, be aware that TRUNCATE commits the transaction in which it is run\.

 *table\_name*   
A temporary or persistent table\. Only the owner of the table or a user with DELETE privilege on the table may delete rows from the table\.  
Consider using the TRUNCATE command for fast unqualified delete operations on large tables; see [TRUNCATE](r_TRUNCATE.md)\.  
After deleting a large number of rows from a table:  
+ Vacuum the table to reclaim storage space and re\-sort rows\.
+ Analyze the table to update statistics for the query planner\.

USING *table\_name*, \.\.\.  
The USING keyword is used to introduce a table list when additional tables are referenced in the WHERE clause condition\. For example, the following statement deletes all of the rows from the EVENT table that satisfy the join condition over the EVENT and SALES tables\. The SALES table must be explicitly named in the FROM list:  

```
delete from event using sales where event.eventid=sales.eventid;
```
If you repeat the target table name in the USING clause, the DELETE operation runs a self\-join\. You can use a subquery in the WHERE clause instead of the USING syntax as an alternative way to write the same query\.

WHERE *condition*   
Optional clause that limits the deletion of rows to those that match the condition\. For example, the condition can be a restriction on a column, a join condition, or a condition based on the result of a query\. The query can reference tables other than the target of the DELETE command\. For example:  

```
delete from t1
where col1 in(select col2 from t2);
```
If no condition is specified, all of the rows in the table are deleted\.

## Examples<a name="r_DELETE-examples"></a>

Delete all of the rows from the CATEGORY table:

```
delete from category;
```

Delete rows with CATID values between 0 and 9 from the CATEGORY table:

```
delete from category
where catid between 0 and 9;
```

Delete rows from the LISTING table whose SELLERID values don't exist in the SALES table:

```
delete from listing
where listing.sellerid not in(select sales.sellerid from sales);
```

The following two queries both delete one row from the CATEGORY table, based on a join to the EVENT table and an additional restriction on the CATID column:

```
delete from category
using event
where event.catid=category.catid and category.catid=9;
```

```
delete from category
where catid in
(select category.catid from category, event
where category.catid=event.catid and category.catid=9);
```