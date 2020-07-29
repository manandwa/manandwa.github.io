---
title: Exploring NYC School Perceptions
date: 2020-07-29
tags: [NYC Schools, ]
excerpt: "Exploring School Perceptions in NYC"
mathjax: "true"
---

In this project I will analyze data from the New York City school department survey to answer the following questions:

1. Do student, teacher and parent perceptions of NYC school quality appear to be related to demographic and academic success metrics (such as socieoenomic factors and test scores)?

2. Do students, teachers and parents have similar perceptions of NYC school quallity?

The data was collected in 2011 and is publicly available for access.  The data can be accessed [here](https://data.cityofnewyork.us/Education/2011-NYC-School-Survey/mnz3-dyi8)

We will first load all the packages needed for analysis
```{r}
library(readr)
library(dplyr)
library(stringr)
library(purrr)
library(tidyr)
library(ggplot2)
```

We will now import the data
```{r}
combined <- read_csv("combined.csv")
survey <- read_tsv("masterfile11_gened_final.txt")
survey_d75 <- read_tsv("masterfile11_d75_final.txt")
```

We are only interested in high schools (the schooltype column is helpful here) We will also use the `spec()` function get the full column names from each dataframe
```{r}
spec(combined)
```

```{r}
spec(survey_d75)
```
According to the data dictionary only grades 6-12 particiapted in the survey but we are only interested in high schools so we will filter `survey` to get high schools and academic data

```{r}
survey_selected <- survey %>% filter(schooltype == "High School") %>% select(dbn:aca_tot_11)
```

Filter `survey_d75` to get only the columns we need
```{r}
survey_d75_selected <- survey_d75 %>% select(dbn:aca_tot_11)
```

We will now combine both `survey_selected` and `survey_d75_selected` into a single data frame
```{r}
survey_combined <- survey_selected %>% bind_rows(survey_d75_selected)
```

We used the `bind_rows()` function to combine the dataframes

Since the `combined` dataframe as the dbn as DBN we will need to fix this in the `survey_combined` dataframe

```{r}
survey_combined <- survey_combined %>% rename(DBN = dbn)
```

As we are interested in the results listed in the combined dataframe we will use `left_join()` which will match what is in the `survey_combined` dataframe to what is listed in the `combined` dataframe

We will also use DBN as the key we are joining on

```{r}
combined_survey <- combined %>% left_join(survey_combined, by = "DBN")
```

We will use a correlation matrix to answer the first question we had for this data



```{r}
cor_mat <- combined_survey %>% select(avg_sat_score, saf_p_11:aca_tot_11) %>% cor(use = "pairwise.complete.obs")
cor_tibble <- cor_mat %>% as_tibble(rownames = "variable")
```

Look through the `cor_tibble` for strong correlations (greater than 0.25 or less than -0.25) with `avg_sat_score`

```{r}
strong_correlations <- cor_tibble %>% select(variable, avg_sat_score) %>% filter(avg_sat_score > 0.25 | avg_sat_score < -0.25)
```

We will now visualize the correlations using a scatter plot
```{r}
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

We will have to reshape the data to see the responses from students, teachers and parents

```{r}
combined_survey_longer <- combined_survey %>% pivot_longer(cols = c(saf_p_11:aca_tot_11), names_to = "survey_question", values_to = "score")
```

We will need two new variables `response_type` which indicates if students, teachers or parents are the respondents and `question` which is the question being asked.  Both of these will come from the newly created `survey_question` variable

```{r}
combined_survey_longer$survey_question
```

```{r}
combined_survey_longer <- combined_survey_longer %>% mutate(response_type = str_sub(combined_survey_longer$survey_question,4, 6)) %>% mutate(question = str_sub(combined_survey_longer$survey_question,1, 3)) 
```

We want the response type to correspond to who gave the response (parent, teacher, student) thus changing `response_type` to values such as "parent", "teacher", and "student"

```{r}
combined_survey_longer <- combined_survey_longer %>% mutate(response_type = if_else(response_type == "_p_", "parent",
      if_else(response_type == "_t_", "teacher",
             if_else(response_type == "_s_", "student",
                    if_else(response_type == "_tot_", "total", "NA")))))
```

Using a boxplot we will visualize these reponses
```{r}
combined_survey_longer %>% 
  filter(response_type != "total") %>%
  ggplot() +
  aes(x = question, y = score, fill = response_type) +
  geom_boxplot()
```


Parent responses seem to have a higher score for safety compared to students where as students score academics lower compared to parents and teachers.
