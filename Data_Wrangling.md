---
title: "Data wrangling"
sidebar: toc
category: getting-started
order: 1
---

## I'm a cowboy!

* 80% of data analysis is spent on the process of cleaning and preparing the data.
* `dplyr` and `tidyr` "package" the data processing tasks.
 
## The `%>%` operator

* `dplyr` and `tidyr` packages make use of the pipe operator `%>%` (from the `magrittr` package).
* The `%>%` operator strings multiple functions together (forwards a value, or the result of an expression, into the next function call/expression). 
* The `%>%` operator allows to flow from data manipulation tasks straight into vizualization functions.
 
**Example:** Cereal dataset

* `Name`: Name of cereal
* `mfr`: Manufacturer of cereal (A = American Home Food Products; G = General Mills; K = Kelloggs; N = Nabisco; P = Post; Q = Quaker Oats; R = Ralston Purina)
* `type`: cold; hot
* `calories`: calories per serving
* `protein`: grams of protein
* `fat`: grams of fat
* `sodium`: milligrams of sodium
* `fiber`: grams of dietary fiber
* `carbo`: grams of complex carbohydrates
* `sugars`: grams of sugars
* `potass`: milligrams of potassium
* `vitamins`: vitamins and minerals - 0, 25, or 100, indicating the typical percentage of FDA recommended
* `shelf`: display shelf (1, 2, or 3, counting from the floor)
* `weight`: weight in ounces of one serving
* `cups`: number of cups in one serving
* `rating`: a rating of the cereals (Possibly from Consumer Reports?)

```{r}
cereals <- read.csv("cereal.csv") # Read in the cereal data set
str(cereals)                      # investigating the structure of the cereal data set
```

```{r}
dim(cereals)           # check the dimension (number of observations and number of columns)
```

## The `dplyr` package

```{r}
library(dplyr)         # load the dplyr package into your current worksession          
```

Calculating the mean
```{r}
mean(cereals$rating)   # apply the function mean() on ratings
```
is equal to
```{r}
cereals$rating %>%     # forward the ratings into the function mean()
    mean()
```

`round()` the result of `mean()`
```{r}
round(mean(cereals$rating),2)
```
is equal to
```{r}
cereals$rating %>%      # forward the ratings into the function mean()
    mean() %>%          # forward the result of the function mean() into the function round()
      round(2)
```
 
### `select()`

- Extracting existing variables ("similar" to **subsetting**)

```{r}
out <- cereals %>% 
          select(calories,protein,fat,fiber,carbo,sugars)
```

- Unselecting certain variables:

```{r}
out <- cereals %>% 
          select(-name,-mfr,-type,-sodium,-potass,-vitamins,-shelf,-weight,-cups,-rating)
```

- Selecting a set of variables:

```{r}
out <- cereals %>% 
           select(calories:fat,fiber:sugars)
```

**Further options**

* `contains()`: Select columns whose name contains a character string.
* `ends_with()`: Select columns whose name ends with a string.
* `everything()`: Select every column.
* `matches()`: Select columns whose name matches a regular expression.
* `num_range()`: Select columns named x1, x2, x3, x4, x5.
* `one_of()`: Select columns whose names are in a group of names.
* `starts_with()`: select columns whose name starts with a character string.

### `filter()` 

- Extracting existing observations using "logicals":

```{r}
sub.out <- cereals %>% 
             filter(calories >= 100)
```

```{r}
sub.out <- cereals %>% 
             filter(calories >= 100, type == "H")
```

### `mutate()`

- Data transformation (deriving new variables from existing variables)

```{r}
mutate.out <- cereals %>% 
                 mutate(CP = calories/cups) # calculate calories per cups
head(mutate.out)
```

###  `summarise()`

- The "ultimate" goal: Summarizing the data and obtaining summary tables:

```{r}
cereals %>% 
    summarise(Mean_calories = mean(calories))
```
```{r}
cereals %>% 
     summarise(Mean = mean(calories, na.rm=TRUE), # calculating the mean of calories
               Var = var(calories, na.rm=TRUE),   # calculating the variance of calories
               SD = sd(calories, na.rm=TRUE),     # calculating the standard deviation of calories
               N = n())                           # counting the number of observations
# note: na.rm indicates what to do with missing values (na.rm = TRUE states remove missing values)
```

###  `group_by()`

- Grouping data by categorical variables and comparing data summaries at multiple levels

```{r}
cereals %>%
        group_by(type)%>% 
        summarise(Mean_calories = mean(calories, na.rm=TRUE),
                  SD_calories = sd(calories, na.rm=TRUE))
```


## The `tidyr` package

Used for reshaping/reformatting the layout of a dataset

```{r}
library(tidyr)    # load the `tidyr` package into your current worksession
```

### `spread()` and `gather()`

* The `spread()` function generates multiple columns (makes "long" data wider)

```{r}
data %>% 
   spread(key, value, fill = NA, convert = FALSE)
```

* `data`: The data to be reformatted.
* `key`: The column you want to split apart.
* `value`: The column you want to use to populate the new columns (the value column we just created in the spread step).
* `fill`: What to substitute if there are combinations that don’t exist.
* `convert`: Whether to fix incorrect data types as it goes.

**Example: ** Bike sharing in Washington D.C.  
```{r}
day <- read.csv("day.csv") # Read in the full daily bike sharing dataset
head(day)
```

```{r}
out <- day %>% 
        spread(season, cnt)
out
```

* The `gather()` function will take multiple columns and collapse them (makes "wide" data longer).

```{r}
data %>% gather(key, value, ..., na.rm = FALSE, convert = FALSE)
```

* `data`: The data to be modified.
* `key`: The name of the new "naming"" variable.
* `value`: The name of the new "result"" variable.
* `na.rm`: Whether missing values are removed.
* `convert`: Convert anything that seems like it should be in another format to that other format, e.g. numeric to numeric.

### The `unite()` & `separate()` commands

* The `separate()` function splits a single column into multiple columns.

```{r}
data %>% separate(col, into, sep = " ", remove = TRUE, convert = FALSE)
```

* `data`: The data to be modified.
* `col`: The column name representing current variable.
* `into`: The name of the new "result"" variable.
* `sep`:  How to separate current variable (char, num, or symbol)
* `remove`: if TRUE, remove input column from output.
* `convert`: Convert anything that seems like it should be in another format to that other format, e.g. numeric to numeric.

The day data set has three values stored in the `dteday` column (year, month, day). In order to get these values into different columns, the `separate()` function can be used.

```{r}
out <- day %>% separate(dteday, c("year", "month", "day"), sep = "-")
out
```

* The `unite()` function combines multiple columns into a single column.

```{r}
data %>% unite(col, ..., sep = " ", remove = TRUE)
```

* `data`: The data to be modified.
* `col`: The column name of the new column.
* `sep`:  How to separate current variable (char, num, or symbol)
* `remove`: if TRUE, remove input column from output.

## Ressources

* The R for Data Science section on [Spreading and gathering](http://r4ds.had.co.nz/tidy-data.html#spreading-and-gathering)

* The Data Science With R section on [Data tidying](http://garrettgman.github.io/tidying/)
