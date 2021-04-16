# extra\_float\_digits<a name="r_extra_float_digits"></a>

## Values \(default in bold\)<a name="r_extra_float_digits-values"></a>

 **0**, \-15 to 2 

## Description<a name="r_extra_float_digits-description"></a>

Sets the number of digits displayed for floating\-point values, including float4 and float8\. The value is added to the standard number of digits \(FLT\_DIG or DBL\_DIG as appropriate\)\. The value can be set as high as 2, to include partially significant digits; this is especially useful for outputting float data that needs to be restored exactly\. Or it can be set negative to suppress unwanted digits\. 