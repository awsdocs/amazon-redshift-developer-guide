# Tutorial: Building remote inference models<a name="tutorial_remote_inference"></a>

The following tutorial goes over the steps of how to create a [Random Cut Forest model](https://docs.aws.amazon.com/sagemaker/latest/dg/randomcutforest.html) that has been previously trained and deployed in Amazon SageMaker, outside of Amazon Redshift\. The Random Cut Forest algorithm detects anomalous data points within a dataset\. Creating a model with remote inference allows you to bring your Random Cut Forest SageMaker model into Amazon Redshift\. Then, in Amazon Redshift, you use SQL to perform predictions on a remote SageMaker endpoint\.

You can use a CREATE MODEL command to import a machine learning model from an Amazon SageMaker endpoint and prepare an Amazon Redshift prediction function\. When using the CREATE MODEL operation, you provide the SageMaker machine learning model’s endpoint name\.

In this tutorial, you create an Amazon Redshift machine learning model using a SageMaker model endpoint\. Once your machine learning model is ready, you can use it to perform predictions in Amazon Redshift\. First, you train and create an endpoint in Amazon SageMaker, and then you get the endpoint name\. Then, you use the CREATE MODEL command to create a model with Amazon Redshift ML\. Finally, you perform predictions on the model using the prediction function that the CREATE MODEL command generates\.

## Use case examples<a name="tutorial_remote_inference_tasks"></a>

You can use Random Cut Forest models and remote inference for anomaly detection in other datasets, such as predicting a rapid increase or decrease in e\-commerce transactions\. You could also predict significant changes in weather or seismic activity\.

**Tasks**
+ Prerequisites
+ Step 1: Deploy the Amazon SageMaker model
+ Step 2: Get the SageMaker model endpoint
+ Step 3: Load the data from Amazon S3 to Amazon Redshift
+ Step 4: Create a model with Amazon Redshift ML
+ Step 5: Perform predictions with the model

## Prerequisites<a name="tutorial_remote_inference_prereqs"></a>

To complete this tutorial, you must have the following prerequisites:
+ You have completed the [Administrative setup](https://docs.aws.amazon.com/redshift/latest/dg/admin-setup.html) for Amazon Redshift ML\.
+ You have downloaded the [NYC taxi dataset](https://s3.amazonaws.com/sagemaker-sample-files/datasets/tabular/anomaly_benchmark_taxi/NAB_nyc_taxi.csv), [created an Amazon S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/create-bucket-overview.html), and [uploaded the data into the Amazon S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/upload-objects.html)\.
+ You must train, deploy the SageMaker model and endpoint, and get the name of the SageMaker endpoint\. Use [this AWS CloudFormation template](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/template?stackName=RedshiftBYOM&templateURL=https://redshift-ml-multiclass.s3.amazonaws.com/redshift-byom-blog.yaml) to provision all the SageMaker resources in your AWS account automatically\.

## Step 1: Deploy the Amazon SageMaker model<a name="tutorial_remote_inference_step_deploy"></a>

1. To deploy the model, go to the Amazon SageMaker console, choose **Notebook instances** under **Notebook** in the navigation pane\.

1.  Choose **Open Jupyter** for the Jupyter notebook that was created by the CloudFormation template\.

1. Choose `bring-your-own-model-remote-inference.ipynb`\.

1. Set up the parameters to store the training input and output in Amazon S3 by replacing the following lines with your Amazon S3 bucket and prefix\.

   ```
   data_location=f"s3://{bucket}/{prefix}/",
   output_path=f"s3://{bucket}/{prefix}/output",
   ```

1. Choose the **fast\-forward** button to run all cells\.

## Step 2: Get the SageMaker model endpoint<a name="tutorial_remote_inference_step_endpoint"></a>

On the Amazon SageMaker console, under **Inference** in the navigation pane, choose **Endpoints** and find your model name\. You must copy your model’s endpoint name when you create the remote inference model in Amazon Redshift\.

## Step 3: Load the data from Amazon S3 to Amazon Redshift<a name="tutorial_remote_inference_step_load"></a>

Use the [ Amazon Redshift query editor v2](https://docs.aws.amazon.com/redshift/latest/mgmt/query-editor-v2-using.html) to run the following SQL commands in Amazon Redshift\. These commands drop the `rcf_taxi_data` table if it exists, create a table of the same name, and load the sample dataset into the table\.

```
DROP TABLE IF EXISTS public.rcf_taxi_data CASCADE;

CREATE TABLE public.rcf_taxi_data (ride_timestamp timestamp, nbr_passengers int);

COPY public.rcf_taxi_data
FROM
    's3://sagemaker-sample-files/datasets/tabular/anomaly_benchmark_taxi/NAB_nyc_taxi.csv'
    IAM_ROLE default
    IGNOREHEADER 1
    FORMAT AS CSV;
```

## Step 4: Create a model with Amazon Redshift ML<a name="tutorial_remote_inference_step_create_model"></a>

Run the following query to create a model in Amazon Redshift ML using the SageMaker model endpoint you got in the previous step\. Replace *`randomcutforest-xxxxxxxxx`* with your own SageMaker endpoint’s name\.

```
CREATE MODEL public.remote_random_cut_forest
FUNCTION remote_fn_rcf(int)
RETURNS decimal(10, 6) SAGEMAKER '<randomcutforest-xxxxxxxxx>' IAM_ROLE default;
```

### Check the model status \(optional\)<a name="tutorial_remote_inference_check_status"></a>

You can use the SHOW MODEL command to know when your model is ready\.

To check the model status, use the following SHOW MODEL operation\.

```
SHOW MODEL public.remote_random_cut_forest
```

The output shows the SageMaker endpoint and function name\.

```
+--------------------------+-----------------------------------------+
|        Model Name        |        remote_random_cut_forest         |
+--------------------------+-----------------------------------------+
|       Schema Name        |                 public                  |
|          Owner           |                 awsuser                 |
|      Creation Time       |        Wed, 15.06.2022 17:58:21         |
|       Model State        |                  READY                  |
|                          |                                         |
|       PARAMETERS:        |                                         |
|         Endpoint         |      <randomcutforest-xxxxxxxxx>        |
|      Function Name       |              remote_fn_rcf              |
|      Inference Type      |                 Remote                  |
| Function Parameter Types |                  int4                   |
|         IAM Role         |          default-aws-iam-role           |
+--------------------------+-----------------------------------------+
```

## Step 5: Perform predictions with the model<a name="tutorial_remote_inference_step_perform_predictions"></a>

The Amazon SageMaker Random Cut Forest algorithm is designed to detect anomalous data points within a dataset\. In this example, your model is designed to detect spikes in taxi rides due to important events\. You can use the model to predict anomalous events by generating an anomaly score for each data point\.

Use the following query to compute anomaly scores across the entire taxi dataset\. Note that you reference the function that you used in your CREATE MODEL statement in the previous step\.

```
SELECT
    ride_timestamp,
    nbr_passengers,
    public.remote_fn_rcf(nbr_passengers) AS score
FROM
    public.rcf_taxi_data;
```

### Check for high and low anomalies \(optional\)<a name="tutorial_remote_inference_anomalies"></a>

Run the following query to find any data points with scores greater than three standard deviations from the mean score\.

```
WITH score_cutoff AS (
    SELECT
        STDDEV(public.remote_fn_rcf(nbr_passengers)) AS std,
        AVG(public.remote_fn_rcf(nbr_passengers)) AS mean,
        (mean + 3 * std) AS score_cutoff_value
    FROM
        public.rcf_taxi_data
)
SELECT
    ride_timestamp,
    nbr_passengers,
    public.remote_fn_rcf(nbr_passengers) AS score
FROM
    public.rcf_taxi_data
WHERE
    score > (
        SELECT
            score_cutoff_value
        FROM
            score_cutoff
    )
ORDER BY
    2 DESC;
```

Run the following query to find any data points with scores greater than three standard deviations from the mean score\.

```
WITH score_cutoff AS (
    SELECT
        STDDEV(public.remote_fn_rcf(nbr_passengers)) AS std,
        AVG(public.remote_fn_rcf(nbr_passengers)) AS mean,
        (mean - 3 * std) AS score_cutoff_value
    FROM
        public.rcf_taxi_data
)
SELECT
    ride_timestamp,
    nbr_passengers,
    public.remote_fn_rcf(nbr_passengers) AS score
FROM
    public.rcf_taxi_data
WHERE
    score < (
        SELECT
            score_cutoff_value
        FROM
            score_cutoff
    )
ORDER BY
    2 DESC;
```

## Related topics<a name="tutorial_remote_inference_related_topics"></a>

For more information about Amazon Redshift ML, see the following documentation:
+ [Costs for using Amazon Redshift ML](https://docs.aws.amazon.com/redshift/latest/dg/cost.html)
+ [CREATE MODEL operation](https://docs.aws.amazon.com/redshift/latest/dg/r_CREATE_MODEL.html)
+ [EXPLAIN\_MODEL function](https://docs.aws.amazon.com/redshift/latest/dg/r_explain_model_function.html)

For more information about machine learning, see the following documentation:
+ [Machine learning overview](https://docs.aws.amazon.com/redshift/latest/dg/machine_learning_overview.html)
+ [Machine learning for novices and experts](https://docs.aws.amazon.com/redshift/latest/dg/novice_expert.html)
+ [What Is Fairness and Model Explainability for Machine Learning Predictions?](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-fairness-and-explainability.html)