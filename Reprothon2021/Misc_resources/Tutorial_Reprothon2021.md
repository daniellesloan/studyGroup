---
title: "3. How to Generate & Test Your Question"
author: "Max Tschol"
output:
  html_document:
    toc: true
    toc_float: true
visible: true
tags:
  # repro
  - Resources
---

Hello fellow Hacker,  

In this tutorial we provide an example of what the data collection for the 2021 Reprothon will look like. If you want to learn how you can contribute, please see the [<span style="color: blue">"How To Get Started" guide.</span>](https://aberdeenstudygroup.github.io/studyGroup/Reprothon2021/Misc_resources/How_to_get_started_Reprothon2021/)


### Get a StackOverflow question

**1. Click on the link below to obtain a question**  

Here is a link to the [<span style="color: blue">Question Generator</span>](https://shiny.abdn.ac.uk/biostat/Reprothon/) which will take you to our ShinyApp. Press the button "Get A New Sample" and this will generate a link to a StackOverflow question, please click this link!

<p align=centre>
<img src="../images/quest_gen.png" style="max-width:100%;">
</p>


### Test the accepted answer to the question

**2. Familiarize yourself with the question**  

Read through the question, comments, code and answers to make sure you understand what the question is asking. If there is anything unsual here you can look at our troubleshooting guide(link) and/or include it in the "comments" section of the data entry form. If in doubt ask one of the admin team!


<p align=centre>
<img src="../images/stack_example3.png" style="max-width:100%;">
</p>
<br>

**3. Look for the accepted answer**  

The accepted answer shows a green tickmark on the left and is usually the first answer after the question.

<p align=centre>
<img src="../images/stack_example4.png" style="max-width:100%;">
</p>
<br>

**4. Open a new R or Rstudio session**  

Please always restart R before reproducing a question.

<p align=centre>
<img src="../images/open_r.png" style="max-width:100%;">
</p>
<br>

**5. Reproduce accepted answer**  

In this example, you can just copy and paste the accepted solution from StackOverflow into R. Press "crtl + A" to highlight all your code and press "enter" to run it.

<p align=centre>
<img src="../images/copy_paste_stack.png" style="max-width:100%;">
</p>

<p align=centre>
<img src="../images/copy_paste_stack_r.png" style="max-width:100%;">
</p>
<br>

In this example, the code provided in the answer was missing the function to load the package used. Add the code required to load the appropriate package with functions `require()` or `library()`.

In other cases you might have to install new packages before you can reproduce the example. Sometimes the code generating the example will only be provided in the question and not the answer provided, you should then copy the code that generates the data from the question.   Check if your own solution reproduces the output of the solution on Stack Overflow.

<br>

<p align=centre>
<img src="../images/r_pass.png" style="max-width:100%;">
</p>
<br>

## Filling out the data collection form 

**6. The Reprothon2021 Data Entry Google Form**  

Click this [<span style="color: blue">link</span>](https://docs.google.com/forms/d/e/1FAIpQLScnkiWlEgSmdAfwQjN-AvkqPmjAMi-D947jAr2YcABiEMYU9g/viewform) to access the Data Entry form. 

You will be asked to enter the following:  
- Your personal ReprothonID which you should have received via email   
- Copy and paste the URL link to the question on StackOverflow  
- The R libraries (package) used in the accepted answer. In case there are multiple libraries, separate library names by "#". If the accepted solution uses base R functions, add "base".  
- Click whether you passed or failed to reproduce the question with the accepted solution provided. If you are unsure whether you reproduced the accepted solution correctly click Unsure. If no reproducible example was provided please select this option.  
- Write the name of the R-library (package) that caused the error, if no error write "NA". Please note that this is case sensitive.  
- write the function that caused the error, if no error write "NA". Please note that this is case sensitive. 

<p align=centre>
<img src="../images/fill_gform2.png" style="max-width:100%;">
</p>
<br>

Lastly,:   
- Copy and paste the all the code from your R console into the form, including code taken from the original question as well as its answer, if required.  
- Add any comments.  
- click submit.  
 
 
<p align=centre>
<img src="../images/fill_gform3.png" style="max-width:70%;">
</p>
<br>

**Congrats !!**  
**You did it!!**  
**Thank you so much for contributing!!**   
{insert cute you rock kitten meme here}
Take on another question from the [<span style="color: blue">Question Generator</span>](https://shiny.abdn.ac.uk/biostat/Reprothon/) and don't forget to reload your R before starting to reproduce.  

<p align=centre>
<img src="../images/submit2.png" style="max-width:70%;">
</p>
<br>

## Common problems while reproducing  

Reproducing code is not always easy, in fact it rarely is! Check out the next Tutorial [<span style="color: blue">here</span>](link) about solutions to common problems when reproducing code on StackOverflow.  
