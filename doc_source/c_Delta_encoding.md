# Delta encoding<a name="c_Delta_encoding"></a>

Delta encodings are very useful for datetime columns\.

Delta encoding compresses data by recording the difference between values that follow each other in the column\. This difference is recorded in a separate dictionary for each block of column values on disk\. \(An Amazon Redshift disk block occupies 1 MB\.\) For example, if the column contains 10 integers in sequence from 1 to 10, the first will be stored as a 4\-byte integer \(plus a 1\-byte flag\), and the next 9 will each be stored as a byte with the value 1, indicating that it is one greater than the previous value\.

Delta encoding comes in two variations: 
+ DELTA records the differences as 1\-byte values \(8\-bit integers\)
+ DELTA32K records differences as 2\-byte values \(16\-bit integers\)

If most of the values in the column could be compressed by using a single byte, the 1\-byte variation is very effective; however, if the deltas are larger, this encoding, in the worst case, is somewhat less effective than storing the uncompressed data\. Similar logic applies to the 16\-bit version\.

If the difference between two values exceeds the 1\-byte range \(DELTA\) or 2\-byte range \(DELTA32K\), the full original value is stored, with a leading 1\-byte flag\. The 1\-byte range is from \-127 to 127, and the 2\-byte range is from \-32K to 32K\.

The following table shows how a delta encoding works for a numeric column:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/c_Delta_encoding.html)