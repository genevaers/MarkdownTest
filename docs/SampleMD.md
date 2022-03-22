---
title: SampleMD
nav_order: 50
---
# How to create your new topic
Please **COPY** the lines in this file to your new topic.  

# Use Front matter
The text between the three dashes is called “front matter” and contains fields.

The field **"title"** above defines the display name of your topic.
For example, your topic might be PELT.md and the display name "Performance Engine Logic Test". 
The field "title" is how your display name can have spaces but the file name does not.

The field **"nav_order"** gives the relative order of the topics listed at left of the website.

# First heading in your new topic
Delete the stuff between "How to create your new topic" to just before this latest heading.  Good idea to use a single hash for your headings - will display well in both Jekyll and Gatsby.


# Links 

Place following text in body of topic:  
    ````
        [Home](index)
    ````

The link displays as:    
[Home](index)

The target topic is where "index" is - there must NO EXTENSION (e.g. no .md).



# Display an image

Two steps:
1.  First step is always to place the image file in the **images** folder which is inside the **docs** folder.
1.  Second step is to refer to the image in your markdown topic.

There are two ways to refer to an image: (a) markdown coding and (b) html coding.  Option (b) is recommended, but I'll show you both.

Markdown coding means using the following text.  IF THAT LINE CONSISTS OF 4 OR MORE SPACES AND THEN THE IMAGE CODING BELOW IT WILL FAIL.  Ensure less spaces before the exclamation mark:

    ````````  
    See my fantastic image ![(Missing image)]( ./images/SampleImage.jpg )to see what I mean.
    ````````

Note: the spaces before and after image file path is for safety - without them we can get problems with "regular expressions" and other coding issues.
  
The result is:

 See my fantastic image ![(Missing image)]( ./images/SampleImage.jpg ) to see what I mean.
  
This suits an icon.  For example image SampleIcon.jpg.  Use this coding.

    ````````  
    Press this icon ![(Missing image)]( ./images/SampleIcon.jpg ) to see the groovy part.
    ````````

The result is:

Press this icon ![(Missing image)]( ./images/SampleIcon.jpg ) to see the groovy part.
  

# Size the image

This is where we use html coding instead.
Use the "width" property.

    See my fantastic image <img  src=" ./images/SampleImage.jpg  " alt="(Missing image)" width="100"/> to see what I mean.
    
Note: the space around the reference to the image file is for safety - without them we can get problems with "regular expressions" and other coding issues.
You get the following.

See my fantastic image <img src=" ./images/SampleImage.jpg  " alt="(Missing image)" width="100"/> to see what I mean.


Height can be specified but does not work.  Whether you put width and height or just height nothing happens.  For example:

     <img src=" ./images/SampleImage.jpg " alt="(Missing image)" height="50"/>

The result is:

<img src=" ./images/SampleImage.jpg " alt="(Missing image)" height="50"/>


# Center the image

To center the image use more html coding:

    <p align="center"> Text before.
    <img src=" ./images/SampleImage.jpg  " alt="(Missing image)" width="100"/>  Text after.
    </p>

 The result is:

 <p align="center"> Text before.
 <img src=" ./images/SampleImage.jpg  " alt="(Missing image)" width="100"/> Text after.
 </p>

The text after the closing p tag is below the image.



# Left align - simple

Use this html coding:

     <img src=" ./images/SampleImage.jpg " alt="(Missing image)" align="left" width="100"/>  The text that follows is very close to the image.  You may want change your image to include some white space on the right hand side.

 The result is:

 <img src=" ./images/SampleImage.jpg " alt="(Missing image)" align="left" width="100"/>  The text that follows is very close to the image.  You may want change your image to include some white space on the right hand side.


# Left align - more control

Use this html coding:

    <p align="left"> Text before.
    <img  src=" ./images/SampleImage.jpg  " alt="(Missing image)" width="100"/> Text after.
    </p>

 The result is:

 <p align="left"> Text before.
 <img src=" ./images/SampleImage.jpg  " alt="(Missing image)" width="100"/> Text after.
 </p>

The text after the closing p tag is below the image.


# Right align - simple

Use this html coding:

     <img src=" ./images/SampleImage.jpg " alt="(Missing image)" align="right" width="100"/> The text that follows is left and very close to the image.  You may want to change your image to include some extra white space on the left hand side.

 The result is:

 <img src=" ./images/SampleImage.jpg " alt="(Missing image)" align="right" width="100"/> The text that follows is left and very close to the image.  You may want to change your image to include some extra white space on the left hand side.


# Right align - more control

Use this html coding:

    <p align="right"> Text before.
    <img  src=" ./images/SampleImage.jpg  " alt="(Missing image)" width="100"/> Text after.
    </p>

 The result is:

 <p align="right"> Text before.
 <img src=" ./images/SampleImage.jpg  " alt="(Missing image)" width="100"/> Text after.
 </p>

The text after the closing p tag is below the image.






# Image text displays but no image !

If your img coding comes out as text, make sure there are less than 4 spaces before the coding.  If you have too many spaces Markdown assumes a "codeblock" and does not display the image.

If you see (Missing Image) check the spelling of the image file reference.

