# Columnar storage<a name="c_columnar_storage_disk_mem_mgmnt"></a>

Columnar storage for database tables is an important factor in optimizing analytic query performance because it drastically reduces the overall disk I/O requirements and reduces the amount of data you need to load from disk\.

The following series of illustrations describe how columnar data storage implements efficiencies and how that translates into efficiencies when retrieving data into memory\.

This first illustration shows how records from database tables are typically stored into disk blocks by row\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/03a-Rows-vs-Columns.png)

In a typical relational database table, each row contains field values for a single record\. In row\-wise database storage, data blocks store values sequentially for each consecutive column making up the entire row\. If block size is smaller than the size of a record, storage for an entire record may take more than one block\. If block size is larger than the size of a record, storage for an entire record may take less than one block, resulting in an inefficient use of disk space\. In online transaction processing \(OLTP\) applications, most transactions involve frequently reading and writing all of the values for entire records, typically one record or a small number of records at a time\. As a result, row\-wise storage is optimal for OLTP databases\.

The next illustration shows how with columnar storage, the values for each column are stored sequentially into disk blocks\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/redshift/latest/dg/images/03b-Rows-vs-Columns.png)

Using columnar storage, each data block stores values of a single column for multiple rows\. As records enter the system, Amazon Redshift transparently converts the data to columnar storage for each of the columns\.

In this simplified example, using columnar storage, each data block holds column field values for as many as three times as many records as row\-based storage\. This means that reading the same number of column field values for the same number of records requires a third of the I/O operations compared to row\-wise storage\. In practice, using tables with very large numbers of columns and very large row counts, storage efficiency is even greater\.

An added advantage is that, since each block holds the same type of data, block data can use a compression scheme selected specifically for the column data type, further reducing disk space and I/O\. For more information about compression encodings based on data types, see [Compression encodings](c_Compression_encodings.md)\.

The savings in space for storing data on disk also carries over to retrieving and then storing that data in memory\. Since many database operations only need to access or operate on one or a small number of columns at a time, you can save memory space by only retrieving blocks for columns you actually need for a query\. Where OLTP transactions typically involve most or all of the columns in a row for a small number of records, data warehouse queries commonly read only a few columns for a very large number of rows\. This means that reading the same number of column field values for the same number of rows requires a fraction of the I/O operations and uses a fraction of the memory that would be required for processing row\-wise blocks\. In practice, using tables with very large numbers of columns and very large row counts, the efficiency gains are proportionally greater\. For example, suppose a table contains 100 columns\. A query that uses five columns will only need to read about five percent of the data contained in the table\. This savings is repeated for possibly billions or even trillions of records for large databases\. In contrast, a row\-wise database would read the blocks that contain the 95 unneeded columns as well\.

Typical database block sizes range from 2 KB to 32 KB\. Amazon Redshift uses a block size of 1 MB, which is more efficient and further reduces the number of I/O requests needed to perform any database loading or other operations that are part of query execution\.