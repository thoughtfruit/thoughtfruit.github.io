---
layout: post
title: "Tracking Side Projects"
categories:
- blog
tags:
- tangential
---

I’ve been experimenting with using Emacs [Org-Mode tables](http://orgmode.org/manual/Tables.html) to track my key performance indicators for my different side projects.

I like the idea of using emacs and org-mode for this type of data because it allows for quick data analysis via the programming language of your choice.

Alternatively, when using Google Spreadsheets you don’t have any integration with a programming language right at-hand.

Having a deep integration with a programming language on the data you collect, is invaluable. In this case, we are using the Emacs integration with babel’s [literate coding](https://en.wikipedia.org/wiki/Literate_programming) source code blocks.

This article is meant to be a very basic bare-bones intro to emacs org-mode, passing data from org-mode tables to souce-code, plotting, and rendering the result.

Due to the fact that we want to keep this approachable, we will use very basic data that produces very basic plots for this workflow tutorial.

****

Let’s dive in…

Here’s our scenario

> We have a side project called StartHereFM (spoiler: true story). StartHereFM is a podcast network and the team needs to be able to see a range of different statistics on a daily basis.

Here’s a preview of the final result, let’s reverse engineer it.

![](https://web.archive.org/web/20160313084734im_/http://dain.io/wp-content/uploads/2015/11/Screen-Shot-2015-11-30-at-1.42.36-AM6.png)

I’m assuming you know how to install emacs, install org-mode, open a website and read the basics of org-mode.

Let’s first create the org-mode file: I named mine `dashboard.org`.

The next thing we need is the data, which we store in org-mode tables. Pick a few stats from your side project, and go ahead and replicate this table setup above. Enter your dates on the left, and columns with the data rows. FYI: You can automate this step if you want, but I won’t go into detail about it. At a high level you can use apps like IFTT to send data to a backend service that you write that formats the data then serializes it into your file’s table on Dropbox/etc.

NOTE: Make sure you use the “#+TBLNAME” syntax to put a name on the table, for use in code in a minute.

Now that we have the data, let’s go ahead and send it on over into a code block, in this case we are using Python 3 primarily to take advantage of the SciPy stack.

```python
    #+begin_src python :var data=startherefm :exports both :results output :width 300
     import numpy as np
     import matplotlib.pyplot as plt
     import matplotlib.dates as mdates
     import datetime 
    #+end_src
```

You can see the source code block begins with “#+begin_src” and ends with “#+end_src”. Inside of that you put your code. You can hit Ctrl-‘ to edit the code in the block with syntax highlighting for that language. Then hit Ctrl-‘ again to get back to your source code block.

The next thing you’ll notice is the params I’m passing into the block. You can see I’m setting a “data” variable equal to the table name I defined above. This is very easy to do with org-mode and is a very cool way to toss data around. Thanks org! I’m also passing in [a few options](http://orgmode.org/manual/Code-block-specific-header-arguments.html) to verify the org file displays the stdout of the script’s execution. And you can ignore the width bit.

Now that we have the data in the source code block with the SciPy stack, let’s make use of it!

*****

First, let’s assign all columns data to a variable.

```python
    dates, daily_dls, total_dls, total_yt_views, site_views, email_list, twitter_followers = zip(*data)
```

Next, let’s clean the data. Also called “data munging”.

Actually, pause, before we jump in let’s review data analysis 101 which states that there are four phases to managing data: [data extraction](https://en.wikipedia.org/wiki/Data_extraction), [data munging](https://en.wikipedia.org/wiki/Data_wrangling) (cleaning), [data governance](http://searchdatamanagement.techtarget.com/definition/data-governance) (controlling data quality), and [data architecture](https://en.wikipedia.org/wiki/Data_architecture) (breaking silo’d data warehouses).

Okay, now that we have that out of the way let’s dive into munging or cleaning this data..

```python
 
     dates = list(dates)
     dates = dates[1:]
     if "" in dates: dates.remove("")

     daily_dls = list(daily_dls)
     daily_dls = daily_dls[1:]
     if "" in daily_dls: daily_dls.remove("")

     total_dls = list(total_dls)
     total_dls = total_dls[1:]
     if "" in total_dls: total_dls.remove("")

     _total_dls = []
     for dl in total_dls:
         dl = int(dl.replace(',',''))
         _total_dls.append(dl)

     total_yt_views = list(total_yt_views)
     total_yt_views = total_yt_views[1:]
     if "" in total_yt_views: total_yt_views.remove("")

     _total_yt_views = []
     for yt in total_yt_views:
         yt = int(yt.replace(',',''))
         _total_yt_views.append(yt)
```

NOTE: I’m aware there are better ways to write this in Python, but I’m not a big Python coder and this works fine for my scripts like this one.

Very basic cleaning procedure for this example, just converting some strings to integers, and removing some empty strings from an array, etc.

The end goal of this cleaning step is to verify you have two arrays in their ready-to-plot data-type, which for now means integers and date objects.

To that end, let’s get our dates array ready…

```python
     _dates = []
     for date in dates:
         date = datetime.datetime.strptime(date, "%b %d %Y").date()
         _dates.append(date)
```

NOTE: I’m aware there are better ways to write this in Python, but I’m not a big Python coder and this works fine for my scripts like this one.

Now the data is fairly cleaned up and we have our 3 arrays (1 dates array, and 2 integer arrays to plot) let’s go ahead and create the figures using matplotlib and lay the data onto it in the most simple possible fashion.

The first thing you have to ask when plotting data is how many plots do I need? Could I plot all of this data on one plot?

Plotting all the arrays of data on one figure usually only works if the data sets are within the same range. If you have a diverse pool of ranges on the y axis the visualization will lose its ocular efficiency.

Based on that, we know that this data is very different and probably won’t work on 1 figure. Why? Mainly because we are plotting total_downloads which is near the 100 thousands but the daily_downloads is very low around the couple hundred mark. Thus, this will be optimally plotted in two separate plots.

So, we are going to create two plots: the first is total downloads and the second is daily downloads. They both are going to have the dates in the x axis for this example, and the y axis will be the respective download count.

```python
     fig = plt.gcf()
     fig.set_size_inches(5,2)
     fig2 = plt.figure()
     fig2.set_size_inches(5,2)
     fig3 = plt.figure()
     fig3.set_size_inches(5,2)

     fig.gca().xaxis.set_major_formatter(mdates.DateFormatter('%b %d %Y'))
     fig.gca().xaxis.set_major_locator(mdates.DayLocator())

     fig2.gca().xaxis.set_major_formatter(mdates.DateFormatter('%b %d %Y'))
     fig2.gca().xaxis.set_major_locator(mdates.DayLocator())

     fig3.gca().xaxis.set_major_formatter(mdates.DateFormatter('%b %d %Y'))
     fig3.gca().xaxis.set_major_locator(mdates.DayLocator())

     ax = fig.add_subplot(111)
     ax2 = fig2.add_subplot(111)
     ax3 = fig3.add_subplot(111)

     ax.plot(_dates, _total_dls, label="Total Downloads")
     ax.legend(loc='upper center', bbox_to_anchor=(0.5, 1.09))
     fig.tight_layout()
     fig.savefig('starthere.png')

     ax2.plot(_dates, daily_dls, label="Daily Downloads")
     ax2.legend(loc='upper center', bbox_to_anchor=(0.5, 1.09))
     fig2.tight_layout()
     fig2.savefig('starthere-daily.png')

     ax3.plot(_dates, _total_yt_views, label="YouTube Views")
     ax3.legend(loc='upper center', bbox_to_anchor=(0.5, 1.09))
     fig3.tight_lay
     fig3.savefig('starthere-ytviews.png')
```

NOTE: I’m aware this is not compact or DRY, but it works for mer. Also, you’ll notice we are plotting a third chart – please ignore that for this tutorial.

The first thing we do is create the figures and set their size. You can read up on the options by visiting the matplotlib documentation.

Next we set the formatting for the date axis on the plots. Then we add subplots. Then we call plot! Plot simply takes as params an x, y, and label. How amazing right?

I then call tight_layout(), which is a nice helper function that basically makes tiny charts look better by using a different spacing technique on the axis labels.

Then I save the figure, because want to the visualization to appear in the document.

How do we do that? Well, first you should know that Emacs is basically an [operating system for text](http://unix.stackexchange.com/questions/986/what-are-the-pros-and-cons-of-vim-and-emacs). Thus, [files can be linked to and fro’](http://orgmode.org/manual/External-links.html), and you do that by using the syntax: “[[file_name.png]]”. 

Here we want to put these links in for the plots we just saved.

```
   *StartHereFM Key Performance Indicators Over Time*
   [[starthere.png]]

   [[./starthere-daily.png]]
   
   [[./starthere-ytviews.png]]
```

Once these are set, you can put your cursor in the source code block and hit Ctrl-c Ctrl-c and it will run the babel-execute function for the language you listed in the “#+begin_src python” snippet.

Now your plots should be saved in the folder you’ve placed this .org file.

The magic is now possible and you can toggle between the link to your plot and the plot image itself inline with Ctrl-c Ctrl-x Ctrl-v.

Here’s the [full org file source](https://gist.githubusercontent.com/dainmiller/9f2e38f7a09e25dcd2a9/raw/ca63ef5a0fa1dfe703ce16d90e04cfdb71e376ca/dash-example.org) for you to run (Ctrl-c Ctrl-c in code block then Ctrl-c Ctrl-x Ctrl-v) in org-mode and learn from.