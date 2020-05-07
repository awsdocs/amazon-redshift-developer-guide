# Use the smallest possible column size<a name="c_best-practices-smallest-column-size"></a>

Don't make it a practice to use the maximum column size for convenience\. 

Instead, consider the largest values you are likely to store in a VARCHAR column, for example, and size your columns accordingly\. Because Amazon Redshift compresses column data very effectively, creating columns much larger than necessary has minimal impact on the size of data tables\. During processing for complex queries, however, intermediate query results might need to be stored in temporary tables\. Because temporary tables are not compressed, unnecessarily large columns consume excessive memory and temporary disk space, which can affect query performance\. 