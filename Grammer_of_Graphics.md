---
title: "Grammar of Graphics"
sidebar: toc
category: getting-started
order: 1
---

Graphs are forms of communication:

- **Analysis**: describing data, detecting patterns and trends (default settings often give reasonable results)
- **Communication**: illustrating a conclusion (should be directly readable and often require changing graph titles, axis, labels, colors, symbols, adding legends, ...)

One of the main advantages of R is its strong graphic capabilities!

# Standard graphs

**Example**: Cereals

```{r}
cereals <- read.csv("cereal.csv")  # read-in the cereals data set
```

E.g.: scatterplots, boxplots, histograms, barplots, piecharts...

```{r}
hist(cereals$rating)    # Histogramm of ratings
```
```{r}
plot(calories ~ rating, data = cereals)  # Scatterplot of calories against rating
```
```{r}
boxplot(rating ~ mfr, data = cereals)    # Boxplot of rating vs. manufacturer
```

## Customizing standard graphs

Controlling graphic parameters: colors, point symbols, line styles, labels and titles.

### Histogramm

 * changing bar colors and the title
 
```{r}
hist(cereals$rating, col="gray", main = "Histogramm of cerals ratings")
```

* changing x axis labels

```{r}
hist(cereals$rating, col="gray", main = "Histogramm of cerals ratings", xlab = "Rating")
```

* changing y axis scale

```{r}
hist(cereals$rating, col="gray", main = "Histogramm of cerals ratings", xlab = "Rating", freq = FALSE)
```

### Scatterplot

* changing plotting symbol color

```{r}
plot(calories ~ rating, data = cereals)
```

* adding a title

```{r}
plot(calories ~ rating, data = cereals, main = "Scatterplot of cereals")
```

* changing the x and y range (limits: from = ?, to = ?)

```{r}
plot(calories ~ rating, data = cereals, main = "Scatterplot of cereals", xlim = c(0,100), ylim = c(0,200))
```

* changing the plotting symbol size

```{r}
plot(calories ~ rating, data = cereals, main = "Scatterplot of cereals", xlim = c(0,100), ylim = c(0,200), cex = 2)
```

* changing the plotting symbol (plotting character)

```{r}
plot(calories ~ rating, data = cereals, main = "Scatterplot of cereals", xlim=c(0,100), ylim=c(0,200), cex = 2, pch = 19)
```

Boxplot of rating vs. manufacturer
```{r}
# changing the box color
boxplot(rating ~ mfr, data = cereals, col = "gray")
# changing the box colors
boxplot(rating ~ mfr, data = cereals, col = c("gray","dodgerblue"))
# changing the tick labels (A: American Home Food Products; G: General Mills; K: Kelloggs; N: Nabisco; P: Post; Q: Quaker Oats; R: Ralston Purina)
boxplot(rating ~ mfr, data = cereals, col = c("gray","dodgerblue"), names = c("A.H.F.P.","Mills","Kellogs","Nabisco","Post","Quaker", "Purina"))
```

### Colors in R

* R uses **hexadecimal** (a base-16 number system) to represent colors (translates RGB, HSV, HCL color models to hex).

* Built-in 657 named colors:

```{r}
colors()
```
* Useful look-up [latex color table](http://latexcolor.com)


## Annotating standard graphs

Adding text to a scatterplot
```{r}
plot(calories ~ rating, data = cereals, col="gray", main = "Scatterplot of cereals", xlim = c(0,100), ylim = c(0,200), cex=2, pch = 19)
text(cereals$rating, cereals$calories, labels=cereals$name)
```

Adding fitted lines to a scatterplot
```{r}
plot(calories ~ rating, data = cereals, col = "gray", main = "Scatterplot of cereals", xlim = c(0,100), ylim = c(0,200), cex = 2, pch = 19)
abline(lm(calories ~ rating, data = cereals))
```

Adding text to the fitted line in a scatterplot
```{r}
plot(calories ~ rating, data = cereals, col="gray", main = "Scatterplot of cereals", xlim = c(0,100), ylim = c(0,200), cex = 2, pch=19)
mod <- lm(calories ~ rating, data = cereals)
abline(coef(mod))
text(20,150,paste("y =",round(coef(mod)[1],2),"beta_0 +",round(coef(mod)[2],2),"beta_1"))
```

Adding data points the boxplot of rating vs. manufacturer
```{r}
boxplot(rating ~ mfr, data = cereals, col = rainbow(7), names = c("A.H.F.P.","Mills","Kellogs","Nabisco","Post","Quaker", "Purina"))
points(rating ~ mfr, data = cereals, pch = 19)
```

Adding a legend to the boxplot of rating vs. manufacturer
```{r}
legend("topleft", legend=c("A.H.F.P.","Mills","Kellogs","Nabisco","Post","Quaker", "Purina"), bty="n", fill=rainbow(7))
```

Further annotatins
```{r}
par(las=2) # make label text perpendicular to axis
boxplot(rating ~ mfr, data = cereals, col=rainbow(7), names = c("A.H.F.P.","Mills","Kellogs","Nabisco","Post","Quaker", "Purina"))
legend("topleft", legend=c("A.H.F.P.","Mills","Kellogs","Nabisco","Post","Quaker", "Purina"), bty="n", fill=rainbow(7))
```

```{r}
par(las=2, mar=c(5,4,1,2)) # increase the top margin (as not title is provided)
boxplot(rating ~ mfr, data = cereals, col=rainbow(7), names = c("A.H.F.P.","Mills","Kellogs","Nabisco","Post","Quaker", "Purina"))
legend("topleft", legend=c("A.H.F.P.","Mills","Kellogs","Nabisco","Post","Quaker", "Purina"), bty="n", fill=rainbow(7))
```

# Grammer of Graphics

* Grammar to describe and construct statistical graphics; based on the idea of building up a graphic from multiple layers of data.

* `ggplot2` is an R library for creating graphics, based on the **The Grammar of Graphics** (composed of layers, `geoms` (points, lines, regions), each with graphical `aesthetics` (color, size, shape))

* Add layers (`geom_point()`, `geom_histogram()`, `geom_boxplot()`,...), scales (`scale_colour_brewer()`), faceting specifications (`facet_wrap()`) and coordinate systems (`coord_flip()`).


### Boxplot

```{r}
library(ggplot2)
```

* Supply the `cereal` dataset and aesthetic mapping with `aes()` to the function call `ggplot()`

```{r}
p <- ggplot(cereals, aes(x=mfr, y=rating)) + 
   geom_boxplot()        # Box plot of rating vs. manufacturer
p
```

```{r}
p + coord_flip()         # Horizontal boxes (rotating the boxes)
```

```{r}
p + geom_jitter(shape = 16, position = position_jitter(0.1)) # Add jittered data points (0.1 : degree of jitter in x direction)
```

```{r}
p <- ggplot(cereals, aes(x=mfr, y=rating)) + 
    geom_boxplot(outlier.colour = "red", outlier.shape = 8, outlier.size = 4) # Change outlier (color, shape and size)
p
```

```{r}
p <- ggplot(cereals, aes(x=mfr, y=rating, color=mfr)) +   # Change box plot line colors by groups
  geom_boxplot() +
  labs(title="Boxplot of cereals",x = "Manufacturer", y = "Rating")  
p
```

```{r}
p + scale_color_brewer(palette="Dark2")  # Change box plot line colors by groups using brewer color palettes
```

```{r}
p + scale_color_grey() + theme_classic() # Change box plot line colors by groups using grey scale
```

## Ressources

* http://r4ds.had.co.nz/data-visualisation.html
* http://vita.had.co.nz/papers/layered-grammar.pdf
* R Graphics Cookbook by Winston Chang
