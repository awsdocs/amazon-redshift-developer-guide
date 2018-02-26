# File Compression Parameters<a name="copy-parameters-file-compression"></a>

You can load from compressed data files by specifying the following parameters\. File Compression Parameters

BZIP2   <a name="copy-bzip2"></a>
A value that specifies that the input file or files are in compressed bzip2 format \(\.bz2 files\)\. The COPY operation reads each compressed file and uncompresses the data as it loads\.

GZIP   <a name="copy-gzip"></a>
A value that specifies that the input file or files are in compressed gzip format \(\.gz files\)\. The COPY operation reads each compressed file and uncompresses the data as it loads\.

LZOP   <a name="copy-lzop"></a>
A value that specifies that the input file or files are in compressed lzop format \(\.lzo files\)\. The COPY operation reads each compressed file and uncompresses the data as it loads\.  
COPY does not support files that are compressed using the lzop *\-\-filter* option\.