# AZ64 encoding<a name="az64-encoding"></a>

AZ64 is Amazon's proprietary compression encoding algorithm designed to achieve a high compression ratio and improved query processing\. At its core, the AZ64 algorithm compresses smaller groups of data values and uses single instruction, multiple data \(SIMD\) instructions for parallel processing\. Use AZ64 to achieve significant storage savings and high performance for numeric, date, and time data types\. You can use AZ64 as the compression encoding when defining columns using CREATE TABLE and ALTER TABLE statements with the following data types:
+ SMALLINT
+ INTEGER
+ BIGINT
+ DECIMAL
+ DATE
+ TIMESTAMP
+ TIMESTAMPTZ