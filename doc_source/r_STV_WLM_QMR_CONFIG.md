# STV\_WLM\_QMR\_CONFIG<a name="r_STV_WLM_QMR_CONFIG"></a>

Records the configuration for WLM query monitoring rules \(QMR\)\. For more information, see [WLM Query Monitoring Rules](cm-c-wlm-query-monitoring-rules.md)\.

STV\_WLM\_QMR\_CONFIG is visible only to superusers\. For more information, see [Visibility of Data in System Tables and Views](c_visibility-of-data.md)\.

## Table Columns<a name="r_STV_WLM_QMR_CONFIG-table-columns2"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/redshift/latest/dg/r_STV_WLM_QMR_CONFIG.html)

## Sample Query<a name="r_STV_WLM_QMR_CONFIG-sample-query2"></a>

To view the QMR rule definitions for all user\-defined queues \(service classes greater than 5\), run the following query\.

```
Select *
from stv_wlm_qmr_config
where service_class > 5
order by service_class;
```