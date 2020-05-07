# Step 9: Clean up your resources<a name="tutorial-tuning-tables-clean-up"></a>

Your cluster continues to accrue charges as long as it is running\. When you have completed this tutorial, you should return your environment to the previous state by following the steps in [Step 5: Revoke Access and Delete Your Sample Cluster](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-clean-up-tasks.html) in the *Amazon Redshift Getting Started*\.

If you want to keep the cluster, but recover the storage used by the SSB tables, execute the following commands\.

```
drop table part cascade;
drop table supplier cascade;
drop table customer cascade;
drop table dwdate cascade;
drop table lineorder cascade;
```

## Next step<a name="next-step-cleanup"></a>

[Summary](tutorial-tuning-tables-summary.md)