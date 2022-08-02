---
title: GenevaERS User Exit Programming Guide
nav_order: 1
---

# User Exit Overview

Exits are a common tool that allow a software product to call custom coded programs at specific points to perform functions the product itself does not do.
GenevaERS has four points which can invoke a user exit.  The first three are Extract Phase exits, and the last one a Format Phase exit.
  - __Read Exits__ present source data to GenevaERS threads for processing.  It is associated with an Extract-Phase Source Logical Record. It is called each time the source records in the input buffer have been processed, until EOF is indicated. IT is associated with the REEX logic table function.
  - __Lookup Exits__ return records in response to Lookup parameters, such as a key.  These exits can be used as simple function calls. It is associated with a lookup LR and a LUEX logic table function (rather than a LUSM which searches the in memory lookup data), and is called each time a lookup to that LR is required.  
  - __Write Exits__ accept extract records and can manipulate them before being written to extract files. It is associated with a View or a write statement within a view and creates the extract record, or can indicate to skip a record.
  - __Format Exits__, the only GVBMR88 exit which accept summarized and formatted Format Phase output records prior to being written to files. It is associated with a View that is assigned to the Format Phase.  Format exits are very similar to write exits, except that the record being dealt with is the final output record, rather than the extract record. Because of its infrequent use, we won’t describe this exit in any more detail here.

The [Introduction to User Exit Routines](Intro20-User-Exit_Routines.md) gives an overview of User Exits and how to define them in the GenevaERS Workbench.  

# Sample User Exits and Relative Exit Complexities

The following gives a better sense of each exit type, and the complexity and options involved for each one. 

## Read Exit

The read exit emulates an access method; in other words instead of GenevaERS calling the system BSAM access method to get data from disk, it calls the program name specified. This means that the program must return the results that an access method would return. GenevaERS can handle data records returned in blocks with RECFM, LRECL and BLKSIZ set accordingly. This is an efficient way of passing data to GenevaERS, so the exit does not need to be called for every new record, only for every new block.

The most efficient method is for the read exit to process many records as if they had come from disk as one block, and then return the full block to GenevaERS.  GenevaERS will parse the individual records according to the Logical Record Length, and only return to the read exit when a new block of data is needed.

## Lookup Exits 

Lookup exits accept a set of parameters and return a record.  The parameters passed to the lookup exit are whatever values are placed in the fields of the Lookup key. These can be constants, fields from the source file, or fields from another lookup, including calls to other exits. The output from the lookup exit is a record that must match the LR for the “reference file” record it is to return.

Although it appears to a GenevaERS developer as if GenevaERS has taken the keys and performed a search of a reference table to find the appropriate record, the exit may have done no such thing.  In fact, it could do something as simple as reordering the fields passed to it and returning the record.

One example of a lookup exit kept a copy of the key it had been passed in the last call, and compared the key from the current call with that key. If the keys were the same, then it returned a “not found” condition.  If they were different, it returned a “found” condition.  This exit was called by multiple views, which had selection logic to only include found records. This allowed selection of a source record only once no matter how many views it qualified for.  

Lookup exits can be written to perform complex mathematical calculations that GenevaERS doesn’t do natively. The resulting record contains the result of the calculation.

## Write Exits

Extract exits are called whenever a view is to write an extract record. The write exit is passed the extract record, and the source record. It can evaluate these records and determine if it is to: 
  -	Tell GenevaERS to write a record the exit specifies (could be any record) and continue with source file processing.
  -	Tell GenevaERS to skip this extract record and go on
  -	Tell GenevaERS to write a record the exit specifies (could be any record) and return to the exit to do more processing.

The exit can manipulate the exit record; substitute a new record, table the extract record in some way and then dump the table at the end of source file processing, among others.  Note, though, that unlike read exits which open and actually read files, write exits typically do not.  They return records to GenevaERS to write to the extract file.  They could do their own IO, but there is typically no benefit to doing so. GenevaERS’s write routines are very efficient.  

The write exit must know what the extract record will look like for a particular View. This might be easiest to determine by writing the View, then generating the LR for the extract record. Any changes in the Views can create a new to update the write exit.

Write exits are defined in the WRITE statement.

## Calling and Parameter Overview

Each exit receives a set of static parameters that do not change throughout source file processing.  These can be used to further generalize an exit.

For example, a lookup exit will accept a set of parameters that described how long the lookup key is. It can use this length to determine what length of data to store and compare. In this way, this same exit could be used on multiple lookups with different keys, of different lengths, regardless of whether the key was composed of 1 field or 50. This same approach can be used by a calculation exit so that it can work with different sizes of numbers, and different arithmetic functions.

The Each exit is called:
 - Once before each source file is processed, typically in a new subtask, so it can initialize memory. This is the OPEN phase.
 - Multiple times during processing, depending on the exit type (per block of data required for read exits, for each lookup, or for each extract record to be written). This is called the READ phase, even for lookup and write exits.
 - One time after each source file is processed to perform clean up. This is the CLOSE phase.
 
Assembler write exits are also called:
 - Once before any subtasks are attached. This is the INIT phase.
 - Once after all subtasks are complete. This is the TERM phase.
 
 This is specifically designed so that the write exit can open files from the parent task, and close those files when all subtasks (source file reads) are complete.

# Calling Parameters

The GenevaERS Performance Engine Extract Engine (GVBMR95) uses a standard call parameter list when invoking user exits. This structure is passed to user exits by GVBMR95 to pass and receive data (where applicable) and return codes.  

Below are the COBOL and Assembler versions of the Structures (copybooks) with explanations of the individual fields above the corresponding field.  

The [COBOL copybook for GenevaERS exit parameter list](this should be a link to the actual source) 

Note: The terms 'Event data' and 'Event file' were previously used to refer to the Source data and Source file. The term 'join' was previously used for the term 'lookup'.

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

The Thread Number is the number of the thread (the source file number) being processed.

      05  X95PARM1-THREAD-NBR         PIC S9(04)  COMP.

The Phase Code defines what type of call to the exit is being executed in this instance. 

      05  X95PARM1-PHASE-CODE         PIC  X(02).
          88  X95PARM1-OPEN-PHASE     VALUE 'OP'.
          88  X95PARM1-READ-PHASE     VALUE 'RD'.
          88  X95PARM1-CLOSE-PHASE    VALUE 'CL'.

- Open is a call to the exit the first time, before source file record processing begins
- Read is on each call to the exit during source file record processing
- Close is after source file record processing has finished.

The View ID is the view calling the exit.  This parameter is not applicable for Read Exits

      05  X95PARM1-CURRENT-VIEW-ID    PIC S9(09)  COMP.

The Environment Variable Table pointer is the address of the environment variable list. Environment variables can be set in DD EXTRENVV. Table entries are described by ENVVTBL in [GVBMR95C](https://github.com/genevaers/pe/blob/main/PM4/MAC/GVBMR95C.MAC)

      05  X95PARM1-ENV-VAR-TABLE-PTR  POINTER.

These parameters are only applicable for Lookup Exits. X95PARM1-JOIN-STACK-PTR points to a slot in a stack where the address of the last found lookup from this exit is saved. The slots are managed by GVBMR95, but can be referenced by User exits. Each slot format is 12 bytes; 4 byte LR ID and 8 byte address of 'last found'.

      05  X95PARM1-JOIN-STEP-COUNT    PIC S9(09)  COMP.
      05  X95PARM1-JOIN-STACK-PTR     POINTER.

The Process Date and Time is the data and time stamp from the XLT records.

      05  X95PARM1-PROCESS-DATE-TIME.
          10  X95PARM1-PROCESS-DATE   PIC  X(08).
          10  X95PARM1-PROCESS-TIME   PIC  X(08).

The following fields can be set by the User Exit.

If the Error Reason code is set to be non-zero, then the text in the Error Buffer will be written to EXTRLOG and WTO.

      05  X95PARM1-ERROR-REASON       PIC S9(08)  COMP.

The Error Buffer pointer and length fields allow for indicative data that caused the error to be printed to EXTRLOG and WTO. The maximum length of the buffer is 126 bytes.

      05  X95PARM1-ERROR-BUFFER-PTR   POINTER.
      05  X95PARM1-ERROR-BUFFER-LEN   PIC S9(08)  COMP.

### X95PARM2-EVENT-FILE-DATA.

The Event File structure gives the view data relative to the source file being processed by this thread. Read exits may set these fields. If there is no read exit GVBMR95 sets these values.

    01  X95PARM2-EVENT-FILE-DATA.
        05  X95PARM2-EVENT-DDNAME       PIC  X(08).
        05  X95PARM2-EVENT-REC-NBR      PIC  9(16)  COMP.
        05  X95PARM2-EVENT-REC-FMT      PIC  X(01).
            88  X95PARM2-FIXED-LENGTH       VALUE 'F'.
            88  X95PARM2-VARIABLE-LENGTH    VALUE 'V'.
            88  X95PARM2-DELIMITED          VALUE 'D'.
        05  X95PARM2-REC-FLD-DELIM      PIC  X(01).
        05  X95PARM2-EVENT-REC-LEN      PIC S9(09)  COMP.
        05  X95PARM2-MAX-REC-LEN        PIC S9(09)  COMP.
        05  X95PARM2-MAX-BLOCK-SIZE     PIC S9(09)  COMP.

Specifics of each field are below.  

The Value Of X95PARM2-EVENT-DDNAME Is Passed From GVBMR95.  This is the source File DD Name of the thread being processed.

     05  X95PARM2-EVENT-DDNAME       PIC  X(08).

The value of X95PARM2-EVENT-REC-NBR is passed from GVBMR95.  This is the source file record number that is currently being processed. It is an 8 byte binary count.

     05  X95PARM2-EVENT-REC-NBR      PIC  9(16)  COMP.

Read Exits may set the value of X95PARM2-EVENT-REC-FMT in the Open Phase and pass it to GVBMR95. GVBMR95 passes the value to Lookup Exits And Write Exits, representing the format of the source file being processed by this thread, for this and the following parameters.

     05  X95PARM2-EVENT-REC-FMT      PIC  X(01).
         88  X95PARM2-FIXED-LENGTH       VALUE 'F'.
         88  X95PARM2-VARIABLE-LENGTH    VALUE 'V'.
         88  X95PARM2-DELIMITED          VALUE 'D'.

Read Exits may set the value of X95PARM2-REC-FLD-DELIM in the Open Phase and pass it to GVBMR95. GVBMR95 passes the value to Lookup Exits and Write Exits.

     05  X95PARM2-REC-FLD-DELIM      PIC  X(01).

Read Exits may set the value of X95PARM2-EVENT-REC-LEN on every execution and pass it to GVBMR95. GVBMR95 passes the value to Lookup Exits and Write Exits.

     05  X95PARM2-EVENT-REC-LEN      PIC S9(09)  COMP.

Read Exits may set the value of X95PARM2-MAX-REC-LEN in the Open Phase and pass it to GVBMR95. GVBMR95 passes the value to Lookup Exits and Write Exits.

     05  X95PARM2-MAX-REC-LEN        PIC S9(09)  COMP.

Read Exits may set the value of X95PARM2-MAX-BLOCK-SIZE in the Open Phase and pass it to GVBMR95. GVBMR95 passes the value to Lookup Exits and Write Exits.

     05  X95PARM2-MAX-BLOCK-SIZE     PIC S9(09)  COMP.

## Variable Start-Up Parameters

### X95PARM3-STARTUP-DATA

Startup data values can be set for Read, Lookup and Write exits. There is no fixed layout of X95PARM3-Startup-Data. The maximum length allowed is 32 bytes.

These parameters can be used to generalize an exit to be used for more GenevaERS processes, for example telling the exit to perform a set of functions when called in this context throughout this GenevaERS processing.  For example, a single lookup exit might do multiple mathematical functions, and the function to be performed each time it is called for a specific lookup logical record could be specified as a start-up parameter. 

The structure and values to be passed to each exit for each type of exit are set in the workbench in the following location:

- For Read Exits, in the Physical File Entity. 
- For Lookup Exits, in the Logical Record Entity when the lookup exit is specified. 
- For Write Exits, in the View Properties Or On The Write Verb in the View Logic Text. 

These values are passed to the Exit Program from GVBMR95.

    01  X95PARM3-STARTUP-DATA           PIC  X(32).

When the exit is first called (the Open Phase call) the exit needs to point the record layout inside your program (in the example below the Ls-Startup-Parms) to the location of value passed as X95PARM3-Startup-Data by executing the statement:

`Set Address Of Ls-Startup-Parms To Address Of X95PARM3-Startup-Data`

## Source, Extract and Lookup Record Parameters

### X95PARM4-EVENT-REC-PTR

X95PARM4-EVENT-REC-PTR is not applicable to Read Exits. For Lookup and Write Exits, this parameter points to the current record from the Source File.

Like the Startup data, the structure of the Source file record is dependent upon the specific GenevaERS processing being performed. 

    01  X95PARM4-EVENT-REC-PTR.
           05  X95PARM4-EVENT-REC-PTR-HIGH     POINTER.
           05  X95PARM4-EVENT-REC-PTR-LOW      POINTER.

X95PARM4-EVENT-REC-PTR points to an 8 byte 64-bit address. 
Currently GenevaERS only supports COBOL exits compiled for 31-bit addressing, but some of the parameters contain 64-bit addresses. COBOL exits must use the low 4-bytes of the address only.

<!-- more notes here on this topic? -->

To point your exit's definition of the source file record (for this example, LS-EVENT-REC) to the address of the data contained in X95PARM4-EVENT-REC execute the statement:

`Set Address Of LS-EVENT-REC To X95PARM4-EVENT-REC-PTR-LOW`

Do this whenever this program is given control as the location of the source file record changes on each call to the exit.

### X95PARM5-EXTRACT-REC

Like the Source File Record structure, X95PARM5-Extract-Rec is not applicable to Read Exits. For Lookup and Write Exits, this parameter points to the current Extract Record.  

The layout of the beginning of each extract record is fixed as outlined in [Introduction to Format Phase Views](Intro17_Format_Phase_Views.md), but the layout of the rest of the record is determined by the View. The following is the structure of the fixed portion of the record:

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

To point your exit's copybook structure (for this example, Ls-Extract-Data) to the address of the data contained in X95PARM5-Var-Len-Area execute the statement whenever this program is given control:

`Set Address Of Ls-Extract-Rec To Address Of X95PARM5-Extract-Var-Len-Area`

### X95PARM6-LOOKUP-KEY

This parameter is only applicable to Lookup exits. Like the prior parameters, the layout of X95PARM6-Lookup-Key is dependent upon the GenevaERS processes being run, and the metadata defining them.

For Lookup Exits the structure is set in the View Logic Text or in Join Step Parameters. The values are populated by the logic table LK rows. This value is passed to the exit program from GVBMR95.

    01  X95PARM6-LOOKUP-KEY             PIC  X(256).

To point your program's copybooks structure (in this example, Ls-Lookup-Parms) to the address of the data contained in X95PARM6-Lookup-Key execute the statement whenever this program is given control:

`Set Address Of Ls-Lookup-Parms To Address Of X95PARM6-Lookup-Key`

## Working Storage

### X95PARM7-WORK-AREA-ANCHOR
GVBMR95 does not guarantee that working storage in a COBOL program will remain in the same state on subsequent calls to the program, so any data items which need to be retained between calls should be placed in a dynamically allocated area, and the address of that area stored in this pointer, which will be returned by GVBMR95 on each subsequent call.

 01  X95PARM7-WORK-AREA-ANCHOR       POINTER.

During the open phase, the GenevaERS routine GVBUR05 should be called to allocate a work area (in this example, LS-Work-Area) and X95PARM7-WORK-AREA-ANCHOR should be set to point to it.  

For example:

          MOVE LENGTH OF LS-WORK-AREA TO WS-WORK-AREA-LENGTH
          CALL GVBUR05
              USING X95PARM7-WORK-AREA-ANCHOR
                    WS-WORK-AREA-LENGTH
          SET ADDRESS OF LS-WORK-AREA TO
              X95PARM7-WORK-AREA-ANCHOR

On Subsequent Calls, X95PARM7-WORK-AREA-ANCHOR retains its value and may be pointed to the structure again, by using:

          SET ADDRESS OF LS-WORK-AREA TO X95PARM7-WORK-AREA-ANCHOR

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
- _Lookup Found+_:  A return value of +0 signals to GVBMR95 that the lookup exit successfully returned a result pointed to by X95PARM9-RESULT-PTR.
- _Lookup Not Found_:  Alternatively a return code value of +4 signals GVBMR95 that no successful result was returned, so the view should use the Not Found condition.
- _Lookup Skip Event File Record_: A +8 signals GVBMR95 that the view should completely bypass further processing of the event file record.  This means execution continues with the next record, so no extract record will be written for the view for this record, but the view is not disabled from processing future records (see Disabled below).

__Write Exits__:
- _Write Extract Record_:  A return value of +0 signals GVBMR95 to write the record returned by the Write Exit pointed to by X95PARM9-RESULT-PTR, to the extract file.
- _Write and Repeat Call_: A return code of +4 signals GVBMR95 to write the record returned by the Write Exit, and then return to the Write Exit for further processing.
- _Skip Extract Record_:  A return code of +8 signals GVBMR95 that no extract record should be written after this call, and processing should continue to the next logic table function.

__Read Exits__: 
- _Process Buffer_:  A return code of +0 signals GVBMR95 to process the buffer returned by the Read Exit.
- _End of File_:  A return code of +8 signals GVBMR95 that end of file has been reached by the read exit; The current GVBMR95 subtask should move to the Close Phase.

__Error Processing__
- _Disable Current View_:  Only applicable for Lookup and Write Exits, (Not Read Exits, which are not view specific) a return code of +12 signals GVBMR95 to disable the view for further processing. The control report will signal a view was disabled.

     If the view is a Format Phase View, the extract records will be skipped and no output will be produced.  GenevaERS cannot stop automatically downstream non-extract file processing, but GenevaERS facilities can be used to build processes to detect disabled views.
- _Abort Processing_:  A return code of +16 signals GVBMR95 to immediately stop all processing.

The X95PARM1-ERROR-REASON (in the PARM1 structure) can be populated for any return values. This value will be printed in the z/OS Message Logs, along with the indicative data pointed to by the error buffer pointer.

### X95PARM9-RESULT-PTR and X95PARMA-RESULT-BLOCK-SIZE

Memory for the result sets are not provided by GVBMR95. Exits are responsible for allocations of this memory. X95PARM9-RESULT-PTR should be set in the exit program to the address of the structure to be returned to GVBMR95.

X95PARM9-RESULT-PTR points to an 8 byte 64-bit address. Currently GenevaERS only supports COBOL exits compiled for 31-bit addressing, but some of GenevaERS's parameters contain 64-bit addresses. COBOL exits must use the low 4-bytes of the address only. Hence for the COBOL version of the parameter list we define a 'high' and 'low' field.

<!-- more notes here on this topic? -->

       01  X95PARM9-RESULT-PTR.
           05  X95PARM9-RESULT-PTR-HIGH        POINTER.
           05  X95PARM9-RESULT-PTR-NUMERICH    REDEFINES
               X95PARM9-RESULT-PTR-HIGH        PIC S9(09)  COMP.
           05  X95PARM9-RESULT-PTR-LOW         POINTER.
           05  X95PARM9-RESULT-PTR-NUMERICL    REDEFINES
               X95PARM9-RESULT-PTR-LOW         PIC S9(09)  COMP.


To point X95PARM9-RESULT-PTR to your own structure (in this example, WS-RESULT), execute the statement whenever this program is given control:

      SET  X95PARM9-RESULT-PTR-LOW TO ADDRESS OF WS-RESULT                        
      MOVE ZERO TO X95PARM9-RESULT-PTR-NUMERIC

_Lookup Exits Only_:  Though Optional, it is a good exit programming practice to move -1 to X95PARM9-RESULT-PTR-NUMERICH and X95PARM9-RESULT-PTR-NUMERICL when the lookup result is "Not Found". This guarantees that the pointer is no longer pointing to valid data.

    01  X95PARM9-RESULT-PTR-NUMERIC     REDEFINES
        X95PARM9-RESULT-PTR             PIC S9(09)  COMP.

_Read Exits Only_: During the Open Phase, Read Exits should return the first event record block.

Read Exits should set the value of X95PARMA-RESULT-BLOCK-SIZE to the length of the block pointed to by X95PARM9-RESULT-PTR.

    01  X95PARMA-RESULT-BLOCK-SIZE      PIC S9(09)  COMP.


# ASSEMBLER VERSION GVBX95PA

The [Assembler dsects for GenevaERS exit parameter list](this should be a link to the actual source) 




# Links

Place following text in the topic:  
    ````
    [Topic A](TopicA)
    ````

The link displays as:   
[Topic A](TopicA)