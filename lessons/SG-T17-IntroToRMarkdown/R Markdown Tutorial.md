---
layout: page
title: "R Markdown Tutorial"
author: "Alexandra Jebb"
output: html_document
visible: true
tags:
  # languages
  - R
  # levels
  - beginner
---

## Overview

### Syntax

Using R Markdown syntax allows you to format your report to allow for the best possible communication of your results. There are some examples of formatting you may often want to use on the [R Markdown cheatsheet](https://www.rstudio.com/wp-content/uploads/2015/02/rmarkdown-cheatsheet.pdf) ("RMarkdown Cheatsheet Session 1" within the session materials)

### Managing Code

R Markdown can present code and outputs to you in different ways once you knit your document together.  
If you present r code as below, contained within back ticks and prefaced by 'r', the code will be replaced by the output as so:  

Two plus two equals `r 2 + 2` 

If you would prefer your code to show both the written input and the output you should contain it within `"```{r}"` and `"```"` as so...

For example: how to calculate two plus two:

```

```{r}
x <- 2 + 2
print(x)```
```

Adding arguments to `"```{r}"` can further modify the way your code is presented. `"eval = FALSE"` will present your code without the associated output.

For example: how to calculate two plus two...

```

```{r eval = FALSE}
x <- 2 + 2
print(x)```
```

`"echo = FALSE"` will not present your code but will show the associated output.

For example: how to calculate two plus two...

```

```{r echo = FALSE}
x <- 2 + 2
print(x)```
```

Here are some additional arguments you may be interested in using. Remember that each argument will have a default value so you will only need to modify it if you want to change away from the defaults which are listed below:

Argument             | Default         | Effect                                   
---------------------|-----------------|------------------------------------------
eval                 | TRUE            | Evaluate the code and include results
echo                 | TRUE            | Display code with results
warning              | TRUE            | Display warnings
error                | FALSE           | Display errors
message              | TRUE            | Display messages
tidy                 | FALSE           | Reformat code in a tidy way for display
results              | "markup"        | "markup", "asis", "hold" or "hide"
cache                | FALSE           | Cache results for future readers
comment              | "##"            | Comment character to preface results
fig.width            | 7               | Figure width in inches
fig.height           | 7               | Figure height in inches


### Knit your document

You can finish your document in multiple ways. You might use the **Knit** menu button at the top of the document. Or, you might choose to call the ```rmarkdown::render()``` function.

For now, **Knit** may be the easier option. We will return to the advantages of ```rmarkdown::render()``` later when we discuss parameters.

## Formatting Your (html) Document

### Theme and Highlight

Specifying ```theme:``` and ```highlight:``` within your html document allows you to change the appearance of your knitted output. Themes change the entire document whereas highlights change a small proportion of the syntax. Themes are taken from the [Bootswatch theme library](https://bootswatch.com/) The styles that are easily accessible (without installing other packages) are:

**Themes:**
* default
* cerulean
* journal
* flatly
* darkly
* readable
* spacelab
* united
* cosmo
* lumen
* paper
* sandstone
* simplex
* yeti

**Highlight:**
* default
* tango
* pygments
* kate
* monochrome
* espresso
* zenburn
* haddock
* textmate

### Table of contents

If you have a particularly long html document you can enhance the navigation by including a table of contents. By adding ```toc:true``` to your output you create this table of contents within the knitted document. ```toc_depth: number``` will specify the extent to which you want your titles, headings and subheadings included in your document. ```toc_float: true``` creates a really useful table of contents that will follow your document as you scroll. See below for how to input these arguments into your **YAML** header.
```
title: "R Markdown Tutorial"
author: "Alexandra Jebb"
date: "30 April 2019"
output: 
  html_document:
    toc: true
    toc_depth: 2
    toc_float: true
```
Naturally there are more options you can use to create a table of contents but these will give you a basic and functional aid to navigating your document!

### Figure Formatting

You may also control the formatting of all your figures within the document (7x5 is the default setting). Adding ```fig_width:``` or ```fig_height:``` to your **YAML** header will allow you to alter the dimensions of figures. Adding ```fig_caption:``` will add a caption to your figure.
```
title: "R Markdown Tutorial"
author: "Alexandra Jebb"
date: "30 April 2019"
output: 
  html_document:
    fig_width: 6
    fig_height: 7
    fig_caption: true
```

## Creating an interactive document

If you are creating an HTML document you can also create some interactive content that your users can manipulate. The two ways you can do this is by using HTML widgets or the Shiny framework. An example of a HTML widget will follow!

### HTML widgets (A bit of fun!)

HTML widgets interface with javascript objects to create interesting interactive applications within your document. Often you will have to use a widget package to enable these.

Here is an example (fun!) using the leaflet package. Leaflet provides open source and interactive maps. You will need to install the package before you run this code using ```install.packages("leaflet")```

```{r}
library(leaflet)
```

```{r out.width='100%'}
map <- leaflet()
map <- addTiles(map)
map <- addMarkers(map, lng=174.768, lat=-36.852, popup="The birthplace of R")
map
```

Here is [more information about widget packages](https://www.htmlwidgets.org) if you want to do more research.

## Parameters and Documents

Parameters are a **particularly exciting** aspect of RMarkdown! Parameterized reports improve the efficiency of your time by allowing you to reproduce an analysis with different parameters with just one knit! Thus, parameters are particularly useful when you want to repeatedly run code for different scenarios. For example:

* If your project involves running the same analyses on different species or strains.
* If your project involves running the same analyses on data from different locations or time periods.
* If your project involves running the same analyses on different demographic categories e.g. ages/genders.
* If you want to apply a particular format to your entire document.

### Using Parameters

To use our parameters we have to return to the **YAML** header as each of our parameters will be specified in this space. See below for an example:


```
#title: "R Markdown Tutorial"
#author: "Alexandra Jebb"
#date: "30 April 2019"
#output: html_document
#runtime : shiny
#params:
#   year:2018
```

Once we have specified our parameters here they will be stored in a read-only list called ```params```. These parameters then can be called later in your document. With the previous example we might apply this argument to our code: 

```
params$year
```

Remember arguments, such as ```echo```, from the earlier overview section? We can use arguments such as this as global parameters and apply them to the whole document. 

(remember ```echo = FALSE``` prevents code being printed)

First you need to add your argument to the **YAML** header.

```
#title: "R Markdown Tutorial"
#author: "Alexandra Jebb"
#date: "30 April 2019"
#output: html_document
#runtime : shiny
#params:
#   printcode: false
```
Then you must call this option in your first code chunk as so:

```
#```{r, setup, include = FALSE}
#
#knitr::opts_chunk$set(echo = params$printcode)
#
#```

```
Therefore, **after this line**, no more code will appear in your knitted output. Bear in mind that the first line of the code above, contained within ```{}``` is necessary to manually remove the first code section before we apply as our parameters only affect the document after this section. 

### Overriding Parameters

At any point you can override the parameters you used in your document by knitting your document using the following code. In this example the parameters provided are the ones to be over-ridden from the values specified in the **YAML** header. Any parameters that exist in the **YAML** header, that are not mentioned in this output will behave as specified in the header.


```
#rmarkdown::render("MyDocument.Rmd", params = list(
#  year = 2017,
#  region = "Asia",
#  printcode = FALSE,
#))

```

## Resources

A list of resources and references used to create this document which users of RMarkdown may find helpful!

1. The RMarkdown Cheatsheet (https://www.rstudio.com/wp-content/uploads/2015/02/rmarkdown-cheatsheet.pdf)
2. The Second RMarkdown Cheatsheet     (https://www.rstudio.com/wp-content/uploads/2016/03/rmarkdown-cheatsheet-2.0.pdf)
3. R Markdown: The Definitive Guide (https://bookdown.org/yihui/rmarkdown/)
4. Authoring Books and Technical Documents with R Markdown (https://bookdown.org/yihui/bookdown/)
5. The R Markdown Website (https://rmarkdown.rstudio.com/index.html)









