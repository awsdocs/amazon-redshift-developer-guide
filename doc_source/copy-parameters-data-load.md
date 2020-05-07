# Data load operations<a name="copy-parameters-data-load"></a>

Manage the default behavior of the load operation for troubleshooting or to reduce load times by specifying the following parameters\. 
+ [COMPROWS](#copy-comprows) 
+ [COMPUPDATE](#copy-compupdate) 
+ [MAXERROR](#copy-maxerror) 
+ [NOLOAD](#copy-noload) 
+ [STATUPDATE](#copy-statupdate) <a name="copy-data-load-parameters"></a>Parameters

COMPROWS *numrows*   <a name="copy-comprows"></a>
Specifies the number of rows to be used as the sample size for compression analysis\. The analysis is run on rows from each data slice\. For example, if you specify `COMPROWS 1000000` \(1,000,000\) and the system contains four total slices, no more than 250,000 rows for each slice are read and analyzed\.  
If COMPROWS isn't specified, the sample size defaults to 100,000 for each slice\. Values of COMPROWS lower than the default of 100,000 rows for each slice are automatically upgraded to the default value\. However, automatic compression will not take place if the amount of data being loaded is insufficient to produce a meaningful sample\.  
If the COMPROWS number is greater than the number of rows in the input file, the COPY command still proceeds and runs the compression analysis on all of the available rows\. The accepted range for this argument is a number between 1000 and 2147483647 \(2,147,483,647\)\.

COMPUPDATE \[ PRESET \| \{ ON \| TRUE \} \| \{ OFF \| FALSE \} \]  <a name="copy-compupdate"></a>
Controls whether compression encodings are automatically applied during a COPY\.   
When COMPUPDATE is PRESET, the COPY command chooses the compression encoding for each column if the target table is empty; even if the columns already have encodings other than RAW\. Currently specified column encodings can be replaced\. Encoding for each column is based on the column data type\. No data is sampled\. Amazon Redshift automatically assigns compression encoding as follows:  
+ Columns that are defined as sort keys are assigned RAW compression\.
+ Columns that are defined as BOOLEAN, REAL, or DOUBLE PRECISION data types are assigned RAW compression\.
+ Columns that are defined as SMALLINT, INTEGER, BIGINT, DECIMAL, DATE, TIMESTAMP, or TIMESTAMPTZ are assigned AZ64 compression\.
+ Columns that are defined as CHAR or VARCHAR are assigned LZO compression\.
When COMPUPDATE is omitted, the COPY command chooses the compression encoding for each column only if the target table is empty and you have not specified an encoding \(other than RAW\) for any of the columns\. The encoding for each column is determined by Amazon Redshift\. No data is sampled\.   
When COMPUPDATE is ON \(or TRUE\), or COMPUPDATE is specified without an option, the COPY command applies automatic compression if the table is empty; even if the table columns already have encodings other than RAW\. Currently specified column encodings can be replaced\. Encoding for each column is based on an analysis of sample data\. For more information, see [Loading tables with automatic compression](c_Loading_tables_auto_compress.md)\.  
When COMPUPDATE is OFF \(or FALSE\), automatic compression is disabled\. Column encodings aren't changed\.  
For information about the system table to analyze compression, see [STL\_ANALYZE\_COMPRESSION](r_STL_ANALYZE_COMPRESSION.md)\. 

MAXERROR \[AS\] *error\_count*   <a name="copy-maxerror"></a>
If the load returns the *error\_count* number of errors or greater, the load fails\. If the load returns fewer errors, it continues and returns an INFO message that states the number of rows that could not be loaded\. Use this parameter to allow loads to continue when certain rows fail to load into the table because of formatting errors or other inconsistencies in the data\.   
Set this value to `0` or `1` if you want the load to fail as soon as the first error occurs\. The AS keyword is optional\. The MAXERROR default value is `0` and the limit is `100000`\.  
 The actual number of errors reported might be greater than the specified MAXERROR because of the parallel nature of Amazon Redshift\. If any node in the Amazon Redshift cluster detects that MAXERROR has been exceeded, each node reports all of the errors it has encountered\.

NOLOAD   <a name="copy-noload"></a>
Checks the validity of the data file without actually loading the data\. Use the NOLOAD parameter to make sure that your data file loads without any errors before running the actual data load\. Running COPY with the NOLOAD parameter is much faster than loading the data because it only parses the files\.

STATUPDATE \[ \{ ON \| TRUE \} \| \{ OFF \| FALSE \} \]  <a name="copy-statupdate"></a>
Governs automatic computation and refresh of optimizer statistics at the end of a successful COPY command\. By default, if the STATUPDATE parameter isn't used, statistics are updated automatically if the table is initially empty\.  
Whenever ingesting data into a nonempty table significantly changes the size of the table, we recommend updating statistics either by running an [ANALYZE](r_ANALYZE.md) command or by using the STATUPDATE ON argument\.  
With STATUPDATE ON \(or TRUE\), statistics are updated automatically regardless of whether the table is initially empty\. If STATUPDATE is used, the current user must be either the table owner or a superuser\. If STATUPDATE is not specified, only INSERT permission is required\.  
With STATUPDATE OFF \(or FALSE\), statistics are never updated\.  
For additional information, see [Analyzing tables](t_Analyzing_tables.md)\.