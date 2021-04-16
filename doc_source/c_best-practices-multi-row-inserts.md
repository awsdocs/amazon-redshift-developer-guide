# Use a multi\-row insert<a name="c_best-practices-multi-row-inserts"></a>

If a COPY command is not an option and you require SQL inserts, use a multi\-row insert whenever possible\. Data compression is inefficient when you add data only one row or a few rows at a time\.

Multi\-row inserts improve performance by batching up a series of inserts\. The following example inserts three rows into a four\-column table using a single INSERT statement\. This is still a small insert, shown simply to illustrate the syntax of a multi\-row insert\.

```
insert into category_stage values
(default, default, default, default),
(20, default, 'Country', default),
(21, 'Concerts', 'Rock', default);
```

For more details and examples, see [INSERT](r_INSERT_30.md)\. 