---
title: Extract report description
nav_order: 25
---
# Extract Report
The different sections of the extract report written to EXTRRPT are described below.

Where report section columns are sorted, the heading of the value they are sorted on is underlined with “=======” rather than “-------".


## PARM - Contents of EXTRPARM file 
The PARM section echos the EXTRPARM file.
```
================================== 
~PARM - Contents of EXTRPARM file: 
================================== 
*   STANDARD OPTIONS                                                   
* 
 RUN_DATE=20170105                      DEFAULT: CURRENT DATE          
*                                                                      
*   DEBUGGING OPTIONS                                                  
* 
 TRACE=Y                                DEFAULT: N                     
 DUMP_LT_AND_GENERATED_CODE=Y           DEFAULT: N                     
 
```
## OPTS - General options in effect
The OPTS section lists all the parameters and values in effect, that is either the default value, or it’s override as specified in the EXTRPARM file.
For some options there may be other factors involved, for example PAGE_FIX_IO_BUFFERS=Y requires the job to be running authorised. If the job is not running authorised, then this parameter will be set to N.

```
==================================               
~OPTS - General options in effect:               
==================================               
                                                 
ABEND_ON_CALCULATION_OVERFLOW       = Y          
ABEND_ON_ERROR_CONDITION            = N          
ABEND_ON_LOGIC_TABLE_ROW_NBR        = 0          
ABEND_ON_MESSAGE_NBR                = 0          
DB2_SQL_PLAN_NAME                   = GVBMRSQ    
DISK_THREAD_LIMIT                   = 9999       
DUMP_LT_AND_GENERATED_CODE          = Y          
EXECUTE_IN_PARENT_THREAD            = N          
FISCAL_DATE_DEFAULT                 = 20090914   
FISCAL_DATE_OVERRIDE                = 1:20080303 
INCLUDE_REF_TABLES_IN_SYSTEM_DUMP   = Y          
IO_BUFFER_LEVEL                     = 4          
LOG_MESSAGE_LEVEL                   = STANDARD   
OPTIMIZE_PACKED_OUTPUT              = Y          
PAGE_FIX_IO_BUFFERS                 = N          
RECOVER_FROM_ABEND                  = Y          
RUN_DATE                            = 20170105   
SOURCE_RECORD_LIMIT                 = 0          
TAPE_THREAD_LIMIT                   = 9999       
TRACE                               = Y          
TREAT_MISSING_VIEW_OUTPUTS_AS_DUMMY = N          
USE_ZIIP                            = N          
VERIFY_CREATION_TIMESTAMP           = Y          
ZIIP_THREAD_LIMIT                   = 9999       
                                                 
```
## TPRM - Contents of EXTRTPRM file
The TPRM section lists the contents of the EXTRTPRM file.
```
==================================                            
~TPRM - Contents of EXTRTPRM file:                            
==================================                            
                                                              
*TRACE KEYWORDS:                                              
VIEW=12074                                                   
FROMREC=1,THRUREC=5,LTFUNC=WR                                
* DISPLAYSOURCE = ACCTLOG2
```
## TOPT - TRACE options in effect
Displays the validated trace options in effect.
```
================================                                                                                                            
~TOPT - TRACE options in effect:                                                                                                            
================================  
                                                                                                                                            
         Func  Source    From Source    Thru Source    From     Thru           >From    Thru    Source Field  Source Field  Source Field    
View ID  Code  DD Name   Record Number  Record Number  LT Row   LT Row         >Column  Column  Value Pos     Value Length  Value           
-------  ----  --------  -------------  -------------  -------  -------        >------  ------  ------------  ------------  ----------------
  12074  WR**                        1              5        0  9999999        >     0  999999                                              
                                                                                                                                            
```

## ENVV  Contents of EXTRENVV file                              
The ENVV section displays the contents of the environment variable file EXTRENVV. 
If DB2 is a source of data, the environment file can be used for variable substitution of the DB2 subsystem name.
```
==================================     
~ENVV - Contents of EXTRENVV file:     
==================================     
$CONNECT=DM12                          
$QUALIFIER=ABC10                    
$OWNER=ABC10                        
```

## RUNV  Contents of RUNVIEWS file                              
The section RUNV displays the contents of RUNVIEWS, a file containing a subset of View numbers to be processed.
```
==================================
~RUNV - Contents of RUNVIEWS file:
==================================  
123
456
789
```
## VIEW  Views selected 
VIEW lists all the views processed in this run. If Views are specified in RUNVIEWS, that subset of views is listed. They are sorted by View ID order.

**Output Phase** values are:
- Extract (extract only view, no format phase), 
- Format (view will have a format phase, so output will be in ‘SAFR standard format’ with a 12-byte prefix.)

**Output Format** values are: 
- Source-Record (output layout is a copy of the source file record layout), 
- Fixed-Length (output fields are fixed length as defined in the columns), 
- Delimited (CSV output will be produced from the Format phase), 
- Report (Format phase will produce a report)

**ERA On**: Y means Extract-phase record aggregation is selected; aggregate records with identical sort keys.

**ERA Buf Size (in records)**: The number of records with different sort keys that can be aggregated in the buffer at one time.

**FRA On**: Y means Format-phase record aggregation is selected; aggregate all records with identical sort keys in the format phase.
```
=======================                                                                                              
~VIEW - Views selected:                                                                                              
=======================                                                                                              
                                                                                                                     
                                                           Output              >Output         ERA  ERA Buf Size  FRA
View ID  View Name                                         Phase               >Format         On   (in Records)  On 
=======  ------------------------------------------------  ---------           >-------------  ---  ------------  ---
   1307  TC_00_04_SALES_TAX                                Format              >Fixed-Length   N               0  N  
   1308  TC_00_05_State_File                               Format              >Fixed-Length   N               0  N  
   1310  TC_00_06_USA_Sales_by_Product                     Format              >Fixed-Length   N               0  Y  
   1312  TC_00_08_Accessories_Sales                        Format              >Report         N               0  Y  
   1313  TC_00_09_Top_Stores                               Format              >Fixed-Length   N               0  Y  
   1317  TC_00_11_Employee_Test                            Format              >Fixed-Length   N               0  N  
   1319  TC_00_12_Real_Sort_Key_Title_Defect               Format              >Report         N               0  Y  
   1320  TC_00_13_Product_list                             Format              >Fixed-Length   N               0  N  
   1321  TC_00_14_Product_Type_List                        Format              >Report         N               0  N  
   1322  TC_00_15_Total_product_Sales                      Format              >Report         N               0  Y  
   1323  TC_00_16_Accessories_by_Month                     Format              >Report         N               0  Y  
   1324  TC_00_17_accessories_by_month_2                   Format              >Report         N               0  Y  
   1325  TC_00_18_Total_Sales_by_Month                     Format              >Report         N               0  Y  
   1326  TC_00_19_Top_Stores                               Format              >Fixed-Length   N               0  Y  
   1333  TC_00_21_Threads_products                         Format              >Fixed-Length   N               0  N  
   ```

## LITP  Literal pool usage (in bytes)                          
This section lists the storage used for constants and addresses, by each Work-unit. The machine code generated by GVBMR95 is reentrant and uses offsets into each literal pool area to access the constants and addresses for each work-unit.
## IRUN  Input run-control files       
IRUN lists the required input files other than the source files and reference files, such as the XLT, VDP files, and reference header files.

Developer note: The VDP and XLT information displayed is from the Generation records (VDP0001 and “GEN”).
```
================================                                                             
~IRUN - Input run-control files:                                                             
================================                                                             
                                                                                             
DD Name   Record Count  Create Date/Time     Created By                                      
========  ------------  -------------------  ---------------------------------->-------------
MR95VDP          2,080  2022-05-04 00:21:50  GVBMR91 PM 4.18.098 D z/OS  
EXTRLTBL         1,383  2022-05-04 00:21:50  GVBMR91 PM 4.18.098         
EXTRREH              7                                                   
```
## IRWF  Input reference-phase work files
IRWF lists all the reference files sorted by DD Name.

The Memory Usage is in bytes and is allocated in 64-bit storage. The actual total memory allocated is rounded up to the nearest MB. The column 'St Dt' indicates if an effective start date is used. The column 'En Dt' indicates if an effective end date is used.
```
=========================================                                                    
~IRWF - Input reference-phase work files:                                                    
=========================================                                                    
                                                                                                                        
                                                                                          Target   Target   Key  St  En 
DD Name   Record Count  Memory Usage    PF Name                                           LR ID    LF ID    Len  Dt  Dt 
========  ------------  --------------  --------------------------------------->--------  -------  -------  ---  --  -- 
REFR001         15,000       1,530,000  PRODUCTS                                              270      199   10  Y   Y  
REFR002         15,000       1,530,000  PRODUCTS                                              270      199   10  Y   Y  
REFR003             29           1,392  PRODUCT_TYPES                                         271      200    2  Y   Y  
REFR004            322          25,438  STORES                                                272      202    5  N   N  
REFR005              9             297  STATES                                                296      215    2  Y   Y  
REFR006             32           1,472  PRODUCT_TYPES_ADVANCED                                271      233    2  Y   Y  
REFR007         15,000       1,410,000  PRODUCTS_ADVANCED                                     270      234   10  Y   Y  
          ------------  --------------                                         >                                        
Total           45,392       4,498,599                                         >                                        
          ============  ==============                                      
```

## ISRC  Input source files                                     
This section lists all the input source files, sorted by Work-Unit and source DD name.

**Thd**: The number of the thread that processed all Views for the source file. A thread may process more than one Work-Unit.

**Work-Unit DD Name**: The DD name of the source that is driving the unit of work.

**Source DD Name**: The source file DD name. For Token view input files. this can be different to the Work-Unit DD name.

**Record Count**: Number of records read.

**Source File Type**: values are: Disk file, Tape, Database, Exit: <*ExitName*>, Token, Pipe

**Byte Count**: Record Count multiplied by record length.
```
===========================                                                                                                              
~ISRC - Input source files:                                                                                                              
===========================                                                                                                              
                                                                                                                                         
     Work-Unit  Source                                                                                                                   
Thd  DD Name    DD Name   Record Count       PF Name                           >               Source File Type       Byte Count         
---  =========  ========  -----------------  ---------------------------------->-------------  ---------------------  -------------------
004  CLMCK00    CLMCK00                  46  CKB_CLMCK                                         Exit: GVBXRCK                        4,646
005  CLMCK01    CLMCK01                   0  CKB_CLMCK1                                        Exit: GVBXRCK                            0
001  CUST0001   CUST0001                 15  CUSTOMERS                                         Disk File                            2,055
002  PRODCODE   PRODCODE                  9  PRODUCTS_FILE                                     Disk File                              864
003  PRODUCT2   PRODUCT2                 11  PRODUCT2                                          Disk File                            1,056
                          -----------------                                    >                                      -------------------
Total                                    81                                    >                                                    8,621
                          =================                                    >                                      ===================
```

## OFIL  Output files 
OFIL lists all the output files, sorted by DD name. Note that multiple threads can write to the same output file.

**Output File Type** values are Work File, Disk file, Pipe, Token.

Developer notes: The counts are taken from the Extract file control area. The PF Name is retrieved from the VDP 200 records.
```
=====================                                                                                                                   
~OFIL - Output files:                                                                                                                   
=====================                                                                                                                   
                                                                                                                                        
------------->  Output                                                                                                                   
------------->  DD Name   Record Count       PF Name                           >               Output File Type       Byte Count         
------------->  ========  -----------------  ---------------------------------->-------------  ---------------------  -------------------
------------->  EXTOUT01                 11  EXTOUT01                                          Work File                              737
------------->  F0001816                  9  Auto-generated Name for Extract Phase Output      Disk File                              603
------------->  F0002029                  1  Auto-generated Name for Extract Phase Output      Disk File                               91
------------->  F0005044                 46  Auto-generated Name for Extract Phase Output      Disk File                              460
------------->            -----------------                                    >                                      -------------------
------------->  Total                    67                                    >                                                    1,891
------------->            =================                                    >                                      ===================
                                                                                                                                         
```

## OWRT  Output details by WRITE function 
OWRT displays details of writes to extract files sorted by Work-Unit DD Name and logic table row number.

**The Source DD Name**: The source file (Pysical file) DD name. For token view sources, this is an internally allocated name for a token source e.g. I0011970

**The Output DD Name**: Similarly for token view sources, this is an internally allocated name for a token output e.g. O0011970

**Record Count**: The number of records written to the output file.

**Output file types**:  Work File, Disk file, Tape, Token, Pipe. A ‘Work File’ is an extract file. i.e. the input to the format phase for a format view.

**Records Input to ERA buffer**: This is only applicable if Extract Phase Record Aggregation is selected. In this case the function code is WRSU. This column is the number of records written into the ERA buffer.

**ERA Savings**: “In” means “Records Input to ERA Buffer” column value and “Out” means “Record Count” column value. This is displayed as a percentage.
```
=========================================                                                                                              
~OWRT - Output details by WRITE function:                                                                                              
=========================================                                                                                              
                                                                                                                                       
     Work-Unit           Func  Source             Output                       >  Output     Write      Records Input to   ERA Savings 
Thd  DD Name    LT Row   Code  DD Name   View ID  DD Name   Record Count       >  File Type  User-Exit  ERA Buffer         (In-Out)/In 
---  =========  =======  ----  --------  -------  --------  -----------------  >  ---------  ---------  -----------------  ------------
001  ORDIT001        38  WRSU  I0011970    10702  EXTR003                 339  >  Work File                           442        23.30%
001  ORDIT001       103  WRSU  I0011970    10714  EXTR002                 339  >  Work File                           442        23.30%
001  ORDIT001       139  WRDT  ORDIT001    10689  DEXLKUPO                  0  >  Disk File                                            
001  ORDIT001       171  WRTK  ORDIT001    10700  O0011970                442  >  Token                                                
001  ORDIT001       190  WRSU  ORDIT001    10715  EXTR001                  41  >  Work File                           442        90.72%
002  ORDIT002        38  WRSU  I0011970    10702  EXTR003                 340  >  Work File                           442        23.07%
002  ORDIT002       103  WRSU  I0011970    10714  EXTR002                 340  >  Work File                           442        23.07%
002  ORDIT002       139  WRDT  ORDIT002    10689  DEXLKUPO                  0  >  Disk File                                            
002  ORDIT002       171  WRTK  ORDIT002    10700  O0011970                442  >  Token                                                
002  ORDIT002       190  WRSU  ORDIT002    10715  EXTR001                  43  >  Work File                           442        90.27%
003  ORDIT003        38  WRSU  I0011970    10702  EXTR003                 340  >  Work File                           441        22.90%
003  ORDIT003       103  WRSU  I0011970    10714  EXTR002                 340  >  Work File                           441        22.90%
003  ORDIT003       139  WRDT  ORDIT003    10689  DEXLKUPO                  0  >  Disk File                                            
003  ORDIT003       171  WRTK  ORDIT003    10700  O0011970                441  >  Token                                                
003  ORDIT003       190  WRSU  ORDIT003    10715  EXTR001                  43  >  Work File                           441        90.24%
```

## LKUP  Lookup steps

**Work-Unit DD Name**: The DD name of the source that is driving the unit of work. 

**Source DD Name**: The source file DD name. This can be different to the Work-Unit DD name for Token input files.

**Runtime LP ID**: The Lookup path ID allocated by the compiler in the MR91 job, starting from 1.

**Step ID**: The step number within the path.

**Lookup Steps Found**: The number of lookup searches executed where the key was matched.
Note that lookups optimized by JOINs will not be counted here, nor lookups where the key matches with the previously found key for that LP ID and Step ID.
**Lookup Steps Not Found**: The number of lookup searches executed where the key was not matched, and not in an effective date range, if applicable.
Note the lookups optimized by JOINs will not be counted here.

**Total Lookup Steps**: Lookup Steps found + Lookup Steps Not Found.

**LP ID**: Lookup path ID allocated in the Workbench.

**Target LR ID**: The target LR of the lookup step.

**Target LF ID**: The target LF of the lookup step.

**St Dt**: Specifies if an effective Start date is used. This is defined in the target LR definition.

**En Dt**: Specifies if an effective end date is used. This is defined in the target LR definition.

**Lookup User Exit**: The exit name defined in the target LR definition.

**Opt**: Specifies if the Lookup User-Exit is defined as optimizable (Y) or non-optimizable (N).

```
=====================                                                                                                                               
~LKUP - Lookup steps:                                                                                                                               
=====================                                                                                                                               
                                                                                                                                                    
     Work-Unit  Source    Runtime  Step  Lookup Steps       Lookup Steps       >Total Lookup                Target   Target   Key  St  En  Lookup        
Thd  DD Name    DD Name   LP ID    ID    Found              Not Found          >Steps              LP ID    LR ID    LF ID    Len  Dt  Dt  User-Exit  Opt
---  =========  ========  =======  ====  -----------------  -----------------  >-----------------  -------  -------  -------  ---  --  --  ---------  ---
041  CLMCKN00   CLMCKN00       43     1                 10                  0  >               10      418      427      343    1  N   N   GVBXLCK    Y  
041  CLMCKN00   CLMCKN00       44     1                 10                  0  >               10      419      437      343    1  N   N   GVBXLCK    Y  
001  CO000001   CO000001        1     1                  9                  1  >               10      385      270      199   14  Y   Y                 
001  CO000001   CO000001        1     2                  9                  0  >                9      385      271      200    6  Y   Y                 
001  CO000001   CO000001        2     1                  9                  1  >               10      414      270      199   14  Y   Y                 
001  CO000001   CO000001        2     2                  4                  5  >                9      414      354      266    6  Y   Y                 
001  CO000001   CO000001        2     3                  3                  0  >                3      414      187     1069    5  N   N                 
001  CO000001   CO000001        3     1                  9                  1  >               10      379      270      199   14  Y   Y                 
001  CO000001   CO000001        3     2                  4                  5  >                9      379      354      266    6  Y   Y                 
001  CO000001   CO000001        3     3                  3                  0  >                3      379      187     1069    5  N   N                 
001  CO000001   CO000001        3     4                  4                  0  >                4      379      296      215    6  Y   Y                 
001  CO000001   CO000001        4     1                  1                  9  >               10      403      270      315   14  Y   Y                 
001  CO000001   CO000001        5     1                  8                  2  >               10      409      270      313   14  Y   Y                 
001  CO000001   CO000001        6     1                  8                  2  >               10      411      270      312   14  Y   Y                 
001  CO000001   CO000001        7     1                  0                 10  >               10      398      187     1069    5  N   N                  

```

## MEMS  MR95 memory usage (in bytes)

Self-explanatory.

**I/O buffers**: This is a sum of Write buffers (allocated at initialization time) and Read buffers high water mark (allocated and cleaned up by threads as they are used).
```
=====================================                             
~MEMS - MR95 memory usage (in bytes):                             
=====================================                             
                                                                  
                                   Below-the-Bar   Above-the-Bar  
Description                        Memory Usage    Memory Usage   
=================================  --------------  -------------- 
Generated machine code                    121,112                 
Logic table                               683,252                 
VDP data                                5,160,200                 
Literal pools                              35,504                 
I/O buffers                            10,403,825                 
Reference tables                                        1,048,576 
Extract-phase record aggr buffers                       2,097,152 
                                   --------------  -------------- 
Total                                  16,403,893       3,145,728 
                                   ==============  ==============

```

## CPUT  CPU time (available only in APF-authorized mode) 
These statistics are available in authorized mode only; an enclave is created, and the statistics are requested using the function IWMEQTME.
```
===================================================== 
~CPUT - CPU time (available only in authorized mode): 
===================================================== 
                                                      
Description                  HHHH:MM:SS.hh  Percent   
===========================  -------------  -------   
zIIP-eligible time on zIIP            0.00     0.00   
zIIP-eligible time on CP              0.00     0.00   
                             -------------  -------   
zIIP-eligible time                    0.00     0.00   
Other time                            0.00     0.00   
                             -------------  -------   
Total enclave CPU time                0.00     0.00   
===========================  -------------  -------   
                                                      
Total number of SRB/TCB switches:                 0   
```

## EXEC  Execution summary 

**External files** refers to Physical files of type Database, Disk File, or Tape File.

```
==========================                        
~EXEC - Execution summary:                        
==========================                        
                                                  
Views processed:                                2 
Parallel threads executed:                      4 
Lookup steps performed:                54,891,548 
Elapsed time (HHHH:MM:SS.hh):          0:00:28.26 
Elapsed time (seconds):                     28.26 
                                                  
                                                  
External files read:                            3 
External records read:                 27,500,000 
External bytes read:                  990,000,000 
                                                  
Pipe/Token files read:                          1 
Pipe/Token records read:               27,500,000 
Pipe/Token bytes read:              1,402,500,000 
                                                  
Read Exit files read:                           0 
Read Exit records read:                         0 
Read Exit bytes read:                           0 
                                                  
Total source files read:                        4 
Total source records read:             55,000,000 
Total source bytes read:            2,392,500,000 
                                                  
                                                  
External files written:                         1 
External records written:              27,500,000 
External bytes written:               935,000,000 
                                                  
Pipe/Token files written:                       1 
Pipe/Token records written:            27,500,000 
Pipe/Token bytes written:           1,512,500,000 
                                                  
Total output files written:                     2 
Total output records written:          55,000,000 
Total output bytes written:         2,447,500,000  

```