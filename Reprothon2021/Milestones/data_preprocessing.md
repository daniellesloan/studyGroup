---
layout: page
title: "Data Preprocessing - The backstory"
author: "Maria Kamouyiaros"
output: html_document
visible: true
tags:
  # repro
  - Milestones
---

**Primary Author:** [Maria Kamouyiaros](http://github.com/kamouyiaraki/)

**Additional help from:** [Rosie Baillie](https://github.com/RosieBaillie) and [Dr Thomas Cornulier](https://www.abdn.ac.uk/sbs/people/profiles/cornulier)

## Overview 

All StackOverflow data was downloaded from their archive and is available [here](https://ia800107.us.archive.org/view_archive.php?archive=/27/items/stackexchange/stackoverflow.com-Posts.7z) and was downloaded on the 9th of February 2021. 

The full StackOverflow archive available at: https://archive.org/download/stackexchange 

### Data schema:
The downloaded folder called "Posts.xml" has the following schema
```
- Format: 7zipped
- Files:
  - **posts**.xml
       - Id
       - PostTypeId
          - 1: Question
          - 2: Answer
       - ParentID (only present if PostTypeId is 2)
       - AcceptedAnswerId (only present if PostTypeId is 1)
       - CreationDate
       - Score
       - ViewCount
       - Body
       - OwnerUserId
       - LastEditorUserId
       - LastEditorDisplayName="Jeff Atwood"
       - LastEditDate="2009-03-05T22:28:34.823"
       - LastActivityDate="2009-03-11T12:51:01.480"
       - CommunityOwnedDate="2009-03-11T12:51:01.480"
       - ClosedDate="2009-03-11T12:51:01.480"
       - Title=
       - Tags=
       - AnswerCount
       - CommentCount
       - FavoriteCount
```


This includes all posts (and answers) on StackOverflow from 2009 until 2020, for all available languages. To prepare the data for this Reprothon we had to dome some data wrangling. 

## Initial data pre-processing: 
Due to the size of the dataset, these inital steps were run using some bash commands. Bash is available for both Linux and MacOS command line systems, but if you want this for a Windows system, the `Git-bash` command line can be downloaded from Git [here](https://gitforwindows.org/).

The first steps were to:

1. Unzip the downloaded archive 

`7z e stackoverflow.com-Posts.zip`

2. Filter out rows in the archive that we didn't need. 


### Remove lines that do not have a match to specific strings: 

The only rows kept in this dataset were ones containing these three patterns:

- `PostTypeId="1"` (i.e. only the parent post)

- `&lt;r&gt` (i.e. only ones with the tag <r>)

- `AcceptedAnswerId` (i.e. only ones with an accepted answer)

```
sed -i '/&lt;r&gt/!d' Posts.xml  
sed -i '/PostTypeId="1"/!d' Posts.xml 
sed -i '/AcceptedAnswerId/!d' Posts.xml
```

*Note: filtering out all other programming languages took approximately 6 minutes to run using a 12 core/24 thread processor - so depending what machine you are using, this may take a while.*


### Add a xml header
Since we removed any lines that did not match the above patterns, we removed the XML header. 

To add it back in: 

```
sed -i '1s/^/<?xml version="1.0" encoding="utf-8"?>\n<posts>\n> /' Posts.xml
```

This final file was 480Mb.


### Add posts tag to the end of the xml
Similarly we needed to add in the XML tag at the end of the file. This is done specifically so that the XML file can be parsed into R. 

This is easily done in RStudio: 

```
write("\n</posts>", file= Posts.xml, append = T)
```

## Parse xml into R

Now to parse the data into R for some more filtering (mainly to reduce the size of the file) and then finally to subset for the Reprothon. 

### First off, the libraries:

```
library(XML)
library(plyr)
library(jsonlite)
library(purrr)
library(data.table)  
```

### Next: parse in the XML. 

The main issue with this archive in particular is that the scheme is formatted with all variables associated with each post presented in a single row. As such, we had to get a little creative with parsing the data through: 

```
  xD <- xmlParse("Posts.xml")  
        #parse in xml (this requires both xml header and tag)
  xL <- xmlToList(xD) 
        #turns into a list to be able to start turning into a dataframe)
  names(xL)<- 1:length(names(xL)) 
        #424101 entries   (names by default are "row" as that's what each line starts with, changed to numbers)
  xLtest<-lapply(names(xL), function(x) data.frame(as.list(xL[[x]])))  #output "data.list", "data.frame"
  	    #this took a very long time and the final list was 1.2GB)
  
  xDF<- rbindlist(xLtest, fill=TRUE, use.names = T)  
        #fill = T means that any rows that do not have a value for a variable will be filled with an NA
  xDF<- as.data.frame(xDF)  
        #make sure it's only "data.frame"
  str(xDF)  
        #check to make sure everything loaded
        #note they are all classed as "chr"
```

**424,101 entries!**

### Keep only neccessary columns

```
  keep.cols<-c("Id","PostTypeId","AcceptedAnswerId", "CreationDate", "LastActivityDate", "Title", "Tags")
  xDF2<- xDF[, keep.cols]
  str(xDF2)
```
At this point you can export to a much more manageable tab delimited text file using: `write.table(xDF2, file= "Posts_reduced.txt", sep = "\t")`

### Subsetting for the Hackathon







