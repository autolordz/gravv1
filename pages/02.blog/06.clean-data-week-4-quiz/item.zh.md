---
title: 数据科学清洗数据小测Week-4-Quiz
published: true
date: '02/05/2018 05:29 pm'
metadata:
    关键字: '数据科学,小测,quiz'
taxonomy:
    category:
        - blog
    tag:
        - Rstudio
        - R
        - Coursera
        - Project
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
googledesc: "You can use the quantmod (http://www.quantmod.com/) package to get historical stock prices for publicly traded companies on the NASDAQ and NYSE. Use the following code to download data on Amazon's stock price and get the times the data was sampled.\r\nHow many values were collected in 2012? How many values were collected on Mondays in 2012?"
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
---

### Clean Data Week 4

### QUESTION 1. 

* Apply strsplit() to split all the names of the data frame on the 
* characters "wgtp". What is the value of the 123 element of the resulting list?

```r
library(readr)
dat_Fss06hid <- read_csv("D:/gitcode/Rlan/getdata_Fss06hid.csv")
ans <- strsplit(names(dat_Fss06hid),split = "wgtp")
ans[123]
```
### QUESTION 2. 
*  Remove the commas from the GDP numbers in millions of dollars and average them. What is the average? 

```r
dat_FGDP <- read_csv("D:/gitcode/Rlan/getdata_FGDP.csv",skip = 4,n_max = 190)
dat_FGDP <- select(dat_FGDP,X1,X2,X4,X5)
colnames(dat_FGDP) <- c("CountryCode", "Rank", "countryNames", "GDP.Value")
View(dat_FGDP)
mean(as.numeric(gsub(",", "", dat_FGDP$GDP.Value)))
```

### QUESTION 3.

*  In the data set from Question 2 what is a regular expression that would allow you to count the number of countries whose name begins with "United"? 
*  Assume that the variable with the country names in it is named countryNames. How many countries begin with United?

```js
length(grep("^United",dat_FGDP$countryNames))
```
### QUESTION 4.
* Load the Gross Domestic Product data for the 190 ranked countries in this data set:
* Load the educational data from this data set:
* Match the data based on the country shortcode. Of the countries for which the end of the fiscal year is available, how many end in June?

```r
dat_FEDSTATS_Country <- read_csv("D:/gitcode/Rlan/getdata_FEDSTATS_Country.csv")
dat_FEDSTATS_Country <- select(dat_FEDSTATS_Country,"CountryCode","Special Notes")
mergedData <- merge(dat_FGDP, dat_FEDSTATS_Country, as.x = "CountryCode", as.y = "CountryCode")
length(grep("[Ff]iscal year end(.*)+June", mergedData$`Special Notes`))
```

### QUESTION 5.
* You can use the quantmod (http://www.quantmod.com/) package to get historical stock prices for publicly traded companies on the NASDAQ and NYSE. Use the following code to download data on Amazon's stock price and get the times the data was sampled.
*  How many values were collected in 2012? How many values were collected on Mondays in 2012?

```r
library(quantmod)
amzn = getSymbols("AMZN",auto.assign=FALSE)
sampleTimes = index(amzn)
sampleTimes2012 = sampleTimes[grepl("^2012", sampleTimes)]
length(sampleTimes2012)
length(grep("^星期一",sapply(sampleTimes2012,weekdays)))
```

* THE END
  