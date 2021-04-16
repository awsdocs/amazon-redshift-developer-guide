# analyze\_threshold\_percent<a name="r_analyze_threshold_percent"></a>

## Values \(default in bold\)<a name="t_analyze_threshold_percent-values"></a>

 **10**, 0 to 100\.0

## Description<a name="r_analyze_threshold_percent-description"></a>

Sets the threshold for percentage of rows changed for analyzing a table\. To reduce processing time and improve overall system performance, Amazon Redshift skips analyze for any table that has a lower percentage of changed rows than specified by `analyze_threshold_percent`\. For example, if a table contains 100,000,000 rows and 9,000,000 rows have changes since the last ANALYZE, then by default the table is skipped because fewer than 10 percent of the rows have changed\. To analyze tables when only a small number of rows have changed, set `analyze_threshold_percent` to an arbitrarily small number\. For example, if you set `analyze_threshold_percent` to 0\.01, then a table with 100,000,000 rows will not be skipped if at least 10,000 rows have changed\. To analyze all tables even if no rows have changed, set `analyze_threshold_percent` to 0\.

You can modify the `analyze_threshold_percent` parameter for the current session only by using a SET command\. The parameter can't be modified in a parameter group\.

## Example<a name="r_analyze_threshold_percent-example"></a>

```
set analyze_threshold_percent to 15;
set analyze_threshold_percent to 0.01;
set analyze_threshold_percent to 0;
```