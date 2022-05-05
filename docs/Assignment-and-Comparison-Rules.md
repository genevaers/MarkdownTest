# GenevaERS Data Characteristics
  
GenevaERS manipulates several kinds of data items:
- LR field
- View column
- View sort key
- Lookup key
- Logic text function
- Constant

These data items may be defined with the following Data Types:

*Table 1.* **Data Types**
|Data Type|Data Type Category|Data Type Cast Operator|  
|:-|:-|:-|
|Alphanumeric|String|\<ALPHA\>|
|Binary|Numeric|\<BINARY\>|
|Binary-Coded Decimal|Numeric|\<BCD\>|
|Edited Numeric|Numeric|\<EDITED\>|
|Masked Numeric|Numeric|\<MASKED\>|
|Packed Decimal|Numeric|\<PACKED\>|
|Sortable Binary|Numeric|\<SBINARY\>|
|Sortable Packed|Numeric|\<SPACKED\>|
|Zoned Decimal|Numeric|\<ZONED\>|
  
The Data Type for a view column can be overridden by prefixing the `COLUMN` keyword with a cast operator.  For example, if a column is defined as Alphanumeric, that Data Type can be changed to Packed Decimal with the following logic text:  

    <PACKED>COLUMN = {PACKED_FIELD}  
  
Also, the Data Type of an LR field can be overridden with a cast operator.  For example, if a column is defined as Alphanumeric and a field is defined as Packed Decimal, the field can be redefined as Alphanumeric on the fly in logic text as follows:   
  
    COLUMN = <ALPHA>{PACKED_FIELD}  
  
Cast operators can be used in data comparisons as well:  
  
    IF <ALPHA>{PACKED_FIELD} = " "  THEN COLUMN = "Y"  ENDIF  
  
A data item may also be optionally assigned a Date/Time Format that defines the layout of the data it contains.  
  
Date/Time Formats may contain the following elements:

