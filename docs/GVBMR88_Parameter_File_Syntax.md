# GVBMR88 Parameter File Syntax 
  
### Format: MR88PARM file
<pre>
     ┌─SORT_EXTRACT_FILE=Y─┐                                                    
►►───┼─────────────────────┼────────────────────────────────────────────────────►
     └─SORT_EXTRACT_FILE=N─┘
  
     ┌─PROCESS_HEADER_RECORDS=Y─┐ 
►────┼──────────────────────────┼───────────────────────────────────────────────►
     └─PROCESS_HEADER_RECORDS=N─┘       
  
     ┌─ABEND_ON_CALCULATION_OVERFLOW=Y─┐ 
►────┼─────────────────────────────────┼────────────────────────────────────────►
     └─ABEND_ON_CALCULATION_OVERFLOW=N─┘              

     ┌─ABEND_ON_DIVISION_BY_ZERO=Y─┐                                                          
►────┼─────────────────────────────┼────────────────────────────────────────────►
     └─ABEND_ON_DIVISION_BY_ZERO=N─┘            

     ┌─RUN_DATE=current-date─┐                                                    
►────┼───────────────────────┼──────────────────────────────────────────────────► 
     └─RUN_DATE=ccyymmdd─────┘ 

     ┌─FISCAL_DATE_DEFAULT=run-date─┐ 
►────┼──────────────────────────────┼───────────────────────────────────────────► 
     └─FISCAL_DATE_DEFAULT=ccyymmdd─┘ 

     ┌─────────────────────────────────────────────────────┐ 
     ▼                                                     │ 
►──────┬─────────────────────────────────────────────────┬─┴───────────────────►◄ 
       └─FISCAL_DATE_OVERRIDE=control-record-id:ccyymmdd─┘
</pre>            