# Step 6: Vacuum and analyze the database<a name="tutorial-loading-data-vacuum"></a>

Whenever you add, delete, or modify a significant number of rows, you should run a VACUUM command and then an ANALYZE command\. A *vacuum* recovers the space from deleted rows and restores the sort order\. The ANALYZE command updates the statistics metadata, which enables the query optimizer to generate more accurate query plans\. For more information, see [Vacuuming tables](t_Reclaiming_storage_space202.md)\. 

If you load the data in sort key order, a vacuum is fast\. In this tutorial, you added a significant number of rows, but you added them to empty tables\. That being the case, there is no need to resort, and you didn't delete any rows\. COPY automatically updates statistics after loading an empty table, so your statistics should be up\-to\-date\. However, as a matter of good housekeeping, you complete this tutorial by vacuuming and analyzing your database\.

To vacuum and analyze the database, execute the following commands\.

```
vacuum;
analyze;
```

## Next step<a name="tutorial-loading-next-step7"></a>

[Step 7: Clean up your resources](tutorial-loading-data-clean-up.md)