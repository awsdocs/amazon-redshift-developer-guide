# Choose the best distribution style<a name="c_best-practices-best-dist-key"></a>

When you execute a query, the query optimizer redistributes the rows to the compute nodes as needed to perform any joins and aggregations\. The goal in selecting a table distribution style is to minimize the impact of the redistribution step by locating the data where it needs to be before the query is run\. Some suggestions for best approach follow:

1. **Distribute the fact table and one dimension table on their common columns\.**

   Your fact table can have only one distribution key\. Any tables that join on another key aren't collocated with the fact table\. Choose one dimension to collocate based on how frequently it is joined and the size of the joining rows\. Designate both the dimension table's primary key and the fact table's corresponding foreign key as the DISTKEY\. 

1. **Choose the largest dimension based on the size of the filtered dataset\. **

   Only the rows that are used in the join need to be distributed, so consider the size of the dataset after filtering, not the size of the table\. 

1. **Choose a column with high cardinality in the filtered result set\.** 

   If you distribute a sales table on a date column, for example, you should probably get fairly even data distribution, unless most of your sales are seasonal\. However, if you commonly use a range\-restricted predicate to filter for a narrow date period, most of the filtered rows occur on a limited set of slices and the query workload is skewed\. 

1. **Change some dimension tables to use ALL distribution\.**

   If a dimension table cannot be collocated with the fact table or other important joining tables, you can improve query performance significantly by distributing the entire table to all of the nodes\. Using ALL distribution multiplies storage space requirements and increases load times and maintenance operations, so you should weigh all factors before choosing ALL distribution\.

To let Amazon Redshift choose the appropriate distribution style, don't specify DISTSTYLE\.

For more information about choosing distribution styles, see [Tutorial: Tuning table design](tutorial-tuning-tables.md) and [Choosing a data distribution style](t_Distributing_data.md)\.