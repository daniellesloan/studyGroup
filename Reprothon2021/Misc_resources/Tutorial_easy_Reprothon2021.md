---
title: "How to get your question & test it."
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

here we provide an example of how the data collection for the Reprothon 2021 looks like. If you want some basic information how you can contribute, please see the "How_to_get_started_guide".


## Get a StackOverflow question

**1. Click on the Question_ID_Generator to obtain a question**  

Here is a link at the Question_ID_Generator

<p align=centre>
<img src="../images/confused_cat1.jpg" style="max-width:100%;">
</p>

**2. Copy and paste the Question ID into the StackOverflow search bar and click enter**  

Here is a link to StackOverflow https://stackoverflow.com/


<p align=centre>
<img src="../images/stack_example2.png" style="max-width:50%;">
</p>

## Reproduce the accepted answer to the question

**3. Familiarize yourself with the question**  

<p align=centre>
<img src="../images/stack_example3.png">
</p>

**4. Look for the accepted answer**  

The accepted answer shows a green tickmark and is usually the first answer after the question.

<p align=centre>
<img src="../images/stack_example4.png">
</p>

**5. Open a new R or Rstudio session**  

Please always restart R before reproducing a question.

<p align=centre>
<img src="../images/open_r.png">
</p>

**6. Reproduce accepted answer**  

In this example it is straight forward to just copy and paste the accepted solution from StackOverflow into R.

<p align=centre>
<img src="../images/copy_paste_stack.png">
</p>

<p align=centre>
<img src="../images/copy_paste_stack_r.png">
</p>

In this example, the code provided in the answer was missing the function to load the package used. Add the code to load the appropriate package with functions `require()` or `library()`.

In other cases you might have to install new packages before you can proceed to reproduce the example.

<p align=centre>
<img src="../images/r_pass.png">
</p>


## Fill out the GoogleForm 

**7. Fill out the Form**  

We should have sent you an email including a link to the GoogleForm, otherwise ask a fellow hacker for help.  

First fill in:  
- your personal ReprothonID which you should have received via email.   
- the QuestionID you obtained from the QuestionIDGenerator.  
- copy and paste the URL link to the question on StackOverflow.  
- the year the Question was posted, you can find this above the Username.  

<p align=centre>
<img src="../images/fill_gform1.png">
</p>


Then, fill in:  
- the R-library (package) used in the accepted answer. In case there are multiple libraries, separate library names by "#". If the accepted solution uses base R functions, add "base".  
- click whether you passed or failed to reproduce the question with the accepted solution provided. If you are unsure whether you reproduced the accepted solution correctly click Unknown.  
- write the name of the R-library (package) that caused the error, if no error write "NA".  
- write the function that caused the error, if no error write "NA".  

<p align=centre>
<img src="../images/fill_gform2.png">
</p>


Lastly, fill in:   
- copy and paste the code from R into the form.  
- add any comments.  
- click submit.  
 
 
<p align=centre>
<img src="../images/fill_gform3.png">
</p>


**Congrats !!**  
**You did it!!**  
**Thank you so much for contributing!!**   

Take on another question from the Question_ID_Generator and don't forget to reload your R before starting to reproduce.  

<p align=centre>
<img src="../images/submit2.png">
</p>
<br>
</br>
## Common problems while reproducing  

Reproducing code is sometimes not easy. Check out the next Tutorial about solutions to common problems when reproducing code on StackOverflow (link) .  
