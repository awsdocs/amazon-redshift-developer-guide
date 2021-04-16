# Schemas<a name="r_Schemas_and_tables"></a>

A database contains one or more named schemas\. Each schema in a database contains tables and other kinds of named objects\. By default, a database has a single schema, which is named PUBLIC\. You can use schemas to group database objects under a common name\. Schemas are similar to file system directories, except that schemas cannot be nested\.

Identical database object names can be used in different schemas in the same database without conflict\. For example, both MY\_SCHEMA and YOUR\_SCHEMA can contain a table named MYTABLE\. Users with the necessary privileges can access objects across multiple schemas in a database\.

By default, an object is created within the first schema in the search path of the database\. For information, see [Search path](#c_Search_path) later in this section\.

Schemas can help with organization and concurrency issues in a multi\-user environment in the following ways:
+ To allow many developers to work in the same database without interfering with each other\.
+ To organize database objects into logical groups to make them more manageable\.
+ To give applications the ability to put their objects into separate schemas so that their names will not collide with the names of objects used by other applications\.

## Creating, altering, and deleting schemas<a name="r_Schemas_and_tables-creating-altering-and-deleting-schemas"></a>

Any user can create schemas and alter or drop schemas they own\.

You can perform the following actions:
+ To create a schema, use the [CREATE SCHEMA](r_CREATE_SCHEMA.md) command\.
+ To change the owner of a schema, use the [ALTER SCHEMA](r_ALTER_SCHEMA.md) command\.
+ To delete a schema and its objects, use the [DROP SCHEMA](r_DROP_SCHEMA.md) command\.
+ To create a table within a schema, create the table with the format *schema\_name\.table\_name*\. 

To view a list of all schemas, query the PG\_NAMESPACE system catalog table:

```
select * from pg_namespace;
```

To view a list of tables that belong to a schema, query the PG\_TABLE\_DEF system catalog table\. For example, the following query returns a list of tables in the PG\_CATALOG schema\.

```
select distinct(tablename) from pg_table_def
where schemaname = 'pg_catalog';
```

## Search path<a name="c_Search_path"></a>

The search path is defined in the search\_path parameter with a comma\-separated list of schema names\. The search path specifies the order in which schemas are searched when an object, such as a table or function, is referenced by a simple name that does not include a schema qualifier\.

If an object is created without specifying a target schema, the object is added to the first schema that is listed in search path\. When objects with identical names exist in different schemas, an object name that does not specify a schema will refer to the first schema in the search path that contains an object with that name\.

To change the default schema for the current session, use the [SET](r_SET.md) command\.

For more information, see the [search\_path](r_search_path.md) description in the Configuration Reference\.

## Schema\-based privileges<a name="r_Schemas_and_tables-schema-based-privileges"></a>

 Schema\-based privileges are determined by the owner of the schema: 
+ By default, all users have CREATE and USAGE privileges on the PUBLIC schema of a database\. To disallow users from creating objects in the PUBLIC schema of a database, use the [REVOKE](r_REVOKE.md) command to remove that privilege\.
+ Unless they are granted the USAGE privilege by the object owner, users cannot access any objects in schemas they do not own\. 
+ If users have been granted the CREATE privilege to a schema that was created by another user, those users can create objects in that schema\.