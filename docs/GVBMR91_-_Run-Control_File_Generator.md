# GVBMR91 - Run-Control File Generator  
  
## GVBMR91 Input File Syntax   
  
### Format: MR91PARM file  
<pre><body bgcolor="LightGray">
►►───┬─INPUT_TYPE=WBXML─────────────────────┬───────────────────────────────────►  
     ├─INPUT_TYPE=VDPXML────────────────────┤    
     └─INPUT_TYPE=DB2────┤ db2 parameters ├─┘  
       
     ┌─OUTPUT_WB_XML_FILES=N─┐                                                  
►────┴───────────────────────┴──────────────────────────────────────────────────►   
  
     ┌─OUTPUT_VDP_XML_FILE=N─┐                                                     
►────┴───────────────────────┴──────────────────────────────────────────────────►  
  
     ┌─OUTPUT_RUN_CONTROL_FILES=N─┐                                              
►────┴────────────────────────────┴─────────────────────────────────────────────►  
         
     ┌────────────────────────────┐                                             
     ▼                            │                                             
►──────┤ selected output-option ├─┴─────────────────────────────────────────────►  
    
►────┤ general options ├───────────────────────────────────────────────────────►◄ 
<body bgcolor="LightGray"</pre>  
  

### db2 parameters:
<pre>
├────DB2_SUBSYSTEM=<i>db2-subsystem</i>────────────────────────────────────────────────►  
    
►────DB2_SCHEMA=<i>db2-schema</i>──────────────────────────────────────────────────────►  
   
►────DB2_ENVIRONMENT_ID=<i>db2-environment-id</i>──────────────────────────────────────┤  
</pre>

  
### selected output-option:
<pre>
├────┬─OUTPUT_WB_XML_FILES=Y──────┬─────────────────────────────────────────────┤  
     ├─OUTPUT_VDP_XML_FILE=Y──────┤  
     └─OUTPUT_RUN_CONTROL_FILES=Y─┘  
</pre>
  
*`db2-subsystem`*    
This is the name of the Db2 subsystem where your metadata resides.  (For example, DM12.)
  
*`db2-schema`*    
This is the name of the Db2 schema where your metadata resides.  (For example, GENDEV.)
  
*`db2-environment-id`*    
This is the environment ID (such as 15) of the location of the GenevaERS views to be selected.  
    
