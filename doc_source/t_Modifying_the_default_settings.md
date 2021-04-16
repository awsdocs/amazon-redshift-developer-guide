# Modifying the server configuration<a name="t_Modifying_the_default_settings"></a>

You can change the server configuration in the following ways: 
+ By using a [SET](r_SET.md) command to override a setting for the duration of the current session only\.

  For example: 

  ```
  set extra_float_digits to 2;
  ```
+ By modifying the parameter group settings for the cluster\. The parameter group settings include additional parameters that you can configure\. For more information, see [Amazon Redshift Parameter Groups](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-parameter-groups.html) in the *Amazon Redshift Cluster Management Guide*\.
+ By using the [ALTER USER](r_ALTER_USER.md) command to set a configuration parameter to a new value for all sessions run by the specified user\.

  ```
  ALTER USER username SET parameter { TO | = } { value | DEFAULT }
  ```

Use the SHOW command to view the current parameter settings\. Use SHOW ALL to view all the settings that you can configure by using the [SET](r_SET.md) command\.

```
show all;
```

```
name                      | setting      
--------------------------+--------------
analyze_threshold_percent | 10           
datestyle                 | ISO, MDY     
extra_float_digits        | 2            
query_group               | default      
search_path               | $user, public
statement_timeout         | 0            
timezone                  | UTC            
wlm_query_slot_count      | 1
```