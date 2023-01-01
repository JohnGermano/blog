---
layout: post
title:  "Ring in the New Year"
date:   2023-01-01
categories: Podcast
---

## Start Your New Goal to be Creative with Research
If you are anything like me, you celebrate the new year with great excitement every January 1st, and fill your head and maybe even your notebook with all of the cool new goals you’re setting for yourself for the year. All your work is going well, you’re making great progress and you’re proud of what you accomplished. Then, come January 15th, it all comes crashing down for one reason or another — usually, for me, it’s lack of motivation.

This year is different for me, at least. I want to put in the work to actually continue with my goal all year round. What is that goal you ask? For the longest time, I have wanted to start and continue a serious a podcast. Obviously, the hope would be to have a lot of listeners and engagement, but where do you even start? You can make a podcast where you’re just bullshitting with whoever — that’s seen success with Joe Rogan’s podcast, but when my wife and I tried it with our podcast “Bullshitting with B”, the motivation wasn’t really there.  And with all the technology out there like [anchor.fm](https://anchor.fm/), it’s really easy to “start” a podcast. Heck, you could just start a podcast by uploading your audio straight to YouTube. 

But physically starting a podcast is not the hard part, in my opinion.  The hard part is finding a niche that you love, but one that you have a chance to stand out in. What value do you bring to that niche? What’s something that you could do for someone else’s benefit? 

And so, before trying out to answer these questions, I decided to put some of my rusty data analysis skills to the test to try to figure out what niche a new creator should enter without getting drowned out by other creators. Creating a product that, in itself, continues to motivate you as you complete your work.

To do this, I broke out R. This language has always been a favorite of mine and will be something that I continue to use to run any sort of research.

Starting off, we initialize our libraries and set our working directory. In this case, I am working with a [Kaggle](https://www.kaggle.com/datasets/thoughtvector/podcastreviews/download?datasetVersionNumber=27) dataset. Please note, to download the dataset you need a Kaggle account. This dataset is a SQLite database, so if you want to replicate this blog post, you will need to know some basic SQL. The library DBI lets us work with SQLite databases in R.

```r
# Open the library
library(DBI)

# Set working directory
setwd("~/Downloads/R Projects")
```

After downloading the dataset, go ahead and create a database object from the SQLite file in your working directory.

```r
Download Dataset
#https://www.kaggle.com/datasets/thoughtvector/podcastreviews/download?datasetVersionNumber=27

# Read about how to use RSQLite at https://cran.r-project.org/web/packages/RSQLite/vignettes/RSQLite.html
podcasts <- dbConnect(RSQLite::SQLite(),"podcasts.sqlite")
```

After running this, let’s take a look at the tables in the database. We can see we have four tables: category, podcasts, reviews, and runs. From my inspection, the runs table isn’t very informative for anything I would want to do — this table is updated with every update the creator runs on the database, which is monthly.

```r
#Take a look at the tables in the database
dbListTables(podcasts)
```

We can also take a look at what each table looks like. Reminder for all those dusting off their SQL skills — don’t select all from an entire table, this could crash your IDE, but R does a pretty good job at stopping this.

```r
# Look at the top 5 rows in each table
dbGetQuery(podcasts, 'SELECT * FROM categories LIMIT 5')
dbGetQuery(podcasts, 'SELECT * FROM podcasts LIMIT 5')
dbGetQuery(podcasts, 'SELECT * FROM reviews LIMIT 5')
dbGetQuery(podcasts, 'SELECT * FROM runs LIMIT 5')
```

Next, I’m going to join the category and ratings tables on the `podcast_id` variable because I’m really just curious about what categories do the best. This command only queries the database, but it gives me something interesting I can look at while I continue.

```r
# Look at the items we care about
dbGetQuery(podcasts, "SELECT category,
                             rating, 
                             reviews.podcast_id
                      FROM categories
                      INNER JOIN reviews ON categories.podcast_id=reviews.podcast_id
                      LIMIT 5")
```

Next, I’m doing a couple of things:

1. From this previously joined table, collapse all the information to be viewable by category of the podcast.
2. Compute the average rating of each category of podcast
3. Count the number of podcasts within each category. _Note:_ In this dataset, a single podcast could be in multiple different categories by doing this. For example, a podcast could be art & music, and would be included twice.
4. Count the number of rows. The way this SQL is written, this counts the number of reviews, and assumes that a single person can only write one review and can only be one listener. This is a big assumption.
5. I input this output into an R data frame simply called `data` in my notebook.

```r
# Get the data and store it
data <- dbGetQuery(podcasts, "SELECT  category,
                                      AVG(rating) as 'Avg. Rating',
                                      COUNT(DISTINCT reviews.podcast_id) as 'No. of Podcasts',
                                      COUNT(*) as 'No. of Listeners'
                              FROM categories
                                   INNER JOIN reviews ON categories.podcast_id=reviews.podcast_id
                              GROUP BY category")
```

At this point, I’m also adding a new column called `Saturation Ratio`. My definition of this column is essentially how many listeners a new podcast creator could likely expect on _average_ if they were to make a podcast within that category. Higher numbers are better. So, if someone wants to start a podcast about starting or maintaining a nonprofit, they would only expect 5 listeners, because there are too many podcasts for the amount of listeners. That category is “saturated”.

```r
# Saturation Ratio
data$'Saturation Ratio' <- round(data$`No. of Listeners`/data$`No. of Podcasts`,0)
```

![](assets/images/BlogPosts/Podcast01.01.23/csv.jpg)

I ended up exporting this dataset and posting it on [my Github](https://github.com/JohnGermano/Projects/blob/master/Podcasts%2001.01.23/podcasts.csv) for you to take a look at if you wish. I inserted a picture here to show what I am looking at as well. Here is my conclusion b/c the saturation ratio essentially does the interpretation for me: START A TRUE CRIME PODCAST. Holy cow, there are so many listeners that you could expect: 126! Keep in mind, this is still an average number so it can be skewed by the bigger podcasts, but wow. For something that I think a lot of people believe is overdone, there is certainly a market for it. Stories for kids comes in at second place, and this does not really surprise me, but I don’t think there are many people who are interested in this — though, if you’re a teacher, I think this would be a great way to continue sharing your passion with kids. 

The fifth most unsaturated option would also be daily news. This does not surprise me at only 317 podcasts, because the amount of effort it would take to script, record, edit, and get that out early enough for your listeners to enjoy would be immense. No single person can do that, in my opinion. But if you have the drive for it, do it.

Honestly, in 2023, you might be seeing me in my research mode working on my new project: a true crime podcast.

Let me know your thoughts in the comments below, I’m curious what your takes are.

~ Be kind to yourself
