# File compression parameters<a name="copy-parameters-file-compression"></a>

You can load from compressed data files by specifying the following parameters\. File compression parameters

BZIP2   <a name="copy-bzip2"></a>
A value that specifies that the input file or files are in compressed bzip2 format \(\.bz2 files\)\. The COPY operation reads each compressed file and uncompresses the data as it loads\.

GZIP   <a name="copy-gzip"></a>
A value that specifies that the input file or files are in compressed gzip format \(\.gz files\)\. The COPY operation reads each compressed file and uncompresses the data as it loads\.

LZOP   <a name="copy-lzop"></a>
A value that specifies that the input file or files are in compressed lzop format \(\.lzo files\)\. The COPY operation reads each compressed file and uncompresses the data as it loads\.  
COPY doesn't support files that are compressed using the lzop *\-\-filter* option\.

ZSTD   <a name="copy-zstd"></a>
A value that specifies that the input file or files are in compressed Zstandard format \(\.zst files\)\. The COPY operation reads each compressed file and uncompresses the data as it loads\. For more information, see [Zstandard encoding](zstd-encoding.md)\.  
ZSTD is supported only with COPY from Amazon S3\.