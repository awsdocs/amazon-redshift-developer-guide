# Logging errors and warnings in UDFs<a name="udf-logging-messages"></a>

You can use the Python logging module to create user\-defined error and warning messages in your UDFs\. Following query execution, you can query the [SVL\_UDF\_LOG](r_SVL_UDF_LOG.md) system view to retrieve logged messages\.

**Note**  
UDF logging consumes cluster resources and might affect system performance\. We recommend implementing logging only for development and troubleshooting\. 

During query execution, the log handler writes messages to the SVL\_UDF\_LOG system view, along with the corresponding function name, node, and slice\. The log handler writes one row to the SVL\_UDF\_LOG per message, per slice\. Messages are truncated to 4096 bytes\. The UDF log is limited to 500 rows per slice\. When the log is full, the log handler discards older messages and adds a warning message to SVL\_UDF\_LOG\.

**Note**  
The Amazon Redshift UDF log handler escapes newlines \( `\n` \), pipe \( `|` \) characters, and backslash \( `\` \) characters with a backslash \( `\` \) character\.

By default, the UDF log level is set to WARNING\. Messages with a log level of WARNING, ERROR, and CRITICAL are logged\. Messages with lower severity INFO, DEBUG, and NOTSET are ignored\. To set the UDF log level, use the Python logger method\. For example, the following sets the log level to INFO\.

```
logger.setLevel(logging.INFO)
```

For more information about using the Python logging module, see [Logging facility for Python](https://docs.python.org/2.7/library/logging.html) in the Python documentation\.

The following example creates a function named f\_pyerror that imports the Python logging module, instantiates the logger, and logs an error\.

```
CREATE OR REPLACE FUNCTION f_pyerror() 
RETURNS INTEGER
VOLATILE AS
$$
import logging

logger = logging.getLogger()
logger.setLevel(logging.INFO)
logger.info('Your info message here') 
return 0
$$ language plpythonu;
```

The following example queries SVL\_UDF\_LOG to view the message logged in the previous example\.

```
select funcname, node, slice, trim(message) as message 
from svl_udf_log;

  funcname  | query | node | slice |   message  
------------+-------+------+-------+------------------
  f_pyerror | 12345 |     1|     1 | Your info message here
```