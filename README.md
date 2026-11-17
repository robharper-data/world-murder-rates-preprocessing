# World murder rates: data preprocessing in R

Combining two untidy public datasets, country populations and murder counts, into one clean dataset ready for analysis, and calculating a murder rate per 100,000 people. The focus is the preprocessing: reshaping, merging, type conversion, cleaning inconsistent values, and handling skewed variables and outliers.

A joint project by Robert Harper and Sanjay Addihally Lakshmegowda, completed in 2024 during our Master of Data Science at RMIT University.

## Data

- **Murders:** total murders per country per year, 1990 to 2019, from [Gapminder](https://www.gapminder.org/data/) (`murder_total_deaths.csv`, 204 countries).
- **Population:** country populations for eight years between 1970 and 2022, with area, density, growth rate and continent, from the [World Population Dataset](https://www.kaggle.com/datasets/iamsouravbanerjee/world-population-dataset) on Kaggle (`world_population.csv`, 234 countries).

The data files are not included. To run the analysis, download both and place them in a `data` folder beside the R Markdown file.

## Steps

1. **Tidy.** Both files were untidy, with one column per year. Each was reshaped to one row per country and year.
2. **Merge.** The datasets were joined on country and year with an inner join. The years they share are 1990, 2000, 2010 and 2015, giving 756 rows.
3. **Convert types.** Year and continent were converted to factors, with continents labelled, and the numeric columns to numbers.
4. **Clean inconsistent values.** 40 murder counts were recorded as text in thousands, such as "10.6k". A custom function found them and converted them to numbers.
5. **Create a new variable:** murders per 100,000 population.
6. **Check for missing values.** None remained after the merge.
7. **Handle outliers.** Every numeric variable except growth rate was heavily right-skewed. For each one, several transformations were tried (Box-Cox, log, square root, cube root, fourth root) and the one that best improved normality was kept, judged by skewness and the Shapiro-Wilk test. Remaining outliers were then capped at the 5th and 95th percentiles.

Transformation substantially reduced the number of outliers. For example, outliers in the murder rate fell from 75 to 2, and in total murders from 105 to 20. None of the variables became fully normal.

## Limitations

- **Countries lost in the merge.** The join matches on country names, and 15 countries are spelt differently in the two sources, including the USA, the UK, the UAE, both Congos and Côte d'Ivoire. They are dropped from the merged data even though both datasets cover them. Joining on a standard country code would keep them.
- **Repeated values.** Area, density, growth rate and world population share are single 2022 figures in the source, so each country's value repeats for all four years. The outlier and normality checks on those variables therefore count every value four times, which overstates the sample size.
- **Modelled estimates.** Many murder counts are fractional, so they are modelled estimates, not recorded totals.
- **Capping.** Capping keeps every record but pulls extreme values to the 5th and 95th percentiles, so the processed data understates the true spread.

## Running it

Requires R with `kableExtra`, `magrittr`, `tidyr`, `dplyr`, `readr`, `outliers`, `forecast` and `moments`.

```r
rmarkdown::render("murder_rates_preprocessing.Rmd")
```
