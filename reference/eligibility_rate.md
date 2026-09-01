# Survey Eligibility Rate

Provides an estimate for the proportion of cases of unknown eligibility
that are eligible, as described by (Valliant et al. 2013) . The rate is
typically (but not necessarily) calculated on the screener data or other
sources depending on the type of survey, and approaches to calculating
'e' may therefore differ from one survey to the next.

## Usage

``` r
eligibility_rate(x, weight = NULL)
```

## Arguments

- x:

  a character vector of disposition outcomes (I, P, R, NC, O, UH, UR,
  UO, or NE). Alternatively, a named vector/table of (weighted)
  disposition counts.

- weight:

  an optional numeric vector that specifies the weight of each element
  in 'x' if x is a character vector. For probability samples, these will
  normally be base weights (inverse selection probabilities). If none is
  provided (the default), an unweighted estimate is returned. Weights
  cannot be supplied with an already-aggregated named vector or table.

## Value

A named numeric vector of length one containing the estimated
eligibility rate, named `ELR`.

## Details

The present proportional-allocation implementation follows the default
used in the Excel-based [AAPOR Outcome Rate Calculator (Version 5.1,
April
2023)](https://aapor.org/wp-content/uploads/2023/06/Response-Rate-Calculator-5-1_04142023.xlsx),
on the basis of known ineligibles being coded as "NE". It is one
accepted estimator of `e`; researchers should use better design-specific
information when available. This function returns one scalar estimate.
Separate estimates can be supplied directly to
[`outcomerate()`](https://docs.ropensci.org/outcomerate/reference/outcomerate.md)
as a named vector such as `c(UH = 0.4, UR = 0.7, UO = 0.2)`; they cannot
be inferred from the package's aggregate `NE` count alone. See (Amaya et
al. 2025) for category-specific estimation guidance.

The eligibility rate (ELR) is defined as

- ELR = (I + P + R + NC + O) / (I + P + R + NC + O + NE)

## References

The American Association for Public Opinion Research (2023). “Standard
Definitions: Final Dispositions of Case Codes and Outcome Rates for
Surveys.”
<https://aapor.org/wp-content/uploads/2024/03/Standards-Definitions-10th-edition.pdf>.
Amaya A, Marlar J, English N (2025). “Estimating the Eligibility Status
of Cases with Unknown Eligibility.” American Association for Public
Opinion Research.
<https://aapor.org/wp-content/uploads/2025/10/Estimating-the-Eligibility-Status-of-Cases-with-Unknown-Eligibility_FINAL.pdf>.  
  
Valliant R, Dever JA, Kreuter F (2013). *Practical Tools for Designing
and Weighting Survey Samples*, Statistics for Social and Behavioral
Sciences. Springer New York.

## See also

[outcomerate](https://docs.ropensci.org/outcomerate/reference/outcomerate.md)

## Examples

``` r
# load the outcomerate package
library(outcomerate)

# Create a vector of survey dispositions
#
# I  = Complete interview
# P  = Partial interview
# R  = Refusal and break-off
# NC = Non-contact
# O  = Other eligible non-interview (2.30, 2.90)
# UH = Unknown if household/occupied housing unit (3.10)
# UR = Unknown if sampled unit is eligible/housing unit contains an eligible
#      respondent (3.20)
# UO = Unknown, other (3.90)
# NE = Not eligible (4.0)
x <- c("I", "P", "I", "NE", "NC", "UH", "I", "R", "UR", "UO", "I", "O",
       "P", "I")

# estimate the eligibility rate
eligibility_rate(x)
#>       ELR 
#> 0.9090909 

# calculate a weighted rate using illustrative base weights
w <- seq(0.5, 1.8, length.out = length(x))
eligibility_rate(x, weight = w)
#>       ELR 
#> 0.9354839 

# alternatively, provide input as counts
freq <- c(I = 6, P = 2, NC = 3, NE = 1)
eligibility_rate(freq)
#>       ELR 
#> 0.9166667 
```
