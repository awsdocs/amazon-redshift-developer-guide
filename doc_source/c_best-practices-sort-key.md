# Choose the best sort key<a name="c_best-practices-sort-key"></a>

Amazon Redshift stores your data on disk in sorted order according to the sort key\. The Amazon Redshift query optimizer uses sort order when it determines optimal query plans\. Some suggestions for best approach follow:
+ **If recent data is queried most frequently, specify the timestamp column as the leading column for the sort key\. **

  Queries are more efficient because they can skip entire blocks that fall outside the time range\.
+ **If you do frequent range filtering or equality filtering on one column, specify that column as the sort key\.** 

   Amazon Redshift can skip reading entire blocks of data for that column\. It can do so because it tracks the minimum and maximum column values stored on each block and can skip blocks that don't apply to the predicate range\.
+ **If you frequently join a table, specify the join column as both the sort key and the distribution key\. **

  Doing this enables the query optimizer to choose a sort merge join instead of a slower hash join\. Because the data is already sorted on the join key, the query optimizer can bypass the sort phase of the sort merge join\.

For more information about choosing and specifying sort keys, see [Tutorial: Tuning table design](tutorial-tuning-tables.md) and [Choosing sort keys](t_Sorting_data.md)\.