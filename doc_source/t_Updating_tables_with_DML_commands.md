# Updating tables with DML commands<a name="t_Updating_tables_with_DML_commands"></a>

Amazon Redshift supports standard data manipulation language \(DML\) commands \(INSERT, UPDATE, and DELETE\) that you can use to modify rows in tables\. You can also use the TRUNCATE command to do fast bulk deletes\.

**Note**  
We strongly encourage you to use the [COPY](r_COPY.md) command to load large amounts of data\. Using individual INSERT statements to populate a table might be prohibitively slow\. Alternatively, if your data already exists in other Amazon Redshift database tables, use INSERT INTO \.\.\. SELECT FROM or CREATE TABLE AS to improve performance\. For information, see [INSERT](r_INSERT_30.md) or [CREATE TABLE AS](r_CREATE_TABLE_AS.md)\.

If you insert, update, or delete a significant number of rows in a table, relative to the number of rows before the changes, run the ANALYZE and VACUUM commands against the table when you are done\. If a number of small changes accumulate over time in your application, you might want to schedule the ANALYZE and VACUUM commands to run at regular intervals\. For more information, see [Analyzing tables](t_Analyzing_tables.md) and [Vacuuming tables](t_Reclaiming_storage_space202.md)\.