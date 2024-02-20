## Overview

The World Bank publishes extensive socioeconomic data on countries and
economies worldwide. In the `data_world_bank` folder included in this
assignment, there are all the World Bank’s `csv` data files with
economic indicators for each country
(<https://data.worldbank.org/indicator>). Each `csv` file contains data
on a given country economy’s data.

Your tasks are two:

1.  Write a function that imports a data file (one single data file) and
    renames some of the columns in each data file. Remember to document
    your function. Then call the function to import all files. Finally,
    show how to call the function both with a for loop and with map
2.  Tidy the imported data

## Load necessary libraries

    library(tidyverse)

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.4.4     ✔ purrr   1.0.2
    ## ✔ tibble  3.2.1     ✔ dplyr   1.1.2
    ## ✔ tidyr   1.3.0     ✔ stringr 1.5.0
    ## ✔ readr   2.1.4     ✔ forcats 0.5.2
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

    library(purrr)

## Task 1

#### Write a function that imports the data files and renames columns

-   Your function should import a SINGLE data file and take one single
    argument: the file path to the data file. We suggest using a
    relative path. Given this path, the function should import the data,
    rename a few variables (see next bullet point), and return the
    renamed data as output.
-   Your function should rename the following four variables “Country
    Name”, “Country Code”, “Indicator Name”, “Indicator Code” as
    `country`, `country_code`, `indicator`, `indicator_code`. Please
    note the original variables use non-syntactic names (see slides on
    this)
-   Before writing your function: inspect a few of the `csv` files to
    familiarize yourself with the data. A couple of suggestions: when
    you import the data you want to skip the first four rows and drop
    row 67 (which gives problems)
-   Make sure to document your function properly

<!-- -->

    import_and_rename <- function(filepath){
      # Import datafiles and rename some columns  
      # Args: 
      #   filepath : stands for each name of the file importing
      # Returns
      #   The datafile imported with changed columns 
      datafile <- read_csv(filepath, skip = 4) %>% 
      subset(select = -67) %>%
        rename( 
          "country" = "Country Name", 
          "country_code" = "Country Code", 
          "indicator" = "Indicator Name", 
          "indicator_code" = "Indicator Code"
        )
      return(datafile)
    }

#### Call the function to import all data files

Once you are sure your function works as expected to import one SINGLE
data file, you want to use it to import all data files. To do so:

-   First, create a list with the names of all data files using the
    `dir()` function (see the function documentation for more), with the
    following three arguments:
    -   `path`: we recommend using a relative path (this is shorter,
        relative means relative to your working directory) rather than
        an absolute path (longer, more prone to errors); see Chapter 8
        of R for Data Science for more detailed instructions
    -   `pattern`: use a regular expression pattern that matches the
        `csv` extension of your files
    -   `full.names`: set this to `TRUE`
-   Second, import all data files using your function; show how to do
    this in two ways: using a `for loop` and `map()`. Notice these are
    several files, it might take a while (up to 2-3 minutes) for R to
    import them, especially with the loop. Make sure that your final
    result is a dataframe (not a list or anything else).

<!-- -->

    list <- dir(
      path = "data_world_bank",            
      pattern = "\\.csv$",  
      full.names = TRUE    
    )

    print(list[1:5])

    ## [1] "data_world_bank/API_ABW_DS2_en_csv_v2_4346306.csv"
    ## [2] "data_world_bank/API_AFG_DS2_en_csv_v2_4343152.csv"
    ## [3] "data_world_bank/API_AGO_DS2_en_csv_v2_4319971.csv"
    ## [4] "data_world_bank/API_ALB_DS2_en_csv_v2_4331460.csv"
    ## [5] "data_world_bank/API_AND_DS2_en_csv_v2_4346430.csv"

    # Pre-allocate a list
    alldata <- list()

    # Loop over file paths, import and rename them into a new list
    for (i in seq_along(list)) {
      alldata[[i]] <- import_and_rename(list[[i]])
    }

    # Combine list into a data frame
    alldata_df <- bind_rows(alldata) %>% 
      drop_na() 

    # Import all data files using map
    alldata_dfMap <- map_dfr(list, import_and_rename) %>% 
      drop_na() 

## Task 2

Tidy the imported data using the principles of tidy data: each variable
must have its own column, each observation must have its own row, each
value must have its own cell. Before writing any code, take some time to
envision how you want your data to look like once tidy. Add a few
sentences (using Markdown language) to explain what you did and why the
resulting data frame is tidy.

    tidydata <- pivot_longer(alldata_df,
        cols = -c("country", "country_code", "indicator","indicator_code"),
        names_to = "year", 
        values_to = "value") 

To tidy the data, I used pivot\_longer because the originally data frame
have multiple columns representing the years, which makes the data frame
extremely wide horizontally. I want to make the years appear in order in
one column. Thus, I take all the columns representing years and transfer
the name into “year”, and make each value accompanied that year into a
cell.

## Bonus/Extra

These are some (optional) tasks/ideas to consider if you aim at
“Excellent” on “Achievement” (Other ideas: welcome! Completing one of
these two options is not the only way to show your mastery of the
material required for this assignment; we provide them to offer some
guidance, but feel free to explore other options if you would like to).

-   Option 1: after you write the code for tidying the imported data
    (and you have ensured it works as expected), put it into a function,
    and call it with the data.
-   Option 2: use ggplot2 to produce a graph that explores the
    relationship between two variables of your choice in the data (make
    sure to label the graph, etc.), then briefly describe the plotted
    data. To familiarize with the world bank indicators, consult the
    data documentation: <https://data.worldbank.org/indicator>

<!-- -->

    tidy <- function(filepath){
      # Import and rename datafile, then tidy it
      # Args: 
      #   filepath : stands for each name of the file importing
      # Returns
      #   The tidy datafile 
      tidyfile <- import_and_rename(filepath) %>% 
      pivot_longer(
        cols = c(starts_with("19") | starts_with("20")),
        names_to = "year", 
        values_to = "value") %>%
        drop_na(value)
      return(tidyfile)
    }

    tidy_example1 <- tidy("data_world_bank/API_ABW_DS2_en_csv_v2_4346306.csv")

    ## New names:
    ## Rows: 1442 Columns: 67
    ## ── Column specification
    ## ──────────────────────────────────────────────────────── Delimiter: "," chr
    ## (4): Country Name, Country Code, Indicator Name, Indicator Code dbl (62): 1960,
    ## 1961, 1962, 1963, 1964, 1965, 1966, 1967, 1968, 1969, 1970, ... lgl (1): ...67
    ## ℹ Use `spec()` to retrieve the full column specification for this data. ℹ
    ## Specify the column types or set `show_col_types = FALSE` to quiet this message.
    ## • `` -> `...67`

## Session info

    sessioninfo::session_info()

    ## ─ Session info ───────────────────────────────────────────────────────────────
    ##  setting  value
    ##  version  R version 4.3.1 (2023-06-16)
    ##  os       Red Hat Enterprise Linux 8.8 (Ootpa)
    ##  system   x86_64, linux-gnu
    ##  ui       X11
    ##  language (EN)
    ##  collate  en_US.UTF-8
    ##  ctype    en_US.UTF-8
    ##  tz       America/Chicago
    ##  date     2023-11-08
    ##  pandoc   2.17.1.1 @ /usr/lib/rstudio-server/bin/quarto/bin/ (via rmarkdown)
    ## 
    ## ─ Packages ───────────────────────────────────────────────────────────────────
    ##  package       * version date (UTC) lib source
    ##  assertthat      0.2.1   2019-03-21 [2] CRAN (R 4.2.1)
    ##  backports       1.4.1   2021-12-13 [2] CRAN (R 4.2.1)
    ##  bit             4.0.5   2022-11-15 [2] CRAN (R 4.2.2)
    ##  bit64           4.0.5   2020-08-30 [2] CRAN (R 4.2.1)
    ##  broom           1.0.1   2022-08-29 [2] CRAN (R 4.2.1)
    ##  cellranger      1.1.0   2016-07-27 [2] CRAN (R 4.2.1)
    ##  cli             3.6.1   2023-03-23 [2] CRAN (R 4.3.0)
    ##  colorspace      2.0-3   2022-02-21 [2] CRAN (R 4.2.1)
    ##  crayon          1.5.2   2022-09-29 [2] CRAN (R 4.2.1)
    ##  DBI             1.1.3   2022-06-18 [2] CRAN (R 4.2.1)
    ##  dbplyr          2.2.1   2022-06-27 [2] CRAN (R 4.2.1)
    ##  digest          0.6.30  2022-10-18 [2] CRAN (R 4.2.1)
    ##  dplyr         * 1.1.2   2023-04-20 [2] CRAN (R 4.3.0)
    ##  evaluate        0.18    2022-11-07 [2] CRAN (R 4.2.1)
    ##  fansi           1.0.4   2023-01-22 [2] CRAN (R 4.3.0)
    ##  fastmap         1.1.0   2021-01-25 [2] CRAN (R 4.2.1)
    ##  forcats       * 0.5.2   2022-08-19 [2] CRAN (R 4.2.1)
    ##  fs              1.5.2   2021-12-08 [2] CRAN (R 4.2.1)
    ##  gargle          1.2.1   2022-09-08 [2] CRAN (R 4.2.1)
    ##  generics        0.1.3   2022-07-05 [2] CRAN (R 4.2.1)
    ##  ggplot2       * 3.4.4   2023-10-12 [1] CRAN (R 4.3.1)
    ##  glue            1.6.2   2022-02-24 [2] CRAN (R 4.2.1)
    ##  googledrive     2.0.0   2021-07-08 [2] CRAN (R 4.2.1)
    ##  googlesheets4   1.0.1   2022-08-13 [2] CRAN (R 4.2.1)
    ##  gtable          0.3.1   2022-09-01 [2] CRAN (R 4.2.1)
    ##  haven           2.5.1   2022-08-22 [2] CRAN (R 4.2.1)
    ##  hms             1.1.3   2023-03-21 [2] CRAN (R 4.3.0)
    ##  htmltools       0.5.3   2022-07-18 [2] CRAN (R 4.2.1)
    ##  httr            1.4.4   2022-08-17 [2] CRAN (R 4.2.1)
    ##  jsonlite        1.8.3   2022-10-21 [2] CRAN (R 4.2.1)
    ##  knitr           1.40    2022-08-24 [2] CRAN (R 4.2.1)
    ##  lifecycle       1.0.3   2022-10-07 [2] CRAN (R 4.2.1)
    ##  lubridate       1.9.0   2022-11-06 [2] CRAN (R 4.2.1)
    ##  magrittr        2.0.3   2022-03-30 [2] CRAN (R 4.2.1)
    ##  modelr          0.1.9   2022-08-19 [2] CRAN (R 4.2.1)
    ##  munsell         0.5.0   2018-06-12 [2] CRAN (R 4.2.1)
    ##  pillar          1.9.0   2023-03-22 [2] CRAN (R 4.3.0)
    ##  pkgconfig       2.0.3   2019-09-22 [2] CRAN (R 4.2.1)
    ##  purrr         * 1.0.2   2023-08-10 [1] CRAN (R 4.3.1)
    ##  R6              2.5.1   2021-08-19 [2] CRAN (R 4.2.1)
    ##  readr         * 2.1.4   2023-02-10 [2] CRAN (R 4.3.0)
    ##  readxl          1.4.1   2022-08-17 [2] CRAN (R 4.2.1)
    ##  reprex          2.0.2   2022-08-17 [2] CRAN (R 4.2.1)
    ##  rlang           1.1.1   2023-04-28 [2] CRAN (R 4.3.0)
    ##  rmarkdown       2.17    2022-10-07 [2] CRAN (R 4.2.1)
    ##  rstudioapi      0.14    2022-08-22 [2] CRAN (R 4.2.1)
    ##  rvest           1.0.3   2022-08-19 [1] CRAN (R 4.3.1)
    ##  scales          1.2.1   2022-08-20 [2] CRAN (R 4.2.1)
    ##  sessioninfo     1.2.2   2021-12-06 [2] CRAN (R 4.2.1)
    ##  stringi         1.7.12  2023-01-11 [2] CRAN (R 4.2.2)
    ##  stringr       * 1.5.0   2022-12-02 [1] CRAN (R 4.3.1)
    ##  tibble        * 3.2.1   2023-03-20 [2] CRAN (R 4.3.0)
    ##  tidyr         * 1.3.0   2023-01-24 [2] CRAN (R 4.2.2)
    ##  tidyselect      1.2.0   2022-10-10 [2] CRAN (R 4.2.1)
    ##  tidyverse     * 1.3.2   2022-07-18 [2] CRAN (R 4.2.1)
    ##  timechange      0.1.1   2022-11-04 [2] CRAN (R 4.2.1)
    ##  tzdb            0.4.0   2023-05-12 [2] CRAN (R 4.3.0)
    ##  utf8            1.2.3   2023-01-31 [2] CRAN (R 4.3.0)
    ##  vctrs           0.6.3   2023-06-14 [2] CRAN (R 4.3.0)
    ##  vroom           1.6.3   2023-04-28 [2] CRAN (R 4.3.0)
    ##  withr           2.5.0   2022-03-03 [2] CRAN (R 4.2.1)
    ##  xfun            0.34    2022-10-18 [2] CRAN (R 4.2.1)
    ##  xml2            1.3.3   2021-11-30 [2] CRAN (R 4.2.1)
    ##  yaml            2.3.6   2022-10-18 [2] CRAN (R 4.2.1)
    ## 
    ##  [1] /home/yihanwang/R/x86_64-redhat-linux-gnu-library/4.3
    ##  [2] /usr/lib64/R/library
    ##  [3] /usr/share/R/library
    ## 
    ## ──────────────────────────────────────────────────────────────────────────────

## Reflections

Provide 1-2 paragraphs of reflections on what was hard/easy about this
homework (part 1, debugging; and part 2, working with world bank data),
problems you solved and how, helpful resources you consulted, and what
you plan to further improve based on this homework, etc.

Please, list the first and last name of eventual collaborators with whom
you worked to complete this assignment and explain what each did.

For part1, question4, I struggled a little with removing the warning
message “returning NA.” I originally intended to use “na.rm” to directly
remove NA values, but this did not work. So I read carefully about the
warning message and found it is caused because non-numeric values in the
“applicants” dateset, and this can cause warning because strings or
logical values cannot be calculated their mean. Therefore, Monica
suggested me to filter out these kinds of values with an if statement.

For part2, I think this part is harder than part1, especially with the
for loop question in Task1. The question requires us to merge all
imported data to form a big dataframe. However, I struggled on how to
loop a list with full file names and make into a data frame. I googled
and found that function “bind\_rows” can make this happen, so I
preallocate a list contained all the files’ names, and start loop, and
finally convert the output into a dataframe.

Also, I met troubles on commit and push to Github because I have
divergent directories to resolve. After I tried and failed “git fetch,”
“git push origin main,” and so on, in my Terminal, I went to Ram for
help, and finally Ram helped me solve this with “git reset –hard
origin/main.”
