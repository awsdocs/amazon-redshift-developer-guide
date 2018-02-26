# Summary<a name="tutorial-tuning-tables-summary"></a>

In this tutorial, you learned how to optimize the design of your tables by applying table design best practices\. 

You chose sort keys for the SSB tables based on these best practices: 

+ If recent data is queried most frequently, specify the timestamp column as the leading column for the sort key\. 

+ If you do frequent range filtering or equality filtering on one column, specify that column as the sort key\. 

+ If you frequently join a \(dimension\) table, specify the join column as the sort key\. 

You applied the following best practices to improve the distribution of the tables\. 

+ Distribute the fact table and one dimension table on their common columns 

+ Change some dimension tables to use ALL distribution 

You evaluated the effects of compression on a table and determined that using automatic compression usually produces the best results\. 

For more information, see the following links: 

+ [Amazon Redshift Best Practices for Designing Tables](c_designing-tables-best-practices.md) 

+ [Choose the Best Sort Key](c_best-practices-sort-key.md)

+ [Choosing a Data Distribution Style](t_Distributing_data.md) 

+ [Choosing a Column Compression Type](t_Compressing_data_on_disk.md)

+ [Analyzing Table Design](c_analyzing-table-design.md) 

## Next Step<a name="tutorial-tuning-tables-next-step-loading-tutorial"></a>

For your next step, if you haven't done so already, we recommend taking [Tutorial: Loading Data from Amazon S3](tutorial-loading-data.md)\. 