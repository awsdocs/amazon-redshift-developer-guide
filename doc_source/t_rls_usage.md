# RLS policy usage notes<a name="t_rls_usage"></a>

Following are considerations for working with RLS policies:
+ Amazon Redshift applies RLS policies to SELECT, UPDATE, or DELETE statements\.
+ Amazon Redshift doesn't apply RLS policies to INSERT, COPY, ALTER TABLE APPEND statements\.
+ Row\-level security works with column\-level security to protect your data\.
+ When your Amazon Redshift cluster was on the latest generally available version that supports RLS, but is downgraded to an earlier version, Amazon Redshift returns an error when you run a query on base tables with RLS policies attached\. The sys:secadmin can revoke access from users that were granted restricted policies, turn off RLS on tables, and drop the policies\.
+ When RLS is turned on for the source relation, Amazon Redshift supports the ALTER TABLE APPEND statement for superusers, users that have been explicitly granted the system permission IGNORE RLS, or the sys:secadmin role\. In this case, you can run the ALTER TABLE APPEND statement to append rows to a target table by moving data from an existing source table\. Amazon Redshift moves all tuples from the source relation into the target relation\. The RLS status of the target relation doesn't affect the ALTER TABLE APPEND statement\.
+ To facilitate migration from other data warehouse systems, you can set and retrieve customized session context variables for a connection by specifying the variable name and value\.

  The following example sets session context variables for a row\-level security \(RLS\) policy\.

  ```
  -- Set a customized context variable.
  SELECT set_config(‘app.category’, ‘Concerts’, FALSE);
  
  -- Create a RLS policy using current_setting() to get the value of a customized context variable.
  CREATE RLS POLICY policy_categories
  WITH (catgroup VARCHAR(10)) 
  USING (catgroup = current_setting('app.category', FALSE));
  
  -- Set correct roles and attach the policy on the target table to one or more roles.
  ATTACH RLS POLICY policy_categories ON tickit_category_redshift TO ROLE analyst, ROLE dbadmin;
  ```

  For details on how to set and retrieve customized session context variables, see [SET](r_SET.md), [SET\_CONFIG](r_SET_CONFIG.md), [SHOW](r_SHOW.md), [CURRENT\_SETTING](r_CURRENT_SETTING.md), and [RESET](r_RESET.md)\.
+ Changing session user using SET SESSION AUTHORIZATION between DECLARE and FETCH or between subsequent FETCH statements won't refresh the already prepared plan based on the user policies at DECLARE time\. Avoid changing session user when cursors are used with RLS\-protected tables\.
+ When the base objects inside a view object are RLS\-protected, policies attached to the user running the query are applied on the respective base objects\. This is different from object\-level permission checks, where the view owner's permissions are checked against the view base objects\. You can view the RLS\-protected relations of a query in its EXPLAIN plan output\.
+ When a user\-defined function \(UDF\) is referenced in a RLS policy of a relation attached to a user, the user must have the EXECUTE permission over the UDF to query the relation\.