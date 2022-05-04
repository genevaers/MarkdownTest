---
title: Introduction to Using GenevaERS
nav_order: 22
---

# Introduction to Using GenevaERS

This page provides you with a broad overview of GenevaERS and the basics of its graphical user interface. It will help you: 
- Identify the two major software components of GenevaERS
- Identify the major types of GenevaERS metadata
- Navigate the GenevaERS Workbench, and
- Create a simple view.  

## GenevaERS Overview

GenevaERS is a software application development tool that solves high-volume data analysis and reporting problems. GenevaERS provides capabilities for data transformation, data mining, database query, and financial reporting.

![GenevaERS Components!](/docs/images/Module1-Introduction-to-Views/Module1_Slide4.jpeg "GenevaERS Components")

GenevaERS consists of two software components: the PC-based Workbench and the mainframe-based batch process known as the Performance Engine. Developers use the Workbench to build applications that are stored in a metadata repository in an IBM¬Æ  DB2¬Æ  database. These applications are then run by the Performance Engine, which reads data from source files or databases, transforms it, and writes it to output files.

## Metadata
Several types of metadata make up a GenevaERS application. The most common are 
- the environment definition, 
- the physical file definition (or PF), 
- the logical file definition (or LF), - the logical record definition (or LR), 
- the view definition, and the view folder.  

Note that, when discussing GenevaERS metadata, we often omit the word definition, because it is usually clear from the context whether we mean the metadata or the entity it refers to.  

![GenevaERS Environments!](/docs/images/Module1-Introduction-to-Views/Module1_Slide6.jpeg "GenevaERS Environments")

An environment definition describes a logical collection of metadata within the GenevaERS Workbench. Typical types of environments include development, production, or training environments. Access to an environment can be restricted to a certain set of users.  

Some major components of the metadata include:
- A physical file definition, or PF, describes a data source. Examples include customer or order files. 

- A logical file definition, or LF, describes a collection of one or more physical files. 

- A logical record definition, or LR, describes a record layout. In COBOL programs, record layouts are often found in copybooks. In relational databases, they are found in table definitions.

Some examples of these metadata types are shown here. 

![GenevaERS Metadata!](/docs/images/Module1-Introduction-to-Views/Module1_Slide7.jpeg "GenevaERS Metadata")

- A logical record for a customer is used to map data to a customer logical file. The customer logical file refers to data in a customer physical file.  
- An order logical record is used to map data from a logical file named ORDER_001, which refers to data in a single physical file named ORDER_001.  
- The order logical record can also be used to map data from a logical file named ORDER_ALL. ORDER ALL refers to a collection of order physical files.

## Views

A view definition describes a data transformation. It is analogous to a program or a query. Views are the basic units of work that are performed by the Performance Engine.  

![GenevaERS View Folders!](/docs/images/Module1-Introduction-to-Views/Module1_Slide8.jpeg "GenevaERS View Folders")

Views are often grouped together into view folders for ease of maintenance. View folders are often named for a particular developer or function. Security can be applied to view folders to prevent unauthorized access.  

## Workbench
![GenevaERS Workbench!](/docs/images/Module1-Introduction-to-Views/Module1_Slide9.jpeg "GenevaERS Workbench")

The GenevaERS Workbench is used to add, change, and delete GenevaERS metadata. It contains a menu and toolbar, and consists of multiple display areas, or frames.  

- The Navigator area displays the types of metadata available.  

- The Metadata List area displays a list of items for the selected metadata type.  

- And the Editor area is the part of the screen where you modify metadata items.

### Views and Folders
![GenevaERS Workbench!](/docs/images/Module1-Introduction-to-Views/Module1_Slide10.jpeg "GenevaERS Workbench")

By expanding the View Folders item in the Navigator area, you can see a list of all view folders.  

The contents of the selected folder are displayed in the Metadata List area. 

From there, you can select a view for editing and the view will be displayed in the Editor area.

### View Properties
![GenevaERS Properties!](/docs/images/Module1-Introduction-to-Views/Module1_Slide11.jpeg "GenevaERS Properties")

View information is displayed on two separate screens:
- The View Editor screen, where you can define specific data transformations.
- The View Properties screen, where you can modify information that applies to the whole view.  

![GenevaERS Output!](/docs/images/Module1-Introduction-to-Views/Module1_Slide12.jpeg "GenevaERS Output")

You use the General tab on the View Properties screen to specify the output format, flat file or hardcopy report, and other related information.  

This tab also displays information about when the view was created and last modified and by whom.  

![GenevaERS Folder!](/docs/images/Module1-Introduction-to-Views/Module1_Slide13.jpeg "GenevaERS Folder")

In addition, the General tab displays the name of the view folder where the view is stored.   

![GenevaERS Advanced Features!](/docs/images/Module1-Introduction-to-Views/Module1_Slide14.jpeg "GenevaERS Advanced Features")

You can access advanced features on the Extract Phase tab and the Format Phase tab. You can open these tabs by single-clicking them.  

![GenevaERS Toggle!](/docs/images/Module1-Introduction-to-Views/Module1_Slide15.jpeg "GenevaERS Toggle")

You can toggle back and forth between the View Properties screen and the View Editor screen by clicking the first icon in the Editor area toolbar, or by pressing the F9 key. 

### View Editor
![GenevaERS View Editor!](/docs/images/Module1-Introduction-to-Views/Module1_Slide16.jpeg "GenevaERS View Editor")

In View Editor mode, the Workbench displays several frames of view information.  

![GenevaERS View Editor Grid!](/docs/images/Module1-Introduction-to-Views/Module1_Slide17.jpeg "GenevaERS View Editor Grid")
The View Editor grid displays the characteristics of view output columns. These characteristics include the data type, the length, and the alignment, such as left, right, or center.  

![GenevaERS Source Properties!](/docs/images/Module1-Introduction-to-Views/Module1_Slide18.jpeg "GenevaERS Source Properties")

You can display information about the data source for the view by right-clicking a blue cell in the View Editor grid. This information includes the logical record and the logical file.  

![GenevaERS Column Properties!](/docs/images/Module1-Introduction-to-Views/Module1_Slide19.jpeg "GenevaERS Column Properties")

To open a frame showing the column source properties, you right-click a green cell. The source of a column‚Äôs data can be a field in the source file, a constant, a lookup value, or the result of a formula.  

![GenevaERS Edit Functions!](/docs/images/Module1-Introduction-to-Views/Module1_Slide20.jpeg "GenevaERS Edit Functions")

The View Editor incorporates several functions, such as inserting a column or activating a view. You can run a View Editor function in several ways: 
- Select it from the Edit menu or the Action menu for the Workbench
- Left-click the function icon on the View Editor toolbar 
- Right-click in the View Editor grid and select the function from the pop-up menu 
- Or press the appropriate key combination, which is noted on the Workbench menu and the pop-up menu.  

Choose whichever technique you prefer.  

![GenevaERS Edit New Source!](/docs/images/Module1-Introduction-to-Views/Module1_Slide21.jpeg "GenevaERS Edit New Source")

To add a new view source, you right-click on the grid to display the pop-up menu, and then select Insert and View Source.  

![GenevaERS Edit New Source 2!](/docs/images/Module1-Introduction-to-Views/Module1_Slide22.jpeg "GenevaERS Edit New Source 2")

The Insert View Source window opens. You can select from a list of data sources in the window.  

## Example View

Now let’s take what you’ve learned and create your own view. 

The following example is a simple data transformation, reading data from the ORDER001 file and writing out only the Order ID, Customer ID, and Total Amount fields.  

![GenevaERS View Example!](/docs/images/Module1-Introduction-to-Views/Module1_Slide23.jpeg "GenevaERS View Example")

If we were to code a conventional program, we would:
- Define the file attributes
- Define the record layouts
- Code the business logic
- Compile the program
- Link the program and
- Run the program

![GenevaERS Programming!](/docs/images/Module1-Introduction-to-Views/Module1_Slide24.jpeg "GenevaERS Programming")

![GenevaERS View Example!](/docs/images/Module1-Introduction-to-Views/Module1_Slide25.jpeg "GenevaERS View Example")

With the GenevaERS tool, the first three steps are performed in the Workbench and the last three are performed for you by the Performance Engine.  

![GenevaERS View Example 2!](/docs/images/Module1-Introduction-to-Views/Module1_Slide26.jpeg "GenevaERS View Example 2")

Defining files and records will be covered in the GenevaERS training module entitled "Creating Metadata." T

The Performance Engine will be introduced in the Performance Engine Overview module. The topic of coding business logic will be introduced in the next few slides.  

In the example described in the following slides, metadata has been pre-populated in the Workbench for ease of instruction.  


To create a new view, click the Administration menu, select New and then select View.
![GenevaERS View Example Start!](/docs/images/Module1-Introduction-to-Views/Module1_Slide27.jpeg "GenevaERS View Example Start")


The View Properties tab opens.  

![GenevaERS View Example Properties!](/docs/images/Module1-Introduction-to-Views/Module1_Slide28.jpeg "GenevaERS View Example Properties")

Enter a descriptive name for the view, such as Simple_Transformation_View Note that embedded spaces are not allowed in names, so you must use underscores to separate words. Next, clear the Format Phase check box; this feature is not needed for this simple view.

![GenevaERS View Example Name!](/docs/images/Module1-Introduction-to-Views/Module1_Slide29.jpeg "GenevaERS View Example Name")

To display the View Editor Grid, select Show Grid or Properties, from the Edit menu (Alternatively, you can click the toolbar icon or press the F9 key). 

![GenevaERS View Example Editor!](/docs/images/Module1-Introduction-to-Views/Module1_Slide30.jpeg "GenevaERS View Example Editor")

From the Edit menu, select Insert, and then select View Source (Alternatively, you can right-click and select Insert and then select View Source from the pop-up menu, or you can press the Shift key and the Insert key). The Insert View Source window opens.
![GenevaERS View Example Source!](/docs/images/Module1-Introduction-to-Views/Module1_Slide31.jpeg "GenevaERS View Example Source")

From the Logical Record list, select ORDER. Then, from the Logical File list, select ORDER_001.  

![GenevaERS View Example Source 2!](/docs/images/Module1-Introduction-to-Views/Module1_Slide32.jpeg "GenevaERS View Example Source 2")

From the Edit menu, select Add Column (Alternatively, you can click the plus sign icon on the toolbar or press Alt and the Insert key). A new column is added to the grid.  
![GenevaERS View Example Column!](/docs/images/Module1-Introduction-to-Views/Module1_Slide33.jpeg "GenevaERS View Example Column")

Click the green cell. The Column Source Properties frame opens on the right.  

![GenevaERS View Example Source Properties!](/docs/images/Module1-Introduction-to-Views/Module1_Slide34.jpeg "GenevaERS View Example Source Properties")

In the Column Source Type field, click the list box and select Source File Field.  

![GenevaERS View Example Field Type!](/docs/images/Module1-Introduction-to-Views/Module1_Slide35.jpeg "GenevaERS View Example Feild Type")

In the Column Source Value field, click the list box and select ORDER_ID.   

![GenevaERS View Example Value!](/docs/images/Module1-Introduction-to-Views/Module1_Slide36.jpeg "GenevaERS View Example Value")

Repeat the previous steps to add columns for Customer ID and Order Total Amount. Then save the view by selecting Save from the File menu, or by clicking the Save icon in the Workbench toolbar, or by pressing Control and S.  

![GenevaERS View Example Repeat!](/docs/images/Module1-Introduction-to-Views/Module1_Slide37.jpeg "GenevaERS View Example Repeat")

To activate the view, use any of these methods: 
Select Activate from the Action menu
Press the Activate icon on the View Editor toolbar
Press F5.  

The view title bar now displays the word ‚Äúactive‚Äù. Save the view again to preserve this active state. The view is now ready to be run.

![GenevaERS View Example Complete!](/docs/images/Module1-Introduction-to-Views/Module1_Slide38.jpeg "GenevaERS View Example Complete")

# Links

Place following text in the topic:  
    ````
    [Topic A](TopicA)
    ````

The link displays as:   
[Topic A](TopicA)