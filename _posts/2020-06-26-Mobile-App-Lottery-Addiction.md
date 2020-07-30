---
title: Mobile App for Lottery Addiction
date: 2020-06-26
tags: [Permutations, Combinations, Probabilities]
excerpt: "Lottery Addiction App"
mathjax: "true"
---

# Introduction

This project is design a mobile app that guides lottery addicts through exercises that better estimates their winning chances.  The goal of the app is help lottery addicts realize that buying too many tickets will not improve their chances of winning.  For this project we are using the [6/49 lottery](https://en.wikipedia.org/wiki/Lotto_6/49) as the basis.  The app will help us answer the following questions:

1. What is the probability of winning the big prize with a single ticket?
2. What is the probability of winning the big prize if we play 40 different tickets? (or any number of tickets)
3. What is the probability of having at least five (or four, or three, or two) winning numbers?

# Core functions

We will need two functions the factorial and combination functions

formula for factorial function is $$n! = n * (n - 1) * (n - 2) * \dots * 2 * 1$$

formula for combination function is $$C^n_k = \binom{n}{k}= \frac{n!}{k!(n - k)!}$$

for factorial by definition when $$n = 0$$ or $$n = 1$$ it is $$1$$

```{r}
factorial <- function(n) {
  final_product <- 1
  for (i in 1:n) {
    final_product <- final_product * i
  }
  return(final_product)
}
combinations <- function(n, k) {
  numerator <- factorial(n)
  denomimator <- factorial(k) * factorial(n - k)
  return(numerator / denomimator)
}
```

# One ticket probability
As there are 49 total numbers ranging from 1 to 49 with each ticket having a combination of six different numbers

We will create a function that will take an input of six different numbers (a single ticket)
It will calculate the probability of winning the big prize and output it in a user readable format

Theory
$$Total = \frac{49!}{6!*(49 - 6)!} = \frac{49!}{6!43!}$$

$$P(Win) = \frac{1}{Total}$$

Total is the total number of combinations that we can get using 6 different numbers out of 49 numbers

```{r}
one_ticket_probability <- function(numbers) {
  total <- combinations(49, 6)
  probability <- (1 / total) * 100
  user_probability <- sprintf("%1.9f", probability)
  user_output <- paste("You have a ", user_probability, "% chance of winning the big prize.", sep = "")
  return(user_output)
}
one_ticket_probability(c(1, 2, 3, 4, 5, 6))
```

# Historial Data Check for Canadian Lottery

The goal for the first version of the app is for users to compare their ticket to past winning combinations to the lottery in Canada.  Having this functionality allows users to determine if they would have ever won by now.

*Note: We load the tidyverse package because it is the one package that does what we need*

```{r, message = FALSE, warning = FALSE}
#the message false and warning false removes the messages and warnings we get when loading the tidyverse package
library(tidyverse)
lottery <- read.csv("649.csv")
# get the overall rows and columns
print(dim(lottery))
```
```{r}
head(lottery, 3)
```

```{r}
tail(lottery, 3)
```

This shows that our data goes from 1982 to 2018

# A New Data Structure

We will perform an exercise that shows us how to use `pmap` function to use multiple vectors in a single function.
```{r}
data1 <- c(1, 3, 5)
data2 <- c(2, 4, 6)
data3 <- c(8, 9, 7)
unnamed_list <- list(data1, data2, data3)
```

```{r}
first_vector <- unnamed_list[[1]]
named_list <- list(first = data1, second = data2, third = data3)
first_item_sum <- named_list$first[1] + named_list$second[1] + named_list$third[1]
```

# Using pmap

We will perform a simular calculation using averages and the same vectors
```{r}
data1 <- c(1, 3, 5)
data2 <- c(2, 4, 6)
data3 <- c(8, 9, 7)
data_list <- list(data1, data2, data3)
averages <- pmap(data_list, function(x, y , z) (x + y + z)/3)
first_average <- unlist(averages)[1]
```

# Function for Historical Data Check

The engineering team told us that the app works in the following way:

* A user inputs six different numbers from 1 to 49
* The inputs will come in as an R vector and will serve as an input to the function

The function that we will create does the following:

1. Takes in the input vector (user input) and returns how many times that combination occurred in the Canadian Lottery datasett
2. Prints out the probability of winning the big prize in the next drawing with that combination

```{r}
u <- lottery$`NUMBER.DRAWN.1`
v <- lottery$`NUMBER.DRAWN.2`
w <- lottery$`NUMBER.DRAWN.3`
x <- lottery$`NUMBER.DRAWN.4`
y <- lottery$`NUMBER.DRAWN.5`
z <- lottery$`NUMBER.DRAWN.6`
loto_list <- list(u, v, w, x, y , z)
historical_loto_data <- pmap(loto_list, .f <- function(u, v, w, x, y, z) c(u, v, w, x, y, z))
```

```{r, message = FALSE, warning = FALSE}
library(sets)
check_historical_occurence <- function(user_ticket, historical_data = historical_loto_data) {
  historical_match <- map(historical_data, function(x) setequal(x, user_ticket))
  past_matches <- sum(unlist(historical_match))
  message <- paste("The combination you entered has appeared ", past_matches, "times in the past. ","You have a 0.000007151% chance of winning the big prize.")
  return(message)
}
# We used the message from one_ticket_probability because this is only a single ticket
check_historical_occurence(c(3, 12, 11, 14, 41, 43))
```
```{r}
check_historical_occurence(c(1, 2, 3, 4, 5, 6))
```
# Multi-ticket probability

So far our functions only cover a single ticket but this is not usual case.  Lottery addicts play mulitple tickets thinking this increases chances of winning.  We will create a function that estimates the probability of multiple tickets winning

```{r}
multi_ticket_probability <- function(n) {
  total <- combinations(49, 6)
  probability <- (n / total) * 100
  user_probability <- sprintf("%1.9f", probability)
  user_output <- paste("You have a ", user_probability, "% chance of winning the big prize.", sep = "")
  return(user_output)
}
test_vector <- c(1, 10, 100, 10000, 1000000, 6991908, 13983816)
for (n in test_vector) {
  print(paste("For ", n, " tickets, ", multi_ticket_probability(n), sep=""))
}
```

This shows that once you hit 13983816 tickets you will win the big prize but this also feeds into lottery addicts thinking into buying so many tickets

We did this step because our target population **will** buy multiple tickets

# Less Winning Numbers

We will now calculate the probability of the following:

* Three winning numbers
* Four winning numbers
* Five winning numbers

1. We will need the number of sucessful outcomes (3, 4, 5)
2. We will also need the total number of outcomes (combinations)
3. Our sucess ratio is the following: $$C^6_n * C^\left(49 - n\right)_\left(6 - n\right)$$
3. Our probability will be the following: $$\frac{success}{total}$$
4. Finallly we print out the probability

```{r}
probability_less_6 <- function(n) {
  n_combo_ticket <- combinations(6, n) # winning numbers ranging from 3 to 5
  n_combo_remaining <- combinations(49 - n, 6 - n) # remaining
  success <- n_combo_ticket * n_combo_remaining
  total <- combinations(49, 6)
  less_6_prob <- success / total
  less_6_prob_user_output <-  paste("You have a ", less_6_prob, "% chance of winning the big prize.", sep = "")
  return(less_6_prob_user_output)
}
```

```{r}
test_winning <- c(3,4,5)
for (n in test_winning) {
  print(paste("For ", n, " winning numbers, ", probability_less_6(n), sep=""))
}
```

There is a lower chance as the number of winning numbers increase to the point of having 5 winning numbers is 0
