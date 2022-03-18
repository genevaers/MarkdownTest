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

# Graphics in your topic

Do the following:

1.  Place graphic in images folder (under docs folder). For example: genevaers-color.svg
1.  Place following text in body of topic: 

    ```` 
        ![Missing image](./images/genevaers-color.svg)
    ```` 

The result is ![Missing image](./images/genevaers-color.svg)
  
# Links in our topic

Place following text in body of topic:  
    ````
        [Home](index)
    ````

[Home](index)

The target topic is where "index" is - there must NO EXTENSION (e.g. no .md).

