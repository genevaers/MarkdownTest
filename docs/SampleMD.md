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

Markdown coding means using the following text.  Ensure LESS THAN 4 SPACES BEFORE the exclamation mark:

    See my fantastic image ![(Missing image)]( ./images/SampleImage.jpg )to see what I mean.

Note: the spaces before dot slash and after .jpg are for safety - without them we can get problems with "regular expressions" and other coding issues.
  
The result is:

 See my fantastic image ![(Missing image)]( ./images/SampleImage.jpg ) to see what I mean.
  
This suits an icon.  For example image SampleIcon.jpg.  Use this coding.

    Press this icon ![(Missing image)]( ./images/SampleIcon.jpg ) to see the groovy part.

The result is:

Press this icon ![(Missing image)]( ./images/SampleIcon.jpg ) to see the groovy part.

So Markdown coding works well for icons.  Howeveer, for most other situations we will see html coding is more useful.


# Size the image

This is where we use html coding instead.
Use the "width" property.

    See my fantastic image <img  src=" ./images/SampleImage.jpg  " alt="(Missing image)" width="100"/>  to see what I mean.

With the above coding, we get the following.

See my fantastic image <img src=" ./images/SampleImage.jpg  " alt="(Missing image)" width="100"/>  to see what I mean.


The "height" property can be specified but does NOT work. 

# Center the image

To center the image use more html coding:

    <p align="center"> Some text to the left.
    <img  src=" ./images/SampleImage.jpg  " alt="(Missing image)" width="100"/>   Some text to the right.

    </p>

 The result is:

 <p align="center">  Some text to the left.
 <img src=" ./images/SampleImage.jpg  " alt="(Missing image)" width="100"/>  Some text to the right.
 </p>

The text that follows the closing p tag is below the image.

# Left align - simple

Use this html coding:

     <img src=" ./images/SampleImage.jpg " alt="(Missing image)" align="left" width="100"/>  The text that follows wraps around and is very close to the image.  You may want change your image to include some white space on the right hand side.

 The result is:

 <img src=" ./images/SampleImage.jpg " alt="(Missing image)" align="left" width="100"/>  The text that follows wraps around and is very close to the image.  You may want change your image to include some white space on the right hand side.



# Left align - more control

Use this html coding:

    <p align="left"> Text to the left.
    <img  src=" ./images/SampleImage.jpg  " alt="(Missing image)" width="100"/> Text to the right.
    </p>

 The result is:

 <p align="left">  Text to the left.
 <img src=" ./images/SampleImage.jpg  " alt="(Missing image)" width="100"/>  Text to the right.
 </p>

The text after the closing p tag is below the image.


# Right align - simple

Use this html coding:

        <img src=" ./images/SampleImage.jpg " alt="(Missing image)" align="right" width="100"/> The text that follows is at the left and is very close to the image.  You may want to change your image to include some extra white space on the left hand side.

 The result is:

<img src=" ./images/SampleImage.jpg " alt="(Missing image)" align="right" width="100"/>   The text that follows is at the left and is very close to the image.  You may want to change your image to include some extra white space on the left hand side.


# Right align - more control

Use this html coding:

    <p align="right">  Text before.
    <img  src=" ./images/SampleImage.jpg  " alt="(Missing image)" width="100"/>  Test after.
    </p>

 The result is:

 <p align="right">  Text before.
 <img src=" ./images/SampleImage.jpg  " alt="(Missing image)" width="100"/> Test after.
 </p>

The text after the closing p tag is below the image.


# Image text displays but no image !

If your img coding comes out as text, make sure there are less than 4 spaces before the coding.  If you have too many spaces Markdown assumes a "codeblock" and does not display the image.

If you see (Missing Image) check the spelling of the image file reference.

# Hidden comment in your markdown file

Normally everything you type in a markdown file is seen by the readers of that topic.

To create a comment in your file hidden from readers use this (ensure no spaces at laft):

        [//]: # ( Place comment here )
    
 The result is:
 
[//]: # ( Place comment here )
 
Notice that you do not see the comment.
