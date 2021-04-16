# Loading your data in sort key order<a name="vacuum-load-in-sort-key-order"></a>

If you load your data in sort key order using a COPY command, you might reduce or even eliminate the need to vacuum\. 

COPY automatically adds new rows to the table's sorted region when all of the following are true:
+ The table uses a compound sort key with only one sort column\. 
+ The sort column is NOT NULL\. 
+ The table is 100 percent sorted or empty\. 
+ All the new rows are higher in sort order than the existing rows, including rows marked for deletion\. In this instance, Amazon Redshift uses the first eight bytes of the sort key to determine sort order\.

For example, suppose you have a table that records customer events using a customer ID and time\. If you sort on customer ID, it’s likely that the sort key range of new rows added by incremental loads will overlap the existing range, as shown in the previous example, leading to an expensive vacuum operation\. 

If you set your sort key to a timestamp column, your new rows will be appended in sort order at the end of the table, as the following diagram shows, reducing or even eliminating the need to vacuum\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/vacuum-unsorted-region-date-sort.png)