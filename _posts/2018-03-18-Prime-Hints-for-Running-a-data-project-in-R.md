I've been asked more and more for hints and best practices when working with R. It can be a daunting task, depending on how deep or specialised you want to be. So I tried to keep it as balanced as I could and mentioned point that definitely helped me in the last couple of years. Finally, there's lots (and I mean, LOTS) of good advice out there that you should definitely check out - see some examples in the Quick Reference section below.

### **01. Use R projects. Always.**

Human Civilization was built on conventions. R scripts have them, too. For example &gt; Any resident R script is written assuming that it will be run from a fresh R process with working directory set to the project directory. It creates everything it needs, in its own workspace or folder, and it touches nothing it did not create.

You want your project to work. Not only now, but also in 5 years, even if folder and file paths have changed. Also, you want it to work when your collaborator runs it from their computer. Projects create convention that make it possible.

Basically, they create the environment where

1.  all code and outputs are stored in one set location (no more `setwd()`!)
2.  relative file paths are created - this guarantees better reproducibility
3.  clean R environment is created every time you open it (no more `rm(list = ls())`!)

If you're not convinced, read [a more thorough explanation given by Jenny Bryan](https://www.tidyverse.org/articles/2017/12/workflow-vs-script/).

So, go on and start a New Project today! Otherwise, your computer may be at risk of being set on fire...

<img src="/img/2018-03-18-Prime-Hints-for-Running-a-data-project-in-R_files/figure-markdown_github/jb_tweet.png" width="700px" />


### **02. Describe the purpose of your code / project.**

Before you even load the packages or import the data, state the purpose and content of your script in hashed code.

``` r
### Data import and cleaning from sources A, B and C. 
### This script is part of Segmentation project. 
```

Naturally, you can update it along the way, but having such description in place from day one will guarantee that whoever has access to your script will instantly know what he/she is looking at. Also, if you leave it for later, the chances are that you'll forget to do it altogether and believe me, in X months time when you go back to your code it won't be that clear what were you trying to do here.

### **03. Load all necessary packages in the beginning of your script & consider calling functions in a package via `::`.**

Ok, I know how it is: you load some basic packages to start with, but then the analysis takes to wondrous and wonderful places that require more specialized libraries. So you install and load them along the way... WRONG! Whenever you have to load a new package, go back to the beginning of your script and load them there. This way whenever you (or someone else) have to run the script again, they will have ALL necessary libraries to run it and won't get stuck somewhere in the middle of the execution because the function they called is not recognized. It's coding. Small things matter.

Also, if you don't want to load the whole package just for the sake of using a single function, consider specifying package directly via `::`. Use this text cleaning exercise as an example:

``` r
library(readr)
library(dplyr)

tweets <- read_csv("tweets.csv")

all_tweets <- tweets %>% 
  rename(author = handle) %>% 
  select(author, text) %>% 
  mutate(text = qdapRegex::rm_url(text)) %>% #removes URLs from text
  na.omit()
```

Here, I'm expecting to use `readr` and `dplyr` packages regularly and I'm using `qdapRegex` library only once - to remove URL's from my tweet data. In this case I call `rm_url()` once and I specify the package that it comes from. More about in [here](https://stackoverflow.com/a/23232888). Easy peasy.

#### **!!! UPDATE !!!**

As [Brent Auble](https://twitter.com/brentauble/status/975667201595277313) correctly pointed out, if you decide to use `::` way of calling the package, make sure to put the comment clarifying that you're going to use this package - preferrably in the load section of the script - so that the user installs the package before running the script.

Also, [Maëlle Salmon](https://twitter.com/ma_salmon) made me realise that there's already an excellent package [packup](https://github.com/MilesMcBain/packup) that organises your code so that any mentioned libraries anywhere in the script automatically end up in the beginning of your code. Genius!

### **04. Name your code sections and use them for quick navigation.**

Your code will grow. Sometimes it will turn into an R script equivalent of the Bible, at least in terms of volume. To keep it organized, keep your code nice and tidy by assigning relevant code chunks to different sections that can be later folded/unfolded; you can also easily navigate yourself through chunks by using 'drop-up' menu at the bottom of the script screen.

You create a new code section by writing `####` or `----` at the end of any comment that is to become a new code section.

See the image below for a simplified example:

<img src="/img/2018-03-18-Prime-Hints-for-Running-a-data-project-in-R_files/figure-markdown_github/code_sections.png" width="550px" />


#### **!!! UPDATE !!!**

There's even a [keyboard shortcut](https://support.rstudio.com/hc/en-us/articles/200711853-Keyboard-Shortcuts) in RStudio for inserting code sections: `Ctrl+Shift+R` for Windows users and `Command+Shift+R` for Mac users. Even [Hadley Wickham can tell you that!](https://twitter.com/hadleywickham/status/975711350130327552)

### **05. Make your life easier and mark your code when you need to.**

Did you know that if you click anywhere left from line numbers in RStudio, it will create a red mark? I think this is one of the most underused features in RStudio, and a very useful one, too! Imagine having to define a function using another part of a very long R script? Instead of finding a relevant part of the script and then spending seconds or minutes on finding the function that you were about to define, you first mark that function, go to the relevant bit of code and then have no problems finding the function to be defined again, as the red mark will tell you where to look. BTW, it works only in already saved R scripts and it doesn't work in RMarkdown files.

<img src="/img/2018-03-18-Prime-Hints-for-Running-a-data-project-in-R_files/figure-markdown_github/red_dot.png" width="550px" />


#### **!!! UPDATE !!!**

I didn't know at the time of writing, but as [Jonathan Carroll](https://twitter.com/carroll_jono/status/975498243944587264) and many after him pointed out: the red dot actually indicates a debug point. If you leave it in the script while running the code, it will start a debugging process and therefore stop executing your code. Therefore **always remember to remove the red dot when you run/source your code!!**. Still, I find it pretty useful for temporary marking purposes ;)

### **06. Write your code as if someone was going to use it without communicating with you. From. Day. One.**

I know, I know. We all mean to do it, but we rarely do. I always promise myself that I'll be thoroughly commenting my code from the very beginning of the project but even these days I find it difficult to do it consistently. Why? Because:

-   analysis itself is more important (I'm telling myself).
-   I know what I'm doing.
-   I (usually) have no direct collaborators that are using the code.

But how short-sighted those arguments are! The reality is that

-   even most precious and relevant piece of analysis is useless if you or others don't understand it (more about it below)

-   you know what you're doing at the moment, but it won't feel the same way in a month or two when you have moved on to another project but someone asked you an innocent question about how you defined that key variable... Our memory is fallible. Make sure you don't have to rely on it with every single piece of code you produce

-   even you don't have active collaborators at the time of doing analysis, someone will have to use your code sooner or later. You're not going to be in the same position for the rest of your life. You're creating legacy that ONE DAY someone will use, no matter how far away it seems right now.

What makes good, reproducible code?

-   generous and clear comments
-   logical and efficient code
-   code that is ideally timed and tested

#### **!!! UPDATE !!!**

I had discussions online and offline whether your code alone can be used as sufficient documentation. The argument is that clean and clear code should stand for itself, no comments are necessary. There was a time when I thought that, too, but as [Mark Sellors](https://twitter.com/sellorm/status/975845659420119041) pointed out: any code will explain what you did, but it will rarely say WHY you did it. So comment your code generously, please!

### **07. Name your files like a Pro.**

Jenny Bryan is probably the first very high profile R-user I know who's been actively highlighting the importance of things that not many people talk about: file naming is one of them. I'm not going to reinvent the excellent wheel that she has already invented, so I'm only going to summarize her advice here. But for more detail, please, please, have a look at [Jenny's slides](https://t.co/99waX8liuQ).

So, what do all good file names have in common? They are:

#### **MACHINE READABLE **

This means that file names are

-   regular expression and globbing friendly. Meaning? You can search them using key words (with `regex` and/or `stringr` package). In order to make that easier/possible, remember to avoid spaces, punctuation, accented characters, case sensitivity.

This way it will be easy to search for files later and/or narrow file lists based on names.

Jenny's example:

<img src="/img/2018-03-18-Prime-Hints-for-Running-a-data-project-in-R_files/figure-markdown_github/jenny1.png" width="600px" />

-   easy to compute on using delimiters: this means that the file names should have a consistent name structure, where each section of the name has its role and is separated from other name sections with delimiters. This way it will be easy to extract information from file names, e.g. by splitting.

See Jenny's example below for clarification:

<img src="/img/2018-03-18-Prime-Hints-for-Running-a-data-project-in-R_files/figure-markdown_github/jenny2.png" width="600px" />

#### **HUMAN READABLE**

Human readable means exactly what is says on the tin:

> Easy to figure out what the heck something is, based on its name, as simple as that :)

I should add, it's easy to figure it out also for someone who doesn't know your work very well (this is a clear reminder of the previous point). I love the example below:

<img src="/img/2018-03-18-Prime-Hints-for-Running-a-data-project-in-R_files/figure-markdown_github/jenny3.png" width="600px" />

#### **THEY PLAY WELL WITH DEFAULT ORDERING**

The reality is that your computer will sort your files for you, no matter whether you like/need it or not. Therefore:

-   put something numeric in your file name first - if the order of sourcing files doesn't matter, stating when the file was created is always useful. Otherwise, you can indicate the logical order of the files (I'll come back to that in point 9).

-   use the `YYYY-MM-DD` format for dates (it's ISO 8601 standard!) EVEN when you're American :)

-   left pad other numbers with zeroes - otherwise you'll end up with `10` before `1` and so on.

Again, one of Jenny's examples:

<img src="/img/2018-03-18-Prime-Hints-for-Running-a-data-project-in-R_files/figure-markdown_github/jenny4.png" width="550px" />

I couldn't believe how much these little hacks improved the flow of my work. Paying attention to file names was also appreciated by my coworkers, I'm sure.

### **08. If you have to copy/paste excerpt of code 3 or more times, write a function for it.**

.. as Hadley Wickham said many many times in his and Charlotte's [course on DataCamp](https://www.datacamp.com/courses/writing-functions-in-r/). It doesn't only teach you to write more elegant and efficient code, but also it makes it more readable for yourself and others.

To learn how to write good functions, you can have a sneak peak into the first chapter of the DataCamp course for free (the rest is available under paid subscription) or read this [DataCamp tutorial](https://www.datacamp.com/community/tutorials/functions-in-r-a-tutorial).

### **09. With big, complex data projects use project pipeline.**

I'm not sure if `project pipeline` is an official name for what I want to talk about, but for a sake of the argument, let's call it a `project pipeline` ;) Namely, sometimes running a full project from one script - with even clearest and informatively named code sections - is simply difficult, if not unfeasible. Particularly when big(ish) data is involved: imagine trying to run a hefty model on data that requires importing and cleaning, where import into R alone takes about an hour (real life scenario!). You wouldn't possibly want to do it every time you open the project, right?

So what you do instead is write one script for data import and save the data.frame with useful data as an .RData file (using, e.g. `save(data1, file = "data.RData")`. Then start a new script for, let's say, data cleaning where you load previously imported data.frame from the .RData file (using `load("data.RData")`). Then you clean the data and save it as yet another .RData file. Then you write the third script where you load the clean data.frame from the second .RData file and you use it to run your model. Jenny Bryan's advice on file naming comes in handy here, as you want to name and order your scripts or their outputs logically to avoid surprises in the future.

This way you create a clear structure of building blocks, as well inputs and outputs. Additionally, once the data has been imported and cleaned, you can jump straight away into next steps of analysis/modelling without wasting any time on the first two steps.

I started using this approach in big and complex projects after reading [this SO answer](https://stackoverflow.com/a/1434424) and I never looked back.

#### **!!! UPDATE !!!**

Lots of comments regarding this one.

First, I didn't mention it explicitly, but it mentioned in the Stack Overflow reply I refer to: if you define a number of functions, it's a good practice to define them in a separate script that you can later source. [Blazko](http://disq.us/p/1r2d3ga) is clearly a fan of this solution :)

Secondly, saving your data objects as `.rds` file is probably a better solution than using `.RData`, as you can directly assing it to another object in a new session (right, [Kees Mulder](https://twitter.com/keestimmulder/status/975854141401595904) ?) or assign a bunch of similar object into a list (wink wink, [Martin Johnsson](https://twitter.com/mrtnj/status/975856756927750145) ). It's also an official recommendation of [Jenny Bryan](https://twitter.com/JennyBryan/status/975856014019903489).

Finally, if you need a reproducible way of running a number of connected script and/or visualising how they are connected, search no more: [drake package](https://github.com/ropensci/drake) is here to help! Really impressive tool, had no idea it existed at the time of writing this post, so thanks [Peter Higgins](https://twitter.com/ibddoctor/status/975731013266673665) for telling me about it.

### **10. Never save your workspace.**

Again, there were others that already said it before me and said it much better than I would have. So there you go:

> Loading a saved workspace turns your R script from a program, where everything happens logically according to the plan that is the code, to something akin to a cardboard box taken down from the attic, full of assorted pages and notebooks that may or may not be what they seem to be. You end up having to put an inordinate trust in your old self. I don’t know about your old selves, dear reader, but if they are anything like mine, don’t save your workspace.

For details, see [Martin Johnsson](https://twitter.com/mrtnj)'s excellent [blog post](https://martinsbioblogg.wordpress.com/2017/04/02/using-r-dont-save-your-workspace/) that I quoted above.

### **11. Before publishing/sharing your code, run it in the fresh workspace.**

This almost goes without saying, but if I got a £ for every time I forgot to do it... Basically, stuff happens when you write your code: you work with multiple files, maybe you loaded a package in one of them but forgot to do it in the rest of them? If that's the case, you'll be able to run all scripts in this particular session but if you send someone the code without the imported function... they will fail to run it. The same will happen if you try to run the script on its own, on your own machine.

Anyway, this doesn't need much explaining. Simply make sure that you re-run your code in a fresh session before you take it further. Amen.

### **That's it for now.**

So.. these are my 'prime hints' - if someone had told me about them 2 years ago, my `#rstats` life would have been much much easier. By no means is this list complete or exhaustive, but my intention was to highlight the points that I personally found most useful in my daily fun with R. Hope it will help someone!

#### **QUICK REFERENCE**

#### **General good practice**

1.  [Five Tips to Improve Your R Code (DataCamp)](https://www.datacamp.com/community/tutorials/five-tips-r-code-improve)

2.  [Workflow for statistical analysis and report writing (SO)](https://stackoverflow.com/questions/1429907/workflow-for-statistical-analysis-and-report-writing/1434424#1434424)

3.  [R Best Practices: R you writing the R way! (By Milind Paradkar)](https://www.quantinsti.com/blog/r-best-practices-r-you-writing-the-r-way/)

4.  [Good practices in R programming (Indiana University)](https://kb.iu.edu/d/aaxp)

#### **Good practice for package building**

1.  [Good Practices for Writing R Packages (by Roman Tsegelskyi)](https://romantsegelskyi.github.io/blog/2015/11/16/good-practices-r-package/)

2.  [R package development "best practices" (by stevenpollack)](https://gist.github.com/stevenpollack/141b14437c6c4b071fff)

3.  [goodpractice - Advice on R Package Building (by MangoTheCat)](https://github.com/MangoTheCat/goodpractice)
