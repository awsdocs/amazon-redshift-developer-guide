# Choose the Best Distribution Style<a name="c_best-practices-best-dist-key"></a>

When you execute a query, the query optimizer redistributes the rows to the compute nodes as needed to perform any joins and aggregations\. The goal in selecting a table distribution style is to minimize the impact of the redistribution step by locating the data where it needs to be before the query is executed\.

1. **Distribute the fact table and one dimension table on their common columns\.**

   Your fact table can have only one distribution key\. Any tables that join on another key will not be collocated with the fact table\. Choose one dimension to collocate based on how frequently it is joined and the size of the joining rows\. Designate both the dimension table's primary key and the fact table's corresponding foreign key as the DISTKEY\. 

1. **Choose the largest dimension based on the size of the filtered data set\. **

   Only the rows that are used in the join need to be distributed, so consider the size of the data set after filtering, not the size of the table\. 

1. **Choose a column with high cardinality in the filtered result set\.** 

   If you distribute a sales table on a date column, for example, you will probably get fairly even data distribution, unless most of your sales are seasonal\. However, if you commonly use a range\-restricted predicate to filter for a narrow date period, most of the filtered rows will be on a limited set of slices and the query workload will be skewed\. 

1. **Change some dimension tables to use ALL distribution\.**

   If a dimension table cannot be collocated with the fact table or other important joining tables, you can improve query performance significantly by distributing the entire table to all of the nodes\. Using ALL distribution multiplies storage space requirements and increases load times and maintenance operations, so you should weigh all factors before choosing ALL distribution\.

For more information about choosing distribution styles, see [Tutorial: Tuning Table Design](tutorial-tuning-tables.md) and [Choosing a Data Distribution Style](t_Distributing_data.md)\.