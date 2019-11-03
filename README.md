# Fundamentals-of-Computing-and-Data-Display
---
title: "Fundamentals of Computing and Data Display"
subtitle: "Effect of Foreign Aid on Health Outcomes: Subnational Analysis for Indonesia"
author: "Dilare Ecenur Irbik"
date: "`r Sys.Date()`"
output:
  pdf_document:
    toc: yes
    df_print: kable
fontsize: 11pt 
---

```{r, include = FALSE}
library(knitr)
library(tidyverse)
```

<style>
body {
text-align: justify}
</style>





```{r}
#AidData
getwd ()
setwd("C:/Users/Ecenur/Desktop/Indonesia Term Paper")
aid20042014 <- read.csv("aid20042014.csv", header = T, sep = ",")
```

```{r}
library(dplyr)
aid20042014 <- aid20042014 %>% rename (Aid_2004=worldbank_geocodedresearchrelease_level1_v1_4_2.d89eb67.sum, Aid_2014=worldbank_geocodedresearchrelease_level1_v1_4_2.0c1866b.sum, Region=NAME_1)

subset20042014 <- subset(aid20042014, 
            select=c(Aid_2004, Aid_2014, Region))
```


```{r}
#DHS 
getwd ()
setwd("C:/Users/Ecenur/Desktop/Indonesia Term Paper")
library(foreign)
dhs2007 <- read.dta("2007.dta")
subset2007 <- subset(dhs2007,
          select=c(caseid,v001, v002, v007, v012, v024, v025, v106, m14, m18))
dhs2017 <- read.dta("2017.dta")
subset2017 <- subset(dhs2017,
          select=c(caseid,v001, v002, v007, v012, v024, v025, v106, m14, m18))
```


```{r, include=FALSE}
#Merge datasets
library(gtools)
dhs_merged <- smartbind(subset2007,subset2017)


#Rename and reorder variables
library(dplyr)
dhs_merged <- dhs_merged %>% rename (id=caseid, ClusterNumber=v001, HHNumber=v002, Year=v007, Age=v012, Region=v024, Residence=v025, Education=v106, Aid_Antenatal=m14, Birth_Size=m18)


#Missing values
library(naniar)
dhs_merged <- replace_with_na(dhs_merged, replace = list(Aid_Antenatal = c("98","99"))) #Replace strange missing values with NA
dhs_merged <- dhs_merged[!is.na(dhs_merged$Aid_Antenatal),] #Deleted NAs in the column



dhs_merged <- replace_with_na(dhs_merged, replace = list(Birth_Size = c("98","99"))) #Replace strange missing values with NA
dhs_merged <- dhs_merged[!is.na(dhs_merged$Birth_Size),] #Deleted NAs in the column


dhs_merged$Year [dhs_merged$Year ==""] <- "2007"

dhs_merged$Education [dhs_merged$Education =="no education" & dhs_merged$Year=="2017" ] <- "0"
dhs_merged$Education [dhs_merged$Education =="primary" & dhs_merged$Year=="2017" ] <- "1"
dhs_merged$Education [dhs_merged$Education =="secondary" & dhs_merged$Year=="2017"] <- "2"
dhs_merged$Education [dhs_merged$Education =="higher" & dhs_merged$Year=="2017"] <- "3"


dhs_last <- dhs_merged[apply(dhs_merged,1,function(x) {any(c("east kalimantan", "west kalimantan", "maluku", "north sulawesi") %in% x)}),]
```


## Analysis

The regression equation is: 


$h1 = \beta0+ \delta0post2004+\beta1aidregion+\delta 1(post2004\times aidregion) + \epsilon$

"h1" is the measure of health outcome. In this paper, health outcome metric is antenatal care visits. The definition of antenatal care visit is in line with that of the World Health Organization (WHO), which is the percentage of women aged 15 to 49 with a live birth in a given time period that received antenatal care by any provider at least four times (UNICEF 2018).

"aid_region" is a binary variable that equals 1 if individual interviewed was in an province that received health aid (the one treated, and 0, otherwise. I test whether individuals living in the provinces where aid project was implemented benefited from the antenatal care visits.

"post2004" is a dummy that equals 1 if year is 2014, and 0, otherwise. Accordingly, 2004 is the baseline year and the end-line is 2014.   

The main parameter of interest is "$\delta1$", which is the Diff-in-diff estimator. It shows whether individuals received health aid has more antenatal care visits than the individuals within the control group between the two time periods 2004 and 2014. 


## Results

```{r}
mean(subset2007$v012)
mean(subset2017$v012)

length(dhs_last$Aid_Antenatal[dhs_last$Aid_Antenatal >= "4" & dhs_last$Year == "2007"])
length(dhs_last$Aid_Antenatal[dhs_last$Aid_Antenatal >= "4" & dhs_last$Year == "2017"])

length(dhs_last$treated[dhs_last$treated == "1"])/3232*100
length(dhs_last$treated[dhs_last$treated == "0"])/3232*100

length(dhs_last$Aid_Antenatal[dhs_last$Aid_Antenatal >= "4" & dhs_last$Residence == "urban"])
length(dhs_last$Aid_Antenatal[dhs_last$Aid_Antenatal >= "4" & dhs_last$Residence == "rural"])
```





```{r}
#Difference in differences (DID)
library(ggplot2)
library (foreign)
dhs_last$year <- ifelse(dhs_last$Year >= 2017, 1, 0)
dhs_last$treated <- ifelse(dhs_last$Region == "east kalimantan" |
                             dhs_last$Region == "west kalimantan", 1, 0)
dhs_last$did = dhs_last$year*dhs_last$treated
didreg = lm(Aid_Antenatal >= "4" ~ treated + year + did, data = dhs_last)
summary(didreg)
```



```{r}
ggplot(dhs_last, aes(dhs_last$year, Aid_Antenatal, color = dhs_last$treated)) +
  geom_jitter() +
  theme_minimal()
```



