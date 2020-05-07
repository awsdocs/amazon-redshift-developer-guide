# Runlength encoding<a name="c_Runlength_encoding"></a>

Runlength encoding replaces a value that is repeated consecutively with a token that consists of the value and a count of the number of consecutive occurrences \(the length of the run\)\. A separate dictionary of unique values is created for each block of column values on disk\. \(An Amazon Redshift disk block occupies 1 MB\.\) This encoding is best suited to a table in which data values are often repeated consecutively, for example, when the table is sorted by those values\.

For example, if a column in a large dimension table has a predictably small domain, such as a COLOR column with fewer than 10 possible values, these values are likely to fall in long sequences throughout the table, even if the data is not sorted\.

We do not recommend applying runlength encoding on any column that is designated as a sort key\. Range\-restricted scans perform better when blocks contain similar numbers of rows\. If sort key columns are compressed much more highly than other columns in the same query, range\-restricted scans might perform poorly\.

The following table uses the COLOR column example to show how the runlength encoding works:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/c_Runlength_encoding.html)