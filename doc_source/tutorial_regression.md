# Tutorial: Building regression models<a name="tutorial_regression"></a>

In this tutorial, you use Amazon Redshift ML to create a machine learning regression model and run prediction queries on the model\. Regression models allow you to predict numerical outcomes, such as the price of a house, or how many people will use a city’s bike rental service\. You use the CREATE MODEL command in Amazon Redshift with your training data\. Then, Amazon Redshift ML compiles the model, imports the trained model to Redshift, and prepares a SQL prediction function\. You can use the prediction function in SQL queries in Amazon Redshift\.

In this tutorial, you will use Amazon Redshift ML to build a regression model that predicts the number of people that use the city of Toronto’s bike sharing service at any given hour of a day\. The inputs for the model include holidays and weather conditions\. You will use a regression model, because you want a numerical outcome for this problem\.

You can use the CREATE MODEL command to export training data, train a model, and make the model available in Amazon Redshift as a SQL function\. Use the CREATE MODEL operation to specify training data either as a table or a SELECT statement\.

## Use case examples<a name="tutorial_regression_tasks"></a>

You can solve other regression problems with Amazon Redshift ML, such as predicting a customer’s lifetime value\. You could also use Redshift ML to predict the most profitable price and the resulting revenue of a product\.

**Tasks**
+ Prerequisites
+ Step 1: Load the data from Amazon S3 to Amazon Redshift
+ Step 2: Create the machine learning model
+ Step 3: Validate the model

## Prerequisites<a name="tutorial_regression_prereqs"></a>

To complete this tutorial, you must complete the [Administrative setup](https://docs.aws.amazon.com/redshift/latest/dg/admin-setup.html) for Amazon Redshift ML\.

## Step 1: Load the data from Amazon S3 to Amazon Redshift<a name="tutorial_regression_step_load"></a>

Use the [Amazon Redshift query editor v2](https://docs.aws.amazon.com/redshift/latest/mgmt/query-editor-v2-using.html) to run the following queries\.

1. You must create three tables to load the three public datasets into Amazon Redshift\. The datasets are [Toronto Bike Ridership Data](https://open.toronto.ca/dataset/bike-share-toronto-ridership-data/), [historical weather data](https://climate.weather.gc.ca/historical_data/search_historic_data_e.html), and [historical holidays data](https://github.com/uWaterloo/Datasets/blob/master/Holidays/holidays.csv)\. Run the following query in the Amazon Redshift query editor to create tables named `ridership`, `weather`, and `holiday`\.

   ```
   CREATE TABLE IF NOT EXISTS ridership (
       trip_id INT,
       trip_duration_seconds INT,
       trip_start_time timestamp,
       trip_stop_time timestamp,
       from_station_name VARCHAR(50),
       to_station_name VARCHAR(50),
       from_station_id SMALLINT,
       to_station_id SMALLINT,
       user_type VARCHAR(20)
   );
   
   CREATE TABLE IF NOT EXISTS weather (
       longitude_x DECIMAL(5, 2),
       latitude_y DECIMAL(5, 2),
       station_name VARCHAR(20),
       climate_id BIGINT,
       datetime_utc TIMESTAMP,
       weather_year SMALLINT,
       weather_month SMALLINT,
       weather_day SMALLINT,
       time_utc VARCHAR(5),
       temp_c DECIMAL(5, 2),
       temp_flag VARCHAR(1),
       dew_point_temp_c DECIMAL(5, 2),
       dew_point_temp_flag VARCHAR(1),
       rel_hum SMALLINT,
       rel_hum_flag VARCHAR(1),
       precip_amount_mm DECIMAL(5, 2),
       precip_amount_flag VARCHAR(1),
       wind_dir_10s_deg VARCHAR(10),
       wind_dir_flag VARCHAR(1),
       wind_spd_kmh VARCHAR(10),
       wind_spd_flag VARCHAR(1),
       visibility_km VARCHAR(10),
       visibility_flag VARCHAR(1),
       stn_press_kpa DECIMAL(5, 2),
       stn_press_flag VARCHAR(1),
       hmdx SMALLINT,
       hmdx_flag VARCHAR(1),
       wind_chill VARCHAR(10),
       wind_chill_flag VARCHAR(1),
       weather VARCHAR(10)
   );
   
   CREATE TABLE IF NOT EXISTS holiday (holiday_date DATE, description VARCHAR(100));
   ```

1. The following query loads the sample data into the tables that you created in the previous step\.

   ```
   COPY ridership
   FROM
       's3://redshift-ml-bikesharing-data/bike-sharing-data/ridership/' 
       IAM_ROLE default 
       FORMAT CSV 
       IGNOREHEADER 1 
       DATEFORMAT 'auto' 
       TIMEFORMAT 'auto' 
       REGION 'us-west-2' 
       gzip;
   
   COPY weather
   FROM
       's3://redshift-ml-bikesharing-data/bike-sharing-data/weather/' 
       IAM_ROLE default 
       FORMAT csv 
       IGNOREHEADER 1 
       DATEFORMAT 'auto' 
       TIMEFORMAT 'auto' 
       REGION 'us-west-2' 
       gzip;
   
   COPY holiday
   FROM
       's3://redshift-ml-bikesharing-data/bike-sharing-data/holiday/' 
       IAM_ROLE default 
       FORMAT csv 
       IGNOREHEADER 1 
       DATEFORMAT 'auto' 
       TIMEFORMAT 'auto' 
       REGION 'us-west-2' 
       gzip;
   ```

1. The following query performs transformations on the `ridership` and `weather` datasets to remove bias or anomalies\. Removing bias and anomalies results in improved model accuracy\. The query simplifies the tables by creating two new views called `ridership_view` and `weather_view`\.

   ```
   CREATE
   OR REPLACE VIEW ridership_view AS
   SELECT
       trip_time,
       trip_count,
       TO_CHAR(trip_time, 'hh24') :: INT trip_hour,
       TO_CHAR(trip_time, 'dd') :: INT trip_day,
       TO_CHAR(trip_time, 'mm') :: INT trip_month,
       TO_CHAR(trip_time, 'yy') :: INT trip_year,
       TO_CHAR(trip_time, 'q') :: INT trip_quarter,
       TO_CHAR(trip_time, 'w') :: INT trip_month_week,
       TO_CHAR(trip_time, 'd') :: INT trip_week_day
   FROM
       (
           SELECT
               CASE
                   WHEN TRUNC(r.trip_start_time) < '2017-07-01' :: DATE THEN CONVERT_TIMEZONE(
                       'US/Eastern',
                       DATE_TRUNC('hour', r.trip_start_time)
                   )
                   ELSE DATE_TRUNC('hour', r.trip_start_time)
               END trip_time,
               COUNT(1) trip_count
           FROM
               ridership r
           WHERE
               r.trip_duration_seconds BETWEEN 60
               AND 60 * 60 * 24
           GROUP BY
               1
       );
   
   CREATE
   OR REPLACE VIEW weather_view AS
   SELECT
       CONVERT_TIMEZONE(
           'US/Eastern',
           DATE_TRUNC('hour', datetime_utc)
       ) daytime,
       ROUND(AVG(temp_c)) temp_c,
       ROUND(AVG(precip_amount_mm)) precip_amount_mm
   FROM
       weather
   GROUP BY
       1;
   ```

1. The following query creates a table that combines all the relevant input attributes from `ridership_view` and `weather_view` into the `trip_data` table\.

   ```
   CREATE TABLE trip_data AS
   SELECT
       r.trip_time,
       r.trip_count,
       r.trip_hour,
       r.trip_day,
       r.trip_month,
       r.trip_year,
       r.trip_quarter,
       r.trip_month_week,
       r.trip_week_day,
       w.temp_c,
       w.precip_amount_mm,CASE
           WHEN h.holiday_date IS NOT NULL THEN 1
           WHEN TO_CHAR(r.trip_time, 'D') :: INT IN (1, 7) THEN 1
           ELSE 0
       END is_holiday,
       ROW_NUMBER() OVER (
           ORDER BY
               RANDOM()
       ) serial_number
   FROM
       ridership_view r
       JOIN weather_view w ON (r.trip_time = w.daytime)
       LEFT OUTER JOIN holiday h ON (TRUNC(r.trip_time) = h.holiday_date);
   ```

### View the sample data \(optional\)<a name="tutorial_regression_view_data"></a>

The following query shows entries from the table\. You can run this operation to make sure the table was made correctly\.

```
SELECT * 
FROM trip_data 
LIMIT 5;
```

The following is an example of the output of the previous operation\.

```
+---------------------+------------+-----------+----------+------------+-----------+--------------+-----------------+---------------+--------+------------------+------------+---------------+
|      trip_time      | trip_count | trip_hour | trip_day | trip_month | trip_year | trip_quarter | trip_month_week | trip_week_day | temp_c | precip_amount_mm | is_holiday | serial_number |
+---------------------+------------+-----------+----------+------------+-----------+--------------+-----------------+---------------+--------+------------------+------------+---------------+
| 2017-03-21 22:00:00 |         47 |        22 |       21 |          3 |        17 |            1 |               3 |             3 |      1 |                0 |          0 |             1 |
| 2018-05-04 01:00:00 |         19 |         1 |        4 |          5 |        18 |            2 |               1 |             6 |     12 |                0 |          0 |             3 |
| 2018-01-11 10:00:00 |         93 |        10 |       11 |          1 |        18 |            1 |               2 |             5 |      9 |                0 |          0 |             5 |
| 2017-10-28 04:00:00 |         20 |         4 |       28 |         10 |        17 |            4 |               4 |             7 |     11 |                0 |          1 |             7 |
| 2017-12-31 21:00:00 |         11 |        21 |       31 |         12 |        17 |            4 |               5 |             1 |    -15 |                0 |          1 |             9 |
+---------------------+------------+-----------+----------+------------+-----------+--------------+-----------------+---------------+--------+------------------+------------+---------------+
```

### Show the correlation between attributes \(optional\)<a name="tutorial_regression_show_correlation"></a>

Determining correlation helps you measure the strength of association between attributes\. The level of association can help you determine what affects your target output\. In this tutorial, the target output is `trip_count`\.

The following query creates or replaces the `sp_correlation` procedure\. You use the stored procedure called `sp_correlation` to show the correlation between an attribute and other attributes in a table in Amazon Redshift\.

```
CREATE OR REPLACE PROCEDURE sp_correlation(source_schema_name in varchar(255), source_table_name in varchar(255), target_column_name in varchar(255), output_temp_table_name inout varchar(255)) AS $$
DECLARE
  v_sql varchar(max);
  v_generated_sql varchar(max);
  v_source_schema_name varchar(255)=lower(source_schema_name);
  v_source_table_name varchar(255)=lower(source_table_name);
  v_target_column_name varchar(255)=lower(target_column_name);
BEGIN
  EXECUTE 'DROP TABLE IF EXISTS ' || output_temp_table_name;
  v_sql = '
SELECT
  ''CREATE temp table '|| output_temp_table_name||' AS SELECT ''|| outer_calculation||
  '' FROM (SELECT COUNT(1) number_of_items, SUM('||v_target_column_name||') sum_target, SUM(POW('||v_target_column_name||',2)) sum_square_target, POW(SUM('||v_target_column_name||'),2) square_sum_target,''||
  inner_calculation||
  '' FROM (SELECT ''||
  column_name||
  '' FROM '||v_source_table_name||'))''
FROM
  (
  SELECT
    DISTINCT
    LISTAGG(outer_calculation,'','') OVER () outer_calculation
    ,LISTAGG(inner_calculation,'','') OVER () inner_calculation
    ,LISTAGG(column_name,'','') OVER () column_name
  FROM
    (
    SELECT
      CASE WHEN atttypid=16 THEN ''DECODE(''||column_name||'',true,1,0)'' ELSE column_name END column_name
      ,atttypid
      ,''CAST(DECODE(number_of_items * sum_square_''||rn||'' - square_sum_''||rn||'',0,null,(number_of_items*sum_target_''||rn||'' - sum_target * sum_''||rn||
        '')/SQRT((number_of_items * sum_square_target - square_sum_target) * (number_of_items * sum_square_''||rn||
        '' - square_sum_''||rn||''))) AS numeric(5,2)) ''||column_name outer_calculation
      ,''sum(''||column_name||'') sum_''||rn||'',''||
            ''SUM(trip_count*''||column_name||'') sum_target_''||rn||'',''||
            ''SUM(POW(''||column_name||'',2)) sum_square_''||rn||'',''||
            ''POW(SUM(''||column_name||''),2) square_sum_''||rn inner_calculation
    FROM
      (
      SELECT
        row_number() OVER (order by a.attnum) rn
        ,a.attname::VARCHAR column_name
        ,a.atttypid
      FROM pg_namespace AS n
        INNER JOIN pg_class AS c ON n.oid = c.relnamespace
        INNER JOIN pg_attribute AS a ON c.oid = a.attrelid
      WHERE a.attnum > 0
        AND n.nspname = '''||v_source_schema_name||'''
        AND c.relname = '''||v_source_table_name||'''
        AND a.atttypid IN (16,20,21,23,700,701,1700)
      )
    )
)';
  EXECUTE v_sql INTO v_generated_sql;
  EXECUTE  v_generated_sql;
END;
$$ LANGUAGE plpgsql;
```

The following query shows the correlation between the target column, `trip_count`, and other numeric attributes in our dataset\.

```
call sp_correlation(
    'public',
    'trip_data',
    'trip_count',
    'tmp_corr_table'
);

SELECT
    *
FROM
    tmp_corr_table;
```

The following example shows the output of the previous `sp_correlation` operation\.

```
+------------+-----------+----------+------------+-----------+--------------+-----------------+---------------+--------+------------------+------------+---------------+
| trip_count | trip_hour | trip_day | trip_month | trip_year | trip_quarter | trip_month_week | trip_week_day | temp_c | precip_amount_mm | is_holiday | serial_number |
+------------+-----------+----------+------------+-----------+--------------+-----------------+---------------+--------+------------------+------------+---------------+
|          1 |      0.32 |     0.01 |       0.18 |      0.12 |         0.18 |               0 |          0.02 |   0.53 |            -0.07 |      -0.13 |             0 |
+------------+-----------+----------+------------+-----------+--------------+-----------------+---------------+--------+------------------+------------+---------------+
```

## Step 2: Create the machine learning model<a name="tutorial_regression_create_model"></a>

1. The following query splits your data into a training set and a validation set by designating 80% of the dataset for training and 20% for validation\. The training set is the input for the ML model to identify the best possible algorithm for the model\. After the model is created, you use the validation set to validate the model accuracy\.

   ```
   CREATE TABLE training_data AS
   SELECT
       trip_count,
       trip_hour,
       trip_day,
       trip_month,
       trip_year,
       trip_quarter,
       trip_month_week,
       trip_week_day,
       temp_c,
       precip_amount_mm,
       is_holiday
   FROM
       trip_data
   WHERE
       serial_number > (
           SELECT
               COUNT(1) * 0.2
           FROM
               trip_data
       );
   
   CREATE TABLE validation_data AS
   SELECT
       trip_count,
       trip_hour,
       trip_day,
       trip_month,
       trip_year,
       trip_quarter,
       trip_month_week,
       trip_week_day,
       temp_c,
       precip_amount_mm,
       is_holiday,
       trip_time
   FROM
       trip_data
   WHERE
       serial_number <= (
           SELECT
               COUNT(1) * 0.2
           FROM
               trip_data
       );
   ```

1. The following query creates a regression model to predict the `trip_count` value for any input date and time\. In the following example, replace *`DOC-EXAMPLE-BUCKET`* with your own S3 bucket\.

   ```
   CREATE MODEL predict_rental_count
   FROM
       training_data TARGET trip_count FUNCTION predict_rental_count 
       IAM_ROLE default 
       PROBLEM_TYPE regression 
       OBJECTIVE 'mse' 
       SETTINGS (
           s3_bucket '<DOC-EXAMPLE-BUCKET>',
           s3_garbage_collect off,
           max_runtime 5000
       );
   ```

## Step 3: Validate the model<a name="tutorial_regression_step_validate"></a>

1. Use the following query to output aspects of the model, and find the mean square error metric in the output\. Mean square error is a typical accuracy metric for regression problems\.

   ```
   show model predict_rental_count;
   ```

1. Run the following prediction queries against the validation data to compare the predicted trip count to the actual trip count\.

   ```
   SELECT
       trip_time,
       actual_count,
       predicted_count,
       (actual_count - predicted_count) difference
   FROM
       (
           SELECT
               trip_time,
               trip_count AS actual_count,
               PREDICT_RENTAL_COUNT (
                   trip_hour,
                   trip_day,
                   trip_month,
                   trip_year,
                   trip_quarter,
                   trip_month_week,
                   trip_week_day,
                   temp_c,
                   precip_amount_mm,
                   is_holiday
               ) predicted_count
           FROM
               validation_data
       )
   LIMIT
       5;
   ```

1. The following query calculates the mean square error and root mean square error based on your validation data\. You use mean square error and root mean square error to measure the distance between the predicted numeric target and the actual numeric answer\. A good model has a low score in both metrics\. The following query returns the value of both metrics\.

   ```
   SELECT
       ROUND(
           AVG(POWER((actual_count - predicted_count), 2)),
           2
       ) mse,
       ROUND(
           SQRT(AVG(POWER((actual_count - predicted_count), 2))),
           2
       ) rmse
   FROM
       (
           SELECT
               trip_time,
               trip_count AS actual_count,
               PREDICT_RENTAL_COUNT (
                   trip_hour,
                   trip_day,
                   trip_month,
                   trip_year,
                   trip_quarter,
                   trip_month_week,
                   trip_week_day,
                   temp_c,
                   precip_amount_mm,
                   is_holiday
               ) predicted_count
           FROM
               validation_data
       );
   ```

1. The following query calculates the percent error in trip count for each trip time on 2017\-01\-01\. The query orders the trip times from the time with the lowest percent error to the time with the highest percent error\.

   ```
   SELECT
       trip_time,
       CAST(ABS(((actual_count - predicted_count) / actual_count)) * 100 AS DECIMAL (7,2)) AS pct_error
   FROM
       (
           SELECT
               trip_time,
               trip_count AS actual_count,
               PREDICT_RENTAL_COUNT (
                   trip_hour,
                   trip_day,
                   trip_month,
                   trip_year,
                   trip_quarter,
                   trip_month_week,
                   trip_week_day,
                   temp_c,
                   precip_amount_mm,
                   is_holiday
               ) predicted_count
           FROM
               validation_data
       )
   WHERE
      trip_time LIKE '2017-01-01 %%:%%:%%'
   ORDER BY
      2 ASC;
   ```

## Related topics<a name="tutorial_regression_related_topics"></a>

For more information about Amazon Redshift ML, see the following documentation:
+ [Costs for using Amazon Redshift ML](https://docs.aws.amazon.com/redshift/latest/dg/cost.html)
+ [CREATE MODEL operation](https://docs.aws.amazon.com/redshift/latest/dg/r_CREATE_MODEL.html)
+ [EXPLAIN\_MODEL function](https://docs.aws.amazon.com/redshift/latest/dg/r_explain_model_function.html)

For more information about machine learning, see the following documentation:
+ [Machine learning overview](https://docs.aws.amazon.com/redshift/latest/dg/machine_learning_overview.html)
+ [Machine learning for novices and experts](https://docs.aws.amazon.com/redshift/latest/dg/novice_expert.html)
+ [What Is Fairness and Model Explainability for Machine Learning Predictions?](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-fairness-and-explainability.html)