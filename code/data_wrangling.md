---
title: "Data wrangling"
author: 
  name: Arman Shaid
  affiliation: Oregon State University
date: "24 April 2026"  # can also use: # </br>24 April 2026
output:
  html_document:
    theme: flatly  # you can experiment with other themes! for example, yeti
    highlight: haddock 
    toc: yes
    toc_depth: 3
    toc_float: yes
    keep_md: true
---



## Goal and requirements

The goal of this assignment is to impress with your data wrangling skills. 

Some additional points:  

- You can create a new GitHub repo for this assignment.  
- Name your GitHub repo something like `aec699-hw2-datawrangling-lastname`.  
- Make sure to organize your data into a `data/` folder and your code into a `scripts/` or `code/` folder.  
- Do not forget to knit the assignment (click the “Knit” button, or press `Ctrl+Shift+K`) before submitting.  
- **Please put your (written) answers in bold.**  

### What you will be graded on

- Is the code correct?  
- Are the outcomes correct?  
- Did you show your work?  
- Are the figures clear?  
- Did you follow the instructions of the assignment?  

## Preliminaries: 

### Load libraries

It is a good idea to load your libraries at the top of the Rmd document so that everyone can see what you are using. Similarly, it is good practice to set this type of chunk with `cache=FALSE` to ensure that the libraries are dynamically loaded each time you knit the document.

*Hint: I have only added the libraries needed to download and read the data. You will need to load additional libraries to complete this assignment. Add them here once you discover that you need them.* 


``` r
if (!require("pacman")) install.packages("pacman")  # install the pacman package if necessary
pacman::p_load(httr,readxl,here)  # install other packages using pacman::p_load()
```

### Read in the data

Use `httr::GET()` to fetch the EIA excel file for us from web.


``` r
# library(here)  # already loaded
# library(httr)  # already loaded
url <- "https://www.eia.gov/coal/archive/coal_historical_exports.xlsx"
if(!file.exists(here::here("data/coal.xlsx"))) {  # only download the file if we need to
  GET(url, write_disk(here::here("data/coal.xlsx")))  # modify relative path to match directory
}
```

```
## Response [https://www.eia.gov/coal/archive/coal_historical_exports.xlsx]
##   Date: 2026-04-24 08:43
##   Status: 200
##   Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
##   Size: 997 kB
## <ON DISK>  /Users/armanshaid/Desktop/aec699-hw2-datawrangling-shaid/data/coal.xlsx
```

Next, we read in the file.


``` r
# library(readxl) already loaded
coal <- read_xlsx(here::here("data/coal.xlsx"), skip=3, na=".")
```

We are now ready to go.

## 1) Clean the column names

The column (i.e. variable) names are not great: Spacing, uppercase letters, etc. Clean them. 

*Hint: You can use `gsub()` and regular expressions, or external packages like `magrittr` or `janitor`.*

## 2) Total US coal exports over time (year only)

Plot the US's total coal exports over time by year ONLY. What secular trends do you notice in the data?

*Hints: If you want nicely formatted y-axis label, add `+ scale_y_continuous(labels = scales::comma)` to your `ggplot2` code.*

## 3) Total US coal exports over time (year AND quarter)

Now do the same as the above, except aggregated quarter of year (2001Q1, 2002Q2, etc.). Do you notice any seasonality that was masked from the yearly totals?

*Hint: ggplot2 is going to want you to convert your quarterly data into actual date format before it plots nicely. (i.e. Don't leave it as a string.)*

## 4) Exports by destination country

### 4.1) Create a new data frame

Create a new data frame called `coal_country` that aggregates total exports by destination country (and quarter of year). Make sure you print the resulting data frame so that it appears in the knitted R markdown document.

### 4.2) Inspect the data frame

It looks like some countries are missing data for a number of years and periods (e.g. Albania). Confirm that this is the case. What do you think is happening here?

### 4.3) Complete the data frame

Fill in the implicit missing values, so that each country has a representative row for every year-quarter time period. In other words, you should modify the data frame so that every destination country has row entries for all possible year-quarter combinations (from 2002Q1 through the most recent quarter). I.e., a balanced panel. Order your updated data frame by country, year and, quarter. 

*Hints: See `?tidyr::complete()` for some convenient options. Another option is `tidyr::crossing()`. Do not forget to print `coal_country` after you have updated the data frame to show the results.*

### 4.4 Some more tidying up

In answering the previous question, you _may_ encounter a situation where the data frame contains a quarter that is missing total export numbers for *all* countries. Did this happen to you? Filter out the completely missing quarter if so. Also: Why do you think this might have happened? (Please answer the latter question even if it did not happen to you.) 

### 4.5) Culmulative top 10 US coal export destinations

Produce a vector -- call it `coal10_culm` -- of the top 10 top coal destinations over the full study period. What are they?

### 4.6) Recent top 10 US coal export destinations

Now do the same, except for the most recent period on record (i.e. final quarter in the dataset). Call this vector `coal10_recent` and make sure to print it so that it is visible too. Are there any interesting differences between the two vectors? Apart from any secular trends, what else might explain these differences?

### 4.7) US coal exports over time by country

Plot the quarterly coal exports over time, but now disaggregated by country. In particular, highlight the top 10 (cumulative) export destinations and then sum the remaining countries into a combined "Other" category. (In other words, your figure should contain the time series of eleven different countries/categories.)

### 4.8) Make it pretty

Take your previous plot and add some style to it. That is, try to make it as visually appealing as possible without overloading it with chart junk.

*Hint: You have a lot of options here. If you have not already done so, consider a more bespoke theme with the `ggthemes` or `cowplot` packages. Try out `scale_fill_brewer()` and `scale_color_brewer()` for a range of interesting color palettes. Try transparency effects with `alpha`. Give your axis labels more refined names with the `labs()` layer in ggplot2. Or, you might want to scale (i.e. normalize) your y-variable to get rid of all those zeros. You can shorten any country names to their ISO abbreviation; see `?countrycode::countrycode`. More substantively, but more complicated, you might want to re-order your legend (and the plot itself) according to the relative importance of the destination countries. See `?forcats::fct_reorder` or `?forcats::fct_relevel`.*

### 4.9) Make it interactive

Create an interactive version of your previous figure.

*Hint: Take a look at plotly::ggplotly(), or the gganimate package.*

## 5) Show something interesting

There is still a lot to explore with this dataset. Your final task is to show something interesting about the data. Drill down into the data and explain what is driving the secular trends that we have observed above. Or highlight interesting seasonality within a particular country. Or go back to the original `coal` data frame and look at exports by customs district, or by coal type. Do we have changes or trends there? Etcetera. The only requirement is that you show your work and explain what you have found.
