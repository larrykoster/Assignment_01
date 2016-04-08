# POLS 503, Spring 2016: Assignment 1

## Instructions

### Problem 1: Data Wrangling and Viz Refresher

The file [democracy.csv](https://raw.githubusercontent.com/POLS503/pols_503_sp15/master/data/democracy.csv) contains data from Przeworski et. al, *Demoracy and Deveolpment: Political Institutions and Well-Being in the Worlds, 1950-1990* [^1].
The data have been slightly recoded, to make higher values indicate higher levels of political liberty and democracy.

| Variable | Description                      |
|:---------|:---------------------------------|
| `COUNTRY` | numerical code for each country |
| `CTYNAME` | name of each country |
| `REGION` | name of region containing country |
| `YEAR` | year of observation |
| `GDPW`   |  GDP per capita in real international prices |
| `EDT`    |  average years of education |
| `ELF60`  |  ethnolinguistic fractionalization |
| `MOSLEM` |  percentage of Muslims in country |
| `CATH`   |  percentage of Catholics in country |
| `OIL`    |  whether oil accounts for 50+\% of exports |
| `STRA`   |  count of recent regime transitions |
| `NEWC`   |  whether county was created after 1945 |
| `BRITCOL` |  whether country was a British colony |
| `POLLIB` | degree of political liberty (1--7 scale, rising in political liberty) |
| `CIVLIB` | degree of civil liberties (1--7 scale, rising in civil liberties) |
| `REG`    | presence of democracy (0=non-democracy, 1=democracy)|


For these questions use **ggplot2** for plotting, and **dplyr** and **tidyr** for the data manipulation. 

a. Load the democracy data frame


```r
democracy <- read.csv(file = "democracy.csv", stringsAsFactors = FALSE)
```

When you run this, you will notice that some variables that should be numeric are not. There is a problem with how `read.csv` read missing values. Figure out how this dataset indicates missing values, and add the correct argument to `read.csv` to fix this problem.

a. Create a data frame with statistics (means, medians, and ) for all variables. Instead of doing this with `summary`, use **dplyr** and **tidyr** as shown in the example [https://uw-pols501.github.io/pols_501_wi16/lessons/gapminder_intro_to_dplyr_tidyr.html#plotting]. 

```r
dem_summary_stats <-
  ... %>% 
  group_by(...) %>%
  summarize(...) %>%
  gather(...) %>%
  ungroup(...) %>%
  unite(...) %>%
  spread
```
  
    Print this table using the function `kable` in the **knitr** package, an the code chunk option `results='asis`. See the [R Markdown Help](http://rmarkdown.rstudio.com/authoring_rcodechunks.html).
    

d. Create a histogram for political liberties in which each unique
value of the variable is in its own bin.

e. Create a histogram for GDP per capita.

f. Create a histogram for **log** GDP per capita. How is this histogram different than the one for GDP per capita when it was not logged.

g. Plot political liberties against GDP per capita. If you use 
   a scatterplot, there will be overlap. Figure out a way to plot
   these two variables so that the pattern (if any) between them is 
   clear. There could be multiple ways to do this, and not necessarily a scatterplot.

i. Plot political liberties against **log** GDP per
   capita, using the same method as the previous question.  How is the relationship different than  when GDP per capita was not logged?

j. Create a boxplot of GDP per capita for oil producing and non-oil producing nations. Use **ggplot2**. This should be one plot, not two separate plots.

k. Calculate the mean GDP per capita in countries with at least 40 percent Catholics. How does it compare to mean GDP per capita for all countries? Remember to check the units of Catholic.

l. Calculate the average GDP per capita in countries with greater than
   60% ethnolinguistic fractionalization, less than 60%, and missing
   ethnolinguistic fractionalization.  Hint: you can calculate this
   with the **dplyr** verbs: `mutate`, `group_by` and `summarize`.

m. For all years, calculate the median of the country average years of education all countries? Return this as a data-frame. Hint: use **dplyr** functions: `group_by`, `filter`, `summarize`. Plot the median of the years of education for all years using a line. Also show the original data.

o. Repeat the previous question but group by both year and democracy. Plot separate lines for democracies and non-democries and the original data. Use color to differentiate democracies and non-democracies.

n. Which country was (or countries were) closest to the median years of education in 1985 among all countries? Hint: use **dplyr** functions: `filter`, `mutate`, `arrange`, and `slice`. 

q. What were the 25th and 75th percentiles of ethnolinguistic fractionalization for new and old countries? Return this as a data frame with columns `NEWC`, `ELF60_p25`, and `ELF60_p75`. Print it as a nicely formatted table with `kable`.

## Problem 2: Plotting data and regressions

This question will use a dataset included with R

```r
data("anscombe")
```
The dataset consists of 4 seperate datasets each with an $x$ and $y$ variable.[^anscombe]
The original dataset is not a tidy dataset.
The following code creates a tidy dataset of the anscombe data that is easier to analyze than the 

```r
library("dplyr")
library("tidyr")
anscombe2 <- anscombe %>%
	mutate(obs = row_number()) %>%
	gather(variable_dataset, value, - obs) %>%
	separate(variable_dataset, c("variable", "dataset"), sep = 1L) %>%
	spread(variable, value) %>%
	arrange(dataset, obs)
```

a. For each dataset: calculate the mean and standard deviations of x and y, and correlation between x and y, and run a linear regression between x and y for each dataset. How similar do you think that these datasets will look?

b. Create a scatter plot of each dataset and its linear regression fit. Hint: you can do this easily with facet_wrap.

## Problem 3: Predicting Sprint Times

In a 2004 paper in *Nature*, Tatem et al. estimate the trend lines of sprint times for men and women using the winning times of the 100-meters in the Olympics.[^sprint1] They report that using current trends, in the 2156 Olympics, the women's 100-meter will have a faster time.[^sprint2]

The dataset includes the winning times from the 100-meter dash for both men and women for all Olympics 1900-2012 and Track & Field World Championships finals 1976-2015.

| Variable | Description |
|:---|:---|
| `year` | Year of the Olympics or World Championships |
| `time` | Winning time |
| `women` | 1 if women's race; 0 if men's race |
| `olympics` | 1 if in the olympics; 0 if in the World Championships |

Load the data into R from the csv file:

```r
sprinters <- read.csv("sprinters.csv")
```

a. The referenced paper only used data from the Olympics 2004 and before. Create a new dataset named `sprinters_orig` with only those observations.


```r
sprinters_orig <-
  filter(sprinters,
         year <= 2004,
         olympics == 1)
```

b. Run the regressions

```r
library("dplyr")
mod1 <- lm(time ~ year + women, data = sprinters_orig)
mod2 <- lm(time ~ year * women, data = sprinters_orig)
mod3 <- lm(time ~ year, data = filter(sprinters_orig, women == 1))
mod4 <- lm(time ~ year, data = filter(sprinters_orig, women == 0))
```
Interpret each regression. How are they similar or different in their 
slopes? Plot each of these using the **texreg** package.

```r
library("texreg")
htmlreg(list(mod1, mod2, mod3, mod4), stars = numeric())
```


<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<table cellspacing="0" align="center" style="border: none;">
<caption align="bottom" style="margin-top:0.3em;">Statistical models</caption>
<tr>
<th style="text-align: left; border-top: 2px solid black; border-bottom: 1px solid black; padding-right: 12px;"><b></b></th>
<th style="text-align: left; border-top: 2px solid black; border-bottom: 1px solid black; padding-right: 12px;"><b>Model 1</b></th>
<th style="text-align: left; border-top: 2px solid black; border-bottom: 1px solid black; padding-right: 12px;"><b>Model 2</b></th>
<th style="text-align: left; border-top: 2px solid black; border-bottom: 1px solid black; padding-right: 12px;"><b>Model 3</b></th>
<th style="text-align: left; border-top: 2px solid black; border-bottom: 1px solid black; padding-right: 12px;"><b>Model 4</b></th>
</tr>
<tr>
<td style="padding-right: 12px; border: none;">(Intercept)</td>
<td style="padding-right: 12px; border: none;">34.96</td>
<td style="padding-right: 12px; border: none;">31.83</td>
<td style="padding-right: 12px; border: none;">44.35</td>
<td style="padding-right: 12px; border: none;">31.83</td>
</tr>
<tr>
<td style="padding-right: 12px; border: none;"></td>
<td style="padding-right: 12px; border: none;">(1.96)</td>
<td style="padding-right: 12px; border: none;">(2.13)</td>
<td style="padding-right: 12px; border: none;">(4.28)</td>
<td style="padding-right: 12px; border: none;">(1.68)</td>
</tr>
<tr>
<td style="padding-right: 12px; border: none;">year</td>
<td style="padding-right: 12px; border: none;">-0.01</td>
<td style="padding-right: 12px; border: none;">-0.01</td>
<td style="padding-right: 12px; border: none;">-0.02</td>
<td style="padding-right: 12px; border: none;">-0.01</td>
</tr>
<tr>
<td style="padding-right: 12px; border: none;"></td>
<td style="padding-right: 12px; border: none;">(0.00)</td>
<td style="padding-right: 12px; border: none;">(0.00)</td>
<td style="padding-right: 12px; border: none;">(0.00)</td>
<td style="padding-right: 12px; border: none;">(0.00)</td>
</tr>
<tr>
<td style="padding-right: 12px; border: none;">women</td>
<td style="padding-right: 12px; border: none;">1.09</td>
<td style="padding-right: 12px; border: none;">12.52</td>
<td style="padding-right: 12px; border: none;"></td>
<td style="padding-right: 12px; border: none;"></td>
</tr>
<tr>
<td style="padding-right: 12px; border: none;"></td>
<td style="padding-right: 12px; border: none;">(0.06)</td>
<td style="padding-right: 12px; border: none;">(4.08)</td>
<td style="padding-right: 12px; border: none;"></td>
<td style="padding-right: 12px; border: none;"></td>
</tr>
<tr>
<td style="padding-right: 12px; border: none;">year:women</td>
<td style="padding-right: 12px; border: none;"></td>
<td style="padding-right: 12px; border: none;">-0.01</td>
<td style="padding-right: 12px; border: none;"></td>
<td style="padding-right: 12px; border: none;"></td>
</tr>
<tr>
<td style="padding-right: 12px; border: none;"></td>
<td style="padding-right: 12px; border: none;"></td>
<td style="padding-right: 12px; border: none;">(0.00)</td>
<td style="padding-right: 12px; border: none;"></td>
<td style="padding-right: 12px; border: none;"></td>
</tr>
<tr>
<td style="border-top: 1px solid black;">R<sup style="vertical-align: 0px;">2</sup></td>
<td style="border-top: 1px solid black;">0.91</td>
<td style="border-top: 1px solid black;">0.93</td>
<td style="border-top: 1px solid black;">0.79</td>
<td style="border-top: 1px solid black;">0.88</td>
</tr>
<tr>
<td style="padding-right: 12px; border: none;">Adj. R<sup style="vertical-align: 0px;">2</sup></td>
<td style="padding-right: 12px; border: none;">0.91</td>
<td style="padding-right: 12px; border: none;">0.92</td>
<td style="padding-right: 12px; border: none;">0.78</td>
<td style="padding-right: 12px; border: none;">0.88</td>
</tr>
<tr>
<td style="padding-right: 12px; border: none;">Num. obs.</td>
<td style="padding-right: 12px; border: none;">42</td>
<td style="padding-right: 12px; border: none;">42</td>
<td style="padding-right: 12px; border: none;">18</td>
<td style="padding-right: 12px; border: none;">24</td>
</tr>
<tr>
<td style="border-bottom: 2px solid black;">RMSE</td>
<td style="border-bottom: 2px solid black;">0.19</td>
<td style="border-bottom: 2px solid black;">0.17</td>
<td style="border-bottom: 2px solid black;">0.21</td>
<td style="border-bottom: 2px solid black;">0.13</td>
</tr>
<tr>
<td style="padding-right: 12px; border: none;" colspan="6"><span style="font-size:0.8em"></span></td>
</tr>
</table>
  
c. Plot the fitted values of these regressions against the original values. The function `augment` in the **broom** package is useful for this.

d. Use the function `predict` to predict the times of men and women in the 2156 Olympics. Is this plausible?

e. Calculate the square root of the mean of the squared residuals (root mean squared error or RMSE) for the regression `time ~ year * women`.
   Predict the values for the years after 2004 for both Olympics and World Championships. What are the root mean squared residuals for these predictions?
   Is it surprising that the RMSE for the predictions out of
   the sample are lower than those in the sample? 

[^1]: Przeworski, Adam, Michael E. Alvarez, Jose Antonio Cheibub, and Fernando Limongi. 2000. *Democracy and Development: Political Institutions and Well-Being in the World, 1950-1990*. Cambridge University Press.
[^anscombe]: These data are from: Anscombe, F. J. (1973). "Graphs in Statistical Analysis". *American Statistician* 27 (1): 17–21.
[^sprint1]: Andrew J. Tatem, Carlos A. Guerra, Peter M. Atkinson & Simon I. Hay. 2004. "Athletics:  Momentous sprint at the 2156 Olympics?" *Nature.* <https://dx.doi.org/10.1038/431525>
[^sprint2]: I've read the short article several times. I still can't tell whether it is tongue in cheek.
