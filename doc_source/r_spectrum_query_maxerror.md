# spectrum\_query\_maxerror<a name="r_spectrum_query_maxerror"></a>

## Values \(default in bold\)<a name="r_spectrum_query_maxerror-values"></a>

**\-1**, integer

## Description<a name="r_spectrum_query_maxerror-description"></a>

You can specify an integer to indicate the maximum number of errors to accept before canceling the query\. A negative value turns off maximum error data handling\. The results are logged in [SVL\_SPECTRUM\_SCAN\_ERROR](r_SVL_SPECTRUM_SCAN_ERROR.md)\.

## Example<a name="r_spectrum_query_maxerror-example"></a>

The following example assumes ORC data that contains surplus characters and invalid characters\. The column definition for `my_string` specifies a length of 3 characters\. Following is the sample data for this example:

```
my_string
---------------
abcdef
gh�
ab
```

The following commands set the maximum number of errors to 1 and perform the query\. 

```
set spectrum_query_maxerror to 1;
SELECT my_string FROM orc_data;
```

The query stops and the results are logged in [SVL\_SPECTRUM\_SCAN\_ERROR](r_SVL_SPECTRUM_SCAN_ERROR.md)\.