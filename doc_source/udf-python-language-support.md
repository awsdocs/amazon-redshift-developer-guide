# Python language support for UDFs<a name="udf-python-language-support"></a>

You can create a custom UDF based on the Python programming language\. The [Python 2\.7 standard library](https://docs.python.org/2/library/index.html) is available for use in UDFs, with the exception of the following modules:
+ ScrolledText
+ Tix
+ Tkinter
+ tk
+ turtle
+ smtpd

In addition to the Python Standard Library, the following modules are part of the Amazon Redshift implementation:
+ [numpy 1\.8\.2](http://www.numpy.org/)
+ [pandas 0\.14\.1](https://pandas.pydata.org/)
+ [python\-dateutil 2\.2](https://dateutil.readthedocs.org/en/latest/)
+ [pytz 2014\.7](https://pypi.org/project/pytz/2014.7/)
+ [scipy 0\.12\.1](https://www.scipy.org/)
+ [six 1\.3\.0](https://pypi.org/project/six/1.3.0/)
+ [wsgiref 0\.1\.2](https://pypi.python.org/pypi/wsgiref)

You can also import your own custom Python modules and make them available for use in UDFs by executing a [CREATE LIBRARY](r_CREATE_LIBRARY.md) command\. For more information, see [Importing custom Python library modules](#udf-importing-custom-python-library-modules)\.

**Important**  
Amazon Redshift blocks all network access and write access to the file system through UDFs\.

## Importing custom Python library modules<a name="udf-importing-custom-python-library-modules"></a>

You define scalar functions using Python language syntax\. In addition to the native Python Standard Library modules and Amazon Redshift preinstalled modules, you can create your own custom Python library modules and import the libraries into your clusters, or use existing libraries provided by Python or third parties\. 

You cannot create a library that contains a module with the same name as a Python Standard Library module or an Amazon Redshift preinstalled Python module\. If an existing user\-installed library uses the same Python package as a library you create, you must drop the existing library before installing the new library\. 

You must be a superuser or have `USAGE ON LANGUAGE plpythonu` privilege to install custom libraries; however, any user with sufficient privileges to create functions can use the installed libraries\. You can query the [PG\_LIBRARY](r_PG_LIBRARY.md) system catalog to view information about the libraries installed on your cluster\.

### To import a custom Python module into your cluster<a name="udf-import-custom-python-module-procedure"></a>

This section provides an example of importing a custom Python module into your cluster\. To perform the steps in this section, you must have an Amazon S3 bucket, where you upload the library package\. You then install the package in your cluster\. For more information about creating buckets, go to [ Creating a bucket](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/CreatingaBucket.html) in the *Amazon Simple Storage Service Console User Guide*\.

In this example, let's suppose that you create UDFs to work with positions and distances in your data\. Connect to your Amazon Redshift cluster from a SQL client tool, and run the following commands to create the functions\. 

```
CREATE FUNCTION f_distance (x1 float, y1 float, x2 float, y2 float) RETURNS float IMMUTABLE as $$
    def distance(x1, y1, x2, y2):
        import math
        return math.sqrt((y2 - y1) ** 2 + (x2 - x1) ** 2)
 
    return distance(x1, y1, x2, y2)
$$ LANGUAGE plpythonu;
 
CREATE FUNCTION f_within_range (x1 float, y1 float, x2 float, y2 float) RETURNS bool IMMUTABLE as $$ 
    def distance(x1, y1, x2, y2):
        import math
        return math.sqrt((y2 - y1) ** 2 + (x2 - x1) ** 2)
 
    return distance(x1, y1, x2, y2) < 20
$$ LANGUAGE plpythonu;
```

Note that a few lines of code are duplicated in the previous functions\. This duplication is necessary because a UDF cannot reference the contents of another UDF, and both functions require the same functionality\. However, instead of duplicating code in multiple functions, you can create a custom library and configure your functions to use it\. 

To do so, first create the library package by following these steps: 

1. Create a folder named **geometry**\. This folder is the top level package of the library\.

1. In the **geometry** folder, create a file named `__init__.py`\. Note that the file name contains two double underscore characters\. This file indicates to Python that the package can be initialized\.

1. Also in the **geometry** folder, create a folder named **trig**\. This folder is the subpackage of the library\.

1. In the **trig** folder, create another file named `__init__.py` and a file named `line.py`\. In this folder, `__init__.py` indicates to Python that the subpackage can be initialized and that `line.py` is the file that contains library code\.

   Your folder and file structure should be the same as the following: 

   ```
   geometry/
      __init__.py
      trig/
         __init__.py
         line.py
   ```

    For more information about package structure, go to [ Modules](https://docs.python.org/2/tutorial/modules.html) in the Python tutorial on the Python website\. 

1.  The following code contains a class and member functions for the library\. Copy and paste it into `line.py`\. 

   ```
   class LineSegment:
     def __init__(self, x1, y1, x2, y2):
       self.x1 = x1
       self.y1 = y1
       self.x2 = x2
       self.y2 = y2
     def angle(self):
       import math
       return math.atan2(self.y2 - self.y1, self.x2 - self.x1)
     def distance(self):
       import math
       return math.sqrt((self.y2 - self.y1) ** 2 + (self.x2 - self.x1) ** 2)
   ```

 After you have created the package, do the following to prepare the package and upload it to Amazon S3\. 

1. Compress the contents of the **geometry** folder into a \.zip file named **geometry\.zip**\. Do not include the **geometry** folder itself; only include the contents of the folder as shown following: 

   ```
   geometry.zip
      __init__.py
      trig/
         __init__.py
         line.py
   ```

1. Upload **geometry\.zip** to your Amazon S3 bucket\.
**Important**  
 If the Amazon S3 bucket does not reside in the same region as your Amazon Redshift cluster, you must use the REGION option to specify the region in which the data is located\. For more information, see [CREATE LIBRARY](r_CREATE_LIBRARY.md)\.

1.  From your SQL client tool, run the following command to install the library\. Replace *<bucket\_name>* with the name of your bucket, and replace *<access key id>* and *<secret key>* with an access key and secret access key from your AWS Identity and Access Management \(IAM\) user credentials\. 

   ```
   CREATE LIBRARY geometry LANGUAGE plpythonu FROM 's3://<bucket_name>/geometry.zip' CREDENTIALS 'aws_access_key_id=<access key id>;aws_secret_access_key=<secret key>';
   ```

 After you install the library in your cluster, you need to configure your functions to use the library\. To do this, run the following commands\. 

```
CREATE OR REPLACE FUNCTION f_distance (x1 float, y1 float, x2 float, y2 float) RETURNS float IMMUTABLE as $$ 
    from trig.line import LineSegment
 
    return LineSegment(x1, y1, x2, y2).distance()
$$ LANGUAGE plpythonu;
 
CREATE OR REPLACE FUNCTION f_within_range (x1 float, y1 float, x2 float, y2 float) RETURNS bool IMMUTABLE as $$ 
    from trig.line import LineSegment
 
    return LineSegment(x1, y1, x2, y2).distance() < 20
$$ LANGUAGE plpythonu;
```

In the preceding commands, `import trig/line` eliminates the duplicated code from the original functions in this section\. You can reuse the functionality provided by this library in multiple UDFs\. Note that to import the module, you only need to specify the path to the subpackage and module name \(`trig/line`\)\. 