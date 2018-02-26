# Automatic Analysis of New Tables<a name="c_ctas_auto_analyze"></a>

Amazon Redshift automatically analyzes tables that you create with the following commands: 

+ CREATE TABLE AS \(CTAS\) 

+ CREATE TEMP TABLE AS 

+ SELECT INTO 

You do not need to run the ANALYZE command on these tables when they are first created\. If you modify them, you should analyze them in the same way as other tables\. 