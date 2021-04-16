# Example for controlling user and group access<a name="t_user_group_examples"></a>

This example creates user groups and user accounts and then grants them various privileges for an Amazon Redshift database that connects to a web application client\. This example assumes three groups of users: regular users of a web application, power users of a web application, and web developers\.

1. Create the groups where the user accounts will be assigned\. The following set of commands creates three different user groups: 

   ```
   create group webappusers;
   
   create group webpowerusers;
   
   create group webdevusers;
   ```

1.  Create several database user accounts with different privileges and add them to the groups\.  

   1.  Create two users and add them to the WEBAPPUSERS group:  

      ```
      create user webappuser1 password 'webAppuser1pass'
      in group webappusers;
      
      create user webappuser2 password 'webAppuser2pass'
      in group webappusers;
      ```

   1.  Create an account for a web developer and adds it to the WEBDEVUSERS group:  

      ```
      create user webdevuser1 password 'webDevuser2pass'
      in group webdevusers;
      ```

   1.  Create a superuser account\. This user will have administrative rights to create other users:  

      ```
      create user webappadmin  password 'webAppadminpass1'
      createuser;
      ```

1.  Create a schema to be associated with the database tables used by the web application, and grant the various user groups access to this schema:  

   1.  Create the WEBAPP schema:  

      ```
      create schema webapp;
      ```

   1.  Grant USAGE privileges to the WEBAPPUSERS group:  

      ```
      grant usage on schema webapp to group webappusers;
      ```

   1.  Grant USAGE privileges to the WEBPOWERUSERS group:  

      ```
      grant usage on schema webapp to group webpowerusers;
      ```

   1.  Grant ALL privileges to the WEBDEVUSERS group:  

      ```
      grant all on schema webapp to group webdevusers;
      ```

   The basic users and groups are now set up\. You can now make changes to alter the users and groups\. 

1.  For example, the following command alters the search\_path parameter for the WEBAPPUSER1\.  

   ```
   alter user webappuser1 set search_path to webapp, public;
   ```

   The SEARCH\_PATH specifies the schema search order for database objects, such as tables and functions, when the object is referenced by a simple name with no schema specified\. 

1.  You can also add users to a group after creating the group, such as adding WEBAPPUSER2 to the WEBPOWERUSERS group:  

   ```
   alter group webpowerusers add user webappuser2;
   ```