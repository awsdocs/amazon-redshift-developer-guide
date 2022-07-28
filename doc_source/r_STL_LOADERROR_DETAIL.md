# STL\_LOADERROR\_DETAIL<a name="r_STL_LOADERROR_DETAIL"></a>

Displays a log of data parse errors that occurred while using a COPY command to load tables\. To conserve disk space, a maximum of 20 errors per node slice are logged for each load operation\.

 A parse error occurs when Amazon Redshift cannot parse a field in a data row while loading it into a table\. For example, if a table column is expecting an integer data type and the data file contains a string of letters in that field, it causes a parse error\.

Query STL\_LOADERROR\_DETAIL for additional details, such as the exact data row and column where a parse error occurred, after you query [STL\_LOAD\_ERRORS](r_STL_LOAD_ERRORS.md) to find out general information about the error\.

The STL\_LOADERROR\_DETAIL view contains all data columns including and prior to the column where the parse error occurred\. Use the VALUE field to see the data value that was actually parsed in this column, including the columns that parsed correctly up to the error\.

This view is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see [Visibility of data in system tables and views](c_visibility-of-data.md)\.

## Table columns<a name="r_STL_LOADERROR_DETAIL-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STL_LOADERROR_DETAIL.html)

## Sample query<a name="r_STL_LOADERROR_DETAIL-samplequery"></a>

The following query joins STL\_LOAD\_ERRORS to STL\_LOADERROR\_DETAIL to view the details of a parse error that occurred while loading the EVENT table, which has a table ID of 100133: 

```
select d.query, d.line_number, d.value,
le.raw_line, le.err_reason
from stl_loaderror_detail d, stl_load_errors le
where
d.query = le.query
and tbl = 100133;
```

The following sample output shows the columns that loaded successfully, including the column with the error\. In this example, two columns successfully loaded before the parse error occurred in the third column, where a character string was incorrectly parsed for a field expecting an integer\. Because the field expected an integer, it parsed the string "aaa", which is uninitialized data, as a null and generated a parse error\. The output shows the raw value, parsed value, and error reason: 

```
query  | line_number | value | raw_line | err_reason
-------+-------------+-------+----------+----------------
4      |      3      |  1201 |  1201    | Invalid digit
4      |      3      |   126 |   126    | Invalid digit
4      |      3      |       |   aaa    | Invalid digit
(3 rows)
```

When a query joins STL\_LOAD\_ERRORS and STL\_LOADERROR\_DETAIL, it displays an error reason for each column in the data row, which simply means that an error occurred in that row\. The last row in the results is the actual column where the parse error occurred\.