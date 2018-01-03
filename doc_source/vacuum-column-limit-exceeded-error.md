# Vacuum Column Limit Exceeded Error<a name="vacuum-column-limit-exceeded-error"></a>

If your vacuum fails with the message `ERROR: 1036 DETAIL: Vacuum column limit exceeded`, your table has more columns than VACUUM can process with the available memory\. The vacuum column limit is less than the maximum number of columns for a table, which is 1600\. The actual column limit for a vacuum varies depending on your cluster's configuration\. For example, on clusters with DC1 or DS1 node types, you can generally VACUUM tables with up to about 250 columns\. With the DS2 node type, the limit could be as high as 375 columns for DS2\.xlarge or 625 columns for a DS2\.8xlarge cluster\.

You can increase the vacuum column limit by increasing the value of [wlm\_query\_slot\_count](r_wlm_query_slot_count.md), which increases the amount of memory available for the query\. The maximum value for `wlm_query_slot_count` is limited to the concurrency value for the queue\. For more information, see [Best Practices for Designing Queries](c_designing-queries-best-practices.md)\.

If increasing the value of wlm\_query\_slot\_count is not an option, or if it doesn't solve the problem, you can avoid needing to vacuum by performing a deep copy\. To perform a deep copy, you create a copy of the table, insert the rows from the original table into the copy, drop the original table, and then rename the copy\. A deep copy is often much faster than a vacuum\. For more information, see [Performing a Deep Copy](performing-a-deep-copy.md)\.

For example, suppose the table `calendardays` has 365 columns\. After a load operation, you perform a vacuum and the vacuum fails, as the following example shows\. 

```
vacuum calendardays;

An error occurred when executing the SQL command:
vacuum calendardays;

ERROR: 1036
DETAIL: Vacuum column limit exceeded for table calendardays
HINT: Increase the value of wlm_query_slot_count or perform a deep copy instead of a vacuum.
```

The following example sets `wlm_query_slot_count` to 10, performs a vacuum, and then resets `wlm_query_slot_count` to 1\. With the higher slot count, the vacuum succeeds\.

```
set wlm_query_slot_count to 10;
vacuum calendardays;
set wlm_query_slot_count to 1;

vacuum executed successfully
```

You can perform a deep copy instead of a vacuum\. The following example uses CREATE TABLE LIKE to perform a deep copy\. 

```
create table likecalendardays (like calendardays);
insert into likecalendardays (select * from calendardays);
drop table calendardays;
alter table likecalendardays rename to calendardays;
```

Performing a deep copy using CREATE TABLE AS \(CTAS\) is faster than using CREATE TABLE LIKE, but CTAS does not preserve the sort key, encoding, distkey, and notnull attributes of the parent table\. For a comparison of different deep copy methods, see [Performing a Deep Copy](performing-a-deep-copy.md)\.