# Zstandard encoding<a name="zstd-encoding"></a>

Zstandard \(ZSTD\) encoding provides a high compression ratio with very good performance across diverse datasets\. ZSTD works especially well with CHAR and VARCHAR columns that store a wide range of long and short strings, such as product descriptions, user comments, logs, and JSON strings\. Where some algorithms, such as [Delta](c_Delta_encoding.md) encoding or [Mostly](c_MostlyN_encoding.md) encoding, can potentially use more storage space than no compression, ZSTD is very unlikely to increase disk usage\. 

ZSTD supports SMALLINT, INTEGER, BIGINT, DECIMAL, REAL, DOUBLE PRECISION, BOOLEAN, CHAR, VARCHAR, DATE, TIMESTAMP, and TIMESTAMPTZ data types\.