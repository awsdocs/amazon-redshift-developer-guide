# Amazon Redshift best practices for designing tables<a name="c_designing-tables-best-practices"></a>

As you plan your database, certain key table design decisions heavily influence overall query performance\. These design choices also have a significant effect on storage requirements, which in turn affects query performance by reducing the number of I/O operations and minimizing the memory required to process queries\.

In this section, you can find a summary of the most important design decisions and presents best practices for optimizing query performance\. [Designing tables](t_Creating_tables.md) provides more detailed explanations and examples of table design options\.

**Topics**
+ [Take the tuning table design tutorial](c_best-practices-tutorial-tuning-tables.md)
+ [Choose the best sort key](c_best-practices-sort-key.md)
+ [Choose the best distribution style](c_best-practices-best-dist-key.md)
+ [Let COPY choose compression encodings](c_best-practices-use-auto-compression.md)
+ [Define primary key and foreign key constraints](c_best-practices-defining-constraints.md)
+ [Use the smallest possible column size](c_best-practices-smallest-column-size.md)
+ [Use date/time data types for date columns](c_best-practices-timestamp-date-columns.md)