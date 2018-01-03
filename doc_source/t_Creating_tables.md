# Designing Tables<a name="t_Creating_tables"></a>


+ [Choosing a Column Compression Type](t_Compressing_data_on_disk.md)
+ [Choosing a Data Distribution Style](t_Distributing_data.md)
+ [Choosing Sort Keys](t_Sorting_data.md)
+ [Defining Constraints](t_Defining_constraints.md)
+ [Analyzing Table Design](c_analyzing-table-design.md)

A data warehouse system has very different design goals as compared to a typical transaction\-oriented relational database system\. An online transaction processing \(OLTP\) application is focused primarily on single row transactions, inserts, and updates\. Amazon Redshift is optimized for very fast execution of complex analytic queries against very large data sets\. Because of the massive amount of data involved in data warehousing, you must specifically design your database to take full advantage of every available performance optimization\. 

This section explains how to choose and implement compression encodings, data distribution keys, sort keys, and table constraints, and it presents best practices for making these design decisions\.