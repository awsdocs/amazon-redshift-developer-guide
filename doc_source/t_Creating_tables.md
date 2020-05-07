# Designing tables<a name="t_Creating_tables"></a>

**Topics**
+ [Choosing a column compression type](t_Compressing_data_on_disk.md)
+ [Choosing a data distribution style](t_Distributing_data.md)
+ [Choosing sort keys](t_Sorting_data.md)
+ [Defining constraints](t_Defining_constraints.md)
+ [Analyzing table design](c_analyzing-table-design.md)
+ [Tutorial: Tuning table design](tutorial-tuning-tables.md)

A data warehouse system has very different design goals compared to a typical transaction\-oriented relational database system\. An online transaction processing \(OLTP\) application is focused primarily on single row transactions, inserts, and updates\. Amazon Redshift is optimized for very fast execution of complex analytic queries against very large data sets\. Because of the massive amount of data involved in data warehousing, you must specifically design your database to take full advantage of every available performance optimization\. 

This section explains how to choose and implement compression encodings, data distribution keys, sort keys, and table constraints, and it presents best practices for making these design decisions\.