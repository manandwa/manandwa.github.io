---
title: Exploring NYC School Perceptions
date: 2020-07-29
tags: [NYC Schools, ]
excerpt: "Exploring School Perceptions in NYC"
mathjax: "true"
---

In this project I will analyze data from the New York City school
department survey to answer the following questions:

1.  Do student, teacher and parent perceptions of NYC school quality
    appear to be related to demographic and academic success metrics
    (such as socieoenomic factors and test scores)?

2.  Do students, teachers and parents have similar perceptions of NYC
    school quallity?

The data was collected in 2011 and is publicly available for access. The
data can be accessed
[here](https://data.cityofnewyork.us/Education/2011-NYC-School-Survey/mnz3-dyi8)

We will first load all the packages needed for analysis

``` r
library(readr)
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
library(stringr)
```

    ## Warning: package 'stringr' was built under R version 4.0.2

``` r
library(purrr)
library(tidyr)
library(ggplot2)
```

    ## Warning: package 'ggplot2' was built under R version 4.0.2

We will now import the data

``` r
combined <- read_csv("combined.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_double(),
    ##   DBN = col_character(),
    ##   school_name = col_character(),
    ##   boro = col_character()
    ## )

    ## See spec(...) for full column specifications.

``` r
survey <- read_tsv("masterfile11_gened_final.txt")
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_double(),
    ##   dbn = col_character(),
    ##   bn = col_character(),
    ##   schoolname = col_character(),
    ##   studentssurveyed = col_character(),
    ##   schooltype = col_character(),
    ##   p_q1 = col_logical(),
    ##   p_q3d = col_logical(),
    ##   p_q9 = col_logical(),
    ##   p_q10 = col_logical(),
    ##   p_q12aa = col_logical(),
    ##   p_q12ab = col_logical(),
    ##   p_q12ac = col_logical(),
    ##   p_q12ad = col_logical(),
    ##   p_q12ba = col_logical(),
    ##   p_q12bb = col_logical(),
    ##   p_q12bc = col_logical(),
    ##   p_q12bd = col_logical(),
    ##   t_q6m = col_logical(),
    ##   t_q9 = col_logical(),
    ##   t_q10a = col_logical()
    ##   # ... with 18 more columns
    ## )
    ## See spec(...) for full column specifications.

``` r
survey_d75 <- read_tsv("masterfile11_d75_final.txt")
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_double(),
    ##   dbn = col_character(),
    ##   bn = col_character(),
    ##   schoolname = col_character(),
    ##   studentssurveyed = col_character(),
    ##   schooltype = col_character(),
    ##   p_q5 = col_logical(),
    ##   p_q9 = col_logical(),
    ##   p_q13a = col_logical(),
    ##   p_q13b = col_logical(),
    ##   p_q13c = col_logical(),
    ##   p_q13d = col_logical(),
    ##   p_q14a = col_logical(),
    ##   p_q14b = col_logical(),
    ##   p_q14c = col_logical(),
    ##   p_q14d = col_logical(),
    ##   t_q11a = col_logical(),
    ##   t_q11b = col_logical(),
    ##   t_q14 = col_logical(),
    ##   t_q15a = col_logical(),
    ##   t_q15b = col_logical()
    ##   # ... with 14 more columns
    ## )
    ## See spec(...) for full column specifications.

We are only interested in high schools (the schooltype column is helpful
here) We will also use the `spec()` function get the full column names
from each dataframe

``` r
spec(combined)
```

    ## cols(
    ##   DBN = col_character(),
    ##   school_name = col_character(),
    ##   `Num of SAT Test Takers` = col_double(),
    ##   `SAT Critical Reading Avg. Score` = col_double(),
    ##   `SAT Math Avg. Score` = col_double(),
    ##   `SAT Writing Avg. Score` = col_double(),
    ##   avg_sat_score = col_double(),
    ##   `AP Test Takers` = col_double(),
    ##   `Total Exams Taken` = col_double(),
    ##   `Number of Exams with scores 3 4 or 5` = col_double(),
    ##   exams_per_student = col_double(),
    ##   high_score_percent = col_double(),
    ##   avg_class_size = col_double(),
    ##   frl_percent = col_double(),
    ##   total_enrollment = col_double(),
    ##   ell_percent = col_double(),
    ##   sped_percent = col_double(),
    ##   selfcontained_num = col_double(),
    ##   asian_per = col_double(),
    ##   black_per = col_double(),
    ##   hispanic_per = col_double(),
    ##   white_per = col_double(),
    ##   male_per = col_double(),
    ##   female_per = col_double(),
    ##   `Total Cohort` = col_double(),
    ##   grads_percent = col_double(),
    ##   dropout_percent = col_double(),
    ##   boro = col_character(),
    ##   lat = col_double(),
    ##   long = col_double()
    ## )

``` r
spec(survey_d75)
```

    ## cols(
    ##   dbn = col_character(),
    ##   bn = col_character(),
    ##   schoolname = col_character(),
    ##   d75 = col_double(),
    ##   studentssurveyed = col_character(),
    ##   highschool = col_double(),
    ##   schooltype = col_character(),
    ##   rr_s = col_double(),
    ##   rr_t = col_double(),
    ##   rr_p = col_double(),
    ##   N_s = col_double(),
    ##   N_t = col_double(),
    ##   N_p = col_double(),
    ##   nr_s = col_double(),
    ##   nr_t = col_double(),
    ##   nr_p = col_double(),
    ##   saf_p_11 = col_double(),
    ##   com_p_11 = col_double(),
    ##   eng_p_11 = col_double(),
    ##   aca_p_11 = col_double(),
    ##   saf_t_11 = col_double(),
    ##   com_t_11 = col_double(),
    ##   eng_t_11 = col_double(),
    ##   aca_t_11 = col_double(),
    ##   saf_s_11 = col_double(),
    ##   com_s_11 = col_double(),
    ##   eng_s_11 = col_double(),
    ##   aca_s_11 = col_double(),
    ##   saf_tot_11 = col_double(),
    ##   com_tot_11 = col_double(),
    ##   eng_tot_11 = col_double(),
    ##   aca_tot_11 = col_double(),
    ##   p_q1c = col_double(),
    ##   p_q10a = col_double(),
    ##   p_q10b = col_double(),
    ##   p_q10c = col_double(),
    ##   p_q10d = col_double(),
    ##   p_q10e = col_double(),
    ##   p_q10f = col_double(),
    ##   p_q11a = col_double(),
    ##   p_q11b = col_double(),
    ##   p_q11c = col_double(),
    ##   p_q11d = col_double(),
    ##   p_q11e = col_double(),
    ##   p_q1b = col_double(),
    ##   p_q1e = col_double(),
    ##   p_q1f = col_double(),
    ##   p_q2a = col_double(),
    ##   p_q2b = col_double(),
    ##   p_q3c = col_double(),
    ##   p_q3d = col_double(),
    ##   p_q4a = col_double(),
    ##   p_q6c = col_double(),
    ##   p_q12c = col_double(),
    ##   p_q1a = col_double(),
    ##   p_q1d = col_double(),
    ##   p_q3a = col_double(),
    ##   p_q3b = col_double(),
    ##   p_q3e = col_double(),
    ##   p_q4b = col_double(),
    ##   p_q4c = col_double(),
    ##   p_q6e = col_double(),
    ##   p_q7 = col_double(),
    ##   p_q8a = col_double(),
    ##   p_q8b = col_double(),
    ##   p_q12d = col_double(),
    ##   p_q1g = col_double(),
    ##   p_q6a = col_double(),
    ##   p_q6b = col_double(),
    ##   p_q6d = col_double(),
    ##   p_q6f = col_double(),
    ##   p_q6g = col_double(),
    ##   p_q6h = col_double(),
    ##   p_q12a = col_double(),
    ##   p_q12b = col_double(),
    ##   p_q12e = col_double(),
    ##   p_q12f = col_double(),
    ##   p_q5 = col_logical(),
    ##   p_q9 = col_logical(),
    ##   p_q13a = col_logical(),
    ##   p_q13b = col_logical(),
    ##   p_q13c = col_logical(),
    ##   p_q13d = col_logical(),
    ##   p_q14a = col_logical(),
    ##   p_q14b = col_logical(),
    ##   p_q14c = col_logical(),
    ##   p_q14d = col_logical(),
    ##   p_q1a_1 = col_double(),
    ##   p_q1a_2 = col_double(),
    ##   p_q1a_3 = col_double(),
    ##   p_q1a_4 = col_double(),
    ##   p_q1a_5 = col_double(),
    ##   p_q1b_1 = col_double(),
    ##   p_q1b_2 = col_double(),
    ##   p_q1b_3 = col_double(),
    ##   p_q1b_4 = col_double(),
    ##   p_q1b_5 = col_double(),
    ##   p_q1c_1 = col_double(),
    ##   p_q1c_2 = col_double(),
    ##   p_q1c_3 = col_double(),
    ##   p_q1c_4 = col_double(),
    ##   p_q1c_5 = col_double(),
    ##   p_q1d_1 = col_double(),
    ##   p_q1d_2 = col_double(),
    ##   p_q1d_3 = col_double(),
    ##   p_q1d_4 = col_double(),
    ##   p_q1d_5 = col_double(),
    ##   p_q1e_1 = col_double(),
    ##   p_q1e_2 = col_double(),
    ##   p_q1e_3 = col_double(),
    ##   p_q1e_4 = col_double(),
    ##   p_q1e_5 = col_double(),
    ##   p_q1f_1 = col_double(),
    ##   p_q1f_2 = col_double(),
    ##   p_q1f_3 = col_double(),
    ##   p_q1f_4 = col_double(),
    ##   p_q1f_5 = col_double(),
    ##   p_q1g_1 = col_double(),
    ##   p_q1g_2 = col_double(),
    ##   p_q1g_3 = col_double(),
    ##   p_q1g_4 = col_double(),
    ##   p_q1g_5 = col_double(),
    ##   p_q2a_1 = col_double(),
    ##   p_q2a_2 = col_double(),
    ##   p_q2a_3 = col_double(),
    ##   p_q2a_4 = col_double(),
    ##   p_q2a_5 = col_double(),
    ##   p_q2b_1 = col_double(),
    ##   p_q2b_2 = col_double(),
    ##   p_q2b_3 = col_double(),
    ##   p_q2b_4 = col_double(),
    ##   p_q2b_5 = col_double(),
    ##   p_q3a_1 = col_double(),
    ##   p_q3a_2 = col_double(),
    ##   p_q3a_3 = col_double(),
    ##   p_q3a_4 = col_double(),
    ##   p_q3a_5 = col_double(),
    ##   p_q3b_1 = col_double(),
    ##   p_q3b_2 = col_double(),
    ##   p_q3b_3 = col_double(),
    ##   p_q3b_4 = col_double(),
    ##   p_q3b_5 = col_double(),
    ##   p_q3c_1 = col_double(),
    ##   p_q3c_2 = col_double(),
    ##   p_q3c_3 = col_double(),
    ##   p_q3c_4 = col_double(),
    ##   p_q3c_5 = col_double(),
    ##   p_q3d_1 = col_double(),
    ##   p_q3d_2 = col_double(),
    ##   p_q3d_3 = col_double(),
    ##   p_q3d_4 = col_double(),
    ##   p_q3d_5 = col_double(),
    ##   p_q3e_1 = col_double(),
    ##   p_q3e_2 = col_double(),
    ##   p_q3e_3 = col_double(),
    ##   p_q3e_4 = col_double(),
    ##   p_q3e_5 = col_double(),
    ##   p_q4a_1 = col_double(),
    ##   p_q4a_2 = col_double(),
    ##   p_q4a_3 = col_double(),
    ##   p_q4a_4 = col_double(),
    ##   p_q4a_5 = col_double(),
    ##   p_q4b_1 = col_double(),
    ##   p_q4b_2 = col_double(),
    ##   p_q4b_3 = col_double(),
    ##   p_q4b_4 = col_double(),
    ##   p_q4b_5 = col_double(),
    ##   p_q4c_1 = col_double(),
    ##   p_q4c_2 = col_double(),
    ##   p_q4c_3 = col_double(),
    ##   p_q4c_4 = col_double(),
    ##   p_q4c_5 = col_double(),
    ##   p_q5a = col_double(),
    ##   p_q5b = col_double(),
    ##   p_q5c = col_double(),
    ##   p_q5d = col_double(),
    ##   p_q5e = col_double(),
    ##   p_q5f = col_double(),
    ##   p_q5g = col_double(),
    ##   p_q5h = col_double(),
    ##   p_q5i = col_double(),
    ##   p_q5j = col_double(),
    ##   p_q5k = col_double(),
    ##   p_q5l = col_double(),
    ##   p_q5m = col_double(),
    ##   p_q5n = col_double(),
    ##   p_q6a_1 = col_double(),
    ##   p_q6a_2 = col_double(),
    ##   p_q6a_3 = col_double(),
    ##   p_q6a_4 = col_double(),
    ##   p_q6a_5 = col_double(),
    ##   p_q6b_1 = col_double(),
    ##   p_q6b_2 = col_double(),
    ##   p_q6b_3 = col_double(),
    ##   p_q6b_4 = col_double(),
    ##   p_q6b_5 = col_double(),
    ##   p_q6c_1 = col_double(),
    ##   p_q6c_2 = col_double(),
    ##   p_q6c_3 = col_double(),
    ##   p_q6c_4 = col_double(),
    ##   p_q6c_5 = col_double(),
    ##   p_q6d_1 = col_double(),
    ##   p_q6d_2 = col_double(),
    ##   p_q6d_3 = col_double(),
    ##   p_q6d_4 = col_double(),
    ##   p_q6d_5 = col_double(),
    ##   p_q6e_1 = col_double(),
    ##   p_q6e_2 = col_double(),
    ##   p_q6e_3 = col_double(),
    ##   p_q6e_4 = col_double(),
    ##   p_q6e_5 = col_double(),
    ##   p_q6f_1 = col_double(),
    ##   p_q6f_2 = col_double(),
    ##   p_q6f_3 = col_double(),
    ##   p_q6f_4 = col_double(),
    ##   p_q6f_5 = col_double(),
    ##   p_q6g_1 = col_double(),
    ##   p_q6g_2 = col_double(),
    ##   p_q6g_3 = col_double(),
    ##   p_q6g_4 = col_double(),
    ##   p_q6g_5 = col_double(),
    ##   p_q6h_1 = col_double(),
    ##   p_q6h_2 = col_double(),
    ##   p_q6h_3 = col_double(),
    ##   p_q6h_4 = col_double(),
    ##   p_q6h_5 = col_double(),
    ##   p_q7a = col_double(),
    ##   p_q7b = col_double(),
    ##   p_q7c = col_double(),
    ##   p_q7d = col_double(),
    ##   p_q7e = col_double(),
    ##   p_q7f = col_double(),
    ##   p_q7g = col_double(),
    ##   p_q7h = col_double(),
    ##   p_q7i = col_double(),
    ##   p_q7j = col_double(),
    ##   p_q7k = col_double(),
    ##   p_q7l = col_double(),
    ##   p_q7m = col_double(),
    ##   p_q7n = col_double(),
    ##   p_q8a_1 = col_double(),
    ##   p_q8a_2 = col_double(),
    ##   p_q8a_3 = col_double(),
    ##   p_q8a_4 = col_double(),
    ##   p_q8a_5 = col_double(),
    ##   p_q8b_1 = col_double(),
    ##   p_q8b_2 = col_double(),
    ##   p_q8b_3 = col_double(),
    ##   p_q8b_4 = col_double(),
    ##   p_q8b_5 = col_double(),
    ##   p_q9_1 = col_double(),
    ##   p_q9_2 = col_double(),
    ##   p_q9_3 = col_double(),
    ##   p_q9_4 = col_double(),
    ##   p_q9_5 = col_double(),
    ##   p_q9_6 = col_double(),
    ##   p_q9_7 = col_double(),
    ##   p_q9_8 = col_double(),
    ##   p_q9_9 = col_double(),
    ##   p_q9_10 = col_double(),
    ##   p_q9_11 = col_double(),
    ##   p_q9_12 = col_double(),
    ##   p_q9_13 = col_double(),
    ##   p_q9_14 = col_double(),
    ##   p_q9_15 = col_double(),
    ##   p_q10a_1 = col_double(),
    ##   p_q10a_2 = col_double(),
    ##   p_q10a_3 = col_double(),
    ##   p_q10a_4 = col_double(),
    ##   p_q10a_5 = col_double(),
    ##   p_q10b_1 = col_double(),
    ##   p_q10b_2 = col_double(),
    ##   p_q10b_3 = col_double(),
    ##   p_q10b_4 = col_double(),
    ##   p_q10b_5 = col_double(),
    ##   p_q10c_1 = col_double(),
    ##   p_q10c_2 = col_double(),
    ##   p_q10c_3 = col_double(),
    ##   p_q10c_4 = col_double(),
    ##   p_q10c_5 = col_double(),
    ##   p_q10d_1 = col_double(),
    ##   p_q10d_2 = col_double(),
    ##   p_q10d_3 = col_double(),
    ##   p_q10d_4 = col_double(),
    ##   p_q10d_5 = col_double(),
    ##   p_q10e_1 = col_double(),
    ##   p_q10e_2 = col_double(),
    ##   p_q10e_3 = col_double(),
    ##   p_q10e_4 = col_double(),
    ##   p_q10e_5 = col_double(),
    ##   p_q10f_1 = col_double(),
    ##   p_q10f_2 = col_double(),
    ##   p_q10f_3 = col_double(),
    ##   p_q10f_4 = col_double(),
    ##   p_q10f_5 = col_double(),
    ##   p_q11a_1 = col_double(),
    ##   p_q11a_2 = col_double(),
    ##   p_q11a_3 = col_double(),
    ##   p_q11a_4 = col_double(),
    ##   p_q11a_5 = col_double(),
    ##   p_q11b_1 = col_double(),
    ##   p_q11b_2 = col_double(),
    ##   p_q11b_3 = col_double(),
    ##   p_q11b_4 = col_double(),
    ##   p_q11b_5 = col_double(),
    ##   p_q11c_1 = col_double(),
    ##   p_q11c_2 = col_double(),
    ##   p_q11c_3 = col_double(),
    ##   p_q11c_4 = col_double(),
    ##   p_q11c_5 = col_double(),
    ##   p_q11d_1 = col_double(),
    ##   p_q11d_2 = col_double(),
    ##   p_q11d_3 = col_double(),
    ##   p_q11d_4 = col_double(),
    ##   p_q11d_5 = col_double(),
    ##   p_q11e_1 = col_double(),
    ##   p_q11e_2 = col_double(),
    ##   p_q11e_3 = col_double(),
    ##   p_q11e_4 = col_double(),
    ##   p_q11e_5 = col_double(),
    ##   p_q12a_1 = col_double(),
    ##   p_q12a_2 = col_double(),
    ##   p_q12a_3 = col_double(),
    ##   p_q12a_4 = col_double(),
    ##   p_q12b_1 = col_double(),
    ##   p_q12b_2 = col_double(),
    ##   p_q12b_3 = col_double(),
    ##   p_q12b_4 = col_double(),
    ##   p_q12c_1 = col_double(),
    ##   p_q12c_2 = col_double(),
    ##   p_q12c_3 = col_double(),
    ##   p_q12c_4 = col_double(),
    ##   p_q12d_1 = col_double(),
    ##   p_q12d_2 = col_double(),
    ##   p_q12d_3 = col_double(),
    ##   p_q12d_4 = col_double(),
    ##   p_q12e_1 = col_double(),
    ##   p_q12e_2 = col_double(),
    ##   p_q12e_3 = col_double(),
    ##   p_q12e_4 = col_double(),
    ##   p_q12f_1 = col_double(),
    ##   p_q12f_2 = col_double(),
    ##   p_q12f_3 = col_double(),
    ##   p_q12f_4 = col_double(),
    ##   p_q13a_1 = col_double(),
    ##   p_q13a_2 = col_double(),
    ##   p_q13a_3 = col_double(),
    ##   p_q13a_4 = col_double(),
    ##   p_q13a_5 = col_double(),
    ##   p_q13b_1 = col_double(),
    ##   p_q13b_2 = col_double(),
    ##   p_q13b_3 = col_double(),
    ##   p_q13b_4 = col_double(),
    ##   p_q13b_5 = col_double(),
    ##   p_q13c_1 = col_double(),
    ##   p_q13c_2 = col_double(),
    ##   p_q13c_3 = col_double(),
    ##   p_q13c_4 = col_double(),
    ##   p_q13c_5 = col_double(),
    ##   p_q13d_1 = col_double(),
    ##   p_q13d_2 = col_double(),
    ##   p_q13d_3 = col_double(),
    ##   p_q13d_4 = col_double(),
    ##   p_q13d_5 = col_double(),
    ##   p_q14a_1 = col_double(),
    ##   p_q14a_2 = col_double(),
    ##   p_q14a_3 = col_double(),
    ##   p_q14a_4 = col_double(),
    ##   p_q14a_5 = col_double(),
    ##   p_q14b_1 = col_double(),
    ##   p_q14b_2 = col_double(),
    ##   p_q14b_3 = col_double(),
    ##   p_q14b_4 = col_double(),
    ##   p_q14b_5 = col_double(),
    ##   p_q14c_1 = col_double(),
    ##   p_q14c_2 = col_double(),
    ##   p_q14c_3 = col_double(),
    ##   p_q14c_4 = col_double(),
    ##   p_q14c_5 = col_double(),
    ##   p_q14d_1 = col_double(),
    ##   p_q14d_2 = col_double(),
    ##   p_q14d_3 = col_double(),
    ##   p_q14d_4 = col_double(),
    ##   p_q14d_5 = col_double(),
    ##   p_N_q1a_1 = col_double(),
    ##   p_N_q1a_2 = col_double(),
    ##   p_N_q1a_3 = col_double(),
    ##   p_N_q1a_4 = col_double(),
    ##   p_N_q1a_5 = col_double(),
    ##   p_N_q1b_1 = col_double(),
    ##   p_N_q1b_2 = col_double(),
    ##   p_N_q1b_3 = col_double(),
    ##   p_N_q1b_4 = col_double(),
    ##   p_N_q1b_5 = col_double(),
    ##   p_N_q1c_1 = col_double(),
    ##   p_N_q1c_2 = col_double(),
    ##   p_N_q1c_3 = col_double(),
    ##   p_N_q1c_4 = col_double(),
    ##   p_N_q1c_5 = col_double(),
    ##   p_N_q1d_1 = col_double(),
    ##   p_N_q1d_2 = col_double(),
    ##   p_N_q1d_3 = col_double(),
    ##   p_N_q1d_4 = col_double(),
    ##   p_N_q1d_5 = col_double(),
    ##   p_N_q1e_1 = col_double(),
    ##   p_N_q1e_2 = col_double(),
    ##   p_N_q1e_3 = col_double(),
    ##   p_N_q1e_4 = col_double(),
    ##   p_N_q1e_5 = col_double(),
    ##   p_N_q1f_1 = col_double(),
    ##   p_N_q1f_2 = col_double(),
    ##   p_N_q1f_3 = col_double(),
    ##   p_N_q1f_4 = col_double(),
    ##   p_N_q1f_5 = col_double(),
    ##   p_N_q1g_1 = col_double(),
    ##   p_N_q1g_2 = col_double(),
    ##   p_N_q1g_3 = col_double(),
    ##   p_N_q1g_4 = col_double(),
    ##   p_N_q1g_5 = col_double(),
    ##   p_N_q2a_1 = col_double(),
    ##   p_N_q2a_2 = col_double(),
    ##   p_N_q2a_3 = col_double(),
    ##   p_N_q2a_4 = col_double(),
    ##   p_N_q2a_5 = col_double(),
    ##   p_N_q2b_1 = col_double(),
    ##   p_N_q2b_2 = col_double(),
    ##   p_N_q2b_3 = col_double(),
    ##   p_N_q2b_4 = col_double(),
    ##   p_N_q2b_5 = col_double(),
    ##   p_N_q3a_1 = col_double(),
    ##   p_N_q3a_2 = col_double(),
    ##   p_N_q3a_3 = col_double(),
    ##   p_N_q3a_4 = col_double(),
    ##   p_N_q3a_5 = col_double(),
    ##   p_N_q3b_1 = col_double(),
    ##   p_N_q3b_2 = col_double(),
    ##   p_N_q3b_3 = col_double(),
    ##   p_N_q3b_4 = col_double(),
    ##   p_N_q3b_5 = col_double(),
    ##   p_N_q3c_1 = col_double(),
    ##   p_N_q3c_2 = col_double(),
    ##   p_N_q3c_3 = col_double(),
    ##   p_N_q3c_4 = col_double(),
    ##   p_N_q3c_5 = col_double(),
    ##   p_N_q3d_1 = col_double(),
    ##   p_N_q3d_2 = col_double(),
    ##   p_N_q3d_3 = col_double(),
    ##   p_N_q3d_4 = col_double(),
    ##   p_N_q3d_5 = col_double(),
    ##   p_N_q3e_1 = col_double(),
    ##   p_N_q3e_2 = col_double(),
    ##   p_N_q3e_3 = col_double(),
    ##   p_N_q3e_4 = col_double(),
    ##   p_N_q3e_5 = col_double(),
    ##   p_N_q4a_1 = col_double(),
    ##   p_N_q4a_2 = col_double(),
    ##   p_N_q4a_3 = col_double(),
    ##   p_N_q4a_4 = col_double(),
    ##   p_N_q4a_5 = col_double(),
    ##   p_N_q4b_1 = col_double(),
    ##   p_N_q4b_2 = col_double(),
    ##   p_N_q4b_3 = col_double(),
    ##   p_N_q4b_4 = col_double(),
    ##   p_N_q4b_5 = col_double(),
    ##   p_N_q4c_1 = col_double(),
    ##   p_N_q4c_2 = col_double(),
    ##   p_N_q4c_3 = col_double(),
    ##   p_N_q4c_4 = col_double(),
    ##   p_N_q4c_5 = col_double(),
    ##   p_N_q5a = col_double(),
    ##   p_N_q5b = col_double(),
    ##   p_N_q5c = col_double(),
    ##   p_N_q5d = col_double(),
    ##   p_N_q5e = col_double(),
    ##   p_N_q5f = col_double(),
    ##   p_N_q5g = col_double(),
    ##   p_N_q5h = col_double(),
    ##   p_N_q5i = col_double(),
    ##   p_N_q5j = col_double(),
    ##   p_N_q5k = col_double(),
    ##   p_N_q5l = col_double(),
    ##   p_N_q5m = col_double(),
    ##   p_N_q5n = col_double(),
    ##   p_N_q6a_1 = col_double(),
    ##   p_N_q6a_2 = col_double(),
    ##   p_N_q6a_3 = col_double(),
    ##   p_N_q6a_4 = col_double(),
    ##   p_N_q6a_5 = col_double(),
    ##   p_N_q6b_1 = col_double(),
    ##   p_N_q6b_2 = col_double(),
    ##   p_N_q6b_3 = col_double(),
    ##   p_N_q6b_4 = col_double(),
    ##   p_N_q6b_5 = col_double(),
    ##   p_N_q6c_1 = col_double(),
    ##   p_N_q6c_2 = col_double(),
    ##   p_N_q6c_3 = col_double(),
    ##   p_N_q6c_4 = col_double(),
    ##   p_N_q6c_5 = col_double(),
    ##   p_N_q6d_1 = col_double(),
    ##   p_N_q6d_2 = col_double(),
    ##   p_N_q6d_3 = col_double(),
    ##   p_N_q6d_4 = col_double(),
    ##   p_N_q6d_5 = col_double(),
    ##   p_N_q6e_1 = col_double(),
    ##   p_N_q6e_2 = col_double(),
    ##   p_N_q6e_3 = col_double(),
    ##   p_N_q6e_4 = col_double(),
    ##   p_N_q6e_5 = col_double(),
    ##   p_N_q6f_1 = col_double(),
    ##   p_N_q6f_2 = col_double(),
    ##   p_N_q6f_3 = col_double(),
    ##   p_N_q6f_4 = col_double(),
    ##   p_N_q6f_5 = col_double(),
    ##   p_N_q6g_1 = col_double(),
    ##   p_N_q6g_2 = col_double(),
    ##   p_N_q6g_3 = col_double(),
    ##   p_N_q6g_4 = col_double(),
    ##   p_N_q6g_5 = col_double(),
    ##   p_N_q6h_1 = col_double(),
    ##   p_N_q6h_2 = col_double(),
    ##   p_N_q6h_3 = col_double(),
    ##   p_N_q6h_4 = col_double(),
    ##   p_N_q6h_5 = col_double(),
    ##   p_N_q7a = col_double(),
    ##   p_N_q7b = col_double(),
    ##   p_N_q7c = col_double(),
    ##   p_N_q7d = col_double(),
    ##   p_N_q7e = col_double(),
    ##   p_N_q7f = col_double(),
    ##   p_N_q7g = col_double(),
    ##   p_N_q7h = col_double(),
    ##   p_N_q7i = col_double(),
    ##   p_N_q7j = col_double(),
    ##   p_N_q7k = col_double(),
    ##   p_N_q7l = col_double(),
    ##   p_N_q7m = col_double(),
    ##   p_N_q7n = col_double(),
    ##   p_N_q8a_1 = col_double(),
    ##   p_N_q8a_2 = col_double(),
    ##   p_N_q8a_3 = col_double(),
    ##   p_N_q8a_4 = col_double(),
    ##   p_N_q8a_5 = col_double(),
    ##   p_N_q8b_1 = col_double(),
    ##   p_N_q8b_2 = col_double(),
    ##   p_N_q8b_3 = col_double(),
    ##   p_N_q8b_4 = col_double(),
    ##   p_N_q8b_5 = col_double(),
    ##   p_N_q9_1 = col_double(),
    ##   p_N_q9_2 = col_double(),
    ##   p_N_q9_3 = col_double(),
    ##   p_N_q9_4 = col_double(),
    ##   p_N_q9_5 = col_double(),
    ##   p_N_q9_6 = col_double(),
    ##   p_N_q9_7 = col_double(),
    ##   p_N_q9_8 = col_double(),
    ##   p_N_q9_9 = col_double(),
    ##   p_N_q9_10 = col_double(),
    ##   p_N_q9_11 = col_double(),
    ##   p_N_q9_12 = col_double(),
    ##   p_N_q9_13 = col_double(),
    ##   p_N_q9_14 = col_double(),
    ##   p_N_q9_15 = col_double(),
    ##   p_N_q10a_1 = col_double(),
    ##   p_N_q10a_2 = col_double(),
    ##   p_N_q10a_3 = col_double(),
    ##   p_N_q10a_4 = col_double(),
    ##   p_N_q10a_5 = col_double(),
    ##   p_N_q10b_1 = col_double(),
    ##   p_N_q10b_2 = col_double(),
    ##   p_N_q10b_3 = col_double(),
    ##   p_N_q10b_4 = col_double(),
    ##   p_N_q10b_5 = col_double(),
    ##   p_N_q10c_1 = col_double(),
    ##   p_N_q10c_2 = col_double(),
    ##   p_N_q10c_3 = col_double(),
    ##   p_N_q10c_4 = col_double(),
    ##   p_N_q10c_5 = col_double(),
    ##   p_N_q10d_1 = col_double(),
    ##   p_N_q10d_2 = col_double(),
    ##   p_N_q10d_3 = col_double(),
    ##   p_N_q10d_4 = col_double(),
    ##   p_N_q10d_5 = col_double(),
    ##   p_N_q10e_1 = col_double(),
    ##   p_N_q10e_2 = col_double(),
    ##   p_N_q10e_3 = col_double(),
    ##   p_N_q10e_4 = col_double(),
    ##   p_N_q10e_5 = col_double(),
    ##   p_N_q10f_1 = col_double(),
    ##   p_N_q10f_2 = col_double(),
    ##   p_N_q10f_3 = col_double(),
    ##   p_N_q10f_4 = col_double(),
    ##   p_N_q10f_5 = col_double(),
    ##   p_N_q11a_1 = col_double(),
    ##   p_N_q11a_2 = col_double(),
    ##   p_N_q11a_3 = col_double(),
    ##   p_N_q11a_4 = col_double(),
    ##   p_N_q11a_5 = col_double(),
    ##   p_N_q11b_1 = col_double(),
    ##   p_N_q11b_2 = col_double(),
    ##   p_N_q11b_3 = col_double(),
    ##   p_N_q11b_4 = col_double(),
    ##   p_N_q11b_5 = col_double(),
    ##   p_N_q11c_1 = col_double(),
    ##   p_N_q11c_2 = col_double(),
    ##   p_N_q11c_3 = col_double(),
    ##   p_N_q11c_4 = col_double(),
    ##   p_N_q11c_5 = col_double(),
    ##   p_N_q11d_1 = col_double(),
    ##   p_N_q11d_2 = col_double(),
    ##   p_N_q11d_3 = col_double(),
    ##   p_N_q11d_4 = col_double(),
    ##   p_N_q11d_5 = col_double(),
    ##   p_N_q11e_1 = col_double(),
    ##   p_N_q11e_2 = col_double(),
    ##   p_N_q11e_3 = col_double(),
    ##   p_N_q11e_4 = col_double(),
    ##   p_N_q11e_5 = col_double(),
    ##   p_N_q12a_1 = col_double(),
    ##   p_N_q12a_2 = col_double(),
    ##   p_N_q12a_3 = col_double(),
    ##   p_N_q12a_4 = col_double(),
    ##   p_N_q12b_1 = col_double(),
    ##   p_N_q12b_2 = col_double(),
    ##   p_N_q12b_3 = col_double(),
    ##   p_N_q12b_4 = col_double(),
    ##   p_N_q12c_1 = col_double(),
    ##   p_N_q12c_2 = col_double(),
    ##   p_N_q12c_3 = col_double(),
    ##   p_N_q12c_4 = col_double(),
    ##   p_N_q12d_1 = col_double(),
    ##   p_N_q12d_2 = col_double(),
    ##   p_N_q12d_3 = col_double(),
    ##   p_N_q12d_4 = col_double(),
    ##   p_N_q12e_1 = col_double(),
    ##   p_N_q12e_2 = col_double(),
    ##   p_N_q12e_3 = col_double(),
    ##   p_N_q12e_4 = col_double(),
    ##   p_N_q12f_1 = col_double(),
    ##   p_N_q12f_2 = col_double(),
    ##   p_N_q12f_3 = col_double(),
    ##   p_N_q12f_4 = col_double(),
    ##   p_N_q13a_1 = col_double(),
    ##   p_N_q13a_2 = col_double(),
    ##   p_N_q13a_3 = col_double(),
    ##   p_N_q13a_4 = col_double(),
    ##   p_N_q13a_5 = col_double(),
    ##   p_N_q13b_1 = col_double(),
    ##   p_N_q13b_2 = col_double(),
    ##   p_N_q13b_3 = col_double(),
    ##   p_N_q13b_4 = col_double(),
    ##   p_N_q13b_5 = col_double(),
    ##   p_N_q13c_1 = col_double(),
    ##   p_N_q13c_2 = col_double(),
    ##   p_N_q13c_3 = col_double(),
    ##   p_N_q13c_4 = col_double(),
    ##   p_N_q13c_5 = col_double(),
    ##   p_N_q13d_1 = col_double(),
    ##   p_N_q13d_2 = col_double(),
    ##   p_N_q13d_3 = col_double(),
    ##   p_N_q13d_4 = col_double(),
    ##   p_N_q13d_5 = col_double(),
    ##   p_N_q14a_1 = col_double(),
    ##   p_N_q14a_2 = col_double(),
    ##   p_N_q14a_3 = col_double(),
    ##   p_N_q14a_4 = col_double(),
    ##   p_N_q14a_5 = col_double(),
    ##   p_N_q14b_1 = col_double(),
    ##   p_N_q14b_2 = col_double(),
    ##   p_N_q14b_3 = col_double(),
    ##   p_N_q14b_4 = col_double(),
    ##   p_N_q14b_5 = col_double(),
    ##   p_N_q14c_1 = col_double(),
    ##   p_N_q14c_2 = col_double(),
    ##   p_N_q14c_3 = col_double(),
    ##   p_N_q14c_4 = col_double(),
    ##   p_N_q14c_5 = col_double(),
    ##   p_N_q14d_1 = col_double(),
    ##   p_N_q14d_2 = col_double(),
    ##   p_N_q14d_3 = col_double(),
    ##   p_N_q14d_4 = col_double(),
    ##   p_N_q14d_5 = col_double(),
    ##   t_q1g = col_double(),
    ##   t_q6d = col_double(),
    ##   t_q6e = col_double(),
    ##   t_q6f = col_double(),
    ##   t_q13a = col_double(),
    ##   t_q13b = col_double(),
    ##   t_q13c = col_double(),
    ##   t_q13d = col_double(),
    ##   t_q13e = col_double(),
    ##   t_q13f = col_double(),
    ##   t_q13g = col_double(),
    ##   t_q13h = col_double(),
    ##   t_q13i = col_double(),
    ##   t_q13j = col_double(),
    ##   t_q13k = col_double(),
    ##   t_q13l = col_double(),
    ##   t_q13m = col_double(),
    ##   t_q13n = col_double(),
    ##   t_q1a = col_double(),
    ##   t_q1b = col_double(),
    ##   t_q1c = col_double(),
    ##   t_q1f = col_double(),
    ##   t_q6h = col_double(),
    ##   t_q8c = col_double(),
    ##   t_q11c = col_double(),
    ##   t_q11d = col_double(),
    ##   t_q12 = col_double(),
    ##   t_q3_1 = col_double(),
    ##   t_q3_2 = col_double(),
    ##   t_q4 = col_double(),
    ##   t_q5a = col_double(),
    ##   t_q5b = col_double(),
    ##   t_q6b = col_double(),
    ##   t_q6c = col_double(),
    ##   t_q8a = col_double(),
    ##   t_q8b = col_double(),
    ##   t_q9 = col_double(),
    ##   t_q10 = col_double(),
    ##   t_q1d = col_double(),
    ##   t_q1e = col_double(),
    ##   t_q2a = col_double(),
    ##   t_q2b = col_double(),
    ##   t_q2c = col_double(),
    ##   t_q2d = col_double(),
    ##   t_q2e = col_double(),
    ##   t_q2f = col_double(),
    ##   t_q6a = col_double(),
    ##   t_q6g = col_double(),
    ##   t_q6i = col_double(),
    ##   t_q6j = col_double(),
    ##   t_q6k = col_double(),
    ##   t_q7a = col_double(),
    ##   t_q7b = col_double(),
    ##   t_q7c = col_double(),
    ##   t_q7d = col_double(),
    ##   t_q7e = col_double(),
    ##   t_q7f = col_double(),
    ##   t_q11a = col_logical(),
    ##   t_q11b = col_logical(),
    ##   t_q14 = col_logical(),
    ##   t_q15a = col_logical(),
    ##   t_q15b = col_logical(),
    ##   t_q15c = col_logical(),
    ##   t_q15d = col_logical(),
    ##   t_q16a = col_logical(),
    ##   t_q16b = col_logical(),
    ##   t_q16c = col_logical(),
    ##   t_q16d = col_logical(),
    ##   t_q17a = col_logical(),
    ##   t_q17b = col_logical(),
    ##   t_q17c = col_logical(),
    ##   t_q17d = col_logical(),
    ##   t_q17e = col_logical(),
    ##   t_q17f = col_logical(),
    ##   t_q1a_1 = col_double(),
    ##   t_q1a_2 = col_double(),
    ##   t_q1a_3 = col_double(),
    ##   t_q1a_4 = col_double(),
    ##   t_q1b_1 = col_double(),
    ##   t_q1b_2 = col_double(),
    ##   t_q1b_3 = col_double(),
    ##   t_q1b_4 = col_double(),
    ##   t_q1c_1 = col_double(),
    ##   t_q1c_2 = col_double(),
    ##   t_q1c_3 = col_double(),
    ##   t_q1c_4 = col_double(),
    ##   t_q1d_1 = col_double(),
    ##   t_q1d_2 = col_double(),
    ##   t_q1d_3 = col_double(),
    ##   t_q1d_4 = col_double(),
    ##   t_q1e_1 = col_double(),
    ##   t_q1e_2 = col_double(),
    ##   t_q1e_3 = col_double(),
    ##   t_q1e_4 = col_double(),
    ##   t_q1f_1 = col_double(),
    ##   t_q1f_2 = col_double(),
    ##   t_q1f_3 = col_double(),
    ##   t_q1f_4 = col_double(),
    ##   t_q1g_1 = col_double(),
    ##   t_q1g_2 = col_double(),
    ##   t_q1g_3 = col_double(),
    ##   t_q1g_4 = col_double(),
    ##   t_q2a_1 = col_double(),
    ##   t_q2a_2 = col_double(),
    ##   t_q2a_3 = col_double(),
    ##   t_q2a_4 = col_double(),
    ##   t_q2b_1 = col_double(),
    ##   t_q2b_2 = col_double(),
    ##   t_q2b_3 = col_double(),
    ##   t_q2b_4 = col_double(),
    ##   t_q2c_1 = col_double(),
    ##   t_q2c_2 = col_double(),
    ##   t_q2c_3 = col_double(),
    ##   t_q2c_4 = col_double(),
    ##   t_q2d_1 = col_double(),
    ##   t_q2d_2 = col_double(),
    ##   t_q2d_3 = col_double(),
    ##   t_q2d_4 = col_double(),
    ##   t_q2e_1 = col_double(),
    ##   t_q2e_2 = col_double(),
    ##   t_q2e_3 = col_double(),
    ##   t_q2e_4 = col_double(),
    ##   t_q2f_1 = col_double(),
    ##   t_q2f_2 = col_double(),
    ##   t_q2f_3 = col_double(),
    ##   t_q2f_4 = col_double(),
    ##   t_q3a_1 = col_double(),
    ##   t_q3a_2 = col_double(),
    ##   t_q3a_3 = col_double(),
    ##   t_q3b_1 = col_double(),
    ##   t_q3b_2 = col_double(),
    ##   t_q3b_3 = col_double(),
    ##   t_q3c_1 = col_double(),
    ##   t_q3c_2 = col_double(),
    ##   t_q3c_3 = col_double(),
    ##   t_q3d_1 = col_double(),
    ##   t_q3d_2 = col_double(),
    ##   t_q3d_3 = col_double(),
    ##   t_q3e_1 = col_double(),
    ##   t_q3e_2 = col_double(),
    ##   t_q3e_3 = col_double(),
    ##   t_q3f_1 = col_double(),
    ##   t_q3f_2 = col_double(),
    ##   t_q3f_3 = col_double(),
    ##   t_q3g_1 = col_double(),
    ##   t_q3g_2 = col_double(),
    ##   t_q3g_3 = col_double(),
    ##   t_q3h_1 = col_double(),
    ##   t_q3h_2 = col_double(),
    ##   t_q3h_3 = col_double(),
    ##   t_q3i_1 = col_double(),
    ##   t_q3i_2 = col_double(),
    ##   t_q3i_3 = col_double(),
    ##   t_q3j_1 = col_double(),
    ##   t_q3j_2 = col_double(),
    ##   t_q3j_3 = col_double(),
    ##   t_q3k_1 = col_double(),
    ##   t_q3k_2 = col_double(),
    ##   t_q3k_3 = col_double(),
    ##   t_q3l_1 = col_double(),
    ##   t_q3l_2 = col_double(),
    ##   t_q3l_3 = col_double(),
    ##   t_q4_1 = col_double(),
    ##   t_q4_2 = col_double(),
    ##   t_q4_3 = col_double(),
    ##   t_q4_4 = col_double(),
    ##   t_q5a_1 = col_double(),
    ##   t_q5a_2 = col_double(),
    ##   t_q5a_3 = col_double(),
    ##   t_q5a_4 = col_double(),
    ##   t_q5b_1 = col_double(),
    ##   t_q5b_2 = col_double(),
    ##   t_q5b_3 = col_double(),
    ##   t_q5b_4 = col_double(),
    ##   t_q6a_1 = col_double(),
    ##   t_q6a_2 = col_double(),
    ##   t_q6a_3 = col_double(),
    ##   t_q6a_4 = col_double(),
    ##   t_q6b_1 = col_double(),
    ##   t_q6b_2 = col_double(),
    ##   t_q6b_3 = col_double(),
    ##   t_q6b_4 = col_double(),
    ##   t_q6c_1 = col_double(),
    ##   t_q6c_2 = col_double(),
    ##   t_q6c_3 = col_double(),
    ##   t_q6c_4 = col_double(),
    ##   t_q6d_1 = col_double(),
    ##   t_q6d_2 = col_double(),
    ##   t_q6d_3 = col_double(),
    ##   t_q6d_4 = col_double(),
    ##   t_q6e_1 = col_double(),
    ##   t_q6e_2 = col_double(),
    ##   t_q6e_3 = col_double(),
    ##   t_q6e_4 = col_double(),
    ##   t_q6f_1 = col_double(),
    ##   t_q6f_2 = col_double(),
    ##   t_q6f_3 = col_double(),
    ##   t_q6f_4 = col_double(),
    ##   t_q6g_1 = col_double(),
    ##   t_q6g_2 = col_double(),
    ##   t_q6g_3 = col_double(),
    ##   t_q6g_4 = col_double(),
    ##   t_q6h_1 = col_double(),
    ##   t_q6h_2 = col_double(),
    ##   t_q6h_3 = col_double(),
    ##   t_q6h_4 = col_double(),
    ##   t_q6i_1 = col_double(),
    ##   t_q6i_2 = col_double(),
    ##   t_q6i_3 = col_double(),
    ##   t_q6i_4 = col_double(),
    ##   t_q6j_1 = col_double(),
    ##   t_q6j_2 = col_double(),
    ##   t_q6j_3 = col_double(),
    ##   t_q6j_4 = col_double(),
    ##   t_q6k_1 = col_double(),
    ##   t_q6k_2 = col_double(),
    ##   t_q6k_3 = col_double(),
    ##   t_q6k_4 = col_double(),
    ##   t_q7a_1 = col_double(),
    ##   t_q7a_2 = col_double(),
    ##   t_q7a_3 = col_double(),
    ##   t_q7a_4 = col_double(),
    ##   t_q7a_5 = col_double(),
    ##   t_q7b_1 = col_double(),
    ##   t_q7b_2 = col_double(),
    ##   t_q7b_3 = col_double(),
    ##   t_q7b_4 = col_double(),
    ##   t_q7b_5 = col_double(),
    ##   t_q7c_1 = col_double(),
    ##   t_q7c_2 = col_double(),
    ##   t_q7c_3 = col_double(),
    ##   t_q7c_4 = col_double(),
    ##   t_q7c_5 = col_double(),
    ##   t_q7d_1 = col_double(),
    ##   t_q7d_2 = col_double(),
    ##   t_q7d_3 = col_double(),
    ##   t_q7d_4 = col_double(),
    ##   t_q7d_5 = col_double(),
    ##   t_q7e_1 = col_double(),
    ##   t_q7e_2 = col_double(),
    ##   t_q7e_3 = col_double(),
    ##   t_q7e_4 = col_double(),
    ##   t_q7e_5 = col_double(),
    ##   t_q7f_1 = col_double(),
    ##   t_q7f_2 = col_double(),
    ##   t_q7f_3 = col_double(),
    ##   t_q7f_4 = col_double(),
    ##   t_q7f_5 = col_double(),
    ##   t_q8a_1 = col_double(),
    ##   t_q8a_2 = col_double(),
    ##   t_q8a_3 = col_double(),
    ##   t_q8a_4 = col_double(),
    ##   t_q8b_1 = col_double(),
    ##   t_q8b_2 = col_double(),
    ##   t_q8b_3 = col_double(),
    ##   t_q8b_4 = col_double(),
    ##   t_q8c_1 = col_double(),
    ##   t_q8c_2 = col_double(),
    ##   t_q8c_3 = col_double(),
    ##   t_q8c_4 = col_double(),
    ##   t_q9_1 = col_double(),
    ##   t_q9_2 = col_double(),
    ##   t_q9_3 = col_double(),
    ##   t_q9_4 = col_double(),
    ##   t_q9_5 = col_double(),
    ##   t_q10_1 = col_double(),
    ##   t_q10_2 = col_double(),
    ##   t_q10_3 = col_double(),
    ##   t_q10_4 = col_double(),
    ##   t_q10_5 = col_double(),
    ##   t_q11a_1 = col_double(),
    ##   t_q11a_2 = col_double(),
    ##   t_q11a_3 = col_double(),
    ##   t_q11a_4 = col_double(),
    ##   t_q11a_5 = col_double(),
    ##   t_q11b_1 = col_double(),
    ##   t_q11b_2 = col_double(),
    ##   t_q11b_3 = col_double(),
    ##   t_q11b_4 = col_double(),
    ##   t_q11b_5 = col_double(),
    ##   t_q11c_1 = col_double(),
    ##   t_q11c_2 = col_double(),
    ##   t_q11c_3 = col_double(),
    ##   t_q11c_4 = col_double(),
    ##   t_q11c_5 = col_double(),
    ##   t_q11d_1 = col_double(),
    ##   t_q11d_2 = col_double(),
    ##   t_q11d_3 = col_double(),
    ##   t_q11d_4 = col_double(),
    ##   t_q11d_5 = col_double(),
    ##   t_q12_1 = col_double(),
    ##   t_q12_2 = col_double(),
    ##   t_q12_3 = col_double(),
    ##   t_q12_4 = col_double(),
    ##   t_q12_5 = col_double(),
    ##   t_q12_6 = col_double(),
    ##   t_q13a_1 = col_double(),
    ##   t_q13a_2 = col_double(),
    ##   t_q13a_3 = col_double(),
    ##   t_q13a_4 = col_double(),
    ##   t_q13b_1 = col_double(),
    ##   t_q13b_2 = col_double(),
    ##   t_q13b_3 = col_double(),
    ##   t_q13b_4 = col_double(),
    ##   t_q13c_1 = col_double(),
    ##   t_q13c_2 = col_double(),
    ##   t_q13c_3 = col_double(),
    ##   t_q13c_4 = col_double(),
    ##   t_q13d_1 = col_double(),
    ##   t_q13d_2 = col_double(),
    ##   t_q13d_3 = col_double(),
    ##   t_q13d_4 = col_double(),
    ##   t_q13e_1 = col_double(),
    ##   t_q13e_2 = col_double(),
    ##   t_q13e_3 = col_double(),
    ##   t_q13e_4 = col_double(),
    ##   t_q13f_1 = col_double(),
    ##   t_q13f_2 = col_double(),
    ##   t_q13f_3 = col_double(),
    ##   t_q13f_4 = col_double(),
    ##   t_q13g_1 = col_double(),
    ##   t_q13g_2 = col_double(),
    ##   t_q13g_3 = col_double(),
    ##   t_q13g_4 = col_double(),
    ##   t_q13h_1 = col_double(),
    ##   t_q13h_2 = col_double(),
    ##   t_q13h_3 = col_double(),
    ##   t_q13h_4 = col_double(),
    ##   t_q13i_1 = col_double(),
    ##   t_q13i_2 = col_double(),
    ##   t_q13i_3 = col_double(),
    ##   t_q13i_4 = col_double(),
    ##   t_q13j_1 = col_double(),
    ##   t_q13j_2 = col_double(),
    ##   t_q13j_3 = col_double(),
    ##   t_q13j_4 = col_double(),
    ##   t_q13k_1 = col_double(),
    ##   t_q13k_2 = col_double(),
    ##   t_q13k_3 = col_double(),
    ##   t_q13k_4 = col_double(),
    ##   t_q13l_1 = col_double(),
    ##   t_q13l_2 = col_double(),
    ##   t_q13l_3 = col_double(),
    ##   t_q13l_4 = col_double(),
    ##   t_q13m_1 = col_double(),
    ##   t_q13m_2 = col_double(),
    ##   t_q13m_3 = col_double(),
    ##   t_q13m_4 = col_double(),
    ##   t_q13n_1 = col_double(),
    ##   t_q13n_2 = col_double(),
    ##   t_q13n_3 = col_double(),
    ##   t_q13n_4 = col_double(),
    ##   t_q14_1 = col_double(),
    ##   t_q14_2 = col_double(),
    ##   t_q14_3 = col_double(),
    ##   t_q14_4 = col_double(),
    ##   t_q14_5 = col_double(),
    ##   t_q14_6 = col_double(),
    ##   t_q15a_1 = col_double(),
    ##   t_q15a_2 = col_double(),
    ##   t_q15a_3 = col_double(),
    ##   t_q15a_4 = col_double(),
    ##   t_q15a_5 = col_double(),
    ##   t_q15b_1 = col_double(),
    ##   t_q15b_2 = col_double(),
    ##   t_q15b_3 = col_double(),
    ##   t_q15b_4 = col_double(),
    ##   t_q15b_5 = col_double(),
    ##   t_q15c_1 = col_double(),
    ##   t_q15c_2 = col_double(),
    ##   t_q15c_3 = col_double(),
    ##   t_q15c_4 = col_double(),
    ##   t_q15c_5 = col_double(),
    ##   t_q15d_1 = col_double(),
    ##   t_q15d_2 = col_double(),
    ##   t_q15d_3 = col_double(),
    ##   t_q15d_4 = col_double(),
    ##   t_q15d_5 = col_double(),
    ##   t_q16a_1 = col_double(),
    ##   t_q16a_2 = col_double(),
    ##   t_q16a_3 = col_double(),
    ##   t_q16a_4 = col_double(),
    ##   t_q16a_5 = col_double(),
    ##   t_q16b_1 = col_double(),
    ##   t_q16b_2 = col_double(),
    ##   t_q16b_3 = col_double(),
    ##   t_q16b_4 = col_double(),
    ##   t_q16b_5 = col_double(),
    ##   t_q16c_1 = col_double(),
    ##   t_q16c_2 = col_double(),
    ##   t_q16c_3 = col_double(),
    ##   t_q16c_4 = col_double(),
    ##   t_q16c_5 = col_double(),
    ##   t_q16d_1 = col_double(),
    ##   t_q16d_2 = col_double(),
    ##   t_q16d_3 = col_double(),
    ##   t_q16d_4 = col_double(),
    ##   t_q16d_5 = col_double(),
    ##   t_q17a_1 = col_double(),
    ##   t_q17a_2 = col_double(),
    ##   t_q17a_3 = col_double(),
    ##   t_q17a_4 = col_double(),
    ##   t_q17b_1 = col_double(),
    ##   t_q17b_2 = col_double(),
    ##   t_q17b_3 = col_double(),
    ##   t_q17b_4 = col_double(),
    ##   t_q17c_1 = col_double(),
    ##   t_q17c_2 = col_double(),
    ##   t_q17c_3 = col_double(),
    ##   t_q17c_4 = col_double(),
    ##   t_q17d_1 = col_double(),
    ##   t_q17d_2 = col_double(),
    ##   t_q17d_3 = col_double(),
    ##   t_q17d_4 = col_double(),
    ##   t_q17e_1 = col_double(),
    ##   t_q17e_2 = col_double(),
    ##   t_q17e_3 = col_double(),
    ##   t_q17e_4 = col_double(),
    ##   t_q17f_1 = col_double(),
    ##   t_q17f_2 = col_double(),
    ##   t_q17f_3 = col_double(),
    ##   t_q17f_4 = col_double(),
    ##   t_N_q1a_1 = col_double(),
    ##   t_N_q1a_2 = col_double(),
    ##   t_N_q1a_3 = col_double(),
    ##   t_N_q1a_4 = col_double(),
    ##   t_N_q1b_1 = col_double(),
    ##   t_N_q1b_2 = col_double(),
    ##   t_N_q1b_3 = col_double(),
    ##   t_N_q1b_4 = col_double(),
    ##   t_N_q1c_1 = col_double(),
    ##   t_N_q1c_2 = col_double(),
    ##   t_N_q1c_3 = col_double(),
    ##   t_N_q1c_4 = col_double(),
    ##   t_N_q1d_1 = col_double(),
    ##   t_N_q1d_2 = col_double(),
    ##   t_N_q1d_3 = col_double(),
    ##   t_N_q1d_4 = col_double(),
    ##   t_N_q1e_1 = col_double(),
    ##   t_N_q1e_2 = col_double(),
    ##   t_N_q1e_3 = col_double(),
    ##   t_N_q1e_4 = col_double(),
    ##   t_N_q1f_1 = col_double(),
    ##   t_N_q1f_2 = col_double(),
    ##   t_N_q1f_3 = col_double(),
    ##   t_N_q1f_4 = col_double(),
    ##   t_N_q1g_1 = col_double(),
    ##   t_N_q1g_2 = col_double(),
    ##   t_N_q1g_3 = col_double(),
    ##   t_N_q1g_4 = col_double(),
    ##   t_N_q2a_1 = col_double(),
    ##   t_N_q2a_2 = col_double(),
    ##   t_N_q2a_3 = col_double(),
    ##   t_N_q2a_4 = col_double(),
    ##   t_N_q2b_1 = col_double(),
    ##   t_N_q2b_2 = col_double(),
    ##   t_N_q2b_3 = col_double(),
    ##   t_N_q2b_4 = col_double(),
    ##   t_N_q2c_1 = col_double(),
    ##   t_N_q2c_2 = col_double(),
    ##   t_N_q2c_3 = col_double(),
    ##   t_N_q2c_4 = col_double(),
    ##   t_N_q2d_1 = col_double(),
    ##   t_N_q2d_2 = col_double(),
    ##   t_N_q2d_3 = col_double(),
    ##   t_N_q2d_4 = col_double(),
    ##   t_N_q2e_1 = col_double(),
    ##   t_N_q2e_2 = col_double(),
    ##   t_N_q2e_3 = col_double(),
    ##   t_N_q2e_4 = col_double(),
    ##   t_N_q2f_1 = col_double(),
    ##   t_N_q2f_2 = col_double(),
    ##   t_N_q2f_3 = col_double(),
    ##   t_N_q2f_4 = col_double(),
    ##   t_N_q3a_1 = col_double(),
    ##   t_N_q3a_2 = col_double(),
    ##   t_N_q3a_3 = col_double(),
    ##   t_N_q3b_1 = col_double(),
    ##   t_N_q3b_2 = col_double(),
    ##   t_N_q3b_3 = col_double(),
    ##   t_N_q3c_1 = col_double(),
    ##   t_N_q3c_2 = col_double(),
    ##   t_N_q3c_3 = col_double(),
    ##   t_N_q3d_1 = col_double(),
    ##   t_N_q3d_2 = col_double(),
    ##   t_N_q3d_3 = col_double(),
    ##   t_N_q3e_1 = col_double(),
    ##   t_N_q3e_2 = col_double(),
    ##   t_N_q3e_3 = col_double(),
    ##   t_N_q3f_1 = col_double(),
    ##   t_N_q3f_2 = col_double(),
    ##   t_N_q3f_3 = col_double(),
    ##   t_N_q3g_1 = col_double(),
    ##   t_N_q3g_2 = col_double(),
    ##   t_N_q3g_3 = col_double(),
    ##   t_N_q3h_1 = col_double(),
    ##   t_N_q3h_2 = col_double(),
    ##   t_N_q3h_3 = col_double(),
    ##   t_N_q3i_1 = col_double(),
    ##   t_N_q3i_2 = col_double(),
    ##   t_N_q3i_3 = col_double(),
    ##   t_N_q3j_1 = col_double(),
    ##   t_N_q3j_2 = col_double(),
    ##   t_N_q3j_3 = col_double(),
    ##   t_N_q3k_1 = col_double(),
    ##   t_N_q3k_2 = col_double(),
    ##   t_N_q3k_3 = col_double(),
    ##   t_N_q3l_1 = col_double(),
    ##   t_N_q3l_2 = col_double(),
    ##   t_N_q3l_3 = col_double(),
    ##   t_N_q4_1 = col_double(),
    ##   t_N_q4_2 = col_double(),
    ##   t_N_q4_3 = col_double(),
    ##   t_N_q4_4 = col_double(),
    ##   t_N_q5a_1 = col_double(),
    ##   t_N_q5a_2 = col_double(),
    ##   t_N_q5a_3 = col_double(),
    ##   t_N_q5a_4 = col_double(),
    ##   t_N_q5b_1 = col_double(),
    ##   t_N_q5b_2 = col_double(),
    ##   t_N_q5b_3 = col_double(),
    ##   t_N_q5b_4 = col_double(),
    ##   t_N_q6a_1 = col_double(),
    ##   t_N_q6a_2 = col_double(),
    ##   t_N_q6a_3 = col_double(),
    ##   t_N_q6a_4 = col_double(),
    ##   t_N_q6b_1 = col_double(),
    ##   t_N_q6b_2 = col_double(),
    ##   t_N_q6b_3 = col_double(),
    ##   t_N_q6b_4 = col_double(),
    ##   t_N_q6c_1 = col_double(),
    ##   t_N_q6c_2 = col_double(),
    ##   t_N_q6c_3 = col_double(),
    ##   t_N_q6c_4 = col_double(),
    ##   t_N_q6d_1 = col_double(),
    ##   t_N_q6d_2 = col_double(),
    ##   t_N_q6d_3 = col_double(),
    ##   t_N_q6d_4 = col_double(),
    ##   t_N_q6e_1 = col_double(),
    ##   t_N_q6e_2 = col_double(),
    ##   t_N_q6e_3 = col_double(),
    ##   t_N_q6e_4 = col_double(),
    ##   t_N_q6f_1 = col_double(),
    ##   t_N_q6f_2 = col_double(),
    ##   t_N_q6f_3 = col_double(),
    ##   t_N_q6f_4 = col_double(),
    ##   t_N_q6g_1 = col_double(),
    ##   t_N_q6g_2 = col_double(),
    ##   t_N_q6g_3 = col_double(),
    ##   t_N_q6g_4 = col_double(),
    ##   t_N_q6h_1 = col_double(),
    ##   t_N_q6h_2 = col_double(),
    ##   t_N_q6h_3 = col_double(),
    ##   t_N_q6h_4 = col_double(),
    ##   t_N_q6i_1 = col_double(),
    ##   t_N_q6i_2 = col_double(),
    ##   t_N_q6i_3 = col_double(),
    ##   t_N_q6i_4 = col_double(),
    ##   t_N_q6j_1 = col_double(),
    ##   t_N_q6j_2 = col_double(),
    ##   t_N_q6j_3 = col_double(),
    ##   t_N_q6j_4 = col_double(),
    ##   t_N_q6k_1 = col_double(),
    ##   t_N_q6k_2 = col_double(),
    ##   t_N_q6k_3 = col_double(),
    ##   t_N_q6k_4 = col_double(),
    ##   t_N_q7a_1 = col_double(),
    ##   t_N_q7a_2 = col_double(),
    ##   t_N_q7a_3 = col_double(),
    ##   t_N_q7a_4 = col_double(),
    ##   t_N_q7a_5 = col_double(),
    ##   t_N_q7b_1 = col_double(),
    ##   t_N_q7b_2 = col_double(),
    ##   t_N_q7b_3 = col_double(),
    ##   t_N_q7b_4 = col_double(),
    ##   t_N_q7b_5 = col_double(),
    ##   t_N_q7c_1 = col_double(),
    ##   t_N_q7c_2 = col_double(),
    ##   t_N_q7c_3 = col_double(),
    ##   t_N_q7c_4 = col_double(),
    ##   t_N_q7c_5 = col_double(),
    ##   t_N_q7d_1 = col_double(),
    ##   t_N_q7d_2 = col_double(),
    ##   t_N_q7d_3 = col_double(),
    ##   t_N_q7d_4 = col_double(),
    ##   t_N_q7d_5 = col_double(),
    ##   t_N_q7e_1 = col_double(),
    ##   t_N_q7e_2 = col_double(),
    ##   t_N_q7e_3 = col_double(),
    ##   t_N_q7e_4 = col_double(),
    ##   t_N_q7e_5 = col_double(),
    ##   t_N_q7f_1 = col_double(),
    ##   t_N_q7f_2 = col_double(),
    ##   t_N_q7f_3 = col_double(),
    ##   t_N_q7f_4 = col_double(),
    ##   t_N_q7f_5 = col_double(),
    ##   t_N_q8a_1 = col_double(),
    ##   t_N_q8a_2 = col_double(),
    ##   t_N_q8a_3 = col_double(),
    ##   t_N_q8a_4 = col_double(),
    ##   t_N_q8b_1 = col_double(),
    ##   t_N_q8b_2 = col_double(),
    ##   t_N_q8b_3 = col_double(),
    ##   t_N_q8b_4 = col_double(),
    ##   t_N_q8c_1 = col_double(),
    ##   t_N_q8c_2 = col_double(),
    ##   t_N_q8c_3 = col_double(),
    ##   t_N_q8c_4 = col_double(),
    ##   t_N_q9_1 = col_double(),
    ##   t_N_q9_2 = col_double(),
    ##   t_N_q9_3 = col_double(),
    ##   t_N_q9_4 = col_double(),
    ##   t_N_q9_5 = col_double(),
    ##   t_N_q10_1 = col_double(),
    ##   t_N_q10_2 = col_double(),
    ##   t_N_q10_3 = col_double(),
    ##   t_N_q10_4 = col_double(),
    ##   t_N_q10_5 = col_double(),
    ##   t_N_q11a_1 = col_double(),
    ##   t_N_q11a_2 = col_double(),
    ##   t_N_q11a_3 = col_double(),
    ##   t_N_q11a_4 = col_double(),
    ##   t_N_q11a_5 = col_double(),
    ##   t_N_q11b_1 = col_double(),
    ##   t_N_q11b_2 = col_double(),
    ##   t_N_q11b_3 = col_double(),
    ##   t_N_q11b_4 = col_double(),
    ##   t_N_q11b_5 = col_double(),
    ##   t_N_q11c_1 = col_double(),
    ##   t_N_q11c_2 = col_double(),
    ##   t_N_q11c_3 = col_double(),
    ##   t_N_q11c_4 = col_double(),
    ##   t_N_q11c_5 = col_double(),
    ##   t_N_q11d_1 = col_double(),
    ##   t_N_q11d_2 = col_double(),
    ##   t_N_q11d_3 = col_double(),
    ##   t_N_q11d_4 = col_double(),
    ##   t_N_q11d_5 = col_double(),
    ##   t_N_q12_1 = col_double(),
    ##   t_N_q12_2 = col_double(),
    ##   t_N_q12_3 = col_double(),
    ##   t_N_q12_4 = col_double(),
    ##   t_N_q12_5 = col_double(),
    ##   t_N_q12_6 = col_double(),
    ##   t_N_q13a_1 = col_double(),
    ##   t_N_q13a_2 = col_double(),
    ##   t_N_q13a_3 = col_double(),
    ##   t_N_q13a_4 = col_double(),
    ##   t_N_q13b_1 = col_double(),
    ##   t_N_q13b_2 = col_double(),
    ##   t_N_q13b_3 = col_double(),
    ##   t_N_q13b_4 = col_double(),
    ##   t_N_q13c_1 = col_double(),
    ##   t_N_q13c_2 = col_double(),
    ##   t_N_q13c_3 = col_double(),
    ##   t_N_q13c_4 = col_double(),
    ##   t_N_q13d_1 = col_double(),
    ##   t_N_q13d_2 = col_double(),
    ##   t_N_q13d_3 = col_double(),
    ##   t_N_q13d_4 = col_double(),
    ##   t_N_q13e_1 = col_double(),
    ##   t_N_q13e_2 = col_double(),
    ##   t_N_q13e_3 = col_double(),
    ##   t_N_q13e_4 = col_double(),
    ##   t_N_q13f_1 = col_double(),
    ##   t_N_q13f_2 = col_double(),
    ##   t_N_q13f_3 = col_double(),
    ##   t_N_q13f_4 = col_double(),
    ##   t_N_q13g_1 = col_double(),
    ##   t_N_q13g_2 = col_double(),
    ##   t_N_q13g_3 = col_double(),
    ##   t_N_q13g_4 = col_double(),
    ##   t_N_q13h_1 = col_double(),
    ##   t_N_q13h_2 = col_double(),
    ##   t_N_q13h_3 = col_double(),
    ##   t_N_q13h_4 = col_double(),
    ##   t_N_q13i_1 = col_double(),
    ##   t_N_q13i_2 = col_double(),
    ##   t_N_q13i_3 = col_double(),
    ##   t_N_q13i_4 = col_double(),
    ##   t_N_q13j_1 = col_double(),
    ##   t_N_q13j_2 = col_double(),
    ##   t_N_q13j_3 = col_double(),
    ##   t_N_q13j_4 = col_double(),
    ##   t_N_q13k_1 = col_double(),
    ##   t_N_q13k_2 = col_double(),
    ##   t_N_q13k_3 = col_double(),
    ##   t_N_q13k_4 = col_double(),
    ##   t_N_q13l_1 = col_double(),
    ##   t_N_q13l_2 = col_double(),
    ##   t_N_q13l_3 = col_double(),
    ##   t_N_q13l_4 = col_double(),
    ##   t_N_q13m_1 = col_double(),
    ##   t_N_q13m_2 = col_double(),
    ##   t_N_q13m_3 = col_double(),
    ##   t_N_q13m_4 = col_double(),
    ##   t_N_q13n_1 = col_double(),
    ##   t_N_q13n_2 = col_double(),
    ##   t_N_q13n_3 = col_double(),
    ##   t_N_q13n_4 = col_double(),
    ##   t_N_q14_1 = col_double(),
    ##   t_N_q14_2 = col_double(),
    ##   t_N_q14_3 = col_double(),
    ##   t_N_q14_4 = col_double(),
    ##   t_N_q14_5 = col_double(),
    ##   t_N_q14_6 = col_double(),
    ##   t_N_q15a_1 = col_double(),
    ##   t_N_q15a_2 = col_double(),
    ##   t_N_q15a_3 = col_double(),
    ##   t_N_q15a_4 = col_double(),
    ##   t_N_q15a_5 = col_double(),
    ##   t_N_q15b_1 = col_double(),
    ##   t_N_q15b_2 = col_double(),
    ##   t_N_q15b_3 = col_double(),
    ##   t_N_q15b_4 = col_double(),
    ##   t_N_q15b_5 = col_double(),
    ##   t_N_q15c_1 = col_double(),
    ##   t_N_q15c_2 = col_double(),
    ##   t_N_q15c_3 = col_double(),
    ##   t_N_q15c_4 = col_double(),
    ##   t_N_q15c_5 = col_double(),
    ##   t_N_q15d_1 = col_double(),
    ##   t_N_q15d_2 = col_double(),
    ##   t_N_q15d_3 = col_double(),
    ##   t_N_q15d_4 = col_double(),
    ##   t_N_q15d_5 = col_double(),
    ##   t_N_q16a_1 = col_double(),
    ##   t_N_q16a_2 = col_double(),
    ##   t_N_q16a_3 = col_double(),
    ##   t_N_q16a_4 = col_double(),
    ##   t_N_q16a_5 = col_double(),
    ##   t_N_q16b_1 = col_double(),
    ##   t_N_q16b_2 = col_double(),
    ##   t_N_q16b_3 = col_double(),
    ##   t_N_q16b_4 = col_double(),
    ##   t_N_q16b_5 = col_double(),
    ##   t_N_q16c_1 = col_double(),
    ##   t_N_q16c_2 = col_double(),
    ##   t_N_q16c_3 = col_double(),
    ##   t_N_q16c_4 = col_double(),
    ##   t_N_q16c_5 = col_double(),
    ##   t_N_q16d_1 = col_double(),
    ##   t_N_q16d_2 = col_double(),
    ##   t_N_q16d_3 = col_double(),
    ##   t_N_q16d_4 = col_double(),
    ##   t_N_q16d_5 = col_double(),
    ##   t_N_q17a_1 = col_double(),
    ##   t_N_q17a_2 = col_double(),
    ##   t_N_q17a_3 = col_double(),
    ##   t_N_q17a_4 = col_double(),
    ##   t_N_q17b_1 = col_double(),
    ##   t_N_q17b_2 = col_double(),
    ##   t_N_q17b_3 = col_double(),
    ##   t_N_q17b_4 = col_double(),
    ##   t_N_q17c_1 = col_double(),
    ##   t_N_q17c_2 = col_double(),
    ##   t_N_q17c_3 = col_double(),
    ##   t_N_q17c_4 = col_double(),
    ##   t_N_q17d_1 = col_double(),
    ##   t_N_q17d_2 = col_double(),
    ##   t_N_q17d_3 = col_double(),
    ##   t_N_q17d_4 = col_double(),
    ##   t_N_q17e_1 = col_double(),
    ##   t_N_q17e_2 = col_double(),
    ##   t_N_q17e_3 = col_double(),
    ##   t_N_q17e_4 = col_double(),
    ##   t_N_q17f_1 = col_double(),
    ##   t_N_q17f_2 = col_double(),
    ##   t_N_q17f_3 = col_double(),
    ##   t_N_q17f_4 = col_double(),
    ##   s_q5a = col_double(),
    ##   s_q5b = col_double(),
    ##   s_q5c = col_double(),
    ##   s_q11a = col_double(),
    ##   s_q11b = col_double(),
    ##   s_q11c = col_double(),
    ##   s_q12a = col_double(),
    ##   s_q12b = col_double(),
    ##   s_q12c = col_double(),
    ##   s_q12d = col_double(),
    ##   s_q12e = col_double(),
    ##   s_q12f = col_double(),
    ##   s_q12g = col_double(),
    ##   s_q13a = col_double(),
    ##   s_q13b = col_double(),
    ##   s_q13c = col_double(),
    ##   s_q13d = col_double(),
    ##   s_q13e = col_double(),
    ##   s_q13f = col_double(),
    ##   s_q13g = col_double(),
    ##   s_q1b = col_double(),
    ##   s_q3a = col_double(),
    ##   s_q3b = col_double(),
    ##   s_q7b = col_double(),
    ##   s_q7c = col_double(),
    ##   s_q7d = col_double(),
    ##   s_q1a = col_double(),
    ##   s_q1c = col_double(),
    ##   s_q4a = col_double(),
    ##   s_q4b = col_double(),
    ##   s_q5d = col_double(),
    ##   s_q5e = col_double(),
    ##   s_q5g = col_double(),
    ##   s_q8 = col_double(),
    ##   s_q9 = col_double(),
    ##   s_q10 = col_double(),
    ##   s_q2a = col_double(),
    ##   s_q2b = col_double(),
    ##   s_q2c = col_double(),
    ##   s_q2d = col_double(),
    ##   s_q2e = col_double(),
    ##   s_q2f = col_double(),
    ##   s_q2g = col_double(),
    ##   s_q5f = col_double(),
    ##   s_q6a = col_double(),
    ##   s_q6b = col_double(),
    ##   s_q7a = col_logical(),
    ##   s_q14 = col_logical(),
    ##   s_q1a_1 = col_double(),
    ##   s_q1a_2 = col_double(),
    ##   s_q1a_3 = col_double(),
    ##   s_q1a_4 = col_double(),
    ##   s_q1b_1 = col_double(),
    ##   s_q1b_2 = col_double(),
    ##   s_q1b_3 = col_double(),
    ##   s_q1b_4 = col_double(),
    ##   s_q1c_1 = col_double(),
    ##   s_q1c_2 = col_double(),
    ##   s_q1c_3 = col_double(),
    ##   s_q1c_4 = col_double(),
    ##   s_q2a_1 = col_double(),
    ##   s_q2a_2 = col_double(),
    ##   s_q2a_3 = col_double(),
    ##   s_q2a_4 = col_double(),
    ##   s_q2b_1 = col_double(),
    ##   s_q2b_2 = col_double(),
    ##   s_q2b_3 = col_double(),
    ##   s_q2b_4 = col_double(),
    ##   s_q2c_1 = col_double(),
    ##   s_q2c_2 = col_double(),
    ##   s_q2c_3 = col_double(),
    ##   s_q2c_4 = col_double(),
    ##   s_q2d_1 = col_double(),
    ##   s_q2d_2 = col_double(),
    ##   s_q2d_3 = col_double(),
    ##   s_q2d_4 = col_double(),
    ##   s_q2e_1 = col_double(),
    ##   s_q2e_2 = col_double(),
    ##   s_q2e_3 = col_double(),
    ##   s_q2e_4 = col_double(),
    ##   s_q2f_1 = col_double(),
    ##   s_q2f_2 = col_double(),
    ##   s_q2f_3 = col_double(),
    ##   s_q2f_4 = col_double(),
    ##   s_q2g_1 = col_double(),
    ##   s_q2g_2 = col_double(),
    ##   s_q2g_3 = col_double(),
    ##   s_q2g_4 = col_double(),
    ##   s_q3a_1 = col_double(),
    ##   s_q3a_2 = col_double(),
    ##   s_q3a_3 = col_double(),
    ##   s_q3a_4 = col_double(),
    ##   s_q3b_1 = col_double(),
    ##   s_q3b_2 = col_double(),
    ##   s_q3b_3 = col_double(),
    ##   s_q3b_4 = col_double(),
    ##   s_q4a_1 = col_double(),
    ##   s_q4a_2 = col_double(),
    ##   s_q4a_3 = col_double(),
    ##   s_q4a_4 = col_double(),
    ##   s_q4b_1 = col_double(),
    ##   s_q4b_2 = col_double(),
    ##   s_q4b_3 = col_double(),
    ##   s_q4b_4 = col_double(),
    ##   s_q5a_1 = col_double(),
    ##   s_q5a_2 = col_double(),
    ##   s_q5a_3 = col_double(),
    ##   s_q5a_4 = col_double(),
    ##   s_q5a_5 = col_double(),
    ##   s_q5b_1 = col_double(),
    ##   s_q5b_2 = col_double(),
    ##   s_q5b_3 = col_double(),
    ##   s_q5b_4 = col_double(),
    ##   s_q5b_5 = col_double(),
    ##   s_q5c_1 = col_double(),
    ##   s_q5c_2 = col_double(),
    ##   s_q5c_3 = col_double(),
    ##   s_q5c_4 = col_double(),
    ##   s_q5c_5 = col_double(),
    ##   s_q5d_1 = col_double(),
    ##   s_q5d_2 = col_double(),
    ##   s_q5d_3 = col_double(),
    ##   s_q5d_4 = col_double(),
    ##   s_q5d_5 = col_double(),
    ##   s_q5e_1 = col_double(),
    ##   s_q5e_2 = col_double(),
    ##   s_q5e_3 = col_double(),
    ##   s_q5e_4 = col_double(),
    ##   s_q5e_5 = col_double(),
    ##   s_q5f_1 = col_double(),
    ##   s_q5f_2 = col_double(),
    ##   s_q5f_3 = col_double(),
    ##   s_q5f_4 = col_double(),
    ##   s_q5f_5 = col_double(),
    ##   s_q5g_1 = col_double(),
    ##   s_q5g_2 = col_double(),
    ##   s_q5g_3 = col_double(),
    ##   s_q5g_4 = col_double(),
    ##   s_q5g_5 = col_double(),
    ##   s_q6a_1 = col_double(),
    ##   s_q6a_2 = col_double(),
    ##   s_q6a_3 = col_double(),
    ##   s_q6a_4 = col_double(),
    ##   s_q6b_1 = col_double(),
    ##   s_q6b_2 = col_double(),
    ##   s_q6b_3 = col_double(),
    ##   s_q6b_4 = col_double(),
    ##   s_q7a_1 = col_double(),
    ##   s_q7a_2 = col_double(),
    ##   s_q7a_3 = col_double(),
    ##   s_q7a_4 = col_double(),
    ##   s_q7a_5 = col_double(),
    ##   s_q7b_1 = col_double(),
    ##   s_q7b_2 = col_double(),
    ##   s_q7b_3 = col_double(),
    ##   s_q7b_4 = col_double(),
    ##   s_q7b_5 = col_double(),
    ##   s_q7c_1 = col_double(),
    ##   s_q7c_2 = col_double(),
    ##   s_q7c_3 = col_double(),
    ##   s_q7c_4 = col_double(),
    ##   s_q7c_5 = col_double(),
    ##   s_q7d_1 = col_double(),
    ##   s_q7d_2 = col_double(),
    ##   s_q7d_3 = col_double(),
    ##   s_q7d_4 = col_double(),
    ##   s_q7d_5 = col_double(),
    ##   s_q8a_1 = col_double(),
    ##   s_q8a_2 = col_double(),
    ##   s_q8a_3 = col_double(),
    ##   s_q8b_1 = col_double(),
    ##   s_q8b_2 = col_double(),
    ##   s_q8b_3 = col_double(),
    ##   s_q8c_1 = col_double(),
    ##   s_q8c_2 = col_double(),
    ##   s_q8c_3 = col_double(),
    ##   s_q8d_1 = col_double(),
    ##   s_q8d_2 = col_double(),
    ##   s_q8d_3 = col_double(),
    ##   s_q8e_1 = col_double(),
    ##   s_q8e_2 = col_double(),
    ##   s_q8e_3 = col_double(),
    ##   s_q8f_1 = col_double(),
    ##   s_q8f_2 = col_double(),
    ##   s_q8f_3 = col_double(),
    ##   s_q8g_1 = col_double(),
    ##   s_q8g_2 = col_double(),
    ##   s_q8g_3 = col_double(),
    ##   s_q8h_1 = col_double(),
    ##   s_q8h_2 = col_double(),
    ##   s_q8h_3 = col_double(),
    ##   s_q8i_1 = col_double(),
    ##   s_q8i_2 = col_double(),
    ##   s_q8i_3 = col_double(),
    ##   s_q8j_1 = col_double(),
    ##   s_q8j_2 = col_double(),
    ##   s_q8j_3 = col_double(),
    ##   s_q8k_1 = col_double(),
    ##   s_q8k_2 = col_double(),
    ##   s_q8k_3 = col_double(),
    ##   s_q8l_1 = col_double(),
    ##   s_q8l_2 = col_double(),
    ##   s_q8l_3 = col_double(),
    ##   s_q9a_1 = col_double(),
    ##   s_q9a_2 = col_double(),
    ##   s_q9a_3 = col_double(),
    ##   s_q9b_1 = col_double(),
    ##   s_q9b_2 = col_double(),
    ##   s_q9b_3 = col_double(),
    ##   s_q9c_1 = col_double(),
    ##   s_q9c_2 = col_double(),
    ##   s_q9c_3 = col_double(),
    ##   s_q9d_1 = col_double(),
    ##   s_q9d_2 = col_double(),
    ##   s_q9d_3 = col_double(),
    ##   s_q9e_1 = col_double(),
    ##   s_q9e_2 = col_double(),
    ##   s_q9e_3 = col_double(),
    ##   s_q9f_1 = col_double(),
    ##   s_q9f_2 = col_double(),
    ##   s_q9f_3 = col_double(),
    ##   s_q9g_1 = col_double(),
    ##   s_q9g_2 = col_double(),
    ##   s_q9g_3 = col_double(),
    ##   s_q9h_1 = col_double(),
    ##   s_q9h_2 = col_double(),
    ##   s_q9h_3 = col_double(),
    ##   s_q9i_1 = col_double(),
    ##   s_q9i_2 = col_double(),
    ##   s_q9i_3 = col_double(),
    ##   s_q9j_1 = col_double(),
    ##   s_q9j_2 = col_double(),
    ##   s_q9j_3 = col_double(),
    ##   s_q9k_1 = col_double(),
    ##   s_q9k_2 = col_double(),
    ##   s_q9k_3 = col_double(),
    ##   s_q9l_1 = col_double(),
    ##   s_q9l_2 = col_double(),
    ##   s_q9l_3 = col_double(),
    ##   s_q10_1 = col_double(),
    ##   s_q10_2 = col_double(),
    ##   s_q10_3 = col_double(),
    ##   s_q10_4 = col_double(),
    ##   s_q11a_1 = col_double(),
    ##   s_q11a_2 = col_double(),
    ##   s_q11a_3 = col_double(),
    ##   s_q11a_4 = col_double(),
    ##   s_q11b_1 = col_double(),
    ##   s_q11b_2 = col_double(),
    ##   s_q11b_3 = col_double(),
    ##   s_q11b_4 = col_double(),
    ##   s_q11c_1 = col_double(),
    ##   s_q11c_2 = col_double(),
    ##   s_q11c_3 = col_double(),
    ##   s_q11c_4 = col_double(),
    ##   s_q12a_1 = col_double(),
    ##   s_q12a_2 = col_double(),
    ##   s_q12a_3 = col_double(),
    ##   s_q12a_4 = col_double(),
    ##   s_q12b_1 = col_double(),
    ##   s_q12b_2 = col_double(),
    ##   s_q12b_3 = col_double(),
    ##   s_q12b_4 = col_double(),
    ##   s_q12c_1 = col_double(),
    ##   s_q12c_2 = col_double(),
    ##   s_q12c_3 = col_double(),
    ##   s_q12c_4 = col_double(),
    ##   s_q12d_1 = col_double(),
    ##   s_q12d_2 = col_double(),
    ##   s_q12d_3 = col_double(),
    ##   s_q12d_4 = col_double(),
    ##   s_q12e_1 = col_double(),
    ##   s_q12e_2 = col_double(),
    ##   s_q12e_3 = col_double(),
    ##   s_q12e_4 = col_double(),
    ##   s_q12f_1 = col_double(),
    ##   s_q12f_2 = col_double(),
    ##   s_q12f_3 = col_double(),
    ##   s_q12f_4 = col_double(),
    ##   s_q12g_1 = col_double(),
    ##   s_q12g_2 = col_double(),
    ##   s_q12g_3 = col_double(),
    ##   s_q12g_4 = col_double(),
    ##   s_q13a_1 = col_double(),
    ##   s_q13a_2 = col_double(),
    ##   s_q13a_3 = col_double(),
    ##   s_q13a_4 = col_double(),
    ##   s_q13b_1 = col_double(),
    ##   s_q13b_2 = col_double(),
    ##   s_q13b_3 = col_double(),
    ##   s_q13b_4 = col_double(),
    ##   s_q13c_1 = col_double(),
    ##   s_q13c_2 = col_double(),
    ##   s_q13c_3 = col_double(),
    ##   s_q13c_4 = col_double(),
    ##   s_q13d_1 = col_double(),
    ##   s_q13d_2 = col_double(),
    ##   s_q13d_3 = col_double(),
    ##   s_q13d_4 = col_double(),
    ##   s_q13e_1 = col_double(),
    ##   s_q13e_2 = col_double(),
    ##   s_q13e_3 = col_double(),
    ##   s_q13e_4 = col_double(),
    ##   s_q13f_1 = col_double(),
    ##   s_q13f_2 = col_double(),
    ##   s_q13f_3 = col_double(),
    ##   s_q13f_4 = col_double(),
    ##   s_q13g_1 = col_double(),
    ##   s_q13g_2 = col_double(),
    ##   s_q13g_3 = col_double(),
    ##   s_q13g_4 = col_double(),
    ##   s_q14_1 = col_double(),
    ##   s_q14_2 = col_double(),
    ##   s_q14_3 = col_double(),
    ##   s_q14_4 = col_double(),
    ##   s_q14_5 = col_double(),
    ##   s_q14_6 = col_double(),
    ##   s_q14_7 = col_double(),
    ##   s_q14_8 = col_double(),
    ##   s_q14_9 = col_double(),
    ##   s_q14_10 = col_double(),
    ##   s_q14_11 = col_double()
    ## )

According to the data dictionary only grades 6-12 particiapted in the
survey but we are only interested in high schools so we will filter
`survey` to get high schools and academic data

``` r
survey_selected <- survey %>% filter(schooltype == "High School") %>% select(dbn:aca_tot_11)
```

Filter `survey_d75` to get only the columns we need

``` r
survey_d75_selected <- survey_d75 %>% select(dbn:aca_tot_11)
```

We will now combine both `survey_selected` and `survey_d75_selected`
into a single data frame

``` r
survey_combined <- survey_selected %>% bind_rows(survey_d75_selected)
```

We used the `bind_rows()` function to combine the dataframes

Since the `combined` dataframe as the dbn as DBN we will need to fix
this in the `survey_combined` dataframe

``` r
survey_combined <- survey_combined %>% rename(DBN = dbn)
```

As we are interested in the results listed in the combined dataframe we
will use `left_join()` which will match what is in the `survey_combined`
dataframe to what is listed in the `combined` dataframe

We will also use DBN as the key we are joining on

``` r
combined_survey <- combined %>% left_join(survey_combined, by = "DBN")
```

We will use a correlation matrix to answer the first question we had for
this data

``` r
cor_mat <- combined_survey %>% select(avg_sat_score, saf_p_11:aca_tot_11) %>% cor(use = "pairwise.complete.obs")

cor_tibble <- cor_mat %>% as_tibble(rownames = "variable")
```

Look through the `cor_tibble` for strong correlations (greater than 0.25
or less than -0.25) with `avg_sat_score`

``` r
strong_correlations <- cor_tibble %>% select(variable, avg_sat_score) %>% filter(avg_sat_score > 0.25 | avg_sat_score < -0.25)
```

We will now visualize the correlations using a scatter plot

``` r
gen_scatter <- function(x, y) {
  ggplot(data = combined_survey) +
    aes_string(x = x, y = y) + 
    geom_point(alpha = 0.3) +
    theme(panel.background = element_rect(fill = "white"))
}

x_variables <- strong_correlations$variable[2:5]
y_variables <- "avg_sat_score"

map2(x_variables, y_variables, gen_scatter)
```

    ## [[1]]

    ## Warning: Removed 137 rows containing missing values (geom_point).

![](/images/nyc-school-files/unnamed-chunk-12-1.png)

    ## 
    ## [[2]]

    ## Warning: Removed 139 rows containing missing values (geom_point).

![](/images/nyc-school-files/unnamed-chunk-12-2.png)

    ## 
    ## [[3]]

    ## Warning: Removed 139 rows containing missing values (geom_point).

![](/images/nyc-school-files/unnamed-chunk-12-3.png)

    ## 
    ## [[4]]

    ## Warning: Removed 137 rows containing missing values (geom_point).

![](/images/nyc-school-files/unnamed-chunk-12-4.png)

We will have to reshape the data to see the responses from students,
teachers and parents

``` r
combined_survey_longer <- combined_survey %>% pivot_longer(cols = c(saf_p_11:aca_tot_11), names_to = "survey_question", values_to = "score")
```

We will need two new variables `response_type` which indicates if
students, teachers or parents are the respondents and `question` which
is the question being asked. Both of these will come from the newly
created `survey_question` variable

``` r
combined_survey_longer$survey_question
```

    ##    [1] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ##    [6] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ##   [11] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ##   [16] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ##   [21] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ##   [26] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ##   [31] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ##   [36] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ##   [41] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ##   [46] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ##   [51] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ##   [56] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ##   [61] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ##   [66] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ##   [71] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ##   [76] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ##   [81] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ##   [86] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ##   [91] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ##   [96] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ##  [101] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ##  [106] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ##  [111] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ##  [116] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ##  [121] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ##  [126] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ##  [131] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ##  [136] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ##  [141] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ##  [146] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ##  [151] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ##  [156] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ##  [161] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ##  [166] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ##  [171] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ##  [176] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ##  [181] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ##  [186] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ##  [191] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ##  [196] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ##  [201] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ##  [206] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ##  [211] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ##  [216] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ##  [221] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ##  [226] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ##  [231] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ##  [236] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ##  [241] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ##  [246] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ##  [251] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ##  [256] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ##  [261] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ##  [266] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ##  [271] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ##  [276] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ##  [281] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ##  [286] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ##  [291] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ##  [296] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ##  [301] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ##  [306] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ##  [311] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ##  [316] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ##  [321] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ##  [326] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ##  [331] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ##  [336] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ##  [341] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ##  [346] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ##  [351] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ##  [356] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ##  [361] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ##  [366] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ##  [371] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ##  [376] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ##  [381] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ##  [386] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ##  [391] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ##  [396] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ##  [401] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ##  [406] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ##  [411] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ##  [416] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ##  [421] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ##  [426] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ##  [431] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ##  [436] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ##  [441] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ##  [446] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ##  [451] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ##  [456] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ##  [461] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ##  [466] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ##  [471] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ##  [476] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ##  [481] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ##  [486] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ##  [491] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ##  [496] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ##  [501] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ##  [506] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ##  [511] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ##  [516] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ##  [521] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ##  [526] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ##  [531] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ##  [536] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ##  [541] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ##  [546] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ##  [551] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ##  [556] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ##  [561] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ##  [566] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ##  [571] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ##  [576] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ##  [581] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ##  [586] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ##  [591] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ##  [596] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ##  [601] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ##  [606] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ##  [611] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ##  [616] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ##  [621] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ##  [626] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ##  [631] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ##  [636] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ##  [641] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ##  [646] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ##  [651] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ##  [656] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ##  [661] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ##  [666] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ##  [671] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ##  [676] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ##  [681] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ##  [686] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ##  [691] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ##  [696] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ##  [701] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ##  [706] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ##  [711] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ##  [716] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ##  [721] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ##  [726] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ##  [731] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ##  [736] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ##  [741] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ##  [746] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ##  [751] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ##  [756] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ##  [761] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ##  [766] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ##  [771] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ##  [776] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ##  [781] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ##  [786] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ##  [791] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ##  [796] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ##  [801] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ##  [806] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ##  [811] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ##  [816] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ##  [821] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ##  [826] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ##  [831] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ##  [836] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ##  [841] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ##  [846] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ##  [851] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ##  [856] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ##  [861] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ##  [866] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ##  [871] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ##  [876] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ##  [881] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ##  [886] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ##  [891] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ##  [896] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ##  [901] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ##  [906] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ##  [911] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ##  [916] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ##  [921] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ##  [926] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ##  [931] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ##  [936] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ##  [941] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ##  [946] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ##  [951] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ##  [956] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ##  [961] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ##  [966] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ##  [971] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ##  [976] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ##  [981] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ##  [986] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ##  [991] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ##  [996] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [1001] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [1006] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [1011] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [1016] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [1021] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [1026] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [1031] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [1036] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [1041] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [1046] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [1051] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [1056] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [1061] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [1066] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [1071] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [1076] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [1081] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [1086] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [1091] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [1096] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [1101] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [1106] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [1111] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [1116] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [1121] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [1126] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [1131] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [1136] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [1141] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [1146] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [1151] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [1156] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [1161] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [1166] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [1171] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [1176] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [1181] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [1186] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [1191] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [1196] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [1201] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [1206] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [1211] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [1216] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [1221] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [1226] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [1231] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [1236] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [1241] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [1246] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [1251] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [1256] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [1261] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [1266] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [1271] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [1276] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [1281] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [1286] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [1291] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [1296] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [1301] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [1306] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [1311] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [1316] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [1321] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [1326] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [1331] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [1336] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [1341] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [1346] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [1351] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [1356] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [1361] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [1366] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [1371] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [1376] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [1381] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [1386] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [1391] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [1396] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [1401] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [1406] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [1411] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [1416] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [1421] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [1426] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [1431] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [1436] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [1441] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [1446] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [1451] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [1456] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [1461] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [1466] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [1471] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [1476] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [1481] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [1486] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [1491] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [1496] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [1501] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [1506] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [1511] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [1516] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [1521] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [1526] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [1531] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [1536] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [1541] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [1546] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [1551] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [1556] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [1561] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [1566] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [1571] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [1576] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [1581] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [1586] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [1591] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [1596] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [1601] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [1606] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [1611] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [1616] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [1621] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [1626] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [1631] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [1636] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [1641] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [1646] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [1651] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [1656] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [1661] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [1666] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [1671] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [1676] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [1681] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [1686] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [1691] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [1696] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [1701] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [1706] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [1711] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [1716] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [1721] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [1726] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [1731] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [1736] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [1741] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [1746] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [1751] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [1756] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [1761] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [1766] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [1771] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [1776] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [1781] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [1786] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [1791] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [1796] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [1801] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [1806] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [1811] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [1816] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [1821] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [1826] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [1831] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [1836] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [1841] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [1846] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [1851] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [1856] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [1861] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [1866] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [1871] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [1876] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [1881] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [1886] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [1891] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [1896] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [1901] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [1906] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [1911] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [1916] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [1921] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [1926] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [1931] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [1936] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [1941] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [1946] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [1951] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [1956] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [1961] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [1966] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [1971] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [1976] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [1981] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [1986] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [1991] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [1996] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [2001] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [2006] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [2011] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [2016] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [2021] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [2026] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [2031] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [2036] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [2041] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [2046] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [2051] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [2056] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [2061] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [2066] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [2071] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [2076] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [2081] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [2086] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [2091] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [2096] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [2101] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [2106] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [2111] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [2116] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [2121] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [2126] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [2131] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [2136] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [2141] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [2146] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [2151] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [2156] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [2161] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [2166] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [2171] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [2176] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [2181] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [2186] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [2191] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [2196] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [2201] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [2206] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [2211] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [2216] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [2221] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [2226] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [2231] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [2236] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [2241] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [2246] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [2251] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [2256] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [2261] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [2266] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [2271] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [2276] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [2281] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [2286] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [2291] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [2296] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [2301] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [2306] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [2311] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [2316] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [2321] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [2326] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [2331] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [2336] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [2341] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [2346] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [2351] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [2356] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [2361] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [2366] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [2371] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [2376] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [2381] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [2386] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [2391] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [2396] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [2401] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [2406] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [2411] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [2416] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [2421] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [2426] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [2431] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [2436] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [2441] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [2446] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [2451] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [2456] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [2461] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [2466] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [2471] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [2476] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [2481] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [2486] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [2491] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [2496] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [2501] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [2506] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [2511] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [2516] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [2521] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [2526] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [2531] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [2536] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [2541] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [2546] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [2551] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [2556] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [2561] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [2566] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [2571] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [2576] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [2581] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [2586] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [2591] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [2596] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [2601] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [2606] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [2611] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [2616] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [2621] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [2626] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [2631] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [2636] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [2641] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [2646] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [2651] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [2656] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [2661] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [2666] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [2671] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [2676] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [2681] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [2686] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [2691] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [2696] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [2701] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [2706] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [2711] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [2716] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [2721] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [2726] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [2731] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [2736] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [2741] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [2746] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [2751] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [2756] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [2761] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [2766] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [2771] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [2776] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [2781] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [2786] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [2791] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [2796] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [2801] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [2806] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [2811] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [2816] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [2821] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [2826] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [2831] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [2836] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [2841] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [2846] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [2851] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [2856] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [2861] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [2866] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [2871] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [2876] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [2881] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [2886] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [2891] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [2896] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [2901] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [2906] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [2911] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [2916] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [2921] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [2926] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [2931] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [2936] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [2941] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [2946] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [2951] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [2956] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [2961] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [2966] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [2971] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [2976] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [2981] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [2986] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [2991] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [2996] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [3001] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [3006] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [3011] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [3016] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [3021] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [3026] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [3031] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [3036] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [3041] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [3046] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [3051] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [3056] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [3061] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [3066] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [3071] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [3076] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [3081] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [3086] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [3091] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [3096] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [3101] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [3106] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [3111] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [3116] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [3121] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [3126] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [3131] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [3136] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [3141] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [3146] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [3151] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [3156] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [3161] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [3166] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [3171] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [3176] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [3181] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [3186] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [3191] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [3196] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [3201] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [3206] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [3211] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [3216] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [3221] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [3226] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [3231] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [3236] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [3241] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [3246] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [3251] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [3256] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [3261] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [3266] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [3271] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [3276] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [3281] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [3286] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [3291] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [3296] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [3301] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [3306] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [3311] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [3316] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [3321] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [3326] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [3331] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [3336] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [3341] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [3346] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [3351] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [3356] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [3361] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [3366] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [3371] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [3376] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [3381] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [3386] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [3391] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [3396] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [3401] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [3406] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [3411] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [3416] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [3421] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [3426] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [3431] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [3436] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [3441] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [3446] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [3451] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [3456] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [3461] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [3466] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [3471] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [3476] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [3481] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [3486] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [3491] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [3496] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [3501] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [3506] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [3511] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [3516] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [3521] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [3526] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [3531] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [3536] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [3541] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [3546] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [3551] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [3556] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [3561] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [3566] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [3571] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [3576] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [3581] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [3586] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [3591] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [3596] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [3601] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [3606] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [3611] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [3616] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [3621] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [3626] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [3631] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [3636] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [3641] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [3646] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [3651] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [3656] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [3661] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [3666] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [3671] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [3676] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [3681] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [3686] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [3691] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [3696] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [3701] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [3706] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [3711] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [3716] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [3721] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [3726] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [3731] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [3736] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [3741] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [3746] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [3751] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [3756] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [3761] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [3766] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [3771] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [3776] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [3781] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [3786] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [3791] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [3796] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [3801] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [3806] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [3811] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [3816] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [3821] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [3826] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [3831] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [3836] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [3841] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [3846] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [3851] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [3856] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [3861] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [3866] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [3871] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [3876] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [3881] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [3886] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [3891] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [3896] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [3901] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [3906] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [3911] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [3916] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [3921] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [3926] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [3931] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [3936] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [3941] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [3946] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [3951] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [3956] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [3961] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [3966] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [3971] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [3976] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [3981] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [3986] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [3991] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [3996] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [4001] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [4006] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [4011] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [4016] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [4021] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [4026] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [4031] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [4036] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [4041] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [4046] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [4051] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [4056] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [4061] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [4066] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [4071] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [4076] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [4081] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [4086] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [4091] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [4096] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [4101] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [4106] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [4111] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [4116] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [4121] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [4126] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [4131] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [4136] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [4141] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [4146] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [4151] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [4156] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [4161] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [4166] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [4171] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [4176] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [4181] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [4186] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [4191] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [4196] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [4201] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [4206] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [4211] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [4216] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [4221] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [4226] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [4231] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [4236] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [4241] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [4246] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [4251] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [4256] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [4261] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [4266] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [4271] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [4276] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [4281] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [4286] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [4291] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [4296] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [4301] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [4306] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [4311] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [4316] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [4321] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [4326] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [4331] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [4336] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [4341] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [4346] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [4351] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [4356] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [4361] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [4366] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [4371] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [4376] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [4381] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [4386] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [4391] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [4396] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [4401] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [4406] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [4411] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [4416] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [4421] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [4426] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [4431] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [4436] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [4441] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [4446] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [4451] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [4456] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [4461] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [4466] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [4471] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [4476] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [4481] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [4486] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [4491] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [4496] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [4501] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [4506] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [4511] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [4516] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [4521] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [4526] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [4531] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [4536] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [4541] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [4546] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [4551] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [4556] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [4561] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [4566] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [4571] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [4576] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [4581] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [4586] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [4591] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [4596] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [4601] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [4606] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [4611] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [4616] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [4621] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [4626] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [4631] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [4636] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [4641] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [4646] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [4651] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [4656] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [4661] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [4666] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [4671] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [4676] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [4681] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [4686] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [4691] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [4696] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [4701] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [4706] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [4711] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [4716] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [4721] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [4726] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [4731] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [4736] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [4741] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [4746] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [4751] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [4756] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [4761] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [4766] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [4771] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [4776] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [4781] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [4786] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [4791] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [4796] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [4801] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [4806] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [4811] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [4816] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [4821] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [4826] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [4831] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [4836] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [4841] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [4846] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [4851] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [4856] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [4861] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [4866] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [4871] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [4876] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [4881] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [4886] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [4891] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [4896] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [4901] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [4906] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [4911] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [4916] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [4921] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [4926] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [4931] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [4936] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [4941] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [4946] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [4951] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [4956] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [4961] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [4966] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [4971] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [4976] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [4981] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [4986] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [4991] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [4996] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [5001] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [5006] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [5011] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [5016] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [5021] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [5026] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [5031] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [5036] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [5041] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [5046] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [5051] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [5056] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [5061] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [5066] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [5071] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [5076] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [5081] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [5086] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [5091] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [5096] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [5101] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [5106] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [5111] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [5116] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [5121] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [5126] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [5131] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [5136] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [5141] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [5146] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [5151] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [5156] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [5161] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [5166] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [5171] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [5176] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [5181] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [5186] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [5191] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [5196] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [5201] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [5206] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [5211] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [5216] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [5221] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [5226] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [5231] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [5236] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [5241] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [5246] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [5251] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [5256] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [5261] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [5266] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [5271] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [5276] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [5281] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [5286] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [5291] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [5296] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [5301] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [5306] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [5311] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [5316] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [5321] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [5326] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [5331] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [5336] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [5341] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [5346] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [5351] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [5356] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [5361] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [5366] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [5371] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [5376] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [5381] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [5386] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [5391] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [5396] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [5401] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [5406] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [5411] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [5416] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [5421] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [5426] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [5431] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [5436] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [5441] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [5446] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [5451] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [5456] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [5461] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [5466] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [5471] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [5476] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [5481] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [5486] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [5491] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [5496] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [5501] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [5506] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [5511] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [5516] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [5521] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [5526] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [5531] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [5536] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [5541] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [5546] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [5551] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [5556] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [5561] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [5566] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [5571] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [5576] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [5581] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [5586] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [5591] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [5596] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [5601] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [5606] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [5611] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [5616] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [5621] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [5626] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [5631] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [5636] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [5641] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [5646] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [5651] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [5656] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [5661] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [5666] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [5671] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [5676] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [5681] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [5686] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [5691] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [5696] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [5701] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [5706] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [5711] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [5716] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [5721] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [5726] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [5731] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [5736] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [5741] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [5746] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [5751] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [5756] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [5761] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [5766] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [5771] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [5776] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [5781] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [5786] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [5791] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [5796] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [5801] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [5806] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [5811] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [5816] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [5821] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [5826] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [5831] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [5836] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [5841] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [5846] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [5851] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [5856] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [5861] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [5866] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [5871] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [5876] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [5881] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [5886] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [5891] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [5896] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [5901] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [5906] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [5911] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [5916] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [5921] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [5926] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [5931] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [5936] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [5941] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [5946] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [5951] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [5956] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [5961] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [5966] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [5971] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [5976] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [5981] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [5986] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [5991] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [5996] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [6001] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [6006] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [6011] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [6016] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [6021] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [6026] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [6031] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [6036] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [6041] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [6046] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [6051] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [6056] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [6061] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [6066] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [6071] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [6076] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [6081] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [6086] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [6091] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [6096] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [6101] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [6106] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [6111] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [6116] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [6121] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [6126] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [6131] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [6136] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [6141] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [6146] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [6151] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [6156] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [6161] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [6166] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [6171] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [6176] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [6181] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [6186] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [6191] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [6196] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [6201] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [6206] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [6211] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [6216] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [6221] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [6226] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [6231] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [6236] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [6241] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [6246] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [6251] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [6256] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [6261] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [6266] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [6271] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [6276] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [6281] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [6286] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [6291] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [6296] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [6301] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [6306] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [6311] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [6316] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [6321] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [6326] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [6331] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [6336] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [6341] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [6346] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [6351] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [6356] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [6361] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [6366] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [6371] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [6376] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [6381] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [6386] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [6391] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [6396] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [6401] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [6406] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [6411] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [6416] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [6421] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [6426] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [6431] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [6436] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [6441] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [6446] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [6451] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [6456] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [6461] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [6466] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [6471] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [6476] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [6481] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [6486] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [6491] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [6496] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [6501] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [6506] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [6511] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [6516] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [6521] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [6526] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [6531] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [6536] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [6541] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [6546] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [6551] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [6556] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [6561] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [6566] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [6571] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [6576] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [6581] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [6586] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [6591] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [6596] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [6601] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [6606] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [6611] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [6616] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [6621] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [6626] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [6631] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [6636] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [6641] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [6646] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [6651] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [6656] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [6661] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [6666] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [6671] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [6676] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [6681] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [6686] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [6691] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [6696] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [6701] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [6706] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [6711] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [6716] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [6721] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [6726] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [6731] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [6736] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [6741] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [6746] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [6751] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [6756] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [6761] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [6766] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [6771] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [6776] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [6781] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [6786] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [6791] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [6796] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [6801] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [6806] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [6811] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [6816] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [6821] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [6826] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [6831] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [6836] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [6841] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [6846] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [6851] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [6856] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [6861] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [6866] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [6871] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [6876] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [6881] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [6886] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [6891] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [6896] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [6901] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [6906] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [6911] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [6916] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [6921] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [6926] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [6931] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [6936] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [6941] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [6946] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [6951] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [6956] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [6961] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [6966] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [6971] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [6976] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [6981] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [6986] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [6991] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [6996] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [7001] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [7006] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [7011] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [7016] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [7021] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [7026] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [7031] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [7036] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [7041] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [7046] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [7051] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [7056] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [7061] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [7066] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [7071] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [7076] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [7081] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [7086] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [7091] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [7096] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [7101] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [7106] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [7111] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [7116] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [7121] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [7126] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [7131] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [7136] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [7141] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [7146] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [7151] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [7156] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [7161] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [7166] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [7171] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [7176] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [7181] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [7186] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [7191] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [7196] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [7201] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [7206] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [7211] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [7216] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [7221] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [7226] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [7231] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [7236] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [7241] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [7246] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [7251] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [7256] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [7261] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [7266] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [7271] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [7276] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [7281] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [7286] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [7291] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [7296] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [7301] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [7306] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [7311] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [7316] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [7321] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [7326] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [7331] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [7336] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [7341] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [7346] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [7351] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [7356] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [7361] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [7366] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [7371] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [7376] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [7381] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [7386] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [7391] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [7396] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [7401] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [7406] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [7411] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [7416] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [7421] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [7426] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [7431] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [7436] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [7441] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [7446] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [7451] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [7456] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [7461] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [7466] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [7471] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [7476] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [7481] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [7486] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [7491] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [7496] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [7501] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [7506] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [7511] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [7516] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [7521] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [7526] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [7531] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [7536] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [7541] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [7546] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [7551] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [7556] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [7561] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [7566] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [7571] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [7576] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [7581] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"  
    ## [7586] "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"  
    ## [7591] "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"  
    ## [7596] "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"
    ## [7601] "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"   "saf_t_11"  
    ## [7606] "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"   "com_s_11"  
    ## [7611] "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11" "eng_tot_11"
    ## [7616] "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"   "aca_p_11"  
    ## [7621] "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"   "saf_s_11"  
    ## [7626] "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11" "com_tot_11"
    ## [7631] "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"   "eng_p_11"  
    ## [7636] "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"   "aca_t_11"  
    ## [7641] "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"   "saf_tot_11"
    ## [7646] "com_tot_11" "eng_tot_11" "aca_tot_11" "saf_p_11"   "com_p_11"  
    ## [7651] "eng_p_11"   "aca_p_11"   "saf_t_11"   "com_t_11"   "eng_t_11"  
    ## [7656] "aca_t_11"   "saf_s_11"   "com_s_11"   "eng_s_11"   "aca_s_11"  
    ## [7661] "saf_tot_11" "com_tot_11" "eng_tot_11" "aca_tot_11"

``` r
combined_survey_longer <- combined_survey_longer %>% mutate(response_type = str_sub(combined_survey_longer$survey_question,4, 6)) %>% mutate(question = str_sub(combined_survey_longer$survey_question,1, 3)) 
```

We want the response type to correspond to who gave the response
(parent, teacher, student) thus changing `response_type` to values such
as “parent”, “teacher”, and “student”

``` r
combined_survey_longer <- combined_survey_longer %>% mutate(response_type = if_else(response_type == "_p_", "parent",
      if_else(response_type == "_t_", "teacher",
             if_else(response_type == "_s_", "student",
                    if_else(response_type == "_tot_", "total", "NA")))))
```

Using a boxplot we will visualize these reponses

``` r
combined_survey_longer %>% 
  filter(response_type != "total") %>%
  ggplot() +
  aes(x = question, y = score, fill = response_type) +
  geom_boxplot()
```

    ## Warning: Removed 1688 rows containing non-finite values (stat_boxplot).

![](/images/nyc-school-files/unnamed-chunk-17-1.png)

Parent responses seem to have a higher score for safety compared to
students where as students score academics lower compared to parents and
teachers.
