# Amazon Redshift Best Practices for Designing Tables<a name="c_designing-tables-best-practices"></a>


+ [Take the Tuning Table Design Tutorial](c_best-practices-tutorial-tuning-tables.md)
+ [Choose the Best Sort Key](c_best-practices-sort-key.md)
+ [Choose the Best Distribution Style](c_best-practices-best-dist-key.md)
+ [Let COPY Choose Compression Encodings](c_best-practices-use-auto-compression.md)
+ [Define Primary Key and Foreign Key Constraints](c_best-practices-defining-constraints.md)
+ [Use the Smallest Possible Column Size](c_best-practices-smallest-column-size.md)
+ [Use Date/Time Data Types for Date Columns](c_best-practices-timestamp-date-columns.md)

As you plan your database, there are key table design decisions that will heavily influence overall query performance\. These design choices also have a significant effect on storage requirements, which in turn affects query performance by reducing the number of I/O operations and minimizing the memory required to process queries\.

This section summarizes the most important design decisions and presents best practices for optimizing query performance\. [Designing Tables](t_Creating_tables.md) provides more detailed explanations and examples of table design options\.