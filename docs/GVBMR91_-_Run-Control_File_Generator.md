### Format: MR91PARM file
<pre>
>>---+-INPUT_TYPE=WBXML---------------------+----------------------------------->  
     +-INPUT_TYPE=VDPXML--------------------+    
     '-INPUT_TYPE=DB2----| db2 parameters |-'  
       
     .-OUTPUT_WB_XML_FILES=N-.  
>----+-----------------------+-------------------------------------------------->   
  
     .-OUTPUT_VDP_XML_FILE=N-.  
>----+-----------------------+-------------------------------------------------->  
  
     .-OUTPUT_RUN_CONTROL_FILES=N-.  
>----+----------------------------+--------------------------------------------->  
         
     .----------------------------.  
     V                            |  
>------| selected output-option |-+--------------------------------------------->  
    
>----| general options |------------------------------------------------------->< 
</pre>  
  

### db2 parameters:
<pre>
|----DB2_SUBSYSTEM=<i>db2-subsystem</i>------------------------------------------------>  
    
<----DB2_SCHEMA=<i>db2-schema</i>------------------------------------------------------>  
   
<----DB2_ENVIRONMENT_ID=<i>db2-environment-id</i>--------------------------------------|  
</pre>

*`db2-subsystem`*    
This is the name of the DB2 subsystem.
  
  
### selected output-option:
<pre>
|----+-OUTPUT_WB_XML_FILES=Y------+---------------------------------------------|  
     +-OUTPUT_VDP_XML_FILE=Y------+  
     '-OUTPUT_RUN_CONTROL_FILES=Y-'  
</pre>
