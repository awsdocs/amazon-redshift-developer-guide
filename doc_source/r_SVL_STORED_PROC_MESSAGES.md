# SVL\_STORED\_PROC\_MESSAGES<a name="r_SVL_STORED_PROC_MESSAGES"></a>

You can query the system view SVL\_STORED\_PROC\_MESSAGES to get information about stored procedure messages\. Raised messages are logged even if the stored procedure call is aborted\. Each stored procedure call receives a query ID\. For more information about how to set the minimum level for logged messages, see stored\_proc\_log\_min\_messages\.

SVL\_STORED\_PROC\_MESSAGES is visible to all users\. Superusers can see all rows; regular users can see only their own data\. For more information, see Visibility of data in system tables and views\.

## Table columns<a name="r_SVL_STORED_PROC_MESSAGES-table-columns"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_SVL_STORED_PROC_MESSAGES.html)

## Sample query<a name="r_SVL_STORED_PROC_MESSAGES-sample-query"></a>

The following SQL statements show how to use SVL\_STORED\_PROC\_MESSAGES to review raised messages\.

```
-- Create and run a stored procedure
CREATE OR REPLACE PROCEDURE test_proc1(f1 int) AS
$$
BEGIN
    RAISE INFO 'Log Level: Input f1 is %',f1;
    RAISE NOTICE 'Notice Level: Input f1 is %',f1;
    EXECUTE 'select invalid';
    RAISE NOTICE 'Should not print this';

EXCEPTION WHEN OTHERS THEN
     raise exception 'EXCEPTION level: Exception Handling';
END;
$$ LANGUAGE plpgsql;

-- Call this stored procedure
CALL test_proc1(2);

-- Show raised messages with level higher than INFO
SELECT query, recordtime, loglevel, loglevel_text, trim(message) as message, aborted FROM svl_stored_proc_messages 
  WHERE loglevel > 30 AND query = 193 ORDER BY recordtime;

 query |         recordtime         | loglevel | loglevel_text |               message               | aborted
-------+----------------------------+----------+---------------+-------------------------------------+---------
   193 | 2020-03-17 23:57:18.277196 |       40 | NOTICE        | Notice Level: Input f1 is 2         |       1
   193 | 2020-03-17 23:57:18.277987 |       60 | EXCEPTION     | EXCEPTION level: Exception Handling |       1
(2 rows)

-- Show raised messages at EXCEPTION level
SELECT query, recordtime, loglevel, loglevel_text, trim(message) as message, aborted FROM svl_stored_proc_messages 
  WHERE loglevel_text = 'EXCEPTION' AND query = 193 ORDER BY recordtime;
        
 query |         recordtime         | loglevel | loglevel_text |               message               | aborted
-------+----------------------------+----------+---------------+-------------------------------------+---------
   193 | 2020-03-17 23:57:18.277987 |       60 | EXCEPTION     | EXCEPTION level: Exception Handling |       1
```

The following SQL statments show how to use SVL\_STORED\_PROC\_MESSAGES to review raised messages with the SET option when creating a stored procedure\. Because test\_proc\(\) has a minimum log level of NOTICE, only NOTICE, WARNING, and EXCEPTION level messages are logged in SVL\_STORED\_PROC\_MESSAGES\.

```
-- Create a stored procedure with minimum log level of NOTICE 
CREATE OR REPLACE PROCEDURE test_proc() AS
$$
BEGIN
    RAISE LOG 'Raise LOG messages';
    RAISE INFO 'Raise INFO messages';
    RAISE NOTICE 'Raise NOTICE messages';
    RAISE WARNING 'Raise WARNING messages';
    RAISE EXCEPTION 'Raise EXCEPTION messages';
    RAISE WARNING 'Raise WARNING messages again'; -- not reachable
END;
$$ LANGUAGE plpgsql SET stored_proc_log_min_messages = NOTICE; 

-- Call this stored procedure
CALL test_proc();

-- Show the raised messages
SELECT query, recordtime, loglevel_text, trim(message) as message, aborted FROM svl_stored_proc_messages 
  WHERE query = 149 ORDER BY recordtime;
  
 query |         recordtime         | loglevel_text |          message         | aborted
-------+----------------------------+---------------+--------------------------+---------
   149 | 2020-03-16 21:51:54.847627 | NOTICE        | Raise NOTICE messages    |       1
   149 | 2020-03-16 21:51:54.84766  | WARNING       | Raise WARNING messages   |       1
   149 | 2020-03-16 21:51:54.847668 | EXCEPTION     | Raise EXCEPTION messages |       1
(3 rows)
```