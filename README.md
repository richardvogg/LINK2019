# LINK2019

# Data Manipulation with R

## 30 Minutes: What are we going to learn?

- Data manipulation made easy (The 5 functions of `dplyr`)
- Creating readable code using the pipe `%>%`
- Application on a dataset on earthquakes in Chile
- Hands-on: Try directly out what you learn

## The dataset

```{r dataset2,echo=TRUE,eval=FALSE}
data <- read.csv("Earthquakes Chile.csv",stringsAsFactors = F,skip=9)
head(data)
```

## The 5 functions of `dplyr`

- data manipulation package by Hadley Wickham
- provides simple functions helping with most common data manipulation tasks
- very intuitive use
- common syntax: first argument for each function is a dataframe, output is a new dataframe
- Installation of the package

```{r install,echo=TRUE, warning=FALSE,eval=FALSE}
install.packages("dplyr")
```

- Working with the package

```{r lib,echo=TRUE,eval=TRUE, warning=FALSE,message=FALSE}
library(dplyr)
```


## 1 - `select`

```{r select,echo=TRUE,eval=FALSE}
select(data,place,mag)
```

## 2 - `filter`

```{r filter,echo=TRUE,eval=FALSE}
filter(data,mag>7.6)
```


## 3 - `arrange`

```{r arrange,echo=TRUE,eval=FALSE}
arrange(data,desc(mag))
```

## 4 - `mutate`

```{r mutate,echo=TRUE,eval=FALSE}
data <- mutate(data,year=substr(time,1,4))
```

## 5 - `group_by` / `summarize`

```{r groupby,echo=TRUE,eval=FALSE}
summarize(group_by(data,year),number_earthquakes=n())
```

## Readability

Example: Find the square-root of each element in a vector, round it to two digits and find the maximum among these elements.

```{r example,warning=FALSE,message=FALSE,echo=TRUE}
a <- c(1,2,5,6,3,4)

max(round(sqrt(a),2))

a %>% sqrt() %>% round(2) %>% max()
```

## The pipe `%>%`

- Origin: package "magrittr" by Stefan Milton Bache
- Aim: Make code more readable, maintainable, easier to debug
- Basic piping:
    - `x %>% f` is equivalent to `f(x)`
    - `x %>% f(y)` is equivalent to `f(x, y)`
    - `x %>% f %>% g %>% h` is equivalent to `h(g(f(x)))`



## Extract the country from the place

```{r b,echo=TRUE,eval=FALSE}
data$place
```

or

```{r b2,echo=TRUE,eval=FALSE}
data %>% select(place)
```

## Split the string

```{r c,echo=TRUE}
data$place %>%
  strsplit(split=", ")
```

## Get the country name

```{r d,echo=TRUE}
data$place %>%
  strsplit(split=", ") %>%
  lapply(function(x) x <- x[2])
```

## Convert the list to a vector

```{r e, echo = TRUE}
data$place %>%
  strsplit(split=", ") %>% 
  lapply(function(x) x <- x[2]) %>%
  unlist()
```


## Combining everything: Top 10 regions with most earthquakes?

```{r pressure2,echo=FALSE,eval=TRUE}
data %>%
  select(mag,place) %>%
  mutate(country=place %>% strsplit(split=", ") %>% 
           lapply(function(x) x <- x[2]) %>%
           unlist()
        ) %>%
  filter(country=="Chile") %>%
  mutate(region=place %>% strsplit(split=" of |, ") %>% 
           lapply(function(x) x <- x[length(x)-1]) %>%
           unlist()
        ) %>%
  group_by(region) %>%
  summarize(number_earthquakes=n(),strongest=max(mag)) %>%
  arrange(desc(number_earthquakes)) %>%
  data.frame() %>%
  top_n(10,number_earthquakes)
```

Task: List the 10 strongest earthquakes happening in and around Valparaiso 
(Bonus: Display the year of these events).

## Solution

Strongest earthquakes in Valparaiso

```{r sol2,echo=TRUE,eval=TRUE}
data %>%
  select(mag,place) %>%
  mutate(country=place %>% strsplit(split=", ") %>% 
           lapply(function(x) x <- x[2]) %>% unlist()) %>%
  filter(country=="Chile") %>%
  mutate(region=place %>% strsplit(split=" of |, ") %>% 
           lapply(function(x) x <- x[length(x)-1]) %>% unlist()) %>%
  filter(region=="Valparaiso") %>%
  arrange(desc(mag)) %>%
  data.frame() %>%
  top_n(10,mag)
```


## Solution

Strongest earthquakes in Valparaiso with year

```{r sol3,echo=TRUE,eval=TRUE}
data %>%
  select(mag,place,time) %>%
  mutate(year=substr(time,1,4)) %>%
  select(-time) %>%
  mutate(country=place %>% strsplit(split=", ") %>% 
           lapply(function(x) x <- x[2]) %>% unlist()) %>%
  filter(country=="Chile") %>%
  mutate(region=place %>% strsplit(split=" of |, ") %>% 
           lapply(function(x) x <- x[length(x)-1]) %>% unlist()) %>%
  filter(region=="Valparaiso") %>%
  arrange(desc(mag)) %>%
  data.frame() %>%
  top_n(10,mag)
```

## Want to know more?

- The pipe `%>%`
    - https://github.com/tidyverse/magrittr
    - type `install.packages("magrittr")` and then `browseVignettes("magrittr")` in your RStudio console 
- Data manipulation with `dplyr`
    - https://github.com/tidyverse/dplyr
    - type `install.packages("dplyr")` and then `browseVignettes("dplyr")` in your RStudio console
- Earthquake data
    - https://earthquake.usgs.gov/earthquakes/search/
