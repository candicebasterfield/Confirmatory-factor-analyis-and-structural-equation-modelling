# Confirmatory factor analysis and structural equation modelling in R
#### **Overview**
In this project, I utilized a cross-sectional population survey of college students (N = 8,613 participants) to examine the extent to which broad psychopathology factors account for specific associations between emotional problems and suicideal ideation. Confirmatory factor models were estimated to identify the best structure of psychopathology and structural equation models were used to partition the variance between broad and specific effects.

#### Variables 
###### Predictors 
* Generalized Anxiety Disorder Questionnaire-IV ("gadqiv_tot")
* Social Phobia Diagnostic Questionnaire ("spdq_tot")
* Panic Disorder Self-Report ("pdsr_tot")
* Patient Health Questionnaire-9 ("phq9_1to8"; Probable major depression)
* Primary Care PTSD Screen ("ptsd_tot")
###### Outcome
* Suicidal ideation ("phq9_9")

#### Analysis 
The first stage involved constructing the broad internalizing latent dimensions from observered symptom measures using using confirmatory factor analysis (CFA). In factor-analytics language, this involves generating the measurement model.

The second step in the analysis involved regressing psychopathology variables on suicideal ideation. To answer questions about potential pathways across multiple levels of the dimensional hierarchy, total, direct, and indirect effects of suicideal ideation on psychopathology outcomes were estimated (i.e., symptoms of depression, generalized anxiety disorder, panic, social anxiety, and obsessive compulsive disorder; distress and fear; and internalizing) one at a time.

Multiple imputation utilized the MICE package (van Buuren & Groothuis-Oudshoorn, 2011). Data were imputed with 10 iterations for each analysis and used predictive mean matching to replace the missing values with plausible estimates.

