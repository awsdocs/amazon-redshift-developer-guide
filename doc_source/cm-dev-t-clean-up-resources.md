# Step 7: Clean up your resources<a name="cm-dev-t-clean-up-resources"></a>

If you deployed a cluster in order to complete this exercise, when you are finished with the exercise, you should delete the cluster so that it will stop accruing charges to your AWS account\.

To delete the cluster, follow the steps in [Deleting a cluster](https://docs.aws.amazon.com/redshift/latest/mgmt/managing-clusters-console.html#delete-cluster) in the Amazon Redshift Cluster Management Guide\.

If you want to keep the cluster, you might want to keep the sample data for reference\. Most of the examples in this guide use the tables you created in this exercise\. The size of the data will not have any significant effect on your available storage\.

If you want to keep the cluster, but want to clean up the sample data, you can run the following command to drop the TICKIT database:

```
drop database tickit;
```

If you didn't create a TICKIT database, or if you don't want to drop the database, run the following commands to drop just the tables:

```
drop table testtable;
drop table users;
drop table venue;
drop table category;
drop table date;
drop table event;
drop table listing;
drop table sales;
```