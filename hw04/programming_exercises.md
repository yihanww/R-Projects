### Load necessary libraries

    library(tidyverse)

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.3     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

    library(purrr)

### Question 1. Compute the number of unique values in each column of the `iris` dataset. Write code that solves this task using: a `for` loop, `map()`, and `across()`. Make sure to pre-allocate your output.

The iris data set contains 150 observations for three species (setosa,
virginica, versicolor) and four features measured for each sample. These
quantify the variations of the iris flower in its three species, all
measurements given in centimeters.

Load and check `iris` data (included in the tidyverse)

    data(iris)

Using a `for` loop

    # Pre-allocate
    unique_counts_forloop <- numeric(ncol(iris))

    # Loop through columns
    for (i in 1:ncol(iris)) {
      unique_counts_forloop[i] <- length(unique(iris[, i]))
    }

    print(unique_counts_forloop)

    ## [1] 35 23 43 22  3

Using a `map` function

    # Pre-allocate (though map_int inherently does this)
    unique_counts_map <- integer(ncol(iris))

    # Use map to count unique values
    unique_counts_map <- map_int(iris, ~ length(unique(.)))

    print(unique_counts_map)

    ## Sepal.Length  Sepal.Width Petal.Length  Petal.Width      Species 
    ##           35           23           43           22            3

Using `across`

    # Use across within summarise
    unique_counts_across <- iris %>%
      summarise(across(everything(), ~ length(unique(.))))

    print(unique_counts_across)

    ##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    ## 1           35          23           43          22       3

### Question 2. Calculate the square of each element in the given vector `v` using a `for` loop and `map()`. Make sure to pre-allocate your output.

    v <- seq(from = 20, to = 1)
    v

    ##  [1] 20 19 18 17 16 15 14 13 12 11 10  9  8  7  6  5  4  3  2  1

Using a `for` loop

    # Pre-allocate
    square_forloop <- numeric(length(v))

    # Loop through the vector and square each element
    for (i in 1:length(v)) {
      square_forloop[i] <- v[i]^2
    }

    print(square_forloop)

    ##  [1] 400 361 324 289 256 225 196 169 144 121 100  81  64  49  36  25  16   9   4
    ## [20]   1

Using a `map` function

    library(purrr)

    # Pre-allocate (though map_dbl inherently does this)
    square_map <- double(length(v))

    # Use map to square each element
    square_map <- map_dbl(v, ~ .^2)

    print(square_map)

    ##  [1] 400 361 324 289 256 225 196 169 144 121 100  81  64  49  36  25  16   9   4
    ## [20]   1

### Question 3. Calculate the square of all elements in the given matrix `m` using `for` loops and `apply()`. Check the documentation for `apply()`. Make sure to pre-allocate your output.

    m <- matrix(c(1:16), nrow = 4, ncol = 4, byrow = TRUE)

Using nested `for` loops

    # Pre-allocate a matrix
    m_square_forloop <- matrix(0, nrow=4, ncol=4)  

    # Loop through each element in the matrix's row and column
    for (i in 1:nrow(m)) {
      for (j in 1:ncol(m)) {
        m_square_forloop[i, j] <- m[i, j]^2
      }
    }

    print(m_square_forloop)

    ##      [,1] [,2] [,3] [,4]
    ## [1,]    1    4    9   16
    ## [2,]   25   36   49   64
    ## [3,]   81  100  121  144
    ## [4,]  169  196  225  256

Using one `for` loop

    # Pre-allocate
    m_square_forloop1 <- matrix(0, nrow = 4, ncol = 4, byrow = TRUE)
    elements_length <- length(m)

    # Loop through each element in the matrix via length
    for (i in 1:elements_length) {
      m_square_forloop1[i] <- m[i]^2
    }
    print(m_square_forloop1)

    ##      [,1] [,2] [,3] [,4]
    ## [1,]    1    4    9   16
    ## [2,]   25   36   49   64
    ## [3,]   81  100  121  144
    ## [4,]  169  196  225  256

Using `apply` (check the documentation by typing “apply” in the search
box of the Help tab, or by typing `help(apply)` in the console)

    m_square_apply <- apply(m, MARGIN=c(1, 2), function(x) x^2)

    print(m_square_apply)

    ##      [,1] [,2] [,3] [,4]
    ## [1,]    1    4    9   16
    ## [2,]   25   36   49   64
    ## [3,]   81  100  121  144
    ## [4,]  169  196  225  256

### Question 4. Write a function that takes two numbers and calculates the sum of the squared value of them, e.g. 2^2 + 3^2 should equal 13. Call the function with a couple of examples.

    sum <- function(x,y) {
      return(x^2 + y^2)
    }

    sum_example1 <- sum(4,5)
    sum_exmaple2 <- sum(99,36)
    sum_example3<- sum(67,83)

    print(sum_example1)

    ## [1] 41

    print(sum_exmaple2)

    ## [1] 11097

    print(sum_example3)

    ## [1] 11378

### Question 5. Write code to calculate the length of sides in a right-triangle using the Pythagorean Theorem. You need to use if-else statements to complete this question.

Your tasks:

-   set up your triangle sides, call them a, b, c, and give them values
    3, 4, NULL
-   check how many sides you have provided values for, and for each
    evaluation print a message. If you provided the lengths of only one
    side or all three sides, the printed message should warn you to
    provide value for two sides only
-   perform the calculations applying the Pythagorean Theorem

<!-- -->

    # Set up triangle sides
    a <- 3
    b <- 4
    c <- NULL

    # Count the number of provided sides
    sides_provided <- length(c(a,b,c))

    # Print the number of provided sides
    if (sides_provided == 1) {
      cat("You have provided values for only one side. Values for two sides needed.\n")
    } else if (sides_provided == 3) {
      cat("You have provided values for all three sides. Please provide values for two sides only.\n")
    } else {
      cat("Input of two sides received. The third side value is calculating......\n")

    # Apply the Pythagorean Theorem
      if (is.null(c)) {
        c <- sqrt(a^2 + b^2)
        cat("The length of side c is:", c, "\n")
      } else if (is.null(b)) {
        b <- sqrt(c^2 - a^2)
        cat("The length of side b is:", b, "\n")
      } else if (is.null(a)) {
        a <- sqrt(c^2 - b^2)
        cat("The length of side a is:", a, "\n")
      }
    }

    ## Input of two sides received. The third side value is calculating......
    ## The length of side c is: 5

### Question 6. Rewrite your code from the previous question as a function (e.g., write a function to calculate the length of sides in a right-triangle using the Pythagorean Theorem) and add some additional checks. Call the function with some examples.

Your tasks:

-   set up your triangle sides as function parameters
-   the function should be flexible: the function works if the user
    gives it values for two sides, but they can be either a and b, or b
    and c, or a and c
-   if the user only provides the length of one side, the function
    should throw an error with stop()
-   if the user provides the lengths of all three sides, the function
    should throw an error with stop()
-   if the user provides any values other than numeric values, the
    function should throw an error with stop()
-   perform the calculations applying the Pythagorean Theorem
-   call the function with a few examples, include also examples in
    which the function should throw an error

<!-- -->

    # Set up triangle sides as function parameters to apply Pythagorean Theorem 
    phthagorean <- function (a = NULL, b = NULL, c = NULL) {
      # Computes the third line's value of a right-angled triangle 
      # Args: 
      #   a,b,c : stands for each line of a triangle
      # Returns"
      #   The function calculates the value of a third line and print it out with strings
      sides_provided <- length(c(a, b, c))
      if (sides_provided == 1){
        stop("You have provided values for only one side. Values for two sides needed.\n")}
      else if (sides_provided == 3) {
        stop("You have provided values for all three sides. Please provide values for two sides only.\n")}
      else if (!is.numeric(sides_provided)){
        stop("Please provide with numeric values only. \n")}
      else {if (is.null(c)) {
        c <- sqrt(a^2 + b^2)
        cat("The length of side c is:", c, "\n")
      }   
        else if (is.null(b)) {
          b <- sqrt(c^2 - a^2)
          cat("The length of side b is:", b, "\n")
        }   
        else if (is.null(a)) {
          a <- sqrt(c^2 - b^2)
          cat("The length of side a is:", a, "\n")
        }
      }
    }

    phthagorean_example1 <- phthagorean(5, 12)

    ## The length of side c is: 13

    phthagorean_example2 <- phthagorean(30, NULL, 50)

    ## The length of side b is: 40

    phthagorean_example3 <- phthagorean(NULL, 44, 55)

    ## The length of side a is: 33

### Reflections

Provide 1-2 paragraphs of reflections on what was hard/easy about this
homework, problems you solved and how you solved them, helpful
resources, etc.

Please, list the first and last name of eventual collaborators with whom
you worked to complete this assignment and explain what each did.

This assignment took me much less time to finish compared to the last
one HW03. However, there are still some minor issues that kept troubling
me. First, I find it trouble to print the result of questions5 because
it combines texts and the output numbers. Thus, I googled on how to
present strings and numeric values, and found cat() function really
helpful. The second problem I faced is how to check if the inut number
is valid in question6. I first tried to evaluate each input with the
.is.numeric() function and found it not working if i check each valu eof
a,b,and c one by one (is.numeric(a)|is.numeric(b)|is.numeric(c)) with
warning messages. Therefore, I went to TA Monica’s office hour, and
Monica suggested me that instead of checking each value of a/b/c, I can
treat them as a whole like I did in question5, so I solved question6.

Collab: TA-Monica Ruiz

### Session info

    # don't modify this code chunk
    sessioninfo::session_info()

    ## ─ Session info ───────────────────────────────────────────────────────────────
    ##  setting  value
    ##  version  R version 4.3.1 (2023-06-16)
    ##  os       macOS Sonoma 14.0
    ##  system   x86_64, darwin20
    ##  ui       X11
    ##  language (EN)
    ##  collate  en_US.UTF-8
    ##  ctype    en_US.UTF-8
    ##  tz       America/Chicago
    ##  date     2023-11-02
    ##  pandoc   3.1.1 @ /Applications/RStudio.app/Contents/Resources/app/quarto/bin/tools/ (via rmarkdown)
    ## 
    ## ─ Packages ───────────────────────────────────────────────────────────────────
    ##  package     * version date (UTC) lib source
    ##  cli           3.6.1   2023-03-23 [1] CRAN (R 4.3.0)
    ##  colorspace    2.1-0   2023-01-23 [1] CRAN (R 4.3.0)
    ##  digest        0.6.33  2023-07-07 [1] CRAN (R 4.3.0)
    ##  dplyr       * 1.1.3   2023-09-03 [1] CRAN (R 4.3.0)
    ##  evaluate      0.21    2023-05-05 [1] CRAN (R 4.3.0)
    ##  fansi         1.0.4   2023-01-22 [1] CRAN (R 4.3.0)
    ##  fastmap       1.1.1   2023-02-24 [1] CRAN (R 4.3.0)
    ##  forcats     * 1.0.0   2023-01-29 [1] CRAN (R 4.3.0)
    ##  generics      0.1.3   2022-07-05 [1] CRAN (R 4.3.0)
    ##  ggplot2     * 3.4.3   2023-08-14 [1] CRAN (R 4.3.0)
    ##  glue          1.6.2   2022-02-24 [1] CRAN (R 4.3.0)
    ##  gtable        0.3.4   2023-08-21 [1] CRAN (R 4.3.0)
    ##  hms           1.1.3   2023-03-21 [1] CRAN (R 4.3.0)
    ##  htmltools     0.5.6   2023-08-10 [1] CRAN (R 4.3.0)
    ##  knitr         1.44    2023-09-11 [1] CRAN (R 4.3.0)
    ##  lifecycle     1.0.3   2022-10-07 [1] CRAN (R 4.3.0)
    ##  lubridate   * 1.9.3   2023-09-27 [1] CRAN (R 4.3.0)
    ##  magrittr      2.0.3   2022-03-30 [1] CRAN (R 4.3.0)
    ##  munsell       0.5.0   2018-06-12 [1] CRAN (R 4.3.0)
    ##  pillar        1.9.0   2023-03-22 [1] CRAN (R 4.3.0)
    ##  pkgconfig     2.0.3   2019-09-22 [1] CRAN (R 4.3.0)
    ##  purrr       * 1.0.2   2023-08-10 [1] CRAN (R 4.3.0)
    ##  R6            2.5.1   2021-08-19 [1] CRAN (R 4.3.0)
    ##  readr       * 2.1.4   2023-02-10 [1] CRAN (R 4.3.0)
    ##  rlang         1.1.1   2023-04-28 [1] CRAN (R 4.3.0)
    ##  rmarkdown     2.25    2023-09-18 [1] CRAN (R 4.3.0)
    ##  rstudioapi    0.15.0  2023-07-07 [1] CRAN (R 4.3.0)
    ##  scales        1.2.1   2022-08-20 [1] CRAN (R 4.3.0)
    ##  sessioninfo   1.2.2   2021-12-06 [1] CRAN (R 4.3.0)
    ##  stringi       1.7.12  2023-01-11 [1] CRAN (R 4.3.0)
    ##  stringr     * 1.5.0   2022-12-02 [1] CRAN (R 4.3.0)
    ##  tibble      * 3.2.1   2023-03-20 [1] CRAN (R 4.3.0)
    ##  tidyr       * 1.3.0   2023-01-24 [1] CRAN (R 4.3.0)
    ##  tidyselect    1.2.0   2022-10-10 [1] CRAN (R 4.3.0)
    ##  tidyverse   * 2.0.0   2023-02-22 [1] CRAN (R 4.3.0)
    ##  timechange    0.2.0   2023-01-11 [1] CRAN (R 4.3.0)
    ##  tzdb          0.4.0   2023-05-12 [1] CRAN (R 4.3.0)
    ##  utf8          1.2.3   2023-01-31 [1] CRAN (R 4.3.0)
    ##  vctrs         0.6.3   2023-06-14 [1] CRAN (R 4.3.0)
    ##  withr         2.5.1   2023-09-26 [1] CRAN (R 4.3.0)
    ##  xfun          0.40    2023-08-09 [1] CRAN (R 4.3.0)
    ##  yaml          2.3.7   2023-01-23 [1] CRAN (R 4.3.0)
    ## 
    ##  [1] /Library/Frameworks/R.framework/Versions/4.3-x86_64/Resources/library
    ## 
    ## ──────────────────────────────────────────────────────────────────────────────
