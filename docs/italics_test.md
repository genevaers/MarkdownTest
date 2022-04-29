 # Italics Test
 
 ### Format: MR91PARM file   
 code block

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
  
### db2 parameters:        
single backtick code 

> `|----DB2_SUBSYSTEM=`*`db2_subsystem`*`------------------------------------------------>`  
> ` `   
> `<----DB2_SCHEMA=`*`db2-schema`*`------------------------------------------------------>`   
> ` `  
> `<----DB2_ENVIRONMENT_ID=`*`db2_environment-id`*`--------------------------------------|`  

*`db2_subsystem`*    
This is the name of the DB2 subsystem.


### db2 parameters:        
triple backtick code 

```
|----DB2_SUBSYSTEM=*db2_subsystem*------------------------------------------------>  
  
<----DB2_SCHEMA=*db2-schema*------------------------------------------------------>
  
<----DB2_ENVIRONMENT_ID=*db2_environment-id*--------------------------------------|
```

*`db2_subsystem`*    
This is the name of the DB2 subsystem.


### db2 parameters:        
triple backtick code with js:expose=true

```js:expose=true
|----DB2_SUBSYSTEM=*db2_subsystem*------------------------------------------------>  
  
<----DB2_SCHEMA=*db2-schema*------------------------------------------------------>
  
<----DB2_ENVIRONMENT_ID=*db2_environment-id*--------------------------------------|
```

*`db2_subsystem`*    
This is the name of the DB2 subsystem.

### db2 parameters:        

HTML \<pre\> tag

<pre>
|----DB2_SUBSYSTEM=<i>db2_subsystem</i>------------------------------------------------>  
    
<----DB2_SCHEMA=<i>db2-schema</i>------------------------------------------------------>  
   
<----DB2_ENVIRONMENT_ID=<i>db2_environment-id</i>--------------------------------------|  
</pre>

<pre><i>db2_subsystem</i></pre>  
This is the name of the DB2 subsystem.


### db2 parameters:        
<code>
|----DB2_SUBSYSTEM=<i>db2_subsystem</i>------------------------------------------------>  
  
<----DB2_SCHEMA=<i>db2-schema</i>------------------------------------------------------>  
  
<----DB2_ENVIRONMENT_ID=<i>db2_environment-id</i>--------------------------------------| 
</code>

<code><i>db2_subsystem</i></code>  
This is the name of the DB2 subsystem.

