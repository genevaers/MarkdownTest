---
title: Introduction to User Exits
nav_order: 21
---

# Introduction to User Exits

This page provides you with more information about GenevaERS User Exits.  It will help you to: 
- Describe uses for SAFR user-exit routines 
- Read a Logic Table and Trace which use exits 
- Explain the Function Codes used in the example
- Debug views using exits  

In this page, we examine the following Logic Table Functions:
- REEX - Read a new Event Record with a user exit
- LUEX - Lookup calling a user exit
- WREX - Write calling a user exit

 [Prior Logic Table Functions Codes can be located here.](Intro11a_Logic_Table_Function_Codes.md)

<div style="clear: right" >

# GenevaERS Scan Performance Engine

<img style="float: right;" width="50%" vspace="5" alt="GenevaERS Scan Performance Engine" src=images/Module20-User-Exit_Routines/Module20_Slide4.jpeg title="GenevaERS Scan Performance Engine"/>

Let’s briefly review the GenevaERS Performance Engine processes.

<div style="clear: right" >

## Metadata and View Creation, and the GenevaERS Repository

<img style="float: right;" width="50%" vspace="5" alt="Metadata and View Creation, and the GenevaERS Repository" src=images/Module20-User-Exit_Routines/Module20_Slide5.jpeg title="Metadata and View Creation, and the GenevaERS Repository"/>

GenevaERS begins by developers creating metadata describing records, files, fields, relationships between files and fields, and user exits which might be called to perform processing in the workbench.  They then create views, which specify the logic to be applied to the data through the scan processes.  The metadata and the views are stored in the GenevaERS View and Metadata repository, awaiting the start the Performance Engine.

<div style="clear: right" >

## Select, Logic and Reference File Phases

<img style="float: right;" width="50%" vspace="5" alt="Select, Logic and Reference File Phases" src=images/Module20-User-Exit_Routines/Module20_Slide6.jpeg title="Select, Logic and Reference File Phases"/>

The first parts of the Performance engine performs the Select, Logic and Reference File phases.  The Select Phase selects the views and associated metadata for those views from the View and Metadata Repository.  Alternatively, this phase can read data provided in a GenevaERS XML schema, which defines the metadata and functions to be performed against the data.  It bundles either of these into a VDP, View Definition Parameter file.

The VDP is used in the Logic Phase to produce two Logic Tables, one for the Reference File Phase, and one for the Extract Phase.  The logic tables contain the functions codes described in this and the prior pages.  

The Extract Phase Logic Table file is used to process event files, but before that the Reference File Logic Table is used to extract a core image file from the reference files.  The Reference File Logic Table does not contain any selection logic, thus it does not remove any rows of data.  Rather, the core image file contains only those fields (columns) needed to be loaded into memory for joins, plus the keys required for the join processes and any effective dates.

<div style="clear: right" >

## Extract Phase

<img style="float: right;" width="50%" vspace="5" alt="Extract Phase" src=images/Module20-User-Exit_Routines/Module20_Slide7.jpeg title="Extract Phase"/>

The Extract Phase begins by loading the VDP, Extract Phase Logic Table and Reference Data from disk.  It then uses the logic table to generate machine code for each thread, each input file partition. It then opens input and output files, and executes these threads according to the thread governor, in parallel.  Each event file record is read and processed through the logic table, performing joins, and writing selected data to output extract files. 

The Extract Phase includes multiple types of user exits, including read, write and look-up exits, highlighted on this graphic by the green balls.

<div style="clear: right" >

## Format Phase

<img style="float: right;" width="50%" vspace="5" alt="Format Phase" src=images/Module20-User-Exit_Routines/Module20_Slide8.jpeg title="Format Phase"/>

The format phase, which is optional depending on the view requirements, begins by sorting each extract file using a custom generated sort card based upon the sort criteria of the views written to that extract file.  During the final phase of sort processing—that which writes data to disk—the records are passed in memory to the format engine.  These records are formatted according to the VDP specifications.  The data is written to the final output file.

The Format Phase includes the Format Exit, shown here at the end of the Format process.  Sort utilities also allow creating sort input exits, shown here over the Sort process.

Let’s overview each of these users exits.

<div style="clear: right" >

# User Exit Overview

<img style="float: right;" width="50%" vspace="5" alt="User Exit Overview" src=images/Module20-User-Exit_Routines/Module20_Slide9.jpeg title="User Exit Overview"/>

User exits, which can also be thought of as API points, are custom programs to perform functions GenevaERS does not do natively.  For example, GenevaERS does not perform an exponential mathematical calculation.  A customer could create a program to perform this function, and call it from GenevaERS to return the results of the calculation.

As noted, GenevaERS has four major points which can invoke a user exit, read, lookup, write and format exits.  The first three are Extract Phase exits, and are used much more frequently than the fourth Format Phase exit.  They are:

- Read Exits stand between the actual input event file and the GenevaERS views.  These exits can modify input records to be presented to GenevaERS threads for processing. 
- Lookup Exits stand between GenevaERS views and the look-up data loaded into memory.   Lookup exits accept join parameters and return looked up records in response to individual joins. These exits can also be used as simple function calls which do not actually perform any look-up.  For example the exponential calculation discussed above could be written as a look-up exit.
- Write Exits stand between GenevaERS and the extract files.  They receive extract records and can manipulate them before being written to extract files.  
- Format Exits, the only GVBMR88 exit, accepts summarized and formatted Format Phase output records prior to being written to files. Format exits are very similar to write exits, except that the record used is the final output record, rather than the extract record.  

At the end of this page we will touch on a non-GenevaERS exit, the Sort Input Exits.

<div style="clear: right" >

## Read Exit Functionality

<img style="float: right;" width="50%" vspace="5" alt="Read Exit Functionality" src=images/Module20-User-Exit_Routines/Module20_Slide10.jpeg title="Read Exit Functionality"/>

As noted, read exits sit between the extract engine, and the input event file.  The GenevaERS views, inside GVBMR95, are only aware of the virtual file which is output by the read exit.  Read Exits are assigned to a physical file, and are placed on the RENX logic table function.  It is called each time the RENX function is executed.  

Examples of read exits that have been written for GenevaERS applications include:
- Read a specialized database structure and extract every record to be passed to GenevaERS to allow GenevaERS to produce reports against those records
- Merge multiple sequential files and compare snap shot records with the history file into a single master file, then used to produce reports
- Process sets of records, and perform functions across the entire set, where one record can affect other either later, or earlier records.  GenevaERS does not easily perform these functions in view.  After all affects are determined and applied, the file is passed to GenevaERS for report generation.

Read exits are typically the most complex to write, because they must perform some IO of some kind.  They are further complicated because it is very inefficient to call a read exit for each record; so instead they are usually written to do block level processing.  

<div style="clear: right" >

## Look-up Exits Functionality

<img style="float: right;" width="50%" vspace="5" alt="Look-up Exits Functionality" src=images/Module20-User-Exit_Routines/Module20_Slide11.jpeg title="Look-up Exits Functionality"/>

Look-up exits sit between the extract engine, and a potential look-up file. The GenevaERS views are only aware of the virtual lookup record output by the lookup exit.  A sample application could be doing direct reads to a database table to retrieve a join value for processing.  However, most often look-up exits do not actually load any data from disk; rather they simply use input parameters passed to them by the views to do some function.  Thus the exit is basically a simple function call.  

Look-up exits are the easiest type of exit to create. The parameters passed to the lookup exit are the values placed in the fields of the join key.  These can be constants, fields from the event file, or fields from another lookup, including calls to other exits.  The output from the lookup exit is a record that must match the LR for the “reference file” record it is to return.   Although it appears to a GenevaERS developer as if GenevaERS has taken the keys and performed a search of a reference table to find the appropriate record, the exit may have done no such thing.  In fact, it could do something as simple as reordering the fields passed to it and returning the record.

Lookup Exits are assigned to a target look-up LR.  When used, the typical LUSM functions are changed to LUEX functions.   The exit is called each time the LUEX function is executed.  

Certain pages are delivered with the product, called GenevaERS Standard Look-up Exits.  These perform a common set of functions not done by native GenevaERS.   For example:
- GVBXLEXP	An exponential calculator.  If passed a number, format and exponent, it will perform the calculation
- GVBXLMD	Accepts two dates, and computes days between those dates
- GVBXLRUD	Simply returns the GenevaERS Fiscal Year data, passed in through the VDP
- GVBXLST	Accepts strings and concatenates them and returns a single string value
- GVBXLTM	Performs a trim function against passed string parameters

<div style="clear: right" >

## Write and Format Exit Functionality

<img style="float: right;" width="50%" vspace="5" alt="Write and Format Exit Functionality" src=images/Module20-User-Exit_Routines/Module20_Slide12.jpeg title="Write and Format Exit Functionality"/>

Write exits sit between the extract engine, and a potential extract or output file. Each write exit is tightly coupled to it’s GenevaERS view, because the exit receives the view output.  Extract exits are called whenever a view is to write an extract record.  In addition to the view’s extract record the write exit is also passed and can sees the event record.  

Write Exits are associated with a view, since the view or a write statement within the view generates the WREX logic table function (rather than WRIN, WRDT, or WRSU functions) and creates the extract record 

The write exit can tell GVBMR95 to do any one of the following:
- Tell GVBMR95 to write a record the exit specifies (could be any record) and continue with event file processing
- Tell GVBMR95 to skip this extract record and go on
- Tell GVBMR95 to write a record the exit specifies (could be any record) and return to the exit to do more processing

The exit can manipulate the extract record; substitute a new record, table the extract record in some way and then dump the table at the end of event file processing, or any other number of things.  Note, though, that unlike read exits which do open and actually read files, write exits typically do not.  They return records to GenevaERS to write to the extract file.  They could do their own IO, but there is typically no benefit to doing so.  GenevaERS’s write routines are very efficient.

Some examples of write exits include the following:
- Table multiple records, summarize them if they have common sort keys, and write a record when the key changes. 
- Read a set of parameters giving scoring requirements, table multiple records and upon a key change, score records.  Create a completely new record with the scoring results, write this new record, and drop all the tabled records
  
Write exits are in between read and lookup exits for complexity.  This is mainly because of the complexity of dealing with extract records.  The exit must know what the extract record will look like for a particular view.  This might be easiest to determine by actually writing the view, and inspecting the extracted records to find positions and lengths.  Any changes in the views can create a new to update the write exit.  

Format exits are very much like write exits, except that they are called at the end of GVBMR88.  They are also dependent upon the view specification.  They are called for each format output record.  Like write exits, they are specified on each view.  They are specified only in the VDP; there is no logic table impact for them.

<div style="clear: right" >

## Metadata Setup

<img style="float: right;" width="50%" vspace="5" alt="Metadata Setup" src=images/Module20-User-Exit_Routines/Module20_Slide13.jpeg title="Metadata Setup"/>

Using exits requires that they first be described in the User Exit Routine screens within the Workbench.  The name can be anything desired. The type can be either Read, Look-up, Write, or Format.  The language and path are for documentation only.  The executable must match the load page name stored within an accessible loadlib for either GVBMR95 or GVBMR88. 

<div style="clear: right" >

<img style="float: right;" width="50%" vspace="5" alt="Metadata Setup" src=images/Module20-User-Exit_Routines/Module20_Slide14.jpeg title="Metadata Setup"/>

The Optimizable flag is only applicable for look-up exits.  Remember that GenevaERS bypasses certain look-ups when the look-up has already been performed.  In these cases, the look-up exit would not be called in the subsequent cases.  If the look-up exit is stateless, in other words, it does not function differently from one execution to another given the same input parameters, the exit can be optimized.  If the exit retains its state from one call to another, then it must be called each time and cannot be optimized.  

For example, one exit was written to detect the first time it was called for a particular event file record.  In this case, it would return a return code of 0; every subsequent call would return a code of 1.  This exit cannot be optimized; each potential call must actually call the exit.  Otherwise the exit would always return a code of 0.

<div style="clear: right" >

# Metadata Set-up 

## Read Exits

<img style="float: right;" width="50%" vspace="5" alt="Metadata Set-up Read Exits" src=images/Module20-User-Exit_Routines/Module20_Slide15.jpeg title="Metadata Set-up Read Exits"/>

Once the user exits are entered into the User Exit table, they can be assigned to the other appropriate metadata components.  For Read Exits, the exit must be assigned to specific Physical File entities.  Remember that for each physical file read by views being processed, the Logic Table contains an RENX logic table row for that physical file.  By assigning a read exit on the physical file, the generated RENX entry will contain the exit to be called each time a new record or block of data is needed

The data returned by the read exit must match the Logical Records that are assigned to this physical file.  Thus when a view accesses a field to perform, perhaps, a selection function, the data must match the logical record layout for the GenevaERS “physical” file entity, not the file read by the read exit.

<div style="clear: right" >

## Look-up Exits

<img style="float: right;" width="50%" vspace="5" alt="Look-up Exit LR" src=images/Module20-User-Exit_Routines/Module20_Slide16.jpeg title="Look-up Exit LR"/>

Standard look-ups require the data to be joined to be defined by an LR.  For look-up exits, the logical record to be returned by the exit must match a specific LR (not the data read from file by the look-up exit, if there is any).  In the example shown here, the “phase code” value must be returned by the look-up exit in position 3 for a length of 2.

When defining the logical record to be returned by the exit, define any of the input parameters the exit will require as “keys,” as if the exit were going to search a reference file table to find the required answer. 

Next define a path that will provide the needed inputs to the exit.  The values in the path can be provided as constants in the views, or in the path, or as values passed from the input file or looked up from another look-up table, requiring a multi-level look-up.

<div style="clear: right" >

<img style="float: right;" width="50%" vspace="5" alt="Look-up Exit LR" src=images/Module20-User-Exit_Routines/Module20_Slide17.jpeg title="Look-up Exit LR"/>

After defining the LR and the look-up path, to assign a lookup exit to the Logical record, select the LR Properties tab.  Then select the appropriate exit.  When a field from the target LR is used in a view, this exit will be called to return data in the format of the defined logical record.  Thus when the Logic Table is generated, the LUSM will be changed to LUEX.  This logic table function contains the user page name to be called.

<div style="clear: right" >

## Write and Format Exits

<img style="float: right;" width="50%" vspace="5" alt="Write and Format Exits" src=images/Module20-User-Exit_Routines/Module20_Slide18.jpeg title="Write and Format Exits"/>

Write Exits and Format Exits are assigned in view properties.  Write exits are assigned in the Extract Phase tab; Format Exit are assigned Format Phase tab.  

Because both of these exits sit potentially at the end of the process, these exits do not return data to GenevaERS views; therefore no logical record defines the outputs from these exits.  Rather the view columns and format (file, hardcopy, etc.) define what these exits will receive.  Changes to the view layout will affect the exits.

Often the “Write DT Area” option is used with write exits, to eliminate the complexity of the extract record layout.  Only the column data is passed to the write exit.

<div style="clear: right" >

## Start-up (“Fixed”) Parameters

<img style="float: right;" width="50%" vspace="5" alt="Start-up (“Fixed”) Parameters" src=images/Module20-User-Exit_Routines/Module20_Slide19.jpeg title="Start-up (“Fixed”) Parameters"/>

Each exit has the potential to receive a fixed set of parameters upon start up.  These parameters are assigned for each instance that an exit is invoked.  

For example, a look-up exit may function differently depending on which LR it is supposed to return; perhaps data can be returned in compressed format in one instance, and not in another.  The LR for the compressed data may pass in a start-up parameter of “CMPSD” and the uncompressed LR would pass in a start-up parameter of “UNCMPSD”.  In this way the same exit program can be used, and which LR should be returned can be indicated as a parameter to the exit.  

<div style="clear: right" >

## Look-up Exit Parameters

<img style="float: right;" width="50%" vspace="5" alt="Look-up Exit Parameters" src=images/Module20-User-Exit_Routines/Module20_Slide20.jpeg title="Look-up Exit Parameters"/>

Read exits receive only the start-up parameters.  Write and Format exits also receive start-up parameters; they also receive the extracted record from the view.  Write exits also have visibility to the event file record as well.

Look-up exits, by contrast, receive start-up parameters; they also have visibility to the event file record.  Additionally, Look-up exits receive all the parameters built in the look-up path.  These values must match the required key for the logical record.

Note the difference between these two types of parameters: 
- The start-up parameters do not change throughout the entire run of GenevaERS.  They are constant.  They are typically only used by the exit at start up to determine which mode the program should function in.
- The look-up key values can change based upon every event file record processed.  Customer ID 1 on the first record may become Customer ID 10,000 on the next record.  

<div style="clear: right" >

# Look-up Exit Example View

<img style="float: right;" width="50%" vspace="5" alt="Example View" src=images/Module20-User-Exit_Routines/Module20_Slide21.jpeg title="Example View"/>

We will use this view to show the GVBMR95 Logic Table and MR95 Trace.  Our example will use a look-up exit, which returns various thread parameters that can be of interest for technical reasons in a view.  The look-up exit is assigned on the LR and path we just examined.


<div style="clear: right" >

<img style="float: right;" width="50%" vspace="5" alt="Look Up Exit Logic Table" src=images/Module20-User-Exit_Routines/Module20_Slide22.jpeg title="Look Up Exit Logic Table"/>

This is the logic table for the example view.  Note that lookups, which would normally have the function code of LUSM, have been changed to the function code of LUEX.  Also, for each LUEX, the ID associated with the User Exit is assigned, in this case, ID 13.  This is the ID assigned to the page GVBXLENV.  

Our path required a single character value be passed to the exit.  This “key” value—a constant of “D”—built by the LKC function, will be passed to the exit as part of the lookup.

Note also that the view has no write exit, because the logic table is WRXT, not a WREX.  Also note there is no Exit ID assigned to the WRXT row.

<div style="clear: right" >

## Not Optimized

<img style="float: right;" width="50%" vspace="5" alt="Lookup Exit Trace, Not Optimized" src=images/Module20-User-Exit_Routines/Module20_Slide23.jpeg title="Lookup Exit Trace, Not Optimized"/>

This is the GVBMR95 Trace for the logic table.  

For event file record 1, the LKC function builds a key with a value “D” in it.  This value is passed to the exit GVBXLENV during the LUEX function.  The exit is called.  The view then uses the data, through a DTL function, placing a value 0001 into column 2.  GVBXLENV did not search any data.  It simply queried the GenevaERS thread number to return a value of 0001.  

Also, note the number of times the exit is called in this trace.  Each lookup actually required calling the exit.

<div style="clear: right" >

## Optimized

<img style="float: right;" width="50%" vspace="5" alt="Lookup Exit Trace, Optimized" src=images/Module20-User-Exit_Routines/Module20_Slide24.jpeg title="Lookup Exit Trace, Optimized"/>

This is the same view but a new trace.  In this trace the “Optimize” flag was turned on.  This means that the logic table only has one executed LUEX function for the entire first event file record.  Because the LKC value of “D” did not change between calls to the exit, the exit is not called again.  

<div style="clear: right" >

## Optimized vs Non-Optimized

<img style="float: right;" width="50%" vspace="5" alt="Optimized vs Non-Optimized" src=images/Module20-User-Exit_Routines/Module20_Slide25.jpeg title="Optimized vs Non-Optimized"/>

The difference between the logic tables for optimized and non-optimized is very clear. The Optimized trace on the right saves significant CPU time, including the overhead for linking to the user exit multiple times on each event file record.  Exits require CPU time by their nature, and the efficiency of the language run-time can also have an impact.  Efficiency should carefully be considered when creating any exit.

<div style="clear: right" >

## Static Parameters

<img style="float: right;" width="50%" vspace="5" alt="Static Parameters" src=images/Module20-User-Exit_Routines/Module20_Slide26.jpeg title="Static Parameters"/>

The trace shows the values placed in the key by the LKDT, LKC and other look-up key functions, the value of “D” in our example view.  These parameters are passed to the look-up exit.  It also shows the static parameters passed as well.  These are shown on the end of the LKEX function row, after the Look-up Exit page.  Similar parameters can be seen in the trace for Read and Write exits if start-up parameters are passed.

<div style="clear: right" >

## Exit Program Specification and Linkage

<img style="float: right;" width="50%" vspace="5" alt="Exit Program Specification and Linkage" src=images/Module20-User-Exit_Routines/Module20_Slide27.jpeg title="Exit Program Specification and Linkage"/>

Exits must be written following the GenevaERS User Exit guidelines.   These specify a standard set of linkage parameters to interact with GVBMR95 and GVBMR88.  They include: 
- A standard set of pointers, used to access various data provided by GenevaERS, including the event record, the extract record, look-up keys, and a work area for maintaining working storage parameters for the exit.  

- Environment data, including the Phase Code, Open, Read, or Close, informing the exit what the status of processing is.  Exits are called during the 
    - Open phase, to prepare for processing, 
    - Read phase as event file records are processed, and 
    - Close phase, to print out control reports, flush final records, or clean up.

- Return codes values, informing GenevaERS of the results of processing.  These can include:
    - A Found, Not Found, or Skip Event Record condition on a Look-up Exit, 
    - An End of File on for a Read exit, or 
    - Write the standard extract record, Write a different record and then return to the exit for processing, or Skip the extract record and continue processing for Write Exits

All Exits may signal view or process level errors as well.

More details about how to create a user exit can be found in the [User Exit Programming Guide](UserExitGuide.md)

<div style="clear: right" >

## GenevaERS Standard Sort Exit

<img style="float: right;" width="50%" vspace="5" alt="GenevaERS Standard Sort Exit" src=images/Module20-User-Exit_Routines/Module20_Slide28.jpeg title="GenevaERS Standard Sort Exit"/>

As noted earlier this graphic shows the potential for a Sort Utility Input or Read Exit.  GenevaERS uses standard sort utilities provided with the operating system or otherwise.  These utilities typically allow for a read exit to the sort utility, a program which stands between the sort utility and the file to be sorted.  Procedures for writing these exits depend upon the sort utility used.  Refer to the sort utility documentation for instructions.

As an example of this type of exit, GenevaERS provides a page GVBSR02.  This page accepts a parameter file, instructing it which view(s) to create multiple permutations for.  Before the utility sorts the records, the Sort Exit will replicate the extracted data, creating new records with permuted sort keys.  For example, a record with a sort key of A, B and C will be duplicated, and a records for of the following would be created:
B, C, A, 
C, A, B, 
C, B, A
A, C, B
B, A, C

This produces many more possible outputs without creating all the different views or exploding the extract files with all the possible combination.  

Using this exit requires concatenating special sort cards to the GVBMR95 generated sort cards, and creating parameters for the Logic Table programs instructing them to generate permuted VDP views, similar to the process it undertakes when it creates the JLT for the reference file phase. These views are never found in the Metadata Repository; they are temporary views only in that run’s VDP.  They are required in the VDP for GVBMR88 to refer to in processing the permuted records. 

# Function Overview

<img style="float: right;" width="50%" vspace="5" alt="Function Overview" src=images/Module20-User-Exit_Routines/Module20_Slide29.jpeg title="Function Overview"/>

This page has introduced the following Logic Table Function Code:
- REEX - Read a new Event Record with a user exit
- LUEX - Lookup calling a user exit
- WREX - Write calling a user exit

[Click here to access the list of the most common Logic Table Functions for reference.](Intro11a_Logic_Table_Function_Codes.md)

<div style="clear: right" > 

# Links

Place following text in the topic:  
    ````
    [Topic A](TopicA)
    ````

The link displays as:   
[Topic A](TopicA)