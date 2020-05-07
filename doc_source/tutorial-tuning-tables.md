# Tutorial: Tuning table design<a name="tutorial-tuning-tables"></a>

In this tutorial, you will learn how to optimize the design of your tables\. You will start by creating tables based on the Star Schema Benchmark \(SSB\) schema without sort keys, distribution styles, and compression encodings\. You will load the tables with test data and test system performance\. Next, you will apply best practices to recreate the tables using sort keys and distribution styles\. You will load the tables with test data using automatic compression and then you will test performance again so that you can compare the performance benefits of well\-designed tables\.

**Estimated time:** 60 minutes

**Estimated cost:** $1\.00 per hour for the cluster

## Prerequisites<a name="tutorial-tuning-tables-prerequisites"></a>

You will need your AWS credentials \(access key ID and secret access key\) to load test data from Amazon S3\. If you need to create new access keys, go to [Administering Access Keys for IAM Users](https://docs.aws.amazon.com/IAM/latest/UserGuide/ManagingCredentials.html)\.

## Steps<a name="tutorial-tuning-tables-steps"></a>
+ [Step 1: Create a test data set](tutorial-tuning-tables-create-test-data.md) 
+ [Step 2: Test system performance to establish a baseline](tutorial-tuning-tables-test-performance.md)
+ [Step 3: Select sort keys](tutorial-tuning-tables-sort-keys.md) 
+ [Step 4: Select distribution styles](tutorial-tuning-tables-distribution.md)
+ [Step 5: Review compression encodings](tutorial-tuning-tables-compression.md) 
+ [Step 6: Recreate the test data set](tutorial-tuning-tables-recreate-test-data.md)
+ [Step 7: Retest system performance after tuning](tutorial-tuning-tables-retest.md)
+ [Step 8: Evaluate the results](tutorial-tuning-tables-evaluate.md) 
+ [Step 9: Clean up your resources](tutorial-tuning-tables-clean-up.md)
+ [Summary](tutorial-tuning-tables-summary.md) 