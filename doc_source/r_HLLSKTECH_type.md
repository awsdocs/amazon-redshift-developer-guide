# HLLSKETCH type<a name="r_HLLSKTECH_type"></a>

Use the HLLSKETCH data type for HyperLogLog sketches\. Amazon Redshift supports HyperLogLog sketch representations that are either sparse or dense\. Sketches begin as sparse and switch to dense when the dense format is more efficient to minimize the memory footprint that is used\.

 Amazon Redshift automatically transitions a sparse HyperLogLog sketch when importing, exporting, or printing sketches in the following JSON format\.

```
{"logm":15,"sparse":{"indices":[4878,9559,14523],"values":[1,2,1]}}
```

Amazon Redshift uses a string representation in a Base64 format to represent a dense HyperLogLog sketch\.

Amazon Redshift uses the following string representation in a Base64 format to represent a dense HyperLogLog sketch\.

```
"ABAABA..."
```