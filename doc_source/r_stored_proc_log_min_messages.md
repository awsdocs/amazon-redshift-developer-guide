# stored\_proc\_log\_min\_messages<a name="r_stored_proc_log_min_messages"></a>

## Values \(default in bold\)<a name="r_stored_proc_log_min_messages-values"></a>

**LOG**, INFO, NOTICE, WARNING, EXCEPTION

## Description<a name="description"></a>

Specifies the minimum logging level of raised stored procedure messages\. Messages at or above the specfied level are logged\. The default is LOG \(all messages are logged\)\. The order of log levels from highest to lowest is: 

1. EXCEPTION

1. WARNING

1. NOTICE

1. INFO

1. LOG

For example if you specify a value of NOTICE, then messages are only logged for NOTICE, WARNING, and EXCEPTION\. 