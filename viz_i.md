Visualization
================

``` r
library(tidyverse)
```

    ## ── Attaching packages ───────────────────────────────────────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.3     ✓ dplyr   1.0.2
    ## ✓ tidyr   1.1.2     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ──────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(ggridges)

knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = .6,
  out.width = "90%"
)
```

## Load the weather data

``` r
weather_df =
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USC00519397", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"),
    date_min = "2017-01-01",
    date_max = "2017-12-31") %>% 
  mutate(
    name = recode(
      id,
      USW00094728 = "CentralPark_NY",
      USC00519397 = "Waikiki_HA",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>% 
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## using cached file: /Users/jerrychao/Library/Caches/R/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2020-10-01 11:21:13 (7.519)

    ## file min/max dates: 1869-01-01 / 2020-09-30

    ## using cached file: /Users/jerrychao/Library/Caches/R/noaa_ghcnd/USC00519397.dly

    ## date created (size, mb): 2020-10-01 11:21:23 (1.699)

    ## file min/max dates: 1965-01-01 / 2020-03-31

    ## using cached file: /Users/jerrychao/Library/Caches/R/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2020-10-01 11:21:28 (0.877)

    ## file min/max dates: 1999-09-01 / 2020-09-30

``` r
weather_df
```

    ## # A tibble: 1,095 x 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2017-01-01     0   8.9   4.4
    ##  2 CentralPark_NY USW00094728 2017-01-02    53   5     2.8
    ##  3 CentralPark_NY USW00094728 2017-01-03   147   6.1   3.9
    ##  4 CentralPark_NY USW00094728 2017-01-04     0  11.1   1.1
    ##  5 CentralPark_NY USW00094728 2017-01-05     0   1.1  -2.7
    ##  6 CentralPark_NY USW00094728 2017-01-06    13   0.6  -3.8
    ##  7 CentralPark_NY USW00094728 2017-01-07    81  -3.2  -6.6
    ##  8 CentralPark_NY USW00094728 2017-01-08     0  -3.8  -8.8
    ##  9 CentralPark_NY USW00094728 2017-01-09     0  -4.9  -9.9
    ## 10 CentralPark_NY USW00094728 2017-01-10     0   7.8  -6  
    ## # … with 1,085 more rows

## Scatterplots\!\!

Create my first scatterplot ever.

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) +
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_i_files/figure-gfm/unnamed-chunk-2-1.png" width="90%" />

New approach, same plot - use piping. The benefit of this is you have
the opportunity to do data manipulation (mutate, filter, or select)
before the plot (code this immediately after the weather\_df pipe
command and before the ggplot() code).

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_i_files/figure-gfm/unnamed-chunk-3-1.png" width="90%" />

Save and edit a plot object.

``` r
weather_plot =
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax))

weather_plot + geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_i_files/figure-gfm/unnamed-chunk-4-1.png" width="90%" />

## Advanced scatterplot …

Start with the same one and make it fancy\!

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point() + 
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_i_files/figure-gfm/unnamed-chunk-5-1.png" width="90%" />

What about the `aes` placement?

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(color = name)) +
  geom_smooth()
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_i_files/figure-gfm/unnamed-chunk-6-1.png" width="90%" />

Let’s facet some things\!\!

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point() + 
  geom_smooth(se = FALSE) +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_i_files/figure-gfm/unnamed-chunk-7-1.png" width="90%" />

facet grid syntax is defined by the following: . means don’t define rows
of scatterplot facets. \~ name means which variables define columns of
scatterplot facets.

See next examples which will switch the order.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point() + 
  geom_smooth(se = FALSE) +
  facet_grid(name ~ .)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_i_files/figure-gfm/unnamed-chunk-8-1.png" width="90%" />

This seems to define scatterplot facets by rows.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .2, size = .5) + 
  geom_smooth(se = FALSE, size = 2) +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_i_files/figure-gfm/unnamed-chunk-9-1.png" width="90%" />

The following three options are global, fixed options for the plot
geom\_point(alpha = .5) decreases opacity (or increases transparency) by
50% geom\_point(size = .5) decreases the size of dots by 50%
geom\_smooth(se = FALSE, size = 2) increases the size of fit line by
200%

But if, for example I add alpha = tmin, you can assign a graphic option
that is related to the variable.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, alpha = tmin, color = name)) +
  geom_point() + 
  geom_smooth(se = FALSE) +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_i_files/figure-gfm/unnamed-chunk-10-1.png" width="90%" />

Here, there is a gradient of color density in the points that is a
function of Tmin variable. Is this something that might be meaningful to
show? Don’t know, but this example shows that if you specify options
within ggplot(aes()), graphic options are not fixed, but can change as a
function of a variable.

Now, let’s combine some elements and try a new plot

``` r
weather_df %>% 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point(aes(size = prcp), alpha = .5) +
  geom_smooth(se = FALSE) +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

<img src="viz_i_files/figure-gfm/unnamed-chunk-11-1.png" width="90%" />

Adding aes(size = prcp), the size of the point is related to the amount
of precipitation. You can interpret the amount of precipitation as it
relates to time of year. There is a lot of power here.

So started by defining a dataframe, defined some ggplot stuff, add
simple plot, etc. By making a lot of plots and tweaking things, you can
produce some great data visualization.

## Some small notes

How many geoms have to exist?

You can have whatever geometries you want (geoms).

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

<img src="viz_i_files/figure-gfm/unnamed-chunk-12-1.png" width="90%" />

You can use a neat geom\! Can try any number of these functions:
geom\_hex() geom\_bin2d() geom\_density2d() geom\_point()

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_density2d() +
  geom_point(alpha = .3)
```

    ## Warning: Removed 15 rows containing non-finite values (stat_density2d).

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_i_files/figure-gfm/unnamed-chunk-13-1.png" width="90%" />

## Univariate plots

Histograms are really great.

``` r
weather_df %>% 
  ggplot(aes(x = tmin)) +
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 15 rows containing non-finite values (stat_bin).

<img src="viz_i_files/figure-gfm/unnamed-chunk-14-1.png" width="90%" />

Can we add color … there are a lot of options, refer back to video.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) +
  geom_histogram(position = "dodge")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 15 rows containing non-finite values (stat_bin).

<img src="viz_i_files/figure-gfm/unnamed-chunk-15-1.png" width="90%" />

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) +
  geom_histogram() +
  facet_grid(. ~ name)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 15 rows containing non-finite values (stat_bin).

<img src="viz_i_files/figure-gfm/unnamed-chunk-16-1.png" width="90%" />

There are different choices you can make as to how to visualize your
data. Pros and cons, strengths and weaknesses of different ways to show
your data.

Let’s try a new geometry\!

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) +
  geom_density(alpha = .3)
```

    ## Warning: Removed 15 rows containing non-finite values (stat_density).

<img src="viz_i_files/figure-gfm/unnamed-chunk-17-1.png" width="90%" />

Density plot is a histogram that is smoothed out around the edges. Loose
a little bit of information about individual data (little bumps and
single points) in density plot but makes it easier to compare the
overall shapes of these differences. There are some other manipulations
that can be done.

What about box plots?

``` r
weather_df %>% 
  ggplot(aes(y = tmin)) +
  geom_boxplot()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_boxplot).

<img src="viz_i_files/figure-gfm/unnamed-chunk-18-1.png" width="90%" />

One box plot above.

``` r
weather_df %>% 
  ggplot(aes(y = tmin)) +
  geom_boxplot()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_boxplot).

<img src="viz_i_files/figure-gfm/unnamed-chunk-19-1.png" width="90%" />

by specifying x = name (a categorical variable which can bee
counterintuitive), you can plot multiple box plots below:

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmin)) +
  geom_boxplot()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_boxplot).

<img src="viz_i_files/figure-gfm/unnamed-chunk-20-1.png" width="90%" />

Trendy plots :)

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmin, fill = name)) +
  geom_violin(alpha = .5)
```

    ## Warning: Removed 15 rows containing non-finite values (stat_ydensity).

<img src="viz_i_files/figure-gfm/unnamed-chunk-21-1.png" width="90%" />
compare violin plot next to box plots - and then make decision as to
which is more useful. to add median value to violin plot, would add a
stat command, below:

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmin, fill = name)) +
  geom_violin(alpha = .5) +
  stat_summary(fun = "median")
```

    ## Warning: Removed 15 rows containing non-finite values (stat_ydensity).

    ## Warning: Removed 15 rows containing non-finite values (stat_summary).

    ## Warning: Removed 3 rows containing missing values (geom_segment).

<img src="viz_i_files/figure-gfm/unnamed-chunk-22-1.png" width="90%" />
fun = “median” provides the median

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmin, fill = name)) +
  geom_violin(alpha = .5) +
  stat_summary()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_ydensity).

    ## Warning: Removed 15 rows containing non-finite values (stat_summary).

    ## No summary function supplied, defaulting to `mean_se()`

<img src="viz_i_files/figure-gfm/unnamed-chunk-23-1.png" width="90%" />
stat\_summary() defaults to mean.

Ridge plots – the most popular plot of 2017. Multiple density plots. Can
be useful.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = name)) +
  geom_density_ridges()
```

    ## Picking joint bandwidth of 1.67

    ## Warning: Removed 15 rows containing non-finite values (stat_density_ridges).

<img src="viz_i_files/figure-gfm/unnamed-chunk-24-1.png" width="90%" />

Ridge plots are especially useful for many categories that you are
comparing between. For example, all 50 states.

## Save and Embed

Let’s save a scatterplot.

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_i_files/figure-gfm/unnamed-chunk-25-1.png" width="90%" />

Now that you have plot, define an object

``` r
weather_plot = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5)

ggsave("weather_plot.pdf", weather_plot)
```

    ## Saving 6 x 3.6 in image

    ## Warning: Removed 15 rows containing missing values (geom_point).

can save as pdf, png, etc. ggsave(“weather\_plot.pdf”, weather\_plot)
will save the plot in the name specified in the quotation marks in the
working directory

``` r
weather_plot = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5)

ggsave("weather_plot.pdf", weather_plot, width = 8, height = 5)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

specify some aspects of the plot.

Can add the plots in a relative path

``` r
weather_plot = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5)

ggsave("./results/weather_plot.jpg", weather_plot, width = 8, height = 5)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

What about embedding…

``` r
weather_plot
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_i_files/figure-gfm/unnamed-chunk-29-1.png" width="90%" />

Embed at different size.

``` r
weather_plot
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_i_files/figure-gfm/unnamed-chunk-30-1.png" width="90%" />

Learning assessment \#1

``` r
weather_df %>% 
  filter(name == "CentralPark_NY") %>% 
  mutate(
    tmax_f = (tmax * ((9 / 5) + 32)),
    tmin_f = (tmin * ((9 / 5) + 32))
  ) %>% 
  ggplot(aes(x = tmin, y = tmax_f, color = name)) +
  geom_point(alpha = .5) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

<img src="viz_i_files/figure-gfm/unnamed-chunk-31-1.png" width="90%" />

Not sure how to convert units from celsius to farenheit - let’s see how
Jeff does it:

``` r
weather_df %>% 
  filter(name == "CentralPark_NY") %>% 
  mutate(
    tmax_fahr = tmax * (9 / 5) + 32,
    tmin_fahr = tmin * (9 / 5) + 32) %>% 
  ggplot(aes(x = tmin_fahr, y = tmax_fahr)) +
  geom_point(alpha = .5) + 
  geom_smooth(method = "lm", se = FALSE)
```

    ## `geom_smooth()` using formula 'y ~ x'

<img src="viz_i_files/figure-gfm/unnamed-chunk-32-1.png" width="90%" />

Jeff doesn’t use a function to convert temperature, he specificies the
arithmetic conversion. Also, he specifies a method within geom\_smooth
to specify.

Learning Assessment \#2

Why are these two different??

``` r
ggplot(weather_df) + geom_point(aes(x = tmax, y = tmin), color = "blue")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_i_files/figure-gfm/unnamed-chunk-33-1.png" width="90%" />

``` r
ggplot(weather_df) + geom_point(aes(x = tmax, y = tmin, color = "green"))
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_i_files/figure-gfm/unnamed-chunk-33-2.png" width="90%" />
“In the first attempt, we’re defining the color of the points by hand;
in the second attempt, we’re implicitly creating a color variable that
has the value blue everywhere; ggplot is then assigning colors according
to this variable using the default color scheme.”

Learning Assessment \#3

Make plots that compare precipiation across locations. Try a histogram,
a density plot, a boxplot, a violin plot, and a ridgeplot; use aesthetic
mappings to make your figure readable.

``` r
weather_df %>% 
  ggplot(aes(x = prcp, color = name)) + 
    geom_histogram() +
  facet_grid(. ~ name)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 3 rows containing non-finite values (stat_bin).

<img src="viz_i_files/figure-gfm/unnamed-chunk-34-1.png" width="90%" />

``` r
weather_df %>% 
  ggplot(aes(x = prcp, color = name)) + 
  geom_density() +
  facet_grid(. ~ name)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_density).

<img src="viz_i_files/figure-gfm/unnamed-chunk-34-2.png" width="90%" />

``` r
weather_df %>% 
  ggplot(aes(x = prcp, color = name)) + 
  geom_boxplot() +
  facet_grid(. ~ name)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

<img src="viz_i_files/figure-gfm/unnamed-chunk-34-3.png" width="90%" />

``` r
##weather_df %>% 
##  ggplot(aes(x = prcp, color = name)) + 
##  geom_violin() +
##  facet_grid(. ~ name)
## doesnt work because needs y = name, no facet required

weather_df %>% 
  ggplot(aes(x = prcp, y = name, color = name)) + 
  geom_violin()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

<img src="viz_i_files/figure-gfm/unnamed-chunk-34-4.png" width="90%" />

``` r
weather_df %>% 
  ggplot(aes(x = prcp, y = name, color = name)) + 
  geom_density_ridges()
```

    ## Picking joint bandwidth of 4.61

    ## Warning: Removed 3 rows containing non-finite values (stat_density_ridges).

<img src="viz_i_files/figure-gfm/unnamed-chunk-34-5.png" width="90%" />

Jeff’s code:

``` r
##I’ll show a few possibilities, although this is by no means exhaustive!

##First a density plot:

ggplot(weather_df, aes(x = prcp)) + 
  geom_density(aes(fill = name), alpha = .5) 
```

    ## Warning: Removed 3 rows containing non-finite values (stat_density).

<img src="viz_i_files/figure-gfm/unnamed-chunk-35-1.png" width="90%" />

``` r
##Next a ridge plot:

ggplot(weather_df, aes(x = prcp, y = name)) + 
  geom_density_ridges(scale = .85)
```

    ## Picking joint bandwidth of 4.61

    ## Warning: Removed 3 rows containing non-finite values (stat_density_ridges).

<img src="viz_i_files/figure-gfm/unnamed-chunk-35-2.png" width="90%" />

``` r
##Last a boxplot:

ggplot(weather_df, aes(y = prcp, x = name)) + 
  geom_boxplot() 
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

<img src="viz_i_files/figure-gfm/unnamed-chunk-35-3.png" width="90%" />

``` r
##This is a tough variable to plot because of the highly skewed distribution in each location. Of these, I’d probably choose the boxplot because it shows the outliers most clearly. If the “bulk” of the data were interesting, I’d probably compliment this with a plot showing data for all precipitation less than 100, or for a data omitting days with no precipitation.

weather_df %>% 
  filter(prcp > 0) %>% 
  ggplot(aes(x = prcp, y = name)) + 
  geom_density_ridges(scale = .85)
```

    ## Picking joint bandwidth of 19.7

<img src="viz_i_files/figure-gfm/unnamed-chunk-35-4.png" width="90%" />

For ridge plot, need to specify that y is names. Grouping in built into
the plot. Also, using scale to separate the peaks.

``` r
weather_df %>% 
  ggplot(aes(x = prcp, y = name, color = name)) + 
  geom_density_ridges(scale = .85)
```

    ## Picking joint bandwidth of 4.61

    ## Warning: Removed 3 rows containing non-finite values (stat_density_ridges).

<img src="viz_i_files/figure-gfm/unnamed-chunk-36-1.png" width="90%" />

For boxplots, y axis should be the variable of interest. Facet also not
needed, somehow, as R knows to separate

``` r
weather_df %>% 
  ggplot(aes(y = prcp, color = name)) + 
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

<img src="viz_i_files/figure-gfm/unnamed-chunk-37-1.png" width="90%" />

Learning Assessment \#final

What happens when you set global options for figures sizes in the
“setup” code chunk and re-knit document? What happens when you
change fig.asp? What about out.width?

So this is the code chunk that Jeff likes: knitr::opts\_chunk$set(
fig.width = 6, fig.asp = .6, out.width = “90%” )
