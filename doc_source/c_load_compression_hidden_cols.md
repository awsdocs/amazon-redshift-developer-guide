# Optimizing storage for narrow tables<a name="c_load_compression_hidden_cols"></a>

If you have a table with very few columns but a very large number of rows, the three hidden metadata identity columns \(INSERT\_XID, DELETE\_XID, ROW\_ID\) will consume a disproportionate amount of the disk space for the table\.

 In order to optimize compression of the hidden columns, load the table in a single COPY transaction where possible\. If you load the table with multiple separate COPY commands, the INSERT\_XID column will not compress well\. You will need to perform a vacuum operation if you use multiple COPY commands, but it will not improve compression of INSERT\_XID\.