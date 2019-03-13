# Define Primary Key and Foreign Key Constraints<a name="c_best-practices-defining-constraints"></a>

Define primary key and foreign key constraints between tables wherever appropriate\. Even though they are informational only, the query optimizer uses those constraints to generate more efficient query plans\.

Redshift automatically adds a NOT NULL constraint to the column defined as PRIMARY KEY. This NOT NULL constraint is enforced.

Do not define primary key and foreign key constraints unless your application enforces the constraints\. Amazon Redshift does not enforce unique, primary\-key, and foreign\-key constraints\. 

See [Defining Constraints](t_Defining_constraints.md) for additional information about how Amazon Redshift uses constraints\.
