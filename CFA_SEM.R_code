### CFA and SEM analyses in R ###

# Load packages

library(devtools)
library(VGAM)
library(dplyr)
library(semPlot)
library(lavaanPlot) 
library(mice)
library(miceadds)
library(semTools)
library(semPlot)
library(tidyverse)
library(knitr)
library(lavaan)
library(psych)


# Read data
mids <- read.csv("imputed.csv")
data<-mids

# Select columns of the data-frame
df <- subset(data, select=c("spdq_tot", "pdsr_tot", "phq9_1to8","gadqiv_tot","ptsd_tot", "phq9_9"))

# Descriptive statistics 
## Factor varibales 

table(df$sex_factor)
table(df$gender_factor)
table(df$race_factor)

# Prevalence rates for emmotional disorder factors 
table(df$gad_dsm_newman) 
describe(df$gad_dsm_newman) 
table(df$sad_dsm) 
describe(df$sad_dsm)
table(df$pd_dsm) 
table(df$ptsd_clincial) 

ptsd <- nrow(subset(df, ptsd_tot>3)) 

phq9 <- nrow(subset(df, phq9_tot>5)) 
phq9 <- nrow(subset(df, phq9_tot>10)) 
phq9 <- nrow(subset(df, phq9_tot>15))
phq9 <- nrow(subset(df, phq9_tot>20)) 

# Correlation matrix and vizualization
x<-ma.wtd.corNA(df, weights=NULL, vars=NULL, method="unbiased")

# Get lower correlation matrix only (empty upper matrix)
library(xtable)
upper<-x
upper[upper.tri(x)]<-""
upper<-as.data.frame(upper)
upper 

ggcorrplot(x,
           hc.order = TRUE,
           method=c("square"),
           type = "lower",
           lab = TRUE)


#Two-factor model
psy.model<-'fear=~ pdsr_tot + ptsd_tot              
           distress=~ phq9_1to8 + gadqiv_tot + spdq_tot 
            int=~ 1*fear + 1*distress'

fir<-cfa.mi(psy.model, miPackage = "mice", data=df,estimator= "ML",std.lv= TRUE)

summary(fir, fit.measures = TRUE, standardized=TRUE, pool.robust=TRUE)


# Regress the second-order internalizing factor on suicideal ideation

psy.model<-'fear=~  pdsr_tot + ptsd_tot               
           distress=~ phq9_1to8 + gadqiv_tot + spdq_tot
            int=~ v1*fear + v1*distress
            #regression
            int~phq9_9'


fit.higher <- sem.mi(psy.model, miPackage = "mice", estimator="ML",
                    data=df)
summary(fit.higher, fit.measures = TRUE, standardized = TRUE, rsquare = TRUE) 


# Regress the first-order distress factor (and second-order internalizing factor) on suicidal ideation

direct.dist<-'
            fear=~  pdsr_tot + ptsd_tot              
           dist=~ phq9_1to8 + gadqiv_tot + spdq_tot 
            int=~ b*fear + b*dist #changed label that enforces equality constraint to reflect the "b path" in a mediation analysis
                 #regression
            int~a*phq9_9
            dist~c*phq9_9 #direct effect
            #indirect effect (a*b)
            indirect:= a*b
            #total effect
            total:= c + (a*b)'

fit.direct.dist<- sem.mi(direct.dist, 
                       miPackage="mice",
                      data = df, 
                      estimator="ML", 
                      std.lv = TRUE,
                      bootstrap = 500L)


summary(fit.direct.dist, 
        fit.measures = TRUE,
        standardized = TRUE, 
        rsquare = TRUE) 

# Regress the first-order fear factor (and second-order internalizing factor) on suicideal ideation

direct.fear <- ' #specifies the model
      # measurement model
      fear=~  pdsr_tot + ptsd_tot            
      dist=~ phq9_1to8 + gadqiv_tot + spdq_tot
       int=~ b*fear + b*dist #changed label that enforces equality constraint to reflect the "b path" in a mediation analysis
      # regression
  int ~ a*phq9_9
  fear ~ c*phq9_9 #this is the direct effect
      # indirect effect (a*b)
  indirect := a*b
      # total effect
  total := c + (a*b)'

fit.direct.fear<- sem.mi(direct.fear, 
                       miPackage="mice",
                      data = df, 
                      estimator="ML", 
                      std.lv = TRUE,
                      bootstrap = 500L)


summary(fit.direct.fear, 
        fit.measures = TRUE,
        standardized = TRUE, 
        rsquare = TRUE) 

# Regress depression (and the first-order distress factor and second-order internalizing factor) on suicideal ideation

direct.dep <- ' #specifies the model
      # measurement model
       fear=~  pdsr_tot + ptsd_tot              
      dist=~ c*phq9_1to8 + gadqiv_tot + spdq_tot 
       int=~ b*fear + b*dist
      # regression
  int ~ a*phq9_9
  dist ~ d*phq9_9
  phq9_1to8 ~ e*phq9_9 #this is the direct effect
      # indirect effect
  indirect := (a*b*c) + (d*c)
      # total effect
  total := e + (a*b*c) + (d*c)
'

fit.direct.dep<- sem.mi(direct.dep, 
                       miPackage="mice",
                      data = df, 
                      estimator="ML", 
                      std.lv = TRUE,
                      bootstrap = 500L)


summary(fit.direct.dep, 
        fit.measures = TRUE,
        standardized = TRUE, 
        rsquare = TRUE) 



# Regress generalized anxiety (and the first-order distress factor and second-order internalizing factor) on suicideal ideation


direct.gad <- ' #specifies the model
      # measurement model
       fear=~  pdsr_tot + ptsd_tot              
      dist=~ phq9_1to8 + c*gadqiv_tot + spdq_tot 
       int=~ b*fear + b*dist
      # regression
  int ~ a*phq9_9
  dist ~ d*phq9_9
  gadqiv_tot ~ e*phq9_9 #this is the direct effect
      # indirect effect
  indirect := (a*b*c) + (d*c)
      # total effect
  total := e + (a*b*c) + (d*c)
'

fit.direct.gad<- sem.mi(direct.gad, 
                       miPackage="mice",
                      data = df, 
                      estimator="ML", 
                      std.lv = TRUE,
                      bootstrap = 500L)


summary(fit.direct.gad, 
        fit.measures = TRUE,
        standardized = TRUE, 
        rsquare = TRUE) 

# Regress spdq (and the first-order fear factor and second-order internalizing factor) on suicideal ideation

direct.spdq <- ' #specifies the model
      # measurement model
     fear=~  pdsr_tot + ptsd_tot              
      dist=~ phq9_1to8 + gadqiv_tot + c*spdq_tot 
       int=~ b*fear + b*dist 
      # regression
  int ~ a*phq9_9
  dist ~ d*phq9_9
  spdq_tot ~ e*phq9_9 #this is the direct effect
      # indirect effect
  indirect := (a*b*c) + (d*c)
      # total effect
  total := e + (a*b*c) + (d*c)
'


fit.direct.spdq<- sem.mi(direct.spdq, 
                       miPackage="mice",
                      data = df, 
                      estimator="ML", 
                      std.lv = TRUE,
                      bootstrap = 500L)


summary(fit.direct.spdq, 
        fit.measures = TRUE,
        standardized = TRUE, 
        rsquare = TRUE) 


# Regress ptsd(and the first-order fear factor and second-order internalizing factor) on suicideal ideation

direct.ptsd <- ' #specifies the model
      # measurement model
     fear=~  pdsr_tot + c*ptsd_tot              
      dist=~ phq9_1to8 + gadqiv_tot + spdq_tot 
       int=~ b*fear + b*dist 
      # regression
  int ~ a*phq9_9
  dist ~ d*phq9_9 
  ptsd_tot ~ e*phq9_9 #this is the direct effect
      # indirect effect
  indirect := (a*b*c) + (d*c)
      # total effect
  total := e + (a*b*c) + (d*c)
'


fit.direct.ptsd<- sem.mi(direct.ptsd, 
                       miPackage="mice",
                      data = df, 
                      estimator="ML", 
                      std.lv = TRUE)
                      #bootstrap = 500L)


summary(fit.direct.ptsd, 
        fit.measures = TRUE,
        standardized = TRUE, 
        rsquare = TRUE) 

# Regress panic (and the first-order fear factor and second-order internalizing factor) on suicideal ideation

direct.pan <- ' #specifies the model
      # measurement model
   fear=~  c*pdsr_tot + ptsd_tot              
   dist=~ phq9_1to8 + gadqiv_tot + spdq_tot 
    int=~ b*fear + b*dist 
      # regression
  int ~ a*phq9_9
  fear ~ d*phq9_9
  pdsr_tot ~ e*phq9_9 #this is the direct effect
      # indirect effect
  indirect := (a*b*c) + (d*c)
      # total effect
  total := e + (a*b*c) + (d*c)
'

fit.direct.ptsd<- sem.mi(direct.pan, 
                       miPackage="mice",
                      data = df, 
                      estimator="ML", 
                      std.lv = TRUE,
                      bootstrap = 500L)


summary(fit.direct.ptsd, 
        fit.measures = TRUE,
        standardized = TRUE, 
        rsquare = TRUE) 



