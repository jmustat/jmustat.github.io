---
layout:     post   				    
title:     Factor Analysis in R				
subtitle:    #副标题
date:       2021-04-15 			
author:     Jinjian Mu				
header-img: 
catalog: true 						
tags:							

---

Preliminary Work
================

Exploratory factor analysis is letting the data tell you what the latent
structure could be. Confimatory factor analysis is to verify if the
proposed latent structure fits the data well.

I will use the data set `bfi` in the `psych` package to illustrate how
to perform exploratory factor analysis and confirmatory factor analysis.
In the `bfi` data set, the first 25 columns are some self report items,
and we will conduct factor analyses on these 25 items.

    library(psych)
    data(bfi)
    head(bfi)

    ##       A1 A2 A3 A4 A5 C1 C2 C3 C4 C5 E1 E2 E3 E4 E5 N1 N2 N3 N4 N5 O1 O2 O3 O4
    ## 61617  2  4  3  4  4  2  3  3  4  4  3  3  3  4  4  3  4  2  2  3  3  6  3  4
    ## 61618  2  4  5  2  5  5  4  4  3  4  1  1  6  4  3  3  3  3  5  5  4  2  4  3
    ## 61620  5  4  5  4  4  4  5  4  2  5  2  4  4  4  5  4  5  4  2  3  4  2  5  5
    ## 61621  4  4  6  5  5  4  4  3  5  5  5  3  4  4  4  2  5  2  4  1  3  3  4  3
    ## 61622  2  3  3  4  5  4  4  5  3  2  2  2  5  4  5  2  3  4  4  3  3  3  4  3
    ## 61623  6  6  5  6  5  6  6  6  1  3  2  1  6  5  6  3  5  2  2  3  4  3  5  6
    ##       O5 gender education age
    ## 61617  3      1        NA  16
    ## 61618  3      2        NA  18
    ## 61620  2      2        NA  17
    ## 61621  5      2        NA  17
    ## 61622  3      1        NA  17
    ## 61623  1      2         3  21

In psychometrics, we expect all items are in the same direction,
especially for composite scores, so we can sum them up and get a total
score. Therefore, we need to check the internal consistency, and we can
use Cronbach's *α* to measure internal consistency. The calcualted value
below is 0.53, which is not good. Usually, a value greater than 0.7 or
0.8 indicates good internal consistency. After reversing the items that
are negatively correlated with the total score using option
`check.keys = TRUE`, the calculated value is 0.82 indicating a good
internal consistency. We can basically reverse these items, but since
all the items are in the range 0-6, we also want to keep the range. In
the following, I use 7 minus the original scores as the reversed scores.

    alpha(bfi[,1:25])

    ## Warning in alpha(bfi[, 1:25]): Some items were negatively correlated with the total scale and probably 
    ## should be reversed.  
    ## To do this, run the function again with the 'check.keys=TRUE' option

    ## Some items ( A1 C4 C5 E1 E2 N1 N2 N3 N4 N5 O2 O4 O5 ) were negatively correlated with the total scale and 
    ## probably should be reversed.  
    ## To do this, run the function again with the 'check.keys=TRUE' option

    ## 
    ## Reliability analysis   
    ## Call: alpha(x = bfi[, 1:25])
    ## 
    ##   raw_alpha std.alpha G6(smc) average_r S/N   ase mean   sd median_r
    ##       0.53      0.53    0.69     0.044 1.1 0.013  3.8 0.41    0.036
    ## 
    ##  lower alpha upper     95% confidence boundaries
    ## 0.51 0.53 0.56 
    ## 
    ##  Reliability if an item is dropped:
    ##    raw_alpha std.alpha G6(smc) average_r  S/N alpha se var.r med.r
    ## A1      0.54      0.55    0.70     0.048 1.22    0.012 0.043 0.038
    ## A2      0.52      0.52    0.67     0.042 1.06    0.013 0.041 0.036
    ## A3      0.52      0.51    0.67     0.042 1.04    0.013 0.040 0.037
    ## A4      0.53      0.53    0.68     0.044 1.12    0.013 0.042 0.036
    ## A5      0.53      0.52    0.67     0.043 1.09    0.013 0.039 0.037
    ## C1      0.53      0.53    0.68     0.045 1.12    0.013 0.042 0.036
    ## C2      0.52      0.51    0.67     0.042 1.06    0.013 0.042 0.032
    ## C3      0.54      0.53    0.68     0.045 1.14    0.013 0.042 0.037
    ## C4      0.53      0.54    0.69     0.047 1.18    0.013 0.040 0.036
    ## C5      0.54      0.54    0.69     0.048 1.20    0.013 0.040 0.036
    ## E1      0.56      0.56    0.70     0.051 1.28    0.012 0.041 0.038
    ## E2      0.55      0.55    0.69     0.049 1.24    0.012 0.038 0.036
    ## E3      0.52      0.51    0.67     0.042 1.05    0.013 0.040 0.036
    ## E4      0.54      0.53    0.68     0.045 1.13    0.012 0.039 0.032
    ## E5      0.53      0.52    0.67     0.043 1.08    0.013 0.040 0.032
    ## N1      0.48      0.50    0.65     0.040 0.99    0.014 0.040 0.036
    ## N2      0.49      0.50    0.65     0.040 0.99    0.014 0.040 0.037
    ## N3      0.47      0.49    0.65     0.038 0.96    0.014 0.040 0.036
    ## N4      0.50      0.51    0.67     0.042 1.04    0.014 0.040 0.036
    ## N5      0.49      0.50    0.66     0.040 1.01    0.014 0.042 0.036
    ## O1      0.53      0.52    0.68     0.044 1.10    0.013 0.042 0.036
    ## O2      0.53      0.53    0.68     0.045 1.13    0.013 0.043 0.032
    ## O3      0.53      0.52    0.68     0.044 1.10    0.013 0.041 0.036
    ## O4      0.52      0.52    0.68     0.043 1.07    0.013 0.044 0.018
    ## O5      0.54      0.55    0.69     0.048 1.20    0.013 0.043 0.036
    ## 
    ##  Item statistics 
    ##       n raw.r std.r   r.cor  r.drop mean  sd
    ## A1 2784 0.153 0.124  0.0141  0.0111  2.4 1.4
    ## A2 2773 0.284 0.345  0.3120  0.1714  4.8 1.2
    ## A3 2774 0.309 0.374  0.3529  0.1885  4.6 1.3
    ## A4 2781 0.234 0.270  0.1993  0.0894  4.7 1.5
    ## A5 2784 0.241 0.314  0.2791  0.1204  4.6 1.3
    ## C1 2779 0.213 0.266  0.1996  0.0920  4.5 1.2
    ## C2 2776 0.308 0.352  0.3152  0.1834  4.4 1.3
    ## C3 2780 0.189 0.235  0.1562  0.0616  4.3 1.3
    ## C4 2774 0.226 0.171  0.1052  0.0915  2.6 1.4
    ## C5 2784 0.216 0.150  0.0745  0.0569  3.3 1.6
    ## E1 2777 0.079 0.023 -0.0848 -0.0820  3.0 1.6
    ## E2 2784 0.161 0.083  0.0068  0.0014  3.1 1.6
    ## E3 2775 0.301 0.369  0.3438  0.1730  4.0 1.4
    ## E4 2791 0.194 0.254  0.2158  0.0495  4.4 1.5
    ## E5 2779 0.248 0.316  0.2743  0.1187  4.4 1.3
    ## N1 2778 0.518 0.456  0.4781  0.3909  2.9 1.6
    ## N2 2779 0.507 0.450  0.4688  0.3844  3.5 1.5
    ## N3 2789 0.560 0.499  0.5143  0.4380  3.2 1.6
    ## N4 2764 0.445 0.374  0.3544  0.3072  3.2 1.6
    ## N5 2771 0.490 0.431  0.4042  0.3542  3.0 1.6
    ## O1 2778 0.224 0.292  0.2275  0.1144  4.8 1.1
    ## O2 2800 0.290 0.246  0.1633  0.1390  2.7 1.6
    ## O3 2772 0.227 0.297  0.2479  0.1095  4.4 1.2
    ## O4 2786 0.326 0.339  0.2718  0.2110  4.9 1.2
    ## O5 2780 0.175 0.142  0.0424  0.0426  2.5 1.3
    ## 
    ## Non missing response frequency for each item
    ##       1    2    3    4    5    6 miss
    ## A1 0.33 0.29 0.14 0.12 0.08 0.03 0.01
    ## A2 0.02 0.05 0.05 0.20 0.37 0.31 0.01
    ## A3 0.03 0.06 0.07 0.20 0.36 0.27 0.01
    ## A4 0.05 0.08 0.07 0.16 0.24 0.41 0.01
    ## A5 0.02 0.07 0.09 0.22 0.35 0.25 0.01
    ## C1 0.03 0.06 0.10 0.24 0.37 0.21 0.01
    ## C2 0.03 0.09 0.11 0.23 0.35 0.20 0.01
    ## C3 0.03 0.09 0.11 0.27 0.34 0.17 0.01
    ## C4 0.28 0.29 0.17 0.16 0.08 0.02 0.01
    ## C5 0.18 0.20 0.12 0.22 0.17 0.10 0.01
    ## E1 0.24 0.23 0.15 0.16 0.13 0.09 0.01
    ## E2 0.19 0.24 0.12 0.22 0.14 0.09 0.01
    ## E3 0.05 0.11 0.15 0.30 0.27 0.13 0.01
    ## E4 0.05 0.09 0.10 0.16 0.34 0.26 0.00
    ## E5 0.03 0.08 0.10 0.22 0.34 0.22 0.01
    ## N1 0.24 0.24 0.15 0.19 0.12 0.07 0.01
    ## N2 0.12 0.19 0.15 0.26 0.18 0.10 0.01
    ## N3 0.18 0.23 0.13 0.21 0.16 0.09 0.00
    ## N4 0.17 0.24 0.15 0.22 0.14 0.09 0.01
    ## N5 0.24 0.24 0.14 0.18 0.12 0.09 0.01
    ## O1 0.01 0.04 0.08 0.22 0.33 0.33 0.01
    ## O2 0.29 0.26 0.14 0.16 0.10 0.06 0.00
    ## O3 0.03 0.05 0.11 0.28 0.34 0.20 0.01
    ## O4 0.02 0.04 0.06 0.17 0.32 0.39 0.01
    ## O5 0.27 0.32 0.19 0.13 0.07 0.03 0.01

    alpha(bfi[,1:25], check.keys = TRUE)

    ## Warning in alpha(bfi[, 1:25], check.keys = TRUE): Some items were negatively correlated with total scale and were automatically reversed.
    ##  This is indicated by a negative sign for the variable name.

    ## 
    ## Reliability analysis   
    ## Call: alpha(x = bfi[, 1:25], check.keys = TRUE)
    ## 
    ##   raw_alpha std.alpha G6(smc) average_r S/N    ase mean   sd median_r
    ##       0.82      0.82    0.86      0.15 4.5 0.0049  4.2 0.61     0.13
    ## 
    ##  lower alpha upper     95% confidence boundaries
    ## 0.81 0.82 0.83 
    ## 
    ##  Reliability if an item is dropped:
    ##     raw_alpha std.alpha G6(smc) average_r S/N alpha se var.r med.r
    ## A1-      0.82      0.82    0.86      0.16 4.5   0.0048 0.020  0.13
    ## A2       0.81      0.81    0.85      0.15 4.3   0.0050 0.019  0.12
    ## A3       0.81      0.81    0.85      0.15 4.2   0.0051 0.019  0.12
    ## A4       0.82      0.81    0.86      0.15 4.4   0.0050 0.020  0.12
    ## A5       0.81      0.81    0.85      0.15 4.2   0.0051 0.019  0.12
    ## C1       0.82      0.82    0.86      0.16 4.4   0.0049 0.020  0.13
    ## C2       0.82      0.82    0.86      0.16 4.4   0.0049 0.019  0.13
    ## C3       0.82      0.82    0.86      0.16 4.4   0.0049 0.020  0.13
    ## C4-      0.81      0.81    0.85      0.15 4.2   0.0051 0.020  0.12
    ## C5-      0.81      0.81    0.85      0.15 4.2   0.0052 0.020  0.12
    ## E1-      0.82      0.81    0.86      0.15 4.4   0.0050 0.020  0.13
    ## E2-      0.81      0.80    0.85      0.15 4.1   0.0053 0.019  0.12
    ## E3       0.81      0.81    0.85      0.15 4.2   0.0051 0.019  0.13
    ## E4       0.81      0.81    0.85      0.15 4.2   0.0052 0.019  0.12
    ## E5       0.81      0.81    0.85      0.15 4.2   0.0051 0.020  0.12
    ## N1-      0.81      0.81    0.85      0.15 4.3   0.0051 0.018  0.13
    ## N2-      0.81      0.81    0.85      0.15 4.3   0.0051 0.018  0.13
    ## N3-      0.81      0.81    0.85      0.15 4.3   0.0051 0.019  0.13
    ## N4-      0.81      0.81    0.85      0.15 4.2   0.0052 0.019  0.12
    ## N5-      0.82      0.81    0.86      0.15 4.4   0.0050 0.020  0.13
    ## O1       0.82      0.82    0.86      0.16 4.4   0.0050 0.020  0.13
    ## O2-      0.82      0.82    0.86      0.16 4.5   0.0048 0.020  0.13
    ## O3       0.81      0.81    0.86      0.15 4.3   0.0050 0.020  0.13
    ## O4-      0.83      0.83    0.87      0.17 4.8   0.0048 0.018  0.13
    ## O5-      0.82      0.82    0.86      0.16 4.5   0.0049 0.020  0.13
    ## 
    ##  Item statistics 
    ##        n raw.r std.r r.cor r.drop mean  sd
    ## A1- 2784  0.28  0.28  0.22  0.193  4.6 1.4
    ## A2  2773  0.44  0.47  0.44  0.377  4.8 1.2
    ## A3  2774  0.49  0.51  0.49  0.425  4.6 1.3
    ## A4  2781  0.42  0.42  0.38  0.332  4.7 1.5
    ## A5  2784  0.54  0.56  0.54  0.480  4.6 1.3
    ## C1  2779  0.36  0.38  0.33  0.285  4.5 1.2
    ## C2  2776  0.35  0.37  0.34  0.274  4.4 1.3
    ## C3  2780  0.35  0.36  0.31  0.270  4.3 1.3
    ## C4- 2774  0.52  0.52  0.50  0.448  4.4 1.4
    ## C5- 2784  0.54  0.53  0.51  0.461  3.7 1.6
    ## E1- 2777  0.42  0.41  0.38  0.328  4.0 1.6
    ## E2- 2784  0.61  0.60  0.59  0.535  3.9 1.6
    ## E3  2775  0.50  0.52  0.50  0.432  4.0 1.4
    ## E4  2791  0.54  0.55  0.53  0.473  4.4 1.5
    ## E5  2779  0.51  0.53  0.50  0.440  4.4 1.3
    ## N1- 2778  0.49  0.46  0.46  0.407  4.1 1.6
    ## N2- 2779  0.48  0.45  0.44  0.398  3.5 1.5
    ## N3- 2789  0.48  0.45  0.43  0.391  3.8 1.6
    ## N4- 2764  0.57  0.54  0.53  0.495  3.8 1.6
    ## N5- 2771  0.43  0.40  0.36  0.334  4.0 1.6
    ## O1  2778  0.35  0.38  0.33  0.284  4.8 1.1
    ## O2- 2800  0.30  0.29  0.24  0.202  4.3 1.6
    ## O3  2772  0.42  0.44  0.41  0.347  4.4 1.2
    ## O4- 2786  0.12  0.11  0.04  0.043  2.1 1.2
    ## O5- 2780  0.28  0.29  0.24  0.201  4.5 1.3
    ## 
    ## Non missing response frequency for each item
    ##       1    2    3    4    5    6 miss
    ## A1 0.33 0.29 0.14 0.12 0.08 0.03 0.01
    ## A2 0.02 0.05 0.05 0.20 0.37 0.31 0.01
    ## A3 0.03 0.06 0.07 0.20 0.36 0.27 0.01
    ## A4 0.05 0.08 0.07 0.16 0.24 0.41 0.01
    ## A5 0.02 0.07 0.09 0.22 0.35 0.25 0.01
    ## C1 0.03 0.06 0.10 0.24 0.37 0.21 0.01
    ## C2 0.03 0.09 0.11 0.23 0.35 0.20 0.01
    ## C3 0.03 0.09 0.11 0.27 0.34 0.17 0.01
    ## C4 0.28 0.29 0.17 0.16 0.08 0.02 0.01
    ## C5 0.18 0.20 0.12 0.22 0.17 0.10 0.01
    ## E1 0.24 0.23 0.15 0.16 0.13 0.09 0.01
    ## E2 0.19 0.24 0.12 0.22 0.14 0.09 0.01
    ## E3 0.05 0.11 0.15 0.30 0.27 0.13 0.01
    ## E4 0.05 0.09 0.10 0.16 0.34 0.26 0.00
    ## E5 0.03 0.08 0.10 0.22 0.34 0.22 0.01
    ## N1 0.24 0.24 0.15 0.19 0.12 0.07 0.01
    ## N2 0.12 0.19 0.15 0.26 0.18 0.10 0.01
    ## N3 0.18 0.23 0.13 0.21 0.16 0.09 0.00
    ## N4 0.17 0.24 0.15 0.22 0.14 0.09 0.01
    ## N5 0.24 0.24 0.14 0.18 0.12 0.09 0.01
    ## O1 0.01 0.04 0.08 0.22 0.33 0.33 0.01
    ## O2 0.29 0.26 0.14 0.16 0.10 0.06 0.00
    ## O3 0.03 0.05 0.11 0.28 0.34 0.20 0.01
    ## O4 0.02 0.04 0.06 0.17 0.32 0.39 0.01
    ## O5 0.27 0.32 0.19 0.13 0.07 0.03 0.01

    bfi[, c("A1", "C4", "C5", "E1", "E2", "N1", "N2", "N3", "N4", "N5", "O2", "O4", "O5")] <-
      7 - bfi[, c("A1", "C4", "C5", "E1", "E2", "N1", "N2", "N3", "N4", "N5", "O2", "O4", "O5")]

Exploratory factor analysis
===========================

At first, we want to figure out how many latent factors we need, and we
can use the scree plot. The number of eigenvalues great than 1 is the
maxinum of the number of factors we should try. For this data set, we
should have at most 6 factors. I like to use the `scree.plot` function
in the `psy` package for scree plots.

    library(psy)

    ## 
    ## Attaching package: 'psy'

    ## The following object is masked from 'package:psych':
    ## 
    ##     wkappa

    scree.plot(bfi[,1:25])

![](img/factoranalysis/unnamed-chunk-3-1.png)

We hope 1) the factors can explain enough variance, 2) all items can be
well explained, and 3) each item can only be explained by one facotr.
For 3), we need to do rotation. There are different types of rotation,
and the default oblimin ratation in function `fa` works well. For the
four-factor model, some items are not well explained. For example, all
of the loadings for item A1 are small. The five-factor model is okay in
3), but it can only explain 41% of the variance. The six-factor model
improves a little bit by explaining 3% more, but it is not good in the
sense of 3).

    fit_efa_4f <- fa(bfi[,1:25], nfactors = 4, rotate = "oblimin")

    ## Loading required namespace: GPArotation

    fit_efa_4f

    ## Factor Analysis using method =  minres
    ## Call: fa(r = bfi[, 1:25], nfactors = 4, rotate = "oblimin")
    ## Standardized loadings (pattern matrix) based upon correlation matrix
    ##      MR1   MR2   MR3   MR4    h2   u2 com
    ## A1  0.20  0.09 -0.02  0.02 0.056 0.94 1.4
    ## A2  0.52 -0.08  0.12 -0.01 0.299 0.70 1.2
    ## A3  0.63 -0.06  0.06 -0.01 0.406 0.59 1.0
    ## A4  0.41  0.01  0.21 -0.17 0.243 0.76 1.9
    ## A5  0.63  0.06  0.02  0.01 0.429 0.57 1.0
    ## C1 -0.03 -0.05  0.54  0.15 0.328 0.67 1.2
    ## C2  0.01 -0.15  0.67  0.03 0.454 0.55 1.1
    ## C3  0.03 -0.03  0.57 -0.07 0.319 0.68 1.0
    ## C4 -0.04  0.21  0.60  0.06 0.453 0.55 1.3
    ## C5  0.09  0.25  0.52 -0.08 0.410 0.59 1.6
    ## E1  0.51  0.03 -0.14  0.10 0.276 0.72 1.2
    ## E2  0.56  0.22 -0.04  0.07 0.418 0.58 1.3
    ## E3  0.57 -0.06 -0.02  0.27 0.441 0.56 1.5
    ## E4  0.72  0.06 -0.01 -0.09 0.505 0.49 1.0
    ## E5  0.39 -0.07  0.23  0.21 0.340 0.66 2.3
    ## N1 -0.01  0.74  0.01  0.03 0.551 0.45 1.0
    ## N2  0.02  0.73 -0.01 -0.02 0.542 0.46 1.0
    ## N3 -0.02  0.75  0.01 -0.01 0.563 0.44 1.0
    ## N4  0.21  0.58  0.08 -0.06 0.443 0.56 1.3
    ## N5 -0.03  0.56 -0.03  0.17 0.335 0.66 1.2
    ## O1  0.12 -0.02  0.07  0.50 0.315 0.69 1.2
    ## O2 -0.18  0.20  0.07  0.47 0.264 0.74 1.7
    ## O3  0.22 -0.05  0.01  0.60 0.471 0.53 1.3
    ## O4  0.08  0.24 -0.03 -0.32 0.154 0.85 2.0
    ## O5 -0.10  0.09  0.04  0.54 0.291 0.71 1.1
    ## 
    ##                        MR1  MR2  MR3  MR4
    ## SS loadings           3.14 2.68 1.96 1.53
    ## Proportion Var        0.13 0.11 0.08 0.06
    ## Cumulative Var        0.13 0.23 0.31 0.37
    ## Proportion Explained  0.34 0.29 0.21 0.16
    ## Cumulative Proportion 0.34 0.62 0.84 1.00
    ## 
    ##  With factor correlations of 
    ##      MR1  MR2  MR3  MR4
    ## MR1 1.00 0.19 0.27 0.22
    ## MR2 0.19 1.00 0.18 0.05
    ## MR3 0.27 0.18 1.00 0.18
    ## MR4 0.22 0.05 0.18 1.00
    ## 
    ## Mean item complexity =  1.3
    ## Test of the hypothesis that 4 factors are sufficient.
    ## 
    ## The degrees of freedom for the null model are  300  and the objective function was  7.23 with Chi Square of  20163.79
    ## The degrees of freedom for the model are 206  and the objective function was  1.23 
    ## 
    ## The root mean square of the residuals (RMSR) is  0.05 
    ## The df corrected root mean square of the residuals is  0.06 
    ## 
    ## The harmonic number of observations is  2762 with the empirical chi square  3555.08  with prob <  0 
    ## The total number of observations was  2800  with Likelihood Chi Square =  3421.61  with prob <  0 
    ## 
    ## Tucker Lewis Index of factoring reliability =  0.764
    ## RMSEA index =  0.075  and the 90 % confidence intervals are  0.072 0.077
    ## BIC =  1786.51
    ## Fit based upon off diagonal values = 0.95
    ## Measures of factor score adequacy             
    ##                                                    MR1  MR2  MR3  MR4
    ## Correlation of (regression) scores with factors   0.92 0.92 0.87 0.84
    ## Multiple R square of scores with factors          0.84 0.84 0.76 0.70
    ## Minimum correlation of possible factor scores     0.68 0.68 0.52 0.40

    fit_efa_5f <- fa(bfi[,1:25], nfactors = 5, rotate = "oblimin")
    fit_efa_5f

    ## Factor Analysis using method =  minres
    ## Call: fa(r = bfi[, 1:25], nfactors = 5, rotate = "oblimin")
    ## Standardized loadings (pattern matrix) based upon correlation matrix
    ##      MR2   MR1   MR3   MR5   MR4   h2   u2 com
    ## A1  0.21 -0.17 -0.07  0.41  0.06 0.19 0.81 2.0
    ## A2  0.02  0.00  0.08  0.64  0.03 0.45 0.55 1.0
    ## A3  0.03  0.12  0.02  0.66  0.03 0.52 0.48 1.1
    ## A4  0.06  0.06  0.19  0.43 -0.15 0.28 0.72 1.7
    ## A5  0.11  0.23  0.01  0.53  0.04 0.46 0.54 1.5
    ## C1 -0.07 -0.03  0.55 -0.02  0.15 0.33 0.67 1.2
    ## C2 -0.15 -0.09  0.67  0.08  0.04 0.45 0.55 1.2
    ## C3 -0.03 -0.06  0.57  0.09 -0.07 0.32 0.68 1.1
    ## C4  0.17  0.00  0.61 -0.04  0.05 0.45 0.55 1.2
    ## C5  0.19  0.14  0.55 -0.02 -0.09 0.43 0.57 1.4
    ## E1 -0.06  0.56 -0.11  0.08  0.10 0.35 0.65 1.2
    ## E2  0.10  0.68  0.02  0.05  0.06 0.54 0.46 1.1
    ## E3 -0.08  0.42  0.00  0.25  0.28 0.44 0.56 2.6
    ## E4 -0.01  0.59  0.02  0.29 -0.08 0.53 0.47 1.5
    ## E5 -0.15  0.42  0.27  0.05  0.21 0.40 0.60 2.6
    ## N1  0.81 -0.10  0.00  0.11  0.05 0.65 0.35 1.1
    ## N2  0.78 -0.04 -0.01  0.09 -0.01 0.60 0.40 1.0
    ## N3  0.71  0.10  0.04 -0.08 -0.02 0.55 0.45 1.1
    ## N4  0.47  0.39  0.14 -0.09 -0.08 0.49 0.51 2.3
    ## N5  0.49  0.20  0.00 -0.21  0.15 0.35 0.65 2.0
    ## O1 -0.02  0.10  0.07  0.02  0.51 0.31 0.69 1.1
    ## O2  0.19 -0.06  0.08 -0.16  0.46 0.26 0.74 1.7
    ## O3 -0.03  0.15  0.02  0.08  0.61 0.46 0.54 1.2
    ## O4  0.13  0.32  0.02 -0.17 -0.37 0.25 0.75 2.7
    ## O5  0.13 -0.10  0.03 -0.04  0.54 0.30 0.70 1.2
    ## 
    ##                        MR2  MR1  MR3  MR5  MR4
    ## SS loadings           2.57 2.20 2.03 1.99 1.59
    ## Proportion Var        0.10 0.09 0.08 0.08 0.06
    ## Cumulative Var        0.10 0.19 0.27 0.35 0.41
    ## Proportion Explained  0.25 0.21 0.20 0.19 0.15
    ## Cumulative Proportion 0.25 0.46 0.66 0.85 1.00
    ## 
    ##  With factor correlations of 
    ##      MR2  MR1  MR3  MR5  MR4
    ## MR2 1.00 0.21 0.19 0.04 0.01
    ## MR1 0.21 1.00 0.23 0.33 0.17
    ## MR3 0.19 0.23 1.00 0.20 0.19
    ## MR5 0.04 0.33 0.20 1.00 0.19
    ## MR4 0.01 0.17 0.19 0.19 1.00
    ## 
    ## Mean item complexity =  1.5
    ## Test of the hypothesis that 5 factors are sufficient.
    ## 
    ## The degrees of freedom for the null model are  300  and the objective function was  7.23 with Chi Square of  20163.79
    ## The degrees of freedom for the model are 185  and the objective function was  0.65 
    ## 
    ## The root mean square of the residuals (RMSR) is  0.03 
    ## The df corrected root mean square of the residuals is  0.04 
    ## 
    ## The harmonic number of observations is  2762 with the empirical chi square  1392.16  with prob <  5.6e-184 
    ## The total number of observations was  2800  with Likelihood Chi Square =  1808.94  with prob <  4.3e-264 
    ## 
    ## Tucker Lewis Index of factoring reliability =  0.867
    ## RMSEA index =  0.056  and the 90 % confidence intervals are  0.054 0.058
    ## BIC =  340.53
    ## Fit based upon off diagonal values = 0.98
    ## Measures of factor score adequacy             
    ##                                                    MR2  MR1  MR3  MR5  MR4
    ## Correlation of (regression) scores with factors   0.92 0.89 0.88 0.88 0.84
    ## Multiple R square of scores with factors          0.85 0.79 0.77 0.77 0.71
    ## Minimum correlation of possible factor scores     0.70 0.59 0.54 0.54 0.42

    fit_efa_6f <- fa(bfi[,1:25], nfactors = 6, rotate = "oblimin")
    fit_efa_6f

    ## Factor Analysis using method =  minres
    ## Call: fa(r = bfi[, 1:25], nfactors = 6, rotate = "oblimin")
    ## Standardized loadings (pattern matrix) based upon correlation matrix
    ##      MR2   MR3   MR1   MR5   MR4   MR6   h2   u2 com
    ## A1  0.09 -0.08 -0.09  0.56 -0.06  0.30 0.34 0.66 1.7
    ## A2 -0.04  0.08  0.04  0.68  0.00  0.05 0.50 0.50 1.1
    ## A3  0.02  0.03  0.12  0.61  0.07 -0.11 0.51 0.49 1.2
    ## A4  0.07  0.19  0.06  0.39 -0.10 -0.15 0.28 0.72 2.1
    ## A5  0.16  0.01  0.19  0.45  0.13 -0.21 0.47 0.53 2.3
    ## C1 -0.01  0.54 -0.06 -0.06  0.19 -0.07 0.34 0.66 1.3
    ## C2 -0.07  0.67 -0.14  0.02  0.11 -0.17 0.49 0.51 1.3
    ## C3 -0.01  0.55 -0.06  0.08 -0.04 -0.06 0.31 0.69 1.1
    ## C4  0.06  0.64  0.09  0.06 -0.07  0.30 0.57 0.43 1.5
    ## C5  0.15  0.54  0.18  0.01 -0.11  0.05 0.43 0.57 1.5
    ## E1 -0.13 -0.11  0.59  0.12  0.08  0.09 0.39 0.61 1.3
    ## E2  0.05  0.02  0.70  0.07  0.06  0.03 0.56 0.44 1.0
    ## E3  0.00  0.00  0.34  0.15  0.40 -0.21 0.48 0.52 2.9
    ## E4  0.05  0.03  0.53  0.20  0.04 -0.29 0.55 0.45 1.9
    ## E5 -0.15  0.27  0.40  0.05  0.23 -0.01 0.40 0.60 2.8
    ## N1  0.84 -0.01 -0.10  0.07  0.05  0.00 0.68 0.32 1.0
    ## N2  0.83 -0.02 -0.06  0.04  0.01 -0.07 0.66 0.34 1.0
    ## N3  0.67  0.03  0.13 -0.07 -0.05  0.08 0.54 0.46 1.1
    ## N4  0.43  0.13  0.42 -0.08 -0.10  0.05 0.49 0.51 2.4
    ## N5  0.44  0.00  0.23 -0.18  0.10  0.16 0.35 0.65 2.4
    ## O1  0.05  0.07  0.04 -0.05  0.57 -0.03 0.35 0.65 1.1
    ## O2  0.11  0.07  0.00 -0.09  0.36  0.36 0.29 0.71 2.4
    ## O3  0.02  0.02  0.09  0.03  0.65  0.02 0.48 0.52 1.1
    ## O4  0.09  0.02  0.35 -0.15 -0.37 -0.04 0.25 0.75 2.5
    ## O5  0.03  0.02 -0.04  0.04  0.45  0.41 0.37 0.63 2.0
    ## 
    ##                        MR2  MR3  MR1  MR5  MR4  MR6
    ## SS loadings           2.48 2.05 2.17 1.88 1.68 0.82
    ## Proportion Var        0.10 0.08 0.09 0.08 0.07 0.03
    ## Cumulative Var        0.10 0.18 0.27 0.34 0.41 0.44
    ## Proportion Explained  0.22 0.18 0.20 0.17 0.15 0.07
    ## Cumulative Proportion 0.22 0.41 0.60 0.77 0.93 1.00
    ## 
    ##  With factor correlations of 
    ##       MR2  MR3   MR1   MR5   MR4   MR6
    ## MR2  1.00 0.18  0.25  0.10 -0.02  0.16
    ## MR3  0.18 1.00  0.21  0.19  0.19  0.02
    ## MR1  0.25 0.21  1.00  0.30  0.20 -0.08
    ## MR5  0.10 0.19  0.30  1.00  0.25 -0.14
    ## MR4 -0.02 0.19  0.20  0.25  1.00 -0.02
    ## MR6  0.16 0.02 -0.08 -0.14 -0.02  1.00
    ## 
    ## Mean item complexity =  1.7
    ## Test of the hypothesis that 6 factors are sufficient.
    ## 
    ## The degrees of freedom for the null model are  300  and the objective function was  7.23 with Chi Square of  20163.79
    ## The degrees of freedom for the model are 165  and the objective function was  0.37 
    ## 
    ## The root mean square of the residuals (RMSR) is  0.02 
    ## The df corrected root mean square of the residuals is  0.03 
    ## 
    ## The harmonic number of observations is  2762 with the empirical chi square  639.91  with prob <  4.1e-57 
    ## The total number of observations was  2800  with Likelihood Chi Square =  1032.48  with prob <  1.8e-125 
    ## 
    ## Tucker Lewis Index of factoring reliability =  0.92
    ## RMSEA index =  0.043  and the 90 % confidence intervals are  0.041 0.046
    ## BIC =  -277.19
    ## Fit based upon off diagonal values = 0.99
    ## Measures of factor score adequacy             
    ##                                                    MR2  MR3  MR1  MR5  MR4  MR6
    ## Correlation of (regression) scores with factors   0.93 0.89 0.89 0.87 0.86 0.77
    ## Multiple R square of scores with factors          0.86 0.78 0.79 0.76 0.73 0.59
    ## Minimum correlation of possible factor scores     0.72 0.57 0.59 0.53 0.46 0.17

Confimatory factor analysis
===========================

Based on the results of EFA and the nature of the data, we propose a
five-factor model with uncorrelated latent structure, and we want to use
CFA to verify the latent structure. `=~` means the latent factor can
explain the following items. `f1 ~~ 0*f2 + 0*f3 + 0*f4 + 0*f5` means
factor f1 is not correlated to factor f2 - f5.

    library(lavaan)

    ## This is lavaan 0.6-8
    ## lavaan is FREE software! Please report any bugs.

    ## 
    ## Attaching package: 'lavaan'

    ## The following object is masked from 'package:psych':
    ## 
    ##     cor2cov

    mod_cfa_5f <- "f1 =~ N1 + N2 + N3 + N4 + N5
                   f2 =~ E1 + E2 + E3 + E4 + E5
                   f3 =~ C1 + C2 + C3 + C4 + C5
                   f4 =~ A1 + A2 + A3 + A4 + A5
                   f5 =~ O1 + O2 + O3 + O4 + O5
                   f1 ~~ 0*f2 + 0*f3 + 0*f4 + 0*f5
                   f2 ~~ 0*f3 + 0*f4 + 0*f5
                   f3 ~~ 0*f4 + 0*f5
                   f4 ~~ 0*f5"
    fit_cfa_5f <- cfa(mod_cfa_5f, data = bfi)
    summary(fit_cfa_5f, standardize = TRUE, fit.measures = TRUE, rsquare = TRUE)

    ## lavaan 0.6-8 ended normally after 53 iterations
    ## 
    ##   Estimator                                         ML
    ##   Optimization method                           NLMINB
    ##   Number of model parameters                        50
    ##                                                       
    ##                                                   Used       Total
    ##   Number of observations                          2436        2800
    ##                                                                   
    ## Model Test User Model:
    ##                                                       
    ##   Test statistic                              5639.709
    ##   Degrees of freedom                               275
    ##   P-value (Chi-square)                           0.000
    ## 
    ## Model Test Baseline Model:
    ## 
    ##   Test statistic                             18222.116
    ##   Degrees of freedom                               300
    ##   P-value                                        0.000
    ## 
    ## User Model versus Baseline Model:
    ## 
    ##   Comparative Fit Index (CFI)                    0.701
    ##   Tucker-Lewis Index (TLI)                       0.673
    ## 
    ## Loglikelihood and Information Criteria:
    ## 
    ##   Loglikelihood user model (H0)            -100577.359
    ##   Loglikelihood unrestricted model (H1)     -97757.504
    ##                                                       
    ##   Akaike (AIC)                              201254.718
    ##   Bayesian (BIC)                            201544.623
    ##   Sample-size adjusted Bayesian (BIC)       201385.762
    ## 
    ## Root Mean Square Error of Approximation:
    ## 
    ##   RMSEA                                          0.089
    ##   90 Percent confidence interval - lower         0.087
    ##   90 Percent confidence interval - upper         0.092
    ##   P-value RMSEA <= 0.05                          0.000
    ## 
    ## Standardized Root Mean Square Residual:
    ## 
    ##   SRMR                                           0.138
    ## 
    ## Parameter Estimates:
    ## 
    ##   Standard errors                             Standard
    ##   Information                                 Expected
    ##   Information saturated (h1) model          Structured
    ## 
    ## Latent Variables:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##   f1 =~                                                                 
    ##     N1                1.000                               1.310    0.832
    ##     N2                0.947    0.024   40.245    0.000    1.240    0.809
    ##     N3                0.873    0.024   35.898    0.000    1.144    0.717
    ##     N4                0.665    0.025   26.891    0.000    0.872    0.556
    ##     N5                0.616    0.026   23.814    0.000    0.808    0.498
    ##   f2 =~                                                                 
    ##     E1                1.000                               0.989    0.606
    ##     E2                1.205    0.048   25.012    0.000    1.192    0.739
    ##     E3                0.785    0.036   21.573    0.000    0.776    0.574
    ##     E4                1.034    0.042   24.402    0.000    1.023    0.697
    ##     E5                0.713    0.035   20.195    0.000    0.705    0.525
    ##   f3 =~                                                                 
    ##     C1                1.000                               0.691    0.559
    ##     C2                1.172    0.057   20.409    0.000    0.810    0.614
    ##     C3                1.047    0.054   19.347    0.000    0.723    0.560
    ##     C4                1.377    0.064   21.526    0.000    0.951    0.691
    ##     C5                1.403    0.070   20.029    0.000    0.969    0.594
    ##   f4 =~                                                                 
    ##     A1                1.000                               0.537    0.381
    ##     A2                1.468    0.092   15.885    0.000    0.788    0.668
    ##     A3                1.887    0.116   16.273    0.000    1.012    0.772
    ##     A4                1.394    0.097   14.400    0.000    0.748    0.504
    ##     A5                1.502    0.096   15.658    0.000    0.806    0.634
    ##   f5 =~                                                                 
    ##     O1                1.000                               0.623    0.553
    ##     O2                1.165    0.077   15.042    0.000    0.726    0.467
    ##     O3                1.248    0.074   16.878    0.000    0.777    0.645
    ##     O4               -0.551    0.052  -10.515    0.000   -0.343   -0.288
    ##     O5                1.106    0.069   15.970    0.000    0.689    0.520
    ## 
    ## Covariances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##   f1 ~~                                                                 
    ##     f2                0.000                               0.000    0.000
    ##     f3                0.000                               0.000    0.000
    ##     f4                0.000                               0.000    0.000
    ##     f5                0.000                               0.000    0.000
    ##   f2 ~~                                                                 
    ##     f3                0.000                               0.000    0.000
    ##     f4                0.000                               0.000    0.000
    ##     f5                0.000                               0.000    0.000
    ##   f3 ~~                                                                 
    ##     f4                0.000                               0.000    0.000
    ##     f5                0.000                               0.000    0.000
    ##   f4 ~~                                                                 
    ##     f5                0.000                               0.000    0.000
    ## 
    ## Variances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .N1                0.766    0.037   20.793    0.000    0.766    0.309
    ##    .N2                0.812    0.036   22.814    0.000    0.812    0.345
    ##    .N3                1.234    0.043   28.397    0.000    1.234    0.486
    ##    .N4                1.703    0.053   32.241    0.000    1.703    0.691
    ##    .N5                1.982    0.060   32.950    0.000    1.982    0.752
    ##    .E1                1.682    0.058   29.081    0.000    1.682    0.632
    ##    .E2                1.182    0.052   22.716    0.000    1.182    0.454
    ##    .E3                1.224    0.041   30.002    0.000    1.224    0.670
    ##    .E4                1.106    0.044   25.256    0.000    1.106    0.514
    ##    .E5                1.306    0.042   31.131    0.000    1.306    0.724
    ##    .C1                1.048    0.036   29.420    0.000    1.048    0.687
    ##    .C2                1.084    0.039   27.560    0.000    1.084    0.623
    ##    .C3                1.143    0.039   29.393    0.000    1.143    0.686
    ##    .C4                0.990    0.042   23.804    0.000    0.990    0.523
    ##    .C5                1.726    0.061   28.320    0.000    1.726    0.648
    ##    .A1                1.691    0.051   33.160    0.000    1.691    0.855
    ##    .A2                0.770    0.030   26.076    0.000    0.770    0.554
    ##    .A3                0.694    0.036   19.186    0.000    0.694    0.404
    ##    .A4                1.645    0.052   31.366    0.000    1.645    0.746
    ##    .A5                0.965    0.035   27.652    0.000    0.965    0.598
    ##    .O1                0.881    0.034   26.289    0.000    0.881    0.694
    ##    .O2                1.884    0.064   29.555    0.000    1.884    0.782
    ##    .O3                0.849    0.040   21.122    0.000    0.849    0.584
    ##    .O4                1.305    0.039   33.218    0.000    1.305    0.917
    ##    .O5                1.278    0.046   27.702    0.000    1.278    0.729
    ##     f1                1.716    0.074   23.242    0.000    1.000    1.000
    ##     f2                0.978    0.067   14.564    0.000    1.000    1.000
    ##     f3                0.477    0.037   12.866    0.000    1.000    1.000
    ##     f4                0.288    0.033    8.601    0.000    1.000    1.000
    ##     f5                0.388    0.034   11.331    0.000    1.000    1.000
    ## 
    ## R-Square:
    ##                    Estimate
    ##     N1                0.691
    ##     N2                0.655
    ##     N3                0.514
    ##     N4                0.309
    ##     N5                0.248
    ##     E1                0.368
    ##     E2                0.546
    ##     E3                0.330
    ##     E4                0.486
    ##     E5                0.276
    ##     C1                0.313
    ##     C2                0.377
    ##     C3                0.314
    ##     C4                0.477
    ##     C5                0.352
    ##     A1                0.145
    ##     A2                0.446
    ##     A3                0.596
    ##     A4                0.254
    ##     A5                0.402
    ##     O1                0.306
    ##     O2                0.218
    ##     O3                0.416
    ##     O4                0.083
    ##     O5                0.271

According to the model fit indices, the proposed five-factor model is
not a good fit to the data. Usually, a CFI &gt; 0.95, a TLI &gt; 0.90, a
RMSEA &lt; 0.05, and a SRMR &lt; 0.05 indicate a good fit. We can check
the modification indices to see how we can improve the model.

    modificationindices(fit_cfa_5f, sort. = TRUE, minimum.value = 10)

    ##     lhs op rhs      mi    epc sepc.lv sepc.all sepc.nox
    ## 31   f2 ~~  f4 626.628  0.345   0.651    0.651    0.651
    ## 166  N1 ~~  N2 371.649  0.862   0.862    1.094    1.094
    ## 89   f2 =~  N4 256.519  0.500   0.495    0.315    0.315
    ## 103  f2 =~  O3 247.488  0.386   0.382    0.317    0.317
    ## 100  f2 =~  A5 204.782  0.353   0.349    0.275    0.275
    ## 153  f5 =~  E3 204.756  0.674   0.420    0.311    0.311
    ## 32   f2 ~~  f5 191.600  0.241   0.392    0.392    0.392
    ## 115  f3 =~  E5 172.955  0.532   0.368    0.274    0.274
    ## 30   f2 ~~  f3 168.789  0.235   0.344    0.344    0.344
    ## 213  N3 ~~  N4 161.206  0.445   0.445    0.307    0.307
    ## 33   f3 ~~  f4 153.382  0.122   0.329    0.329    0.329
    ## 75   f1 =~  C5 152.591  0.292   0.382    0.234    0.234
    ## 134  f4 =~  E4 151.750  0.623   0.334    0.228    0.228
    ## 67   f1 =~  E2 123.282  0.235   0.308    0.191    0.191
    ## 34   f3 ~~  f5 116.891  0.135   0.313    0.313    0.313
    ## 400  C4 ~~  C5 113.021  0.456   0.456    0.348    0.348
    ## 27   f1 ~~  f3 112.876  0.243   0.269    0.269    0.269
    ## 143  f4 =~  O3 111.649  0.480   0.258    0.214    0.214
    ## 327  E3 ~~  O3 106.960  0.260   0.260    0.255    0.255
    ## 155  f5 =~  E5 104.519  0.491   0.306    0.228    0.228
    ## 133  f4 =~  E3 102.185  0.507   0.272    0.201    0.201
    ## 245  N4 ~~  C5 102.140  0.391   0.391    0.228    0.228
    ## 26   f1 ~~  f2 100.738  0.322   0.248    0.248    0.248
    ## 340  E4 ~~  A5  97.822  0.252   0.252    0.244    0.244
    ## 35   f4 ~~  f5  93.642  0.092   0.275    0.275    0.275
    ## 361  C1 ~~  C2  92.192  0.278   0.278    0.261    0.261
    ## 109  f3 =~  N4  87.897  0.429   0.296    0.189    0.189
    ## 235  N4 ~~  N5  87.456  0.376   0.376    0.205    0.205
    ## 104  f2 =~  O4  87.200  0.251   0.248    0.208    0.208
    ## 84   f1 =~  O4  86.839  0.181   0.237    0.199    0.199
    ## 74   f1 =~  C4  85.884  0.176   0.230    0.167    0.167
    ## 254  N4 ~~  O4  82.382  0.288   0.288    0.193    0.193
    ## 311  E2 ~~  O4  80.266  0.267   0.267    0.215    0.215
    ## 80   f1 =~  A5  79.191  0.159   0.208    0.163    0.163
    ## 95   f2 =~  C5  76.422  0.286   0.283    0.173    0.173
    ## 410  C4 ~~  O5  74.293  0.240   0.240    0.213    0.213
    ## 419  C5 ~~  O4  73.926  0.286   0.286    0.191    0.191
    ## 407  C4 ~~  O2  72.231  0.282   0.282    0.206    0.206
    ## 28   f1 ~~  f4  72.105  0.148   0.211    0.211    0.211
    ## 156  f5 =~  C1  71.850  0.371   0.231    0.187    0.187
    ## 421  A1 ~~  A2  70.821  0.237   0.237    0.208    0.208
    ## 119  f3 =~  A4  69.577  0.377   0.261    0.176    0.176
    ## 101  f2 =~  O1  69.212  0.196   0.194    0.172    0.172
    ## 168  N1 ~~  N4  67.582 -0.281  -0.281   -0.246   -0.246
    ## 325  E3 ~~  O1  67.000  0.198   0.198    0.191    0.191
    ## 302  E2 ~~  C5  58.980  0.278   0.278    0.195    0.195
    ## 356  E5 ~~  O1  58.748  0.189   0.189    0.176    0.176
    ## 191  N2 ~~  N4  58.678 -0.254  -0.254   -0.216   -0.216
    ## 322  E3 ~~  A3  56.785  0.182   0.182    0.197    0.197
    ## 314  E3 ~~  E5  55.914  0.227   0.227    0.179    0.179
    ## 167  N1 ~~  N3  55.206 -0.294  -0.294   -0.302   -0.302
    ## 462  O2 ~~  O5  55.060  0.313   0.313    0.202    0.202
    ## 257  N5 ~~  E2  54.405  0.273   0.273    0.178    0.178
    ## 352  E5 ~~  A2  53.688  0.172   0.172    0.171    0.171
    ## 98   f2 =~  A3  52.192  0.170   0.168    0.129    0.129
    ## 72   f1 =~  C2  50.282 -0.134  -0.176   -0.133   -0.133
    ## 214  N3 ~~  N5  49.421  0.260   0.260    0.166    0.166
    ## 345  E4 ~~  O5  48.136 -0.201  -0.201   -0.169   -0.169
    ## 414  C5 ~~  A4  47.864  0.265   0.265    0.157    0.157
    ## 82   f1 =~  O2  47.655  0.167   0.218    0.141    0.141
    ## 364  C1 ~~  C5  47.138 -0.245  -0.245   -0.182   -0.182
    ## 385  C2 ~~  O3  46.887  0.166   0.166    0.174    0.174
    ## 150  f5 =~  N5  45.606  0.392   0.244    0.150    0.150
    ## 190  N2 ~~  N3  44.980 -0.251  -0.251   -0.250   -0.250
    ## 192  N2 ~~  N5  44.758 -0.230  -0.230   -0.181   -0.181
    ## 324  E3 ~~  A5  44.344  0.168   0.168    0.155    0.155
    ## 197  N2 ~~  E5  44.274 -0.170  -0.170   -0.165   -0.165
    ## 377  C2 ~~  C5  43.522 -0.256  -0.256   -0.187   -0.187
    ## 453  A5 ~~  O3  41.337  0.147   0.147    0.163    0.163
    ## 401  C4 ~~  A1  40.034  0.193   0.193    0.149    0.149
    ## 301  E2 ~~  C4  39.627  0.183   0.183    0.169    0.169
    ## 347  E5 ~~  C2  39.127  0.171   0.171    0.144    0.144
    ## 346  E5 ~~  C1  39.077  0.164   0.164    0.141    0.141
    ## 339  E4 ~~  A4  38.115  0.197   0.197    0.146    0.146
    ## 264  N5 ~~  C4  37.951  0.204   0.204    0.146    0.146
    ## 457  O1 ~~  O3  36.469  0.225   0.225    0.260    0.260
    ## 260  N5 ~~  E5  35.579  0.208   0.208    0.129    0.129
    ## 381  C2 ~~  A4  34.773  0.180   0.180    0.135    0.135
    ## 439  A3 ~~  A5  33.473  0.213   0.213    0.260    0.260
    ## 90   f2 =~  N5  33.462  0.193   0.191    0.118    0.118
    ## 129  f4 =~  N4  31.979  0.327   0.175    0.112    0.112
    ## 442  A3 ~~  O3  31.678  0.123   0.123    0.160    0.160
    ## 262  N5 ~~  C2  31.425 -0.185  -0.185   -0.126   -0.126
    ## 342  E4 ~~  O2  31.122 -0.193  -0.193   -0.133   -0.133
    ## 391  C3 ~~  A2  31.076  0.124   0.124    0.133    0.133
    ## 240  N4 ~~  E5  29.386  0.177   0.177    0.119    0.119
    ## 295  E2 ~~  E3  29.119 -0.205  -0.205   -0.171   -0.171
    ## 111  f3 =~  E1  28.809 -0.253  -0.174   -0.107   -0.107
    ## 174  N1 ~~  E5  28.572 -0.137  -0.137   -0.137   -0.137
    ## 165  f5 =~  A5  28.295  0.228   0.142    0.112    0.112
    ## 236  N4 ~~  E1  27.647  0.199   0.199    0.118    0.118
    ## 299  E2 ~~  C2  27.217 -0.151  -0.151   -0.134   -0.134
    ## 180  N1 ~~  A1  27.068  0.149   0.149    0.130    0.130
    ## 373  C1 ~~  O4  26.709 -0.132  -0.132   -0.113   -0.113
    ## 69   f1 =~  E4  26.500  0.101   0.133    0.091    0.091
    ## 272  N5 ~~  O2  25.930  0.215   0.215    0.111    0.111
    ## 316  E3 ~~  C2  25.908  0.137   0.137    0.119    0.119
    ## 154  f5 =~  E4  25.307 -0.239  -0.149   -0.101   -0.101
    ## 244  N4 ~~  C4  25.191  0.156   0.156    0.120    0.120
    ## 425  A1 ~~  O1  25.136 -0.137  -0.137   -0.112   -0.112
    ## 140  f4 =~  C5  24.700  0.301   0.162    0.099    0.099
    ## 76   f1 =~  A1  24.650  0.110   0.144    0.102    0.102
    ## 141  f4 =~  O1  24.408  0.216   0.116    0.103    0.103
    ## 135  f4 =~  E5  24.333  0.252   0.135    0.101    0.101
    ## 122  f3 =~  O2  24.292  0.241   0.167    0.107    0.107
    ## 233  N3 ~~  O4  23.075  0.136   0.136    0.107    0.107
    ## 237  N4 ~~  E2  23.065  0.166   0.166    0.117    0.117
    ## 169  N1 ~~  N5  23.030 -0.169  -0.169   -0.137   -0.137
    ## 157  f5 =~  C2  23.012  0.218   0.136    0.103    0.103
    ## 281  E1 ~~  C2  22.541 -0.151  -0.151   -0.112   -0.112
    ## 330  E4 ~~  E5  21.987 -0.155  -0.155   -0.129   -0.129
    ## 408  C4 ~~  O3  21.904 -0.114  -0.114   -0.124   -0.124
    ## 384  C2 ~~  O2  21.894 -0.155  -0.155   -0.108   -0.108
    ## 358  E5 ~~  O3  21.887  0.120   0.120    0.114    0.114
    ## 429  A1 ~~  O5  21.672  0.151   0.151    0.103    0.103
    ## 142  f4 =~  O2  21.361 -0.286  -0.154   -0.099   -0.099
    ## 121  f3 =~  O1  20.896  0.158   0.109    0.097    0.097
    ## 239  N4 ~~  E4  20.136  0.145   0.145    0.105    0.105
    ## 459  O1 ~~  O5  19.875 -0.150  -0.150   -0.141   -0.141
    ## 206  N2 ~~  A4  19.872  0.127   0.127    0.110    0.110
    ## 246  N4 ~~  A1  19.608 -0.160  -0.160   -0.095   -0.095
    ## 409  C4 ~~  O4  19.411  0.117   0.117    0.103    0.103
    ## 29   f1 ~~  f5  19.352  0.097   0.119    0.119    0.119
    ## 372  C1 ~~  O3  18.986  0.102   0.102    0.108    0.108
    ## 318  E3 ~~  C4  18.416 -0.115  -0.115   -0.105   -0.105
    ## 249  N4 ~~  A4  18.352  0.156   0.156    0.093    0.093
    ## 378  C2 ~~  A1  17.762 -0.128  -0.128   -0.095   -0.095
    ## 259  N5 ~~  E4  17.713 -0.145  -0.145   -0.098   -0.098
    ## 188  N1 ~~  O4  17.624 -0.105  -0.105   -0.105   -0.105
    ## 189  N1 ~~  O5  17.480  0.109   0.109    0.110    0.110
    ## 250  N4 ~~  A5  17.355  0.121   0.121    0.095    0.095
    ## 99   f2 =~  A4  17.121  0.128   0.126    0.085    0.085
    ## 97   f2 =~  A2  16.863  0.092   0.091    0.077    0.077
    ## 70   f1 =~  E5  16.730 -0.082  -0.107   -0.080   -0.080
    ## 274  N5 ~~  O4  16.663  0.138   0.138    0.086    0.086
    ## 376  C2 ~~  C4  16.644 -0.144  -0.144   -0.139   -0.139
    ## 309  E2 ~~  O2  16.523  0.150   0.150    0.101    0.101
    ## 428  A1 ~~  O4  16.393 -0.126  -0.126   -0.085   -0.085
    ## 424  A1 ~~  A5  16.047 -0.122  -0.122   -0.096   -0.096
    ## 112  f3 =~  E2  15.936  0.171   0.118    0.073    0.073
    ## 219  N3 ~~  E5  15.878  0.116   0.116    0.092    0.092
    ## 285  E1 ~~  A1  15.859  0.148   0.148    0.088    0.088
    ## 96   f2 =~  A1  15.749 -0.122  -0.121   -0.086   -0.086
    ## 123  f3 =~  O3  15.629  0.142   0.098    0.082    0.082
    ## 283  E1 ~~  C4  15.554  0.126   0.126    0.097    0.097
    ## 461  O2 ~~  O4  15.471  0.141   0.141    0.090    0.090
    ## 328  E3 ~~  O4  15.413 -0.108  -0.108   -0.086   -0.086
    ## 212  N2 ~~  O5  14.954 -0.100  -0.100   -0.098   -0.098
    ## 426  A1 ~~  O2  14.894  0.150   0.150    0.084    0.084
    ## 399  C3 ~~  O5  14.843 -0.107  -0.107   -0.089   -0.089
    ## 334  E4 ~~  C4  14.781 -0.104  -0.104   -0.100   -0.100
    ## 79   f1 =~  A4  14.637  0.085   0.112    0.075    0.075
    ## 320  E3 ~~  A1  14.365 -0.119  -0.119   -0.083   -0.083
    ## 163  f5 =~  A3  14.279  0.155   0.096    0.074    0.074
    ## 138  f4 =~  C3  14.269  0.184   0.099    0.076    0.076
    ## 336  E4 ~~  A1  14.019 -0.119  -0.119   -0.087   -0.087
    ## 464  O3 ~~  O5  14.012 -0.154  -0.154   -0.148   -0.148
    ## 432  A2 ~~  A5  13.743 -0.106  -0.106   -0.123   -0.123
    ## 275  N5 ~~  O5  13.627  0.131   0.131    0.082    0.082
    ## 388  C3 ~~  C4  13.623 -0.122  -0.122   -0.115   -0.115
    ## 436  A2 ~~  O4  13.309 -0.083  -0.083   -0.083   -0.083
    ## 202  N2 ~~  C5  13.199  0.110   0.110    0.093    0.093
    ## 217  N3 ~~  E3  13.155 -0.104  -0.104   -0.084   -0.084
    ## 271  N5 ~~  O1  12.860  0.107   0.107    0.081    0.081
    ## 94   f2 =~  C4  12.839  0.094   0.093    0.068    0.068
    ## 449  A4 ~~  O4  12.670  0.112   0.112    0.076    0.076
    ## 242  N4 ~~  C2  12.515 -0.109  -0.109   -0.080   -0.080
    ## 124  f3 =~  O4  12.472  0.139   0.096    0.080    0.080
    ## 383  C2 ~~  O1  12.342  0.082   0.082    0.084    0.084
    ## 351  E5 ~~  A1  12.297 -0.112  -0.112   -0.076   -0.076
    ## 164  f5 =~  A4  12.216 -0.188  -0.117   -0.079   -0.079
    ## 201  N2 ~~  C4  11.984 -0.084  -0.084   -0.094   -0.094
    ## 117  f3 =~  A2  11.942  0.114   0.078    0.067    0.067
    ## 298  E2 ~~  C1  11.878 -0.096  -0.096   -0.086   -0.086
    ## 417  C5 ~~  O2  11.875  0.143   0.143    0.079    0.079
    ## 159  f5 =~  C4  11.697  0.156   0.097    0.071    0.071
    ## 253  N4 ~~  O3  11.683  0.099   0.099    0.082    0.082
    ## 120  f3 =~  A5  11.521  0.123   0.085    0.067    0.067
    ## 86   f2 =~  N1  11.191 -0.082  -0.081   -0.052   -0.052
    ## 348  E5 ~~  C3  11.190  0.092   0.092    0.075    0.075
    ## 290  E1 ~~  O1  11.189 -0.096  -0.096   -0.079   -0.079
    ## 224  N3 ~~  C5  11.107  0.115   0.115    0.079    0.079
    ## 343  E4 ~~  O3  11.022  0.084   0.084    0.087    0.087
    ## 430  A2 ~~  A3  10.819 -0.120  -0.120   -0.164   -0.164
    ## 451  A5 ~~  O1  10.780  0.072   0.072    0.078    0.078
    ## 204  N2 ~~  A2  10.673 -0.068  -0.068   -0.086   -0.086
    ## 66   f1 =~  E1  10.245 -0.074  -0.097   -0.060   -0.060
    ## 276  E1 ~~  E2  10.198  0.150   0.150    0.106    0.106
    ## 365  C1 ~~  A1  10.088 -0.093  -0.093   -0.070   -0.070
    ## 126  f4 =~  N1  10.058  0.144   0.078    0.049    0.049
    ## 137  f4 =~  C2  10.010  0.153   0.082    0.062    0.062

Based on the modification indices, the first option is to correlate
factor f2 and factor f4 by `f2 ~~ f4`.

    mod_cfa_5f_v2 <- "f1 =~ N1 + N2 + N3 + N4 + N5
                      f2 =~ E1 + E2 + E3 + E4 + E5
                      f3 =~ C1 + C2 + C3 + C4 + C5
                      f4 =~ A1 + A2 + A3 + A4 + A5
                      f5 =~ O1 + O2 + O3 + O4 + O5
                      f1 ~~ 0*f2 + 0*f3 + 0*f4 + 0*f5
                      f2 ~~ 0*f3 + f4 + 0*f5
                      f3 ~~ 0*f4 + 0*f5
                      f4 ~~ 0*f5"
    fit_cfa_5f_v2 <- cfa(mod_cfa_5f_v2, data = bfi)
    summary(fit_cfa_5f_v2, standardize = TRUE, fit.measures = TRUE, rsquare = TRUE)

    ## lavaan 0.6-8 ended normally after 64 iterations
    ## 
    ##   Estimator                                         ML
    ##   Optimization method                           NLMINB
    ##   Number of model parameters                        51
    ##                                                       
    ##                                                   Used       Total
    ##   Number of observations                          2436        2800
    ##                                                                   
    ## Model Test User Model:
    ##                                                       
    ##   Test statistic                              4879.010
    ##   Degrees of freedom                               274
    ##   P-value (Chi-square)                           0.000
    ## 
    ## Model Test Baseline Model:
    ## 
    ##   Test statistic                             18222.116
    ##   Degrees of freedom                               300
    ##   P-value                                        0.000
    ## 
    ## User Model versus Baseline Model:
    ## 
    ##   Comparative Fit Index (CFI)                    0.743
    ##   Tucker-Lewis Index (TLI)                       0.719
    ## 
    ## Loglikelihood and Information Criteria:
    ## 
    ##   Loglikelihood user model (H0)            -100197.010
    ##   Loglikelihood unrestricted model (H1)     -97757.504
    ##                                                       
    ##   Akaike (AIC)                              200496.019
    ##   Bayesian (BIC)                            200791.723
    ##   Sample-size adjusted Bayesian (BIC)       200629.684
    ## 
    ## Root Mean Square Error of Approximation:
    ## 
    ##   RMSEA                                          0.083
    ##   90 Percent confidence interval - lower         0.081
    ##   90 Percent confidence interval - upper         0.085
    ##   P-value RMSEA <= 0.05                          0.000
    ## 
    ## Standardized Root Mean Square Residual:
    ## 
    ##   SRMR                                           0.119
    ## 
    ## Parameter Estimates:
    ## 
    ##   Standard errors                             Standard
    ##   Information                                 Expected
    ##   Information saturated (h1) model          Structured
    ## 
    ## Latent Variables:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##   f1 =~                                                                 
    ##     N1                1.000                               1.310    0.832
    ##     N2                0.947    0.024   40.245    0.000    1.240    0.809
    ##     N3                0.873    0.024   35.898    0.000    1.144    0.717
    ##     N4                0.665    0.025   26.892    0.000    0.872    0.556
    ##     N5                0.616    0.026   23.814    0.000    0.808    0.498
    ##   f2 =~                                                                 
    ##     E1                1.000                               0.942    0.578
    ##     E2                1.201    0.050   24.253    0.000    1.131    0.701
    ##     E3                0.867    0.039   22.154    0.000    0.817    0.604
    ##     E4                1.135    0.046   24.736    0.000    1.069    0.729
    ##     E5                0.752    0.037   20.132    0.000    0.708    0.527
    ##   f3 =~                                                                 
    ##     C1                1.000                               0.691    0.559
    ##     C2                1.172    0.057   20.410    0.000    0.810    0.614
    ##     C3                1.047    0.054   19.348    0.000    0.723    0.560
    ##     C4                1.377    0.064   21.526    0.000    0.951    0.691
    ##     C5                1.403    0.070   20.029    0.000    0.969    0.594
    ##   f4 =~                                                                 
    ##     A1                1.000                               0.482    0.343
    ##     A2                1.585    0.109   14.591    0.000    0.765    0.649
    ##     A3                2.052    0.136   15.039    0.000    0.990    0.755
    ##     A4                1.553    0.115   13.514    0.000    0.749    0.505
    ##     A5                1.804    0.122   14.776    0.000    0.870    0.685
    ##   f5 =~                                                                 
    ##     O1                1.000                               0.623    0.553
    ##     O2                1.165    0.077   15.042    0.000    0.726    0.467
    ##     O3                1.248    0.074   16.878    0.000    0.777    0.645
    ##     O4               -0.551    0.052  -10.515    0.000   -0.343   -0.288
    ##     O5                1.106    0.069   15.970    0.000    0.689    0.520
    ## 
    ## Covariances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##   f1 ~~                                                                 
    ##     f2                0.000                               0.000    0.000
    ##     f3                0.000                               0.000    0.000
    ##     f4                0.000                               0.000    0.000
    ##     f5                0.000                               0.000    0.000
    ##   f2 ~~                                                                 
    ##     f3                0.000                               0.000    0.000
    ##     f4                0.309    0.025   12.306    0.000    0.680    0.680
    ##     f5                0.000                               0.000    0.000
    ##   f3 ~~                                                                 
    ##     f4                0.000                               0.000    0.000
    ##     f5                0.000                               0.000    0.000
    ##   f4 ~~                                                                 
    ##     f5                0.000                               0.000    0.000
    ## 
    ## Variances:
    ##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
    ##    .N1                0.766    0.037   20.793    0.000    0.766    0.309
    ##    .N2                0.812    0.036   22.814    0.000    0.812    0.345
    ##    .N3                1.234    0.043   28.397    0.000    1.234    0.486
    ##    .N4                1.703    0.053   32.241    0.000    1.703    0.691
    ##    .N5                1.982    0.060   32.950    0.000    1.982    0.752
    ##    .E1                1.773    0.058   30.582    0.000    1.773    0.666
    ##    .E2                1.324    0.050   26.456    0.000    1.324    0.509
    ##    .E3                1.159    0.039   29.924    0.000    1.159    0.635
    ##    .E4                1.009    0.040   25.009    0.000    1.009    0.469
    ##    .E5                1.302    0.041   31.590    0.000    1.302    0.722
    ##    .C1                1.048    0.036   29.420    0.000    1.048    0.687
    ##    .C2                1.084    0.039   27.560    0.000    1.084    0.623
    ##    .C3                1.143    0.039   29.393    0.000    1.143    0.686
    ##    .C4                0.990    0.042   23.804    0.000    0.990    0.523
    ##    .C5                1.726    0.061   28.320    0.000    1.726    0.648
    ##    .A1                1.747    0.052   33.723    0.000    1.747    0.882
    ##    .A2                0.806    0.028   28.300    0.000    0.806    0.579
    ##    .A3                0.739    0.032   22.810    0.000    0.739    0.430
    ##    .A4                1.643    0.052   31.852    0.000    1.643    0.745
    ##    .A5                0.857    0.032   26.808    0.000    0.857    0.531
    ##    .O1                0.881    0.034   26.290    0.000    0.881    0.695
    ##    .O2                1.884    0.064   29.555    0.000    1.884    0.782
    ##    .O3                0.849    0.040   21.122    0.000    0.849    0.584
    ##    .O4                1.305    0.039   33.218    0.000    1.305    0.917
    ##    .O5                1.278    0.046   27.702    0.000    1.278    0.729
    ##     f1                1.716    0.074   23.242    0.000    1.000    1.000
    ##     f2                0.887    0.063   14.014    0.000    1.000    1.000
    ##     f3                0.477    0.037   12.867    0.000    1.000    1.000
    ##     f4                0.233    0.030    7.803    0.000    1.000    1.000
    ##     f5                0.388    0.034   11.331    0.000    1.000    1.000
    ## 
    ## R-Square:
    ##                    Estimate
    ##     N1                0.691
    ##     N2                0.655
    ##     N3                0.514
    ##     N4                0.309
    ##     N5                0.248
    ##     E1                0.334
    ##     E2                0.491
    ##     E3                0.365
    ##     E4                0.531
    ##     E5                0.278
    ##     C1                0.313
    ##     C2                0.377
    ##     C3                0.314
    ##     C4                0.477
    ##     C5                0.352
    ##     A1                0.118
    ##     A2                0.421
    ##     A3                0.570
    ##     A4                0.255
    ##     A5                0.469
    ##     O1                0.305
    ##     O2                0.218
    ##     O3                0.416
    ##     O4                0.083
    ##     O5                0.271

The model fit is still not good. We can continue checking the
modification indices. After we get a good model, we usually want to
create a diagram to present the latent structure. Here, I just use the
second CFA model to show how to generate a diagram.

    library(semPlot)
    semPaths(fit_cfa_5f_v2, whatLabels = "std", residuals = FALSE, sizeMan = 3)

![](img/factoranalysis/unnamed-chunk-8-1.png)

References
----------

<https://cran.r-project.org/web/packages/psych/psych.pdf>
<https://stats.idre.ucla.edu/r/seminars/rcfa/>
<http://www.di.fc.ul.pt/~jpn/r/factoranalysis/factoranalysis.html>

