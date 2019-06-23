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



## Introduction


The majority of the previous foreign aid research is about the effects of international aid on economic growth and poverty reduction. For instance, Arndt, Jones, and Tarp (2015), Bornschier, Chase-Dunn, and Robinson (1978) indicate that there is a positive effect of foreign aid on economic growth. On the contrary, Ekanayake, Cookman, and Chatrna (2010) show that there is not an impact of the foreign aid in developing countries. Although these studies give important empirical evidence about the impact of foreign aid on economic development, there is a gap in the literature when addressing how foreign aid affects health outcomes such as mortality or maternal health (Kotsadam, Qstby, Rustad, Tollefsen, and Urdal 2018). There are some recent studies focusing on the effect of foreign aid on infant or child mortality (Burguet and Soto, 2012; Kotsadam et al. 2018). Like the economic growth literature, empirical evidence concerning the effectiveness of aid on health outcomes has been inconclusive (Rajan 2005; Quibria 2010). For example, Williamson (2008) underlines the inefficacy of foreign aid on overall health outcomes. Similarly, Pandolfelli et al. (2014) show that there is an increase in maternal mortality in Sub-Saharan Africa due to IMF loans and structural adjustment. On the other hand, study of Gyimah-Brempong (2015) indicate that health aid indeed has a positive effect on various health outcomes in African countries. Mishra and Newhouse (2009) showed a mixed result; there is not an impact of overall foreign aid on infant mortality whereas health aid reduces mortality levels. 

That the most of the empirical studies are on the basis of cross-country analyses is one reason for the inconclusive results of the aid effectiveness. Because cross-country analyses may not control for differences across countries due to the spurious effects between aid and various outcomes (Odokonyero 2015) as well as the effects of aid being too small and localized to affect aggregate outcomes due to the lack of robust results regarding the effects of aid on development (Briggs 2017). Therefore, the subnational level analysis enable us a highly aggregated unit of analysis to unambiguously determine the aid effectiveness. In general, there is a lack in the literature about the aid effectiveness on health outcomes on the subnational level (Kotsadam et al. 2018) and this paper will try to make a contribution to this gap in the context of Indonesia.

One of the indicators of a society's level of development as well as of the performance of the health care delivery system is the state of maternal health. Antenatal care, at least one visit, is important in monitoring pregnancy and helping to reduce the risks for the mother and child during this period (National Population Commission Federal Republic of Nigeria 2004). Maternal health has been one of the major public health concern in Indonesia. Rate of maternal mortality in 2012 was 359 per 100,000 live births although the United Nations's fifth Millennium Development Goals targeted the number of 102 for Indonesia by 2015. The percentage of the women who have had at least one antenatal care visit is only 66% whereas the national target of antenatal care attendance is 90% (Fauk 2017).  

It is obvious that the information on antenatal care visit is a great value in identifying subgroups of women who do not utilize reproductive health services. This information gives us an insight for the current maternal health state and for planning the improvements in service delivery. In that sense, the aim of this study is, in particular, to answer to what extent foreign aid affect the health outcomes, in my case antenatal care visits, in a developing country, Indonesia, at the subnational level. This work will contribute to the literature in terms of subnational analysis of antenatal care to get an insight about the maternal health in Indonesia through a combined methodology of country level demographic survey and geocoded foreign aid projects. As a result, my main hypothesis is as the following: the number of antenatal care visits of women in areas that received aid, on average, is increased after the foreign aid on health has started.  


## Data

I utilize two different datasets and combine them for the analysis of localized effects of aid on maternal health. First, I use geographic aid data from the USAID-sponsored AidData project which is an open access database including geo-coded aid projects of certain donors (Strandow, Findley, Nielson, and Powell 2011). It gives both global or detailed country level datasets about the aid projects that range from education, industry to health (AidData User’s Guide Version 3.0). To be able to analyze the aid effectiveness on maternal health, I need to match the specific location of the projects; therefore, I used AidData precision coding 2.8 in which a project is defined as specific to a local government area and utilized the projects implemented by World Bank.     

Secondly, to analyze the demographic data, I utilized Demographic Health Surveys conducted over several years in Indonesia, which are nationally representative. For these surveys, a sample of households is selected across the whole country. These surveys are split into different categories such as women's survey, men's survey or birth records. DHS surveys give micro level information about demographic data of the several thousand respondents from both urban and rural areas (Demographic and Health Surveys Datasets 2004-2017). For my analysis, birth records surveys is corner stone.I used data from the two DHS survey rounds that have been conducted in Indonesia in 2007 and 2017 in which totally 3232 mothers are interviewed.The unit of analysis is women who are between 15 and 49 years old and interviewed about sexual and reproductive health, nutrition etc. 

I match the DHS data with the georeferenced AidData that give information about the provinces in which aid projects are conducted. I refer 4 different provinces in Indonesia for the further statistical analysis. Two of these provinces, Maluku and North Sulawesi, are defined as control group and other two, East kalimantan and West Kalimantan, as treatment group. I only use projects designated under the health sector and restrict the years of the data to projects that started at some point in 2004 and in 2014 in order to ensure that projects included in the analysis do not bias survey responses in 2007 and 2017.

Datasets provided by AidData enable us to determine the time of aid projects implemented whereas DHS datasets give information about the number of antenatal care visits in the treated provines before and after the aid projects started. In that sense, there is a limit of the effect of aid on health outcomes due to the fast or slow results of aid; for example, treatable diseases like diarrhea may have an immediate impact on child survival whereas the impact of other forms of aid such as female education for increasing the knowledge on infant mortality may be seen in a much longer time. Therefore, it would be better to test overall effect of aid on health outcomes (Kotsadam et al. 2018).       


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

The data provided by AidData is granularity, so it is difficult to clearly determine the individuals actually received foreign aid and this brings an increased uncertainty in larger areas. However, in the subnational analysis, this type of data can be understood as delineating the treatmnet group which is the individuals living in the provinces that received aid (Odokonyero 2015).

Two datasets give possiblity to make comparisons over both time and location. The knowledge of when and where an aid project is to be established enable us determine the level of health outcomes, before and after the projects have started, in treatment and control provinces. To study effect of aid on health outcomes, in the Demographic Health Surveys, women are asked the number of antenatal care visits during pregnancy. These women are the unit of analysis. My estimation procedure follows a Difference-in-Differences statitical analysis premised on panel data. The regression equation is: 


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

When we examine the descriptive statistics, results are as the following: The age in the samples of the years 2007 and 2017 is quite close to each other 37 and 38 respectively; age of women interviewed is on average level. The number of women who has at least four times antenatal care is 1041 in 2007 whereas it is 1109 in 2017. Approximately 48 percent of the population lived in areas that received aid. According to demographic characteristics and residence type of women interviewed, there is a slight difference in aid allocation between urban and rural areas; amount is higher in the latter one.



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


Diff-in-Diff estimation reveal  significant relation between aid and health outcome. The effect is significant at 10% with the treatment having a positive effect. The result of Difference-in-Difference approach suggest that foreign aid has contributed to improving health outcomes at the least, in my case antenatal care visits, in Indonesia. However, new improved policies and targets about aid could enhance effectiveness in the future. 


## Discussion

In this paper, I used the Difference in  Differences approach with data from Demographic Health Surveys conducted in Indonesia and combined it with geo-coded data from AidData to investigate the impact of aid projects on health outcomes. In conclusion, it can be said that foreign aid seems to slightly increase the antenatal care visits, and so health service system in Indonesia. However, there is still need of new policies and state support to increase resources not only to the health sector but in a more targeted way towards maternal health, and so child health.   

One limitation of this paper is that only the effect of aid by World Bank is analyzed and does not represents multilateral aid or aid from other donors. By taking into consideration other donors, a detailed and clearer map of effect of aid on maternal health may emerge over time and locations.   
 
Subnational level data on aid and health outcomes enable policymakers to analyze in detail the effectiveness of implementations in health sector. I restricted the sample in small size, only four provinces, since future studies requires in depth academic analysis which is based on geospatial programming such as GIS (geographic information systems) provided DHS Programme. Working on different countries and look for detailed spatial analysis might be helpful to identify different mechanisms of the effects of aid and which projects work best for the local people. In that way, our understanding of how location affects health outcomes and of what more effective interventions are  would be improved.  


Github link for the project is as folowing: 

## References

AidData 2019. "AidData User’s Guide Version 3.0.". Retrieved June 15, 2019 (http://www.nber.org/ens/feldstein/ENSA_Sources/AidData/aiddata_3.0_usersguide_july_2014.pdf). 

Arndt, C., Jones, S., & Tarp, F. (2015). Assessing foreign aid’s long run contribution to
growth and development. World Development, 69(1), 6–18.

Bornschier, V., Chase-Dunn, C., & Robinson, R. (1978). Cross-national evidence of the effects of foreign investment and aid on economic growth and inequality: A survey of findings and a reanalysis. American Journal of Sociology, 84(3), 651-683.

Briggs, R. C. (2017). Does foreign aid target the poorest? International Organization, 71(1), 187–206.

Burguet, R., & Soto, M. (2012). Measuring the child mortality impact of official aid for fighting infectious diseases, 2000–2010. Barcelona, Spain: Institute for Economic Analysis (CSIC) and Barcelona GSE.

Demographic and Health Surveys Datasets, 2004-2017. Funded by USAID. Rockville, Maryland. Retrieved May, 2019 (https://dhsprogram.com). 

Ekanayake, E. M., Cookman, B., & Chatrna, D. (2010). The effect of foreign aid on economic growth in developing countries. Journal of International Business and Cultural Studies, 3, 1-13.

Fauk, Cahaya IB, Merry MS, Damayani AD, Liana DS (2017) Exploring DeterminantsInfluencing the Utilisation
of Antenatal Care in Indonesia: A Narrative Systematic Review. J Healthc Commun Vol. 2 No. 4:69.

Gyimah-Brempong, K. (2015). Do African countries get health from health aid? Journal of African Development, 17, 83–114.

Kotsadam, A. et al. (2018). World Development 105, 59–69.

Mishra, P., & Newhouse, D. (2009). Does health aid matter? Journal of Health Economics, 28(4), 855–872.

National Population Commission Federal Republic of Nigeria 2004). 

Odokonyero, T., Alex, I., Robert, M., Tony, M. & Godfrey, O.M. (2015) Sub-national perspectives on aid effectiveness: impact of aid on health outcomes in Uganda. 

Pandolfelli, L. E., Shandra, J., & Tyagi, J. (2014). The International Monetary Fund, structural adjustment, and women’s health: A cross-national analysis of maternal mortality in Sub-Saharan Africa. The Sociological Quarterly, 55(1), 119-142.

Quibria, M.G. (2010). Aid effectiveness in Bangladesh. Morgan State University.

Rajan, R. (2005). Aid and growth: The policy challenge. Finance and development, 42 (4).

Strandow, D., Findley, M., Nielson, D. and Powell, J. 2011. The UCDP-AidData codebook on Geo-referencing Foreign Aid. Version 1.1. Uppsala Conflict Data Program. Uppsala, Sweden: Uppsala University.

UNICEF 2018. "Antenatal care". Retrieved June 18, 2019 (https://data.unicef.org/topic/maternal-health/antenatal-care/).


Williamson, C. R. (2008). Foreign Aid and Human Development: The impact of foreign aid to the health sectors. Southern Economic Journal, 75(1), 188–207.

