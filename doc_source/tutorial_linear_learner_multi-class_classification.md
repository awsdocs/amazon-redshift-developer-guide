# Tutorial: Building multi\-class classification models with linear learner<a name="tutorial_linear_learner_multi-class_classification"></a>

In this tutorial, you create a linear learner model with data from Amazon S3, and then run prediction queries with the model using Amazon Redshift ML\. The SageMaker linear learner algorithm solves either regression or classification problems\. To learn more about regression and multi\-class classification problems, see [Problem types for the machine learning paradigms](https://docs.aws.amazon.com/sagemaker/latest/dg/algorithms-choose.html#basic-machine-learning-paradigms) in the Amazon SageMaker Developer Guide\. In this tutorial, you solve a multi\-class classification problem\. The linear learner algorithm trains many models in parallel, and automatically determines the most optimized model\. You use the CREATE MODEL operation in Amazon Redshift, which creates your linear learner model using SageMaker and sends the prediction function to Amazon Redshift\. For more information about the linear learner algorithm, see the [Linear Learner Algorithm](https://docs.aws.amazon.com/sagemaker/latest/dg/linear-learner.html) in the Amazon SageMaker Developer Guide\.

You can use a CREATE MODEL command to export training data, train a model, import the model, and prepare an Amazon Redshift prediction function\. Use the CREATE MODEL operation to specify training data either as a table or a SELECT statement\.

Linear learner models optimize either continuous objectives or discrete objectives\. Continuous objectives are used for regression, while discrete variables are used for classification\. Some methods provide a solution for only continuous objectives, such as a regression method\. The linear learner algorithm provides an increase in speed over naive hyperparameter optimization techniques, such as the Naive Bayes technique\. A naive optimization technique assumes that each input variable is independent\. The linear learner algorithm trains many models in parallel and selects the most optimized model\. A similar algorithm is XGBoost, which combines estimates from a set of simpler and weaker models to make predictions\. To learn more about XGBoost, see [XGBoost algorithm](https://docs.aws.amazon.com/sagemaker/latest/dg/xgboost.html) in the Amazon SageMaker Developer Guide\.

To use the linear learner algorithm, you must provide columns representing the dimensions of the inputs, and rows representing the observations\. For more information about the linear learner algorithm, see the [Linear Learner Algorithm ](https://docs.aws.amazon.com/sagemaker/latest/dg/linear-learner.html)in the Amazon SageMaker Developer Guide\. 

In this tutorial, you build a linear learner model that predicts the types of cover for a given area\. You use the CREATE MODEL command on the [Covertype dataset](https://archive.ics.uci.edu/ml/datasets/covertype) from the UCI Machine Learning Repository\. Then, you use the prediction function created by the command to determine the types of cover in a wilderness area\. A forest cover type is usually a type of tree\. The inputs that Redshift ML will use to create the model include soil type, distance to roadways, and wilderness area designation\. For more information about the dataset, see the [Covertype Dataset](https://archive.ics.uci.edu/ml/datasets/covertype) from the UCI Machine Learning Repository\.

## Use case examples<a name="tutorial_linear_learner_multi-class_classification_tasks"></a>

You can solve other multi\-class classification problems with linear learner with Amazon Redshift ML, such as predicting the species of a plant from an image\. You could also predict the quantity of a product that a customer will purchase\.

**Tasks**
+ Prerequisites
+ Step 1: Load the data from Amazon S3 to Amazon Redshift
+ Step 2: Create the machine learning model
+ Step 3: Validate the model

## Prerequisites<a name="tutorial_linear_learner_multi-class_classification_prereqs"></a>

To complete this tutorial, you must complete the [Administrative setup](https://docs.aws.amazon.com/redshift/latest/dg/admin-setup.html) for Amazon Redshift ML\.

## Step 1: Load the data from Amazon S3 to Amazon Redshift<a name="tutorial_linear_learner_multi-class_classification_step_load"></a>

Use the [Amazon Redshift query editor v2](https://docs.aws.amazon.com/redshift/latest/mgmt/query-editor-v2-using.html) to run the following queries\. These queries load the sample data into Redshift and divide the data into a training set and a validation set\.

1. The following query creates the `covertype_data` table\.

   ```
   CREATE TABLE public.covertype_data (
       elevation bigint ENCODE az64,
       aspect bigint ENCODE az64,
       slope bigint ENCODE az64,
       horizontal_distance_to_hydrology bigint ENCODE az64,
       vertical_distance_to_hydrology bigint ENCODE az64,
       horizontal_distance_to_roadways bigint ENCODE az64,
       hillshade_9am bigint ENCODE az64,
       hillshade_noon bigint ENCODE az64,
       hillshade_3pm bigint ENCODE az64,
       horizontal_distance_to_fire_points bigint ENCODE az64,
       wilderness_area1 bigint ENCODE az64,
       wilderness_area2 bigint ENCODE az64,
       wilderness_area3 bigint ENCODE az64,
       wilderness_area4 bigint ENCODE az64,
       soil_type1 bigint ENCODE az64,
       soil_type2 bigint ENCODE az64,
       soil_type3 bigint ENCODE az64,
       soil_type4 bigint ENCODE az64,
       soil_type5 bigint ENCODE az64,
       soil_type6 bigint ENCODE az64,
       soil_type7 bigint ENCODE az64,
       soil_type8 bigint ENCODE az64,
       soil_type9 bigint ENCODE az64,
       soil_type10 bigint ENCODE az64,
       soil_type11 bigint ENCODE az64,
       soil_type12 bigint ENCODE az64,
       soil_type13 bigint ENCODE az64,
       soil_type14 bigint ENCODE az64,
       soil_type15 bigint ENCODE az64,
       soil_type16 bigint ENCODE az64,
       soil_type17 bigint ENCODE az64,
       soil_type18 bigint ENCODE az64,
       soil_type19 bigint ENCODE az64,
       soil_type20 bigint ENCODE az64,
       soil_type21 bigint ENCODE az64,
       soil_type22 bigint ENCODE az64,
       soil_type23 bigint ENCODE az64,
       soil_type24 bigint ENCODE az64,
       soil_type25 bigint ENCODE az64,
       soil_type26 bigint ENCODE az64,
       soil_type27 bigint ENCODE az64,
       soil_type28 bigint ENCODE az64,
       soil_type29 bigint ENCODE az64,
       soil_type30 bigint ENCODE az64,
       soil_type31 bigint ENCODE az64,
       soil_type32 bigint ENCODE az64,
       soil_type33 bigint ENCODE az64,
       soil_type34 bigint ENCODE az64,
       soil_type35 bigint ENCODE az64,
       soil_type36 bigint ENCODE az64,
       soil_type37 bigint ENCODE az64,
       soil_type38 bigint ENCODE az64,
       soil_type39 bigint ENCODE az64,
       soil_type40 bigint ENCODE az64,
       cover_type bigint ENCODE az64
   ) DISTSTYLE AUTO;
   ```

1. The following query copies the sample data from the [Covertype dataset ](https://archive.ics.uci.edu/ml/datasets/covertype)in Amazon S3 to the `covertype_data` table you created previously in Amazon Redshift\.

   ```
   COPY public.covertype_data
   FROM
       's3://redshift-ml-multiclass/covtype.data.gz' IAM_ROLE DEFAULT gzip DELIMITER ',' REGION 'us-east-1';
   ```

1. By manually splitting the data, you will be able to verify the accuracy of the model by allocating an additional testing set\. The following query splits the data into three sets\. The `covertype_training` table is for training, the `covertype_validation` table is for validation, and the `covertype_test` table is for testing your model\. You will use the training set to train your model and the validation set to validate the model’s development\. Then, you use the testing set to test the performance of the model and see if the model is overfitting or underfitting the dataset\.

   ```
   CREATE TABLE public.covertype_data_prep AS
   SELECT
       a.*,
       CAST (random() * 100 AS int) AS data_group_id
   FROM
       public.covertype_data a;
   
   --training dataset
    CREATE TABLE public.covertype_training as
   SELECT
       *
   FROM
       public.covertype_data_prep
   WHERE
       data_group_id < 80;
   
   --validation dataset
    CREATE TABLE public.covertype_validation AS
   SELECT
       *
   FROM
       public.covertype_data_prep
   WHERE
       data_group_id BETWEEN 80
       AND 89;
   
   --test dataset
    CREATE TABLE public.covertype_test AS
   SELECT
       *
   FROM
       public.covertype_data_prep
   WHERE
       data_group_id > 89;
   ```

## Step 2: Create the machine learning model<a name="tutorial_linear_learner_multi-class_classification_step_create_model"></a>

In this step, you use the CREATE MODEL statement to create your machine learning model with the linear learner algorithm\. 

The following query creates the linear learner model with the CREATE MODEL operation using your S3 bucket\. Replace *`DOC-EXAMPLE-BUCKET`* with your own S3 bucket\.

```
CREATE MODEL forest_cover_type_model
FROM
    (
        SELECT
            Elevation,
            Aspect,
            Slope,
            Horizontal_distance_to_hydrology,
            Vertical_distance_to_hydrology,
            Horizontal_distance_to_roadways,
            HIllshade_9am,
            Hillshade_noon,
            Hillshade_3pm,
            Horizontal_Distance_To_Fire_Points,
            Wilderness_Area1,
            Wilderness_Area2,
            Wilderness_Area3,
            Wilderness_Area4,
            soil_type1,
            Soil_Type2,
            Soil_Type3,
            Soil_Type4,
            Soil_Type5,
            Soil_Type6,
            Soil_Type7,
            Soil_Type8,
            Soil_Type9,
            Soil_Type10,
            Soil_Type11,
            Soil_Type12,
            Soil_Type13,
            Soil_Type14,
            Soil_Type15,
            Soil_Type16,
            Soil_Type17,
            Soil_Type18,
            Soil_Type19,
            Soil_Type20,
            Soil_Type21,
            Soil_Type22,
            Soil_Type23,
            Soil_Type24,
            Soil_Type25,
            Soil_Type26,
            Soil_Type27,
            Soil_Type28,
            Soil_Type29,
            Soil_Type30,
            Soil_Type31,
            Soil_Type32,
            Soil_Type33,
            Soil_Type34,
            Soil_Type36,
            Soil_Type37,
            Soil_Type38,
            Soil_Type39,
            Soil_Type40,
            Cover_type
        from
            public.covertype_training
    ) TARGET cover_type FUNCTION predict_cover_type IAM_ROLE default MODEL_TYPE LINEAR_LEARNER PROBLEM_TYPE MULTICLASS_CLASSIFICATION OBJECTIVE 'Accuracy' SETTINGS (
        S3_BUCKET '<DOC-EXAMPLE-BUCKET>',
        S3_GARBAGE_COLLECT OFF,
        MAX_RUNTIME 15000
    );
```

### Show the status of model training \(optional\)<a name="tutorial_linear_learner_multi-class_classification_show_status"></a>

You can use the SHOW MODEL command to know when your model is ready\.

Use the following query to monitor the progress of the model training\.

```
SHOW MODEL forest_cover_type_model;
```

When the model is ready, the output of the previous operation should look similar to the following example\. Note that the output provides the `validation:multiclass_accuracy` metric, which you can view on the righthand side of the following example\. Multi\-class accuracy measures the percentage of data points that are classified correctly by the model\. You will use multi\-class accuracy to validate the accuracy of the model in the next step\.

```
+--------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|              Key               |                                                                                                                                                                                                                                                                                                                                                                                                             Value                                                                                                                                                                                                                                                                                                                                                                                                              |
+--------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Model Name                     | forest_cover_type_model                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Schema Name                    | public                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| Owner                          | awsuser                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Creation Time                  | Tue, 12.07.2022 20:24:32                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| Model State                    | READY                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| validation:multiclass_accuracy |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       0.724952 |
| Estimated Cost                 |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       5.341750 |
|                                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| TRAINING DATA:                 |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Query                          | SELECT ELEVATION, ASPECT, SLOPE, HORIZONTAL_DISTANCE_TO_HYDROLOGY, VERTICAL_DISTANCE_TO_HYDROLOGY, HORIZONTAL_DISTANCE_TO_ROADWAYS, HILLSHADE_9AM, HILLSHADE_NOON, HILLSHADE_3PM , HORIZONTAL_DISTANCE_TO_FIRE_POINTS, WILDERNESS_AREA1, WILDERNESS_AREA2, WILDERNESS_AREA3, WILDERNESS_AREA4, SOIL_TYPE1, SOIL_TYPE2, SOIL_TYPE3, SOIL_TYPE4, SOIL_TYPE5, SOIL_TYPE6, SOIL_TYPE7, SOIL_TYPE8, SOIL_TYPE9, SOIL_TYPE10 , SOIL_TYPE11, SOIL_TYPE12 , SOIL_TYPE13 , SOIL_TYPE14, SOIL_TYPE15, SOIL_TYPE16, SOIL_TYPE17, SOIL_TYPE18, SOIL_TYPE19, SOIL_TYPE20, SOIL_TYPE21, SOIL_TYPE22, SOIL_TYPE23, SOIL_TYPE24, SOIL_TYPE25, SOIL_TYPE26, SOIL_TYPE27, SOIL_TYPE28, SOIL_TYPE29, SOIL_TYPE30, SOIL_TYPE31, SOIL_TYPE32, SOIL_TYPE33, SOIL_TYPE34, SOIL_TYPE36, SOIL_TYPE37, SOIL_TYPE38, SOIL_TYPE39, SOIL_TYPE40, COVER_TYPE |
|                                | FROM PUBLIC.COVERTYPE_TRAINING                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Target Column                  | COVER_TYPE                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|                                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| PARAMETERS:                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Model Type                     | linear_learner                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Problem Type                   | MulticlassClassification                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| Objective                      | Accuracy                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| AutoML Job Name                | redshiftml-20220712202432187659                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Function Name                  | predict_cover_type                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Function Parameters            | elevation aspect slope horizontal_distance_to_hydrology vertical_distance_to_hydrology horizontal_distance_to_roadways hillshade_9am hillshade_noon hillshade_3pm horizontal_distance_to_fire_points wilderness_area1 wilderness_area2 wilderness_area3 wilderness_area4 soil_type1 soil_type2 soil_type3 soil_type4 soil_type5 soil_type6 soil_type7 soil_type8 soil_type9 soil_type10 soil_type11 soil_type12 soil_type13 soil_type14 soil_type15 soil_type16 soil_type17 soil_type18 soil_type19 soil_type20 soil_type21 soil_type22 soil_type23 soil_type24 soil_type25 soil_type26 soil_type27 soil_type28 soil_type29 soil_type30 soil_type31 soil_type32 soil_type33 soil_type34 soil_type36 soil_type37 soil_type38 soil_type39 soil_type40                                                                            |
| Function Parameter Types       | int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8 int8                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| IAM Role                       | default-aws-iam-role                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| S3 Bucket                      | DOC-EXAMPLE-BUCKET                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Max Runtime                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          15000 |
+--------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

## Step 3: Validate the model<a name="tutorial_linear_learner_multi-class_classification_step_validate"></a>

1. The following prediction query validates the accuracy of the model on the `covertype_validation` dataset by calculating multi\-class accuracy\. Multi\-class accuracy is the percentage of the model’s predictions that are correct\.

   ```
   SELECT
       CAST(sum(t1.match) AS decimal(7, 2)) AS predicted_matches,
       CAST(sum(t1.nonmatch) AS decimal(7, 2)) AS predicted_non_matches,
       CAST(sum(t1.match + t1.nonmatch) AS decimal(7, 2)) AS total_predictions,
       predicted_matches / total_predictions AS pct_accuracy
   FROM
       (
           SELECT
               Elevation,
               Aspect,
               Slope,
               Horizontal_distance_to_hydrology,
               Vertical_distance_to_hydrology,
               Horizontal_distance_to_roadways,
               HIllshade_9am,
               Hillshade_noon,
               Hillshade_3pm,
               Horizontal_Distance_To_Fire_Points,
               Wilderness_Area1,
               Wilderness_Area2,
               Wilderness_Area3,
               Wilderness_Area4,
               soil_type1,
               Soil_Type2,
               Soil_Type3,
               Soil_Type4,
               Soil_Type5,
               Soil_Type6,
               Soil_Type7,
               Soil_Type8,
               Soil_Type9,
               Soil_Type10,
               Soil_Type11,
               Soil_Type12,
               Soil_Type13,
               Soil_Type14,
               Soil_Type15,
               Soil_Type16,
               Soil_Type17,
               Soil_Type18,
               Soil_Type19,
               Soil_Type20,
               Soil_Type21,
               Soil_Type22,
               Soil_Type23,
               Soil_Type24,
               Soil_Type25,
               Soil_Type26,
               Soil_Type27,
               Soil_Type28,
               Soil_Type29,
               Soil_Type30,
               Soil_Type31,
               Soil_Type32,
               Soil_Type33,
               Soil_Type34,
               Soil_Type36,
               Soil_Type37,
               Soil_Type38,
               Soil_Type39,
               Soil_Type40,
               Cover_type AS actual_cover_type,
               predict_cover_type(
                   Elevation,
                   Aspect,
                   Slope,
                   Horizontal_distance_to_hydrology,
                   Vertical_distance_to_hydrology,
                   Horizontal_distance_to_roadways,
                   HIllshade_9am,
                   Hillshade_noon,
                   Hillshade_3pm,
                   Horizontal_Distance_To_Fire_Points,
                   Wilderness_Area1,
                   Wilderness_Area2,
                   Wilderness_Area3,
                   Wilderness_Area4,
                   soil_type1,
                   Soil_Type2,
                   Soil_Type3,
                   Soil_Type4,
                   Soil_Type5,
                   Soil_Type6,
                   Soil_Type7,
                   Soil_Type8,
                   Soil_Type9,
                   Soil_Type10,
                   Soil_Type11,
                   Soil_Type12,
                   Soil_Type13,
                   Soil_Type14,
                   Soil_Type15,
                   Soil_Type16,
                   Soil_Type17,
                   Soil_Type18,
                   Soil_Type19,
                   Soil_Type20,
                   Soil_Type21,
                   Soil_Type22,
                   Soil_Type23,
                   Soil_Type24,
                   Soil_Type25,
                   Soil_Type26,
                   Soil_Type27,
                   Soil_Type28,
                   Soil_Type29,
                   Soil_Type30,
                   Soil_Type31,
                   Soil_Type32,
                   Soil_Type33,
                   Soil_Type34,
                   Soil_Type36,
                   Soil_Type37,
                   Soil_Type38,
                   Soil_Type39,
                   Soil_Type40
               ) AS predicted_cover_type,
               CASE
                   WHEN actual_cover_type = predicted_cover_type THEN 1
                   ELSE 0
               END AS match,
               CASE
                   WHEN actual_cover_type <> predicted_cover_type THEN 1
                   ELSE 0
               END AS nonmatch
           FROM
               public.covertype_validation
       ) t1;
   ```

   The output of the previous query should look like the following example\. The value of the multi\-class accuracy metric should be similar to the `validation:multiclass_accuracy` metric shown by the SHOW MODEL operation’s output\.

   ```
   +-------------------+-----------------------+-------------------+--------------+
   | predicted_matches | predicted_non_matches | total_predictions | pct_accuracy |
   +-------------------+-----------------------+-------------------+--------------+
   |             41211 |                 16324 |             57535 |   0.71627704 |
   +-------------------+-----------------------+-------------------+--------------+
   ```

1. The following query predicts the most common cover type for `wilderness_area2`\. This dataset includes four wilderness areas and seven cover types\. A wilderness area can have multiple cover types\.

   ```
   SELECT t1. predicted_cover_type, COUNT(*) 
   FROM 
   (     
   SELECT 
      Elevation, 
      Aspect,  
      Slope,  
      Horizontal_distance_to_hydrology,  
      Vertical_distance_to_hydrology,   
      Horizontal_distance_to_roadways,  
      HIllshade_9am,  
      Hillshade_noon,  
      Hillshade_3pm , 
      Horizontal_Distance_To_Fire_Points,  
      Wilderness_Area1,  
      Wilderness_Area2,   
      Wilderness_Area3,  
      Wilderness_Area4,  
      soil_type1,
      Soil_Type2,
      Soil_Type3, 
      Soil_Type4, 
      Soil_Type5,  
      Soil_Type6, 
      Soil_Type7,  
      Soil_Type8, 
      Soil_Type9, 
      Soil_Type10 , 
      Soil_Type11,  
      Soil_Type12 , 
      Soil_Type13 ,
      Soil_Type14, 
      Soil_Type15, 
      Soil_Type16,  
      Soil_Type17, 
      Soil_Type18,  
      Soil_Type19,  
      Soil_Type20,  
      Soil_Type21,  
      Soil_Type22,  
      Soil_Type23,  
      Soil_Type24, 
      Soil_Type25,  
      Soil_Type26, 
      Soil_Type27,  
      Soil_Type28,  
      Soil_Type29,  
      Soil_Type30,  
      Soil_Type31, 
      Soil_Type32, 
      Soil_Type33,  
      Soil_Type34, 
      Soil_Type36, 
      Soil_Type37,  
      Soil_Type38,  
      Soil_Type39, 
      Soil_Type40,
      predict_cover_type( Elevation, 
      Aspect,  
      Slope,  
      Horizontal_distance_to_hydrology,  
      Vertical_distance_to_hydrology,   
      Horizontal_distance_to_roadways,  
      HIllshade_9am,  
      Hillshade_noon,  
      Hillshade_3pm , 
      Horizontal_Distance_To_Fire_Points,  
      Wilderness_Area1,  
      Wilderness_Area2,   
      Wilderness_Area3,  
      Wilderness_Area4,  
      soil_type1,
      Soil_Type2,
      Soil_Type3, 
      Soil_Type4, 
      Soil_Type5,  
      Soil_Type6, 
      Soil_Type7,  
      Soil_Type8, 
      Soil_Type9, 
      Soil_Type10,
      Soil_Type11,  
      Soil_Type12, 
      Soil_Type13,
      Soil_Type14, 
      Soil_Type15, 
      Soil_Type16,  
      Soil_Type17, 
      Soil_Type18,  
      Soil_Type19,  
      Soil_Type20,  
      Soil_Type21,  
      Soil_Type22,  
      Soil_Type23,  
      Soil_Type24, 
      Soil_Type25,  
      Soil_Type26, 
      Soil_Type27,  
      Soil_Type28,  
      Soil_Type29,  
      Soil_Type30,  
      Soil_Type31, 
      Soil_Type32, 
      Soil_Type33,  
      Soil_Type34, 
      Soil_Type36, 
      Soil_Type37,  
      Soil_Type38,  
      Soil_Type39, 
      Soil_Type40) AS predicted_cover_type 
   
   FROM public.covertype_test
   WHERE wilderness_area2 = 1)
   t1
   GROUP BY 1;
   ```

   The output of the previous operation should look similar to the following example\. This output means that the model predicted that the majority of cover is cover type 1, and there is some cover of cover types 2 and 7\.

   ```
   +----------------------+-------+
   | predicted_cover_type | count |
   +----------------------+-------+
   |                    2 |   564 |
   |                    7 |    97 |
   |                    1 |  2309 |
   +----------------------+-------+
   ```

1. The following query shows the most common cover type in a single wilderness area\. The query displays the amount of that cover type and the cover type’s wilderness area\.

   ```
   SELECT t1. predicted_cover_type, COUNT(*), wilderness_area 
   FROM 
   (     
   SELECT 
      Elevation, 
      Aspect,  
      Slope,  
      Horizontal_distance_to_hydrology,  
      Vertical_distance_to_hydrology,   
      Horizontal_distance_to_roadways,  
      HIllshade_9am,  
      Hillshade_noon,  
      Hillshade_3pm , 
      Horizontal_Distance_To_Fire_Points,  
      Wilderness_Area1,  
      Wilderness_Area2,   
      Wilderness_Area3,  
      Wilderness_Area4,  
      soil_type1,
      Soil_Type2,
      Soil_Type3, 
      Soil_Type4, 
      Soil_Type5,  
      Soil_Type6, 
      Soil_Type7,  
      Soil_Type8, 
      Soil_Type9, 
      Soil_Type10 , 
      Soil_Type11,  
      Soil_Type12 , 
      Soil_Type13 ,
      Soil_Type14, 
      Soil_Type15, 
      Soil_Type16,  
      Soil_Type17, 
      Soil_Type18,  
      Soil_Type19,  
      Soil_Type20,  
      Soil_Type21,  
      Soil_Type22,  
      Soil_Type23,  
      Soil_Type24, 
      Soil_Type25,  
      Soil_Type26, 
      Soil_Type27,  
      Soil_Type28,  
      Soil_Type29,  
      Soil_Type30,  
      Soil_Type31, 
      Soil_Type32, 
      Soil_Type33,  
      Soil_Type34, 
      Soil_Type36, 
      Soil_Type37,  
      Soil_Type38,  
      Soil_Type39, 
      Soil_Type40,
      predict_cover_type( Elevation, 
      Aspect,  
      Slope,  
      Horizontal_distance_to_hydrology,  
      Vertical_distance_to_hydrology,   
      Horizontal_distance_to_roadways,  
      HIllshade_9am,  
      Hillshade_noon,  
      Hillshade_3pm , 
      Horizontal_Distance_To_Fire_Points,  
      Wilderness_Area1,  
      Wilderness_Area2,   
      Wilderness_Area3,  
      Wilderness_Area4,  
      soil_type1,
      Soil_Type2,
      Soil_Type3, 
      Soil_Type4, 
      Soil_Type5,  
      Soil_Type6, 
      Soil_Type7,  
      Soil_Type8, 
      Soil_Type9, 
      Soil_Type10,
      Soil_Type11,  
      Soil_Type12, 
      Soil_Type13,
      Soil_Type14, 
      Soil_Type15, 
      Soil_Type16,  
      Soil_Type17, 
      Soil_Type18,  
      Soil_Type19,  
      Soil_Type20,  
      Soil_Type21,  
      Soil_Type22,  
      Soil_Type23,  
      Soil_Type24, 
      Soil_Type25,  
      Soil_Type26, 
      Soil_Type27,  
      Soil_Type28,  
      Soil_Type29,  
      Soil_Type30,  
      Soil_Type31, 
      Soil_Type32, 
      Soil_Type33,  
      Soil_Type34, 
      Soil_Type36, 
      Soil_Type37,  
      Soil_Type38,  
      Soil_Type39, 
      Soil_Type40) AS predicted_cover_type,
      CASE WHEN Wilderness_Area1 = 1 THEN 1
           WHEN Wilderness_Area2 = 1 THEN 2
           WHEN Wilderness_Area3 = 1 THEN 3
           WHEN Wilderness_Area4 = 1 THEN 4
           ELSE 0
      END AS wilderness_area
   
   FROM public.covertype_test)
   t1
   GROUP BY 1, 3
   ORDER BY 2 DESC
   LIMIT 1;
   ```

   The output of the previous operation should look similar to the following example\.

   ```
   +----------------------+-------+-----------------+
   | predicted_cover_type | count | wilderness_area |
   +----------------------+-------+-----------------+
   |                    2 | 15738 |               1 |
   +----------------------+-------+-----------------+
   ```

## Related topics<a name="tutorial_linear_learner_multi-class_classification_related_topics"></a>

For more information about Amazon Redshift ML, see the following documentation:
+ [Costs for using Amazon Redshift ML](https://docs.aws.amazon.com/redshift/latest/dg/cost.html)
+ [CREATE MODEL operation](https://docs.aws.amazon.com/redshift/latest/dg/r_CREATE_MODEL.html)
+ [EXPLAIN\_MODEL function](https://docs.aws.amazon.com/redshift/latest/dg/r_explain_model_function.html)

For more information about machine learning, see the following documentation:
+ [Machine learning overview](https://docs.aws.amazon.com/redshift/latest/dg/machine_learning_overview.html)
+ [Machine learning for novices and experts](https://docs.aws.amazon.com/redshift/latest/dg/novice_expert.html)
+ [What Is Fairness and Model Explainability for Machine Learning Predictions?](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-fairness-and-explainability.html)