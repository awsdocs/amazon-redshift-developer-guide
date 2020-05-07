# Step 7: Clean up your resources<a name="tutorial-loading-data-clean-up"></a>

Your cluster continues to accrue charges as long as it is running\. When you have completed this tutorial, you should return your environment to the previous state by following the steps in [Step 5: Revoke access and delete your sample cluster](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-clean-up-tasks.html) in the *Amazon Redshift Getting Started*\.

If you want to keep the cluster, but recover the storage used by the SSB tables, execute the following commands\.

```
drop table part;
drop table supplier;
drop table customer;
drop table dwdate;
drop table lineorder;
```

## Next<a name="tutorial-loading-next-summary"></a>

[Summary](tutorial-loading-data-summary.md)