---
title: 数据科学清洗数据小测Week-1-Quiz
media_order: pressure-1.png
published: true
date: '01/10/2019 03:39 pm'
metadata:
    关键字: '数据科学,清洗数据'
    keywords: 'R,python,dataclean'
    author: 'Autoz Zhao'
taxonomy:
    category:
        - blog
    tag:
        - Rstudio
        - R
        - Coursera
        - Project
        - Blog
        - Grav
        - Quiz
external_links:
    process: true
    no_follow: true
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
summary:
    enabled: '1'
googledesc: "Load the Gross Domestic Product data for the 190 ranked countries in this data set:\r\nLoad the educational data from this data set:\r\nMatch the data based on the country shortcode. Of the countries for which the end of the fiscal year is available, how many end in June?"
twittercardoptions: summary
articleenabled: false
musiceventenabled: false
orgaenabled: false
orga:
    ratingValue: 2.5
orgaratingenabled: false
eventenabled: false
personenabled: false
restaurantenabled: false
restaurant:
    priceRange: $
header_image: '0'
---

### GETTING AND CLEANING DATA: WEEK 1 QUIZ

Autoz
2017年11月21日


### QUESTION 1

The American Community Survey distributes downloadable data about United States communities. Download the 2006 microdata survey about housing for the state of Idaho using download.file() from here:

<https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06hid.csv>

and load the data into R. The code book, describing the variable names is here:

<https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FPUMSDataDict06.pdf>

How many properties are worth $1,000,000 or more?

``` r
# if (!file.exists("data")) {
#   dir.create("data")
# }
#install.packages("data.table")
library(data.table) 
```

    ## Warning: package 'data.table' was built under R version 3.4.2

``` r
fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Fss06hid.csv"
download.file(fileUrl, destfile = "america_comm_survey.csv")
data <- read.csv("america_comm_survey.csv")
head(data$VAL) # take a look at the contents of the VAL (property worth) variable
```

    ## [1] 17 NA 18 19 20 15

### R Markdown

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

``` r
summary(cars)
```

    ##      speed           dist       
    ##  Min.   : 4.0   Min.   :  2.00  
    ##  1st Qu.:12.0   1st Qu.: 26.00  
    ##  Median :15.0   Median : 36.00  
    ##  Mean   :15.4   Mean   : 42.98  
    ##  3rd Qu.:19.0   3rd Qu.: 56.00  
    ##  Max.   :25.0   Max.   :120.00

### Including Plots

You can also embed plots, for example:

![](pressure-1.png)

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
