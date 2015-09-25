# Introduction to the Hadleyverse
Douglas Bates  
September 24, 2015  


## RStudio and Hadley Wickham

- The biggest changes in the __R__ environment over the last 10 years have come from [Rstudio](http://rstudio.com)
  and its chief scientist [Hadley Wickham](https://github.com/hadley)

- Hadley did his Ph.D. at Iowa State, during which he developed the [ggplot2](https://github.com/hadley/ggplot2) package implementing the "Grammar of Graphics"

- He continues to be a driving force in innovation for the __R__ environment, to the extent that his collection of packages is known as the _Hadleyverse_

- He was also an early adopter of [github](https://github.com) and through its integration with RStudio encouraged its use with __R__

- The packages he has written are characterized by composability
    * [readr](http://github.com/hadley/readr) - replacements for data-reading functions suitable for use with 
    * [dplyr](https://github.com/hadley/dplyr) - data manipulation, grouping and summaries
    * [tidyr](https://github.com/hadley/tidyr) - transfer from `wide` to `long` format and vice-versa
    * [rvest](https://github.com/hadley/rvest) - web-scraping in R
    * [devtools](https://github.com/hadley/devtools) - utilities for building and maintaining __R__ packages

## [readr](http://github.com/hadley/readr) and [dplyr](https://github.com/hadley/dplyr)

Earlier we read the `classroom` data using

```r
str(classroom <- read.csv("http://www-personal.umich.edu/~bwest/classroom.csv"))
```
The equivalent expression using  [readr](http://github.com/hadley/readr) is

```r
(classroom <- read_csv("http://www-personal.umich.edu/~bwest/classroom.csv"))
```

```
## Source: local data frame [1,190 x 12]
## 
##      sex minority mathkind mathgain   ses yearstea mathknow housepov mathprep classid schoolid childid
##    (int)    (int)    (int)    (int) (dbl)    (dbl)    (dbl)    (dbl)    (dbl)   (int)    (int)   (int)
## 1      1        1      448       32  0.46        1       NA    0.082     2.00     160        1       1
## 2      0        1      460      109 -0.27        1       NA    0.082     2.00     160        1       2
## 3      1        1      511       56 -0.03        1       NA    0.082     2.00     160        1       3
## 4      0        1      449       83 -0.38        2    -0.11    0.082     3.25     217        1       4
## 5      0        1      425       53 -0.03        2    -0.11    0.082     3.25     217        1       5
## 6      1        1      450       65  0.76        2    -0.11    0.082     3.25     217        1       6
## 7      0        1      452       51 -0.03        2    -0.11    0.082     3.25     217        1       7
## 8      0        1      443       66  0.20        2    -0.11    0.082     3.25     217        1       8
## 9      1        1      422       88  0.64        2    -0.11    0.082     3.25     217        1       9
## 10     0        1      480       -7  0.13        2    -0.11    0.082     3.25     217        1      10
## ..   ...      ...      ...      ...   ...      ...      ...      ...      ...     ...      ...     ...
```

## Verbs and composition in readr

- The philosophy of `readr` is to use a number of "verbs" to express operations on the data and chain calls together using "pipes"
- The pipe operator is written `%>%`.  It takes the output of the operand on the left and uses it as the first argument to the operand on the right.  There is a keyboard shortcut (ctrl-shift-M) in RStudio
- Various verbs applied to a single frame are:
    * `select` - select a subset of the columns
    * `filter` - select a subset of the rows
    * `arrange` - re-order the rows
    * `mutate` - add new variables
    * `sunnarise` - reduce a group to a smaller number of observations
    * `group_by` - create the groups

## `mutate`

- The equivalent of the `with` and `within` functions in base R is `mutate`
- I prefer to have categorical data expressed as factors.  Some disagree, sometimes vehemently, because of the retaining unused levels behavior.  They would rather express categorical data as strings.  In either case, expressing categorical data as integers is asking for trouble.
- to convert the categorical data we can use

```r
# Set a column to NULL to delete it.
(clf <- mutate(classroom,sex = factor(sex,labels=c('F','M')), minority = factor(minority,labels=c('N','Y')),
               classid = factor(classid), schoolid = factor(schoolid), childid = NULL))
```

```
## Source: local data frame [1,190 x 11]
## 
##       sex minority mathkind mathgain   ses yearstea mathknow housepov mathprep classid schoolid
##    (fctr)   (fctr)    (int)    (int) (dbl)    (dbl)    (dbl)    (dbl)    (dbl)  (fctr)   (fctr)
## 1       M        Y      448       32  0.46        1       NA    0.082     2.00     160        1
## 2       F        Y      460      109 -0.27        1       NA    0.082     2.00     160        1
## 3       M        Y      511       56 -0.03        1       NA    0.082     2.00     160        1
## 4       F        Y      449       83 -0.38        2    -0.11    0.082     3.25     217        1
## 5       F        Y      425       53 -0.03        2    -0.11    0.082     3.25     217        1
## 6       M        Y      450       65  0.76        2    -0.11    0.082     3.25     217        1
## 7       F        Y      452       51 -0.03        2    -0.11    0.082     3.25     217        1
## 8       F        Y      443       66  0.20        2    -0.11    0.082     3.25     217        1
## 9       M        Y      422       88  0.64        2    -0.11    0.082     3.25     217        1
## 10      F        Y      480       -7  0.13        2    -0.11    0.082     3.25     217        1
## ..    ...      ...      ...      ...   ...      ...      ...      ...      ...     ...      ...
```

## Alternative

```r
(clc <- mutate(classroom, sex = ifelse(sex,'M','F'), minority = ifelse(minority,'Y','N'), childid = NULL))
```

```
## Source: local data frame [1,190 x 11]
## 
##      sex minority mathkind mathgain   ses yearstea mathknow housepov mathprep classid schoolid
##    (chr)    (chr)    (int)    (int) (dbl)    (dbl)    (dbl)    (dbl)    (dbl)   (int)    (int)
## 1      M        Y      448       32  0.46        1       NA    0.082     2.00     160        1
## 2      F        Y      460      109 -0.27        1       NA    0.082     2.00     160        1
## 3      M        Y      511       56 -0.03        1       NA    0.082     2.00     160        1
## 4      F        Y      449       83 -0.38        2    -0.11    0.082     3.25     217        1
## 5      F        Y      425       53 -0.03        2    -0.11    0.082     3.25     217        1
## 6      M        Y      450       65  0.76        2    -0.11    0.082     3.25     217        1
## 7      F        Y      452       51 -0.03        2    -0.11    0.082     3.25     217        1
## 8      F        Y      443       66  0.20        2    -0.11    0.082     3.25     217        1
## 9      M        Y      422       88  0.64        2    -0.11    0.082     3.25     217        1
## 10     F        Y      480       -7  0.13        2    -0.11    0.082     3.25     217        1
## ..   ...      ...      ...      ...   ...      ...      ...      ...      ...     ...      ...
```

- Neither of these alter the `classroom` table.

## What's the difference?

The difference between a factor and a character or integer variable is most important when subsetting, which is called `filter` in `dplyr`.

```r
girlsf <- filter(clf, sex == 'F')
girlsc <- filter(clc, sex == 'F')
# This remians all the levels of the factor
xtabs(~sex, girlsf)
```

```
## sex
##   F   M 
## 588   0
```

```r
xtabs(~sex, girlsc)
```

```
## sex
##   F 
## 588
```
- the factor retains information about the original levels.  The character variable doesn't.


## `select` and `filter`

- The base `R` function `subset` allows for subsetting both rows and columns.  These operations are distinct in `dplyr`; `select` for rows and `filter` for columns


```r
# Start with the classroom data and select only the "schoolid" and "housepov"
# and give me the distict values
# "classroom" is used as the first arg. in select()
classroom %>% 
    select(schoolid,housepov) %>% 
    distinct()
```

```
## Source: local data frame [107 x 2]
## 
##    schoolid housepov
##       (int)    (dbl)
## 1         1    0.082
## 2         2    0.082
## 3         3    0.086
## 4         4    0.365
## 5         5    0.511
## 6         6    0.044
## 7         7    0.148
## 8         8    0.085
## 9         9    0.537
## 10       10    0.346
## ..      ...      ...
```

## split-apply-combine
- A common idiom in data exploration is __split-apply-combine__ in which the data are split according to the levels of a variable, an operation is applied and the result is expressed in a new summary.
- The `group_by` verb is valuable here

```r
sclcl <- classroom %>% group_by(schoolid) %>% select(classid) %>%  unique()
xtabs(~schoolid,sclcl)
```

```
## schoolid
##   1   2   3   4   5   6   7   8   9  10  11  12  13  14  15  16  17  18  19  20  21  22  23  24  25  26  27  28 
##   2   3   4   2   1   3   4   3   3   4   9   5   2   3   5   2   5   1   3   3   3   1   3   4   2   3   4   4 
##  29  30  31  32  33  34  35  36  37  38  39  40  41  42  43  44  45  46  47  48  49  50  51  52  53  54  55  56 
##   2   1   4   3   5   2   2   2   4   2   4   2   3   4   2   4   1   5   3   1   2   3   1   2   2   2   3   2 
##  57  58  59  60  61  62  63  64  65  66  67  68  69  70  71  72  73  74  75  76  77  78  79  80  81  82  83  84 
##   5   1   2   2   4   2   1   2   3   3   3   5   2   5   5   3   2   2   4   5   4   3   3   2   2   4   2   3 
##  85  86  87  88  89  90  91  92  93  94  95  96  97  98  99 100 101 102 103 104 105 106 107 
##   5   3   4   2   1   2   4   3   4   4   2   3   1   1   5   4   4   2   3   2   2   1   2
```

```r
tabulate(xtabs(~schoolid,sclcl))
```

```
## [1] 13 34 26 21 12  0  0  0  1
```

## more split-apply-combine

```r
classroom %>% group_by(classid) %>% summarise(mngain = mean(mathgain))
```

```
## Source: local data frame [312 x 2]
## 
##    classid    mngain
##      (int)     (dbl)
## 1        1  47.80000
## 2        2  65.66667
## 3        3  15.66667
## 4        4  91.50000
## 5        5 182.00000
## 6        6  39.40000
## 7        7  51.00000
## 8        8  22.75000
## 9        9  -9.00000
## 10      10 106.00000
## ..     ...       ...
```

## "Data munging"

- Experienced consultants often find that a substantial part of a project is taken up with "data munging" - getting the data into a usable form.

- Extracting data from spreadsheets is often very difficult

- A good place to start exploring data is in the data sets for our Master's exam, in `/afs/cs.wisc.edu/p/stat/Data/MS.exam/`

- Try, for example, initial exploration of the data in `f14`.

- I was unable to read the `LakeSuperior.txt` file with `read_tsv`.  I could use `read.delim` with `header=TRUE`

- The results from `read_tsv` on `organoid.txt` look peculiar.

```r
# Wide data (Many columns for one object)
# It's compact
(org <- read_tsv("http://www.stat.wisc.edu/~yandell/st627/organoid.txt"))
```

```
## Source: local data frame [300 x 12]
## 
##                [EMPTY]    [EMPTY] [EMPTY]  Size [EMPTY] [EMPTY] [EMPTY] [EMPTY] [EMPTY] [EMPTY]        [EMPTY]
##                  (chr)      (chr)   (chr) (chr)   (chr)   (chr)   (chr)   (chr)   (chr)   (chr)          (chr)
## 1              Tumor # Organoid #   Color Day 0   Day 1   Day 2   Day 3   Day 4   Day 5   Day 6          Day 7
## 2  Heterotypic Tumor 1          1     Red    17      13      21      52      74      87     107 differentiated
## 3  Heterotypic Tumor 1          2     Red    10       8      24      71     130     254     305            363
## 4  Heterotypic Tumor 1          3     Red    16      31      44      38      40      44      51             53
## 5  Heterotypic Tumor 1          4     Red    11      12      33      70      79      67      70           dead
## 6  Heterotypic Tumor 1          5     Red     7      11      23      32      25      24      24             24
## 7  Heterotypic Tumor 1          6     Red    19      31      62      80     130     oof     oof            oof
## 8  Heterotypic Tumor 1          7     Red    15      11      30      70      76     177     212            227
## 9  Heterotypic Tumor 1          8     Red    12      21      28      34      38    dead    dead           dead
## 10 Heterotypic Tumor 1          9     Red    12      22      43      61      77     106     121            105
## ..                 ...        ...     ...   ...     ...     ...     ...     ...     ...     ...            ...
## Variables not shown: [EMPTY] (lgl)
```

