---
title: GenevaERS User Exit Programming Guide
nav_order: 1
---

# User Exit Overview

The [Introduction to User Exit Routines](Intro20-User-Exit_Routines.md) gives an overview of User Exits.  

Exits are a common concept that allows software products to call custom coded programs at specific points to perform functions the tool itself does not do.
GenevaERS has four major points which can invoke a user exit.  The first three are Extract Phase exits, and are used much more frequently than the Format Phase exit.  They are:
  - __Read Exits__, which present event file records to GenevaERS threads for processing.  It is associated with an event LR and the RENX logic table function, and is called each time the event LR records in the input buffer have been processed.
  - __Lookup Exits__, which accept join parameters and return looked up records in response to individual joins.  These exits can also be used as simple function calls.  It is associated with a lookup LR and a LUEX logic table function (rather than a LUSM which searches the in memory lookup data), and is called each time a join to that LR is required.  
  - __Write Exits__, which accept extract records and can manipulate them before being written to extract files.  It is associated with a view or a write statement within a view and the WREX logic table function (rather than WRIN, WRDT, or WRSU functions) and creates the extract record  
  - __Format Exits__, the only GVBMR88 exit which accept summarized and formatted Format Phase output records prior to being written to files.  It is associated with a view that is assigned to the Format Phase.  Format exits are very similar to write exits, except that the record being dealt with is the final output record, rather than the extract record.  Because of its infrequent use, we won’t describe this exit in any more detail here.

# Sample User Exits and Relative Exit Complexities

The following gives a better sense of each exit type, and the complexity and options involved for each one. 

## Read Exit
In the early days of GenevaERS a sample read exit read the GenevaERS Field Definition file and calculated the start position of each field based upon the lengths of the prior fields in the file, a field not stored on the file.  GenevaERS at the time did not read VSAM files, the format of the Field Definition File, so the Read exit performed this function. THe exit detected a change in the logical record for the fields, and start the running position over at 0.  This allowed GenevaERS to produce reports of its own metadata.

Besides the file format, it was written because this isn’t a function well suited to GenevaERS.  GenevaERS doesn’t do many functions that carry values from one record to the next.  And it doesn’t do any "look ahead" functions, like when the next record in the file determines if the current record should be written.  GenevaERS is built for business event processing, not “set processes” evaluating a set of records.

To use this exit one must create a logical record which matched the output from the read exit.  That output was the 20 fields or so on Field Definition File, plus the new field.  GenevaERS knows nothing about the input to the read exit.  For all GenevaERS knew, the exit could have read 10 different files and combined them all together to make an event file.  It could have added 19 of the fields on to the field before passing it on to GenevaERS.  

The read exit emulates an access method; in other words instead of GenevaERS calling the system BSAM access method to get data from disk, it calls the program name specified.  This means that the program must return the results that an access method would return.   Access methods that read from disks don’t know about the actual length of the data records processed by the program.  Rather their record lengths are blocks.  

This read exit read (using a the standard VSAM access method) and manipulated one Field Definition record at a time.  It could have been created as if the block size was the length of one of my field definition records, perhaps 50 bytes in length.  The block size would equal the Logical Record Length (LRECL).  However, this meant that GenevaERS would be calling the exit each time a new record was needed.  The overhead for calling a subprogram is very high.  GenevaERS has to save its register values before calling, and then they have to be restored before going back.  This CPU time can really add up on a large file.  

Thus a more efficient method is for the read exit to process many Field Definition records as if they had come from disk as one block, and then returning the full block to GenevaERS.  GenevaERS will parse the individual records according to the Logical Record Length, and only return to the read exit when a new block of data is needed.   

This process can make a read exit quite complex to create.

## Lookup Exits 

Look-up exits are probably the easiest to create.  This is because they accept a set of parameters and return a record.  The parameters passed to the lookup exit are whatever values are placed in the fields of the join key.  These can be constants, fields from the event file, or fields from another lookup, including calls to other exits.  The output from the lookup exit is a record that must match the LR for the “reference file” record it is to return.   

Although it appears to a GenevaERS developer as if GenevaERS has taken the keys and performed a search of a reference table to find the appropriate record, the exit may have done no such thing.  In fact, it could do something as simple as reordering the fields passed to it and returning the record.

An early sample lookup exit kept a copy of the key it had been passed in the last call, and compared the key from the current call with that key.  If the keys were the same, then it returned a “not found” condition.  If they were different, it returned a “found” condition.  This exit was called by multiple views, which had selection logic to only include found records. This allowed selection of an event record only once no matter how many views it qualified for.  

Lookup exits have been written to perform math calculations that GenevaERS didn’t do natively.  In fact, before GenevaERS had extract time calculations, a generalized user exit allowed passing two numbers and an operator.  The resulting record contained the results of the math.  Many GenevaERS functions have first appeared as user exits, and were later folded into the main code base.

## Write Exits

Extract exits are called whenever a view is to write an extract record.  The write exit is passed the extract record, and the event record.  It can evaluate these records and determine if it was to: 
  -	Tell GenevaERS to write a record the exit specifies (could be any record) and continue with event file processing.
  -	Tell GenevaERS to skip this extract record and go on
  -	Tell GenevaERS to write a record the exit specifies (could be any record) and return to the exit to do more processing.

The exit can manipulate the exit record; substitute a new record, table the extract record in some way and then dump the table at the end of event file processing, among others.  Note, though, that unlike read exits which open and actually read files, write exits typically do not.  They return records to GenevaERS to write to the extract file.  They could do their own IO, but there is typically no benefit to doing so.  GenevaERS’s write routines are very efficient.  

Write exits are in between read and lookup exits for complexity.  This is mainly because of the complexity of dealing with extract records.  The exit must know what the extract record will look like for a particular view.  This might be easiest to determine by actually writing the view, and inspecting the extracted records to find positions and lengths.  Any changes in the views can create a new to update the write exit.  Write exits outputs typically have nothing to do with LRs.  

[Extract Time Summarization](Intro18-ETS_and_Other_Functions.md) made its first appearance as a write exit.  The exit was called on each extract record.  The exit allocated its own memory space, and built a stack of summarized event records.  Only if the extract record caused the exit to overflow the stack did the exit tell GenevaERS to write a record.  When the thread has finished processing, on the last call to the write exit, the write exit would loop through the stack and instruct GenevaERS to write each record in the stack and return to it for additional processing until the internal table was empty.

## Calling and Parameter Overview

Each exit receives a set of static parameters that do not change throughout event file processing.  These can be used to further generalize an exit.

For example, the small lookup exit described above accepted a set of static parameters that described how long the passed key was from the join.  It would use this length to determine what length of data to store and compare.  In this way, this same exit could be used on multiple joins with different keys, from 5 bytes to 50 bytes, regardless of whether the key was composed of 1 field or 50.  This same approach was used by the calculation exit so that it could work with different sizes of numbers being passed to it, and different arithmetic functions. 

Each exit is called:
 - Once before event file processing begins so it can initialize memory and set up shop. 
 - Multiple times during processing, depending on the exit type (per block of data required for read exits, for each lookup, or for each extract record to be written) 
 - One time after event file processing is complete to perform clean up.  The purpose of these calls are indicated to the exit through a phase code.

# Calling Parameters

The GenevaERS Performance Engine Extract Engine (GVBMR95) uses a standard call parameter list when invoking user exits.  This structure is passed to user exits by MR95 (and other custom GenevaERS processes like XRCK the common key buffering process) to pass data (where applicable) and return codes back to MR95 via this structure.  

Below are the COBOL and Assembler versions of the Structures (copybooks) with explanations of the individual fields above the corresponding field.  

##  Environment Data

### X95PARM1-ENV-DATA. 

The Value of X95PARM1-ENV-DATA Is Passed From GVBMR95.  

    01  X95PARM1-ENV-DATA. 
      05  X95PARM1-THREAD-NBR         PIC S9(04)  COMP.
      05  X95PARM1-PHASE-CODE         PIC  X(02).
          88  X95PARM1-OPEN-PHASE     VALUE 'OP'.
          88  X95PARM1-READ-PHASE     VALUE 'RD'.
          88  X95PARM1-CLOSE-PHASE    VALUE 'CL'.
      05  X95PARM1-CURRENT-VIEW-ID    PIC S9(09)  COMP.
      05  X95PARM1-ENV-VAR-TABLE-PTR  POINTER.
      05  X95PARM1-JOIN-STEP-COUNT    PIC S9(09)  COMP.
      05  X95PARM1-JOIN-STACK-PTR     POINTER.
      05  X95PARM1-PROCESS-DATE-TIME.
          10  X95PARM1-PROCESS-DATE   PIC  X(08).
          10  X95PARM1-PROCESS-TIME   PIC  X(08).
      05  X95PARM1-ERROR-REASON       PIC S9(08)  COMP.
      05  X95PARM1-ERROR-BUFFER-PTR   POINTER.
      05  X95PARM1-ERROR-BUFFER-LEN   PIC S9(08)  COMP.

Specifics of each field are below.  

The Thread Number is the number of the thread (the event file number) being processed.

      05  X95PARM1-THREAD-NBR         PIC S9(04)  COMP.

The Phase Code defines what type of call to the exit is being executed in this instance. 

      05  X95PARM1-PHASE-CODE         PIC  X(02).
          88  X95PARM1-OPEN-PHASE     VALUE 'OP'.
          88  X95PARM1-READ-PHASE     VALUE 'RD'.
          88  X95PARM1-CLOSE-PHASE    VALUE 'CL'.

- Open is a call to the exit the first time, before event file record processing begins
- Read is on each call to the exit during event file record processing
- Close is after event file record processing has finished.

The View ID is the view calling the exit.  This parameter is not applicable for Read Exits

      05  X95PARM1-CURRENT-VIEW-ID    PIC S9(09)  COMP.

The following need greater definition here in this document. 

      05  X95PARM1-ENV-VAR-TABLE-PTR  POINTER.
      05  X95PARM1-JOIN-STEP-COUNT    PIC S9(09)  COMP.
      05  X95PARM1-JOIN-STACK-PTR     POINTER.

The Process Date and Time is the start-up time of the Extract Phase process.

      05  X95PARM1-PROCESS-DATE-TIME.
          10  X95PARM1-PROCESS-DATE   PIC  X(08).
          10  X95PARM1-PROCESS-TIME   PIC  X(08).

The Error Reason code gives some description of why the process was aborted. 

      05  X95PARM1-ERROR-REASON       PIC S9(08)  COMP.

The Error Buffer pointer and length fields allow for indicative data that caused the error to be printed in GVBMR95 control and run-time messages.  

      05  X95PARM1-ERROR-BUFFER-PTR   POINTER.
      05  X95PARM1-ERROR-BUFFER-LEN   PIC S9(08)  COMP.

### X95PARM2-EVENT-FILE-DATA.

The Event File structure gives the view data relative to the event file being processed by this thread.

    01  X95PARM2-EVENT-FILE-DATA.
        05  X95PARM2-EVENT-DDNAME       PIC  X(08).
        05  X95PARM2-EVENT-REC-NBR      PIC S9(11)  COMP-3.
        05  X95PARM2-EVENT-REC-FMT      PIC  X(01).
            88  X95PARM2-FIXED-LENGTH       VALUE 'F'.
            88  X95PARM2-VARIABLE-LENGTH    VALUE 'V'.
            88  X95PARM2-DELIMITED          VALUE 'D'.
        05  X95PARM2-REC-FLD-DELIM      PIC  X(01).
        05  X95PARM2-EVENT-REC-LEN      PIC S9(09)  COMP.
        05  X95PARM2-MAX-REC-LEN        PIC S9(09)  COMP.
        05  X95PARM2-MAX-BLOCK-SIZE     PIC S9(09)  COMP.

Specifics of each field are below.  

The Value Of X95PARM2-EVENT-DDNAME Is Passed From GVBMR95.  This is the Event File DD Name of the thread being processed.

     05  X95PARM2-EVENT-DDNAME       PIC  X(08).

The Value Of X95PARM2-EVENT-REC-NBR Is Passed From GVBMR95.  This is the event file record number that is currently being processed.

     05  X95PARM2-EVENT-REC-NBR      PIC S9(11)  COMP-3.

Read Exits set the value of X95PARM2-EVENT-REC-FMT in the Open Phase and pass it to GVBMR95.  GVBMR95 passes the value to Lookup Exits And Write Exits, representing the format of the event file being processed by this thread, for this and the following parameters.

     05  X95PARM2-EVENT-REC-FMT      PIC  X(01).
         88  X95PARM2-FIXED-LENGTH       VALUE 'F'.
         88  X95PARM2-VARIABLE-LENGTH    VALUE 'V'.
         88  X95PARM2-DELIMITED          VALUE 'D'.

Read Exits set the value of X95PARM2-REC-FLD-DELIM in the Open Phase and pass it to GVBMR95.  GVBMR95 passes the value to Lookup Exits and Write Exits.

     05  X95PARM2-REC-FLD-DELIM      PIC  X(01).

Read Exits set the value of X95PARM2-Event-Rec-Len on every execution and pass it to GVBMR95.  GVBMR95 passes the value to Lookup Exits and Write Exits.

     05  X95PARM2-EVENT-REC-LEN      PIC S9(09)  COMP.

Read Exits set the value of X95PARM2-Max-Rec-Len in the Open Phase and pass it to GVBMR95.  GVBMR95 passes the value to Lookup Exits and Write Exits.

     05  X95PARM2-MAX-REC-LEN        PIC S9(09)  COMP.

Read Exits set the value of X95PARM2-Max-Block-Size in the Open Phase and pass it to GVBMR95.  GVBMR95 passes the value to Lookup Exits and Write Exits.

     05  X95PARM2-MAX-BLOCK-SIZE     PIC S9(09)  COMP.

## Variable Start-Up Parameters

### X95PARM3-STARTUP-DATA

The layout of X95PARM3-Startup-Data is defined by the GenevaERS configuration for the specific processes involved.  So the structure of the record layout is dependent upon the record layouts (LRs) the GenevaERS configuration.  The startup data values are constant throughout event file record processing.

These parameters can be used to generalize an exit to be used for more GenevaERS processes, for example telling the exit to perform a set of functions when called in this context throughout this GenevaERS processing.  For example, a single lookup exit might do multiple mathematical functions, and the function to be performed each time it is called for a specific lookup logical record could be specified as a start-up parameter. 

The structure and values to be passed to each exit for each type of exit are set in the workbench in the following location:

- For Read Exits, in the Physical File Entity. 
- For Lookup Exits, in the Logical Record Entity when the lookup exit is specified. 
- For Write Exits, in the View Properties Or On The Write Verb in the View Logic Text. 

These values are passed to the Exit Program from GVBMR95.

    01  X95PARM3-STARTUP-DATA           PIC  X(32).

When the exit is first called (the Open Phase call) the exit needs to point the record layout inside your program (in the example below the Ls-Startup-Parms)to the location of value passed as X95PARM3-Startup-Data by executing the statement:

`Set Address Of Ls-Startup-Parms To Address Of X95PARM3-Startup-Data`

## Event, Extract and Lookup Record Parameters

### X95PARM4-EVENT-REC

X95PARM4-Event-Rec is not applicable to Read Exits. For Lookup and Write Exits, this parameter points to the current record from the Event File.

Like the Start-up data, the structure of the event file record is dependent upon the specific GenevaERS processing being performed. 

    01  X95PARM4-EVENT-REC              PIC  X(01).

To point your exits definition of the event file record (for this example, Ls-Event-Rec) to the address of the data contained i X95PARM4-Event-Rec execute the statement:

`Set Address Of Ls-Event-Rec To Address Of X95PARM4-Event-Rec`

Do this whenever this program is given control as the location of the event file record changes on each call to the exit.

### X95PARM5-EXTRACT-REC

Like the Event File Record structure, X95PARM5-Extract-Rec is not applicable to Read Exits. For Lookup and Write Exits, this parameter points to the current Extract Record.  

The layout of the beginning of each extract record is fixed as outlined in [Introduction to Format Phase Views](Intro17_Format_Phase_Views.md), but the layout of the rest of the record is determined by the view.  The following is the structure of the fixed portion of the record:

    01  X95PARM5-EXTRACT-REC.
        05  X95PARM5-EXTRACT-RDW.
            10  X95PARM5-EXT-REC-LENGTH     PIC S9(04)  COMP.
            10  FILLER                      PIC S9(04)  COMP.
        05  X95PARM5-EXTRACT-PREFIX.
            10  X95PARM5-SORT-KEY-LENGTH    PIC S9(04)  COMP.
            10  X95PARM5-TITLE-KEY-LENGTH   PIC S9(04)  COMP.
            10  X95PARM5-DATA-AREA-LENGTH   PIC S9(04)  COMP.
            10  X95PARM5-NBR-CT-COLS        PIC S9(04)  COMP.
            10  X95PARM5-VIEW-ID            PIC S9(09)  COMP.
        05  X95PARM5-EXTRACT-VAR-LEN-AREA   PIC  X(01).

To point your exits copybook structure (for this example, Ls-Extract-Data) to the address of the data contained in X95PARM5-Var-Len-Area execute the statement whenever this program is given control:

`Set Address Of Ls-Extract-Rec To Address Of X95PARM5-Extract-Var-Len-Area`


### X95PARM6-LOOKUP-KEY

Like the prior parameters, the layout of X95PARM6-Lookup-Key is dependent upon the GenevaERS processes being run, and the metadata defining them.  For Read and Write Exits this parameter is not applicable. 

For Lookup Exits the structure is set in the View Logic Text or in Join Step Parameters. The values are populated by the logic table LK rows. This value is passed to the exit program from GVBMR95.

    01  X95PARM6-LOOKUP-KEY             PIC  X(256).

To point your program's copybooks structure (in this example, Ls-Lookup-Parms) to the address of the data contained in X95PARM6-Lookup-Key execute the statement whenever this program is given control:

`Set Address Of Ls-Lookup-Parms To Address Of X95PARM6-Lookup-Key`

## Working Storage

### X95PARM7-WORK-AREA-ANCHOR
GVBMR95 does not guarantee that working storage in a COBOL program will be remain in the same state on subsequent calls to the program, so any data items which need to be retained between calls should be placed in a dynamically allocated area, and the address of that area stored in this pointer, which will be returned by GVBMR95 on each subsequent call.

 01  X95PARM7-WORK-AREA-ANCHOR       POINTER.

During the open phase, the GenevaERS routine GVBUR05 should be called to allocate a work area (in this example, LS-Work-Area) and X95PARM7-WORK-AREA-ANCHOR should be set to point to it.  For example:

`Move Length Of Ls-Work-Area To Ws-Work-Area-Length`

`Call GVBUR05 Using X95PARM7-Work-Area-Anchor Ws-Work-Area-Length`

`Set Address Of Ls-Work-Area To X95PARM7-Work-Area-Anchor`

On Subsequent Calls, X95PARM7-WORK-AREA-ANCHOR retains its value and may be pointed to the structure again, by using:

`Set Address Of Ls-Work-Area To X95PARM7-Work-Area-Anchor`

## Return Code and Result Set

### X95PARM8-RETURN-CODE
X95PARM8-Return-Code should be set in the exit program to the numeric code to be returned to GVBMR95.

      01  X95PARM8-RETURN-CODE            PIC S9(09)  COMP.
      *  All Exits:
          88  X95PARM8-SUCCESSFUL             VALUE +0.
      *  Lookup Exits Only:
          88  X95PARM8-NOT-FOUND              VALUE +4.
      *  Write Exits Only:
          88  X95PARM8-WRITE-AND-REPEAT-CALL  VALUE +4.
      *  READ EXITS ONLY:
          88  X95PARM8-END-OF-FILE            VALUE +8.
      *  LOOKUP EXITS ONLY:
          88  X95PARM8-SKIP-EVENT-REC         VALUE +8.
      *  WRITE EXITS ONLY:
          88  X95PARM8-SKIP-EXTRACT-REC       VALUE +8.
      *  ALL EXITS:
          88  X95PARM8-DISABLE-CURRENT-VIEW   VALUE +12.
          88  X95PARM8-ABORT-RUN              VALUE +16.

__Lookup Exits__:
- _Lookup Found+_:  A return value of +0 signals GVBMR95 that the lookup exit successfully returned a result that can be used by the view.
- _Lookup Not Found_:  Alternatively a return code value of +4 signals GVBMR95 that no successful result was returned, so the view should use the Not Found condition.
- _Lookup Skip Event File Record_: A +8 signals GVBMR95 that the view should completely bypass further processing of the event file record.  This means execution continues with the next view, so no extract record will be written for the view for this record, but the view is not disabled from processing future records (see Disabled below).

__Write Exits__:
- _Write Extract Record_:  A return value of +0 signals GVBMR95 to write the record returned by the Write Exit to the extract file.
- _Write and Repeat Call_: A return code of +4 signals GVBMR95 to write the record returned by the Write Exit, and then return to the Write Exit for further processing.  This return code was used by the initial Extract Time Summarization write exit to dump all tabled records to the extract file at the conclusion of processing in the Close Phase with the last call being a +0 return code.
- _Skip Extract Record_:  A return code of +8 signals GVBMR95 that no extract record should be written after this call, and processing should continue to the next logic table function.

__Read Exits__: 
- _Process Buffer_:  A return code of +0 signals GVBMR95 to process the buffer returned by the Read Exit through the views.  
- _End of File_:  A return code of +8 signals GVBMR95 that end of file has been reached by the read exit; GVBMR95 should move to the Close Phase.


__Error Processing__
- _Disable Current View_:  Only applicable for Lookup and Write Exits, (Not Read Exits, which are not view specific) a return code of +12 signals GVBMR95 to disable the view for further processing.  Although extract records have been written to the output file, no further records will be written.  The control report will signal a view was disabled.  If the view is a Format Phase View, the extract records will be skipped and no output will be produced.  GenevaERS cannot stop automatically downstream non-extract file processing, but GenevaERS facilities can be used to build processes to detect disabled views.
- _Abort Processing_:  A return code of +16 signals GVBMR95 to immediately stop all processing.

The X95PARM1-ERROR-REASON (in the PARM1 structure) can be populated for either the disable view or abort run return values.  This value will be printed in the z/OS Message Logs, along with the indicative data pointed to by the error buffer pointer.

### X95PARM9-RESULT-PTR and X95PARMA-RESULT-BLOCK-SIZE

Like Exit Working Storage, memory for the result sets are not provided by GVBMR95. Exits are responsible for allocations of this memory. X95PARM9-RESULT-PTR should be set in the exit program to the address of the structure to be returned to GVBMR95. 

     01  X95PARM9-RESULT-PTR             POINTER.

The values and structures returned in this area must match the LRs defined for them, for either Look-up or Read Exits. 

Because Write Exit create only extract record typically written directly to extract files and not subject to further view processing, they may not have this requirement.  If the view results are piped to another set of views, they may also need to match the describing LRs.  

To point X95PARM9-Result-Ptr to your own structure (in this example, Ws-Result), execute the statement whenever this program is given control:

`Set X95PARM9-Result-Ptr To Address Of Ws-Result`

_Lookup Exits Only_:  Though Optional, it is a good exit programming practice to move -1 to X95PARM9-Result-Ptr-Numeric when the lookup result is "Not Found".  This guarantees that the pointer is no longer pointing to valid data.

    01  X95PARM9-RESULT-PTR-NUMERIC     REDEFINES
        X95PARM9-RESULT-PTR             PIC S9(09)  COMP.

_Read Exits Only_: During the Open Phase, Read Exits should return the first event record block. 

Read Exits should set the value of X95PARMa-Result-Block-Size to the length of the block pointed to by X95PARM9-Result-Ptr.

    01  X95PARMA-RESULT-BLOCK-SIZE      PIC S9(09)  COMP.

END X95PARMA-RESULT-BLOCK-SIZE

# ASSEMBLER VERSION GVBX95PA

The following are the structures if z/OS Assembler is used for the exit.

    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *                                                                     *
    *        GenevaERS Provided Parameter List 
    *                                                                     *
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *
    GENPARM  DSECT                  GenevaERS  PARAMETER LIST
    *
    GPENVA   DS    AL04             ENVIRONMENT  INFO ADDR
    GPFILEA  DS    AL04             FILE         INFO ADDR
    GPSTARTA DS    AL04             STARTUP      DATA ADDR
    GPEVENTA DS    AL04             EVENT      RECORD ADDR      (N/A)
    GPEXTRA  DS    AL04             EXTRACT    RECORD ADDR      (N/A)
    GPKEYA   DS    AL04             LOOK-UP    KEY    ADDR      (N/A)
    GPWORKA  DS    AL04             WORK AREA POINTER ADDR
    *
    GPRTNCA  DS    AL04             RETURN CODE       ADDR
    GPBLOCKA DS    AL04             OUTPUT BLOCK PTR  ADDR
    GPBLKSIZ DS    AL04             OUTPUT BLOCK SIZE ADDR
            SPACE 3
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *        Environment Information
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *
    GENENV   DSECT                  ENVIRONMENT INFORMATION
    *
    GPTHRDNO DS    HL02             CURRENT THREAD   NUMBER
    GPPHASE  DS    CL02             CURRENT EXECUTION PHASE
    GPVIEW#  DS    FL04             CURRENT EXECUTING  VIEW
    GPENVVA  DS    AL04             ENV VARIABLE TABLE ("MR95ENVV") ADDR
    GPJSTPCT DS    FL04             JOIN STEP COUNT
    GPJSTKA  DS    AL04             JOIN STACK ADDRESS
    GP_PROCESS_DATE_TIME DS 0XL16   Process Date and time
    GP_PROCESS_DATE      DS  XL8    Date
    GP_PROCESS_TIME      DS  XL8    Time
    GP_ERROR_REASON      DS  FL04   Error reason code
    GP_ERROR_BUFFER_PTR  DS  AL04   -> error text
    GP_ERROR_BUFFER_LEN  DS  FL04   To exit - set to length of buffer
    *                               from exit - set to text length
            Space 3
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *        File Information
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *
    GENFILE  DSECT                  EVENT FILE   INFORMATION
    *
    GPDDNAME DS    CL08             EVENT  FILE  DDNAME
    GPRECCNT DS    PL06             EVENT  FILE  RECORD COUNT
    GPRECFMT DS    CL01             RECORD FORMAT  (F,V,D)
    GPRECDEL DS    CL01             RECORD DELIMITER
    GPRECLEN DS    FL04             RECORD LEN   CURRENT
    GPRECMAX DS    FL04             RECORD LEN   MAXIMUM
    GPBLKMAX DS    FL04             BLOCK  SIZE  MAXIMUM
    *
            Space 3
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *        Startup Data Information
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *
    GENSTART DSECT
    *
    GP_STARTUP_DATA DS CL32
    *
            Space 3
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *        Event Record Information
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *
    GENEVENT DSECT
    *
    GP_EVENT_REC    DS C
    *
            Space 3
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *        Extract Record Information
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *
    GENEXTR  DSECT
    *
    GP_EXTRACT_REC          DS  0CL17
    GP_EXTRACT_RDW          DS  0XL4
    GP_EXT_REC_LENGTH       DS  XL2
                            DS  XL2
    GP_EXTRACT_PREFIX       DS  0CL12
    GP_SORT_KEY_LENGTH      DS  XL2
    GP_TITLE_KEY_LENGTH     DS  XL2
    GP_DATA_AREA_LENGTH     DS  XL2
    GP_NBR_CT_COLS          DS  XL2
    GP_VIEW_ID              DS  XL4
    GP_EXTRACT_VAR_LEN_AREA DS  C
    *
            Space 3
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *        Look-Up Key Information
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *
    GENKEY   DSECT
    *
    GP_LOOKUP_KEY  DS  CL128
    *
            Space 3
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *        Work Area Ptr Information
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *
    GENWORK  DSECT
    *
    GP_WORK_AREA_ANCHOR DS  A
    *
            Space 3
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *        Return Code Information
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *
    GENRTNC  DSECT
    *
    GP_RETURN_CODE DS  A
    *
            Space 3
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *        Output Block Ptr Information
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *
    GENBLOCK DSECT
    *
    GP_RESULT_PTR   DS  A
    *
            Space 3
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *        Output Block Size Information
    * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
    *
    GENBLKSZ DSECT
    *
    GP_RESULT_BLK_SIZE DS F
    *







 

# Links

Place following text in the topic:  
    ````
    [Topic A](TopicA)
    ````

The link displays as:   
[Topic A](TopicA)