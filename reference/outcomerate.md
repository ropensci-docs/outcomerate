# AAPOR Survey Outcome Rates

Provides standardized outcome rates for surveys, primarily as defined by
the [American Association for Public Opinion Research
(AAPOR)](https://aapor.org/). Details can be found in the Standard
Definitions manual (The American Association for Public Opinion Research
2023) .

## Usage

``` r
outcomerate(x, e = NULL, rate = NULL, weight = NULL, return_nd = FALSE)
```

## Arguments

- x:

  a character vector of disposition outcomes (I, P, R, NC, O, UH, UR,
  UO, or NE). Alternatively, a named vector/table of (weighted)
  disposition counts.

- e:

  a numeric eligibility estimate in `[0, 1]`. A length-one value is
  applied to all unknown dispositions. Alternatively, use a named vector
  such as `c(UH = 0.4, UR = 0.7, UO = 0.2)` for category-specific
  estimates. A non-scalar vector must contain one uniquely named value
  for every unknown category with a positive aggregate count (weighted
  when `weight` is supplied); categories with a zero count may be
  omitted.
  [`eligibility_rate()`](https://docs.ropensci.org/outcomerate/reference/eligibility_rate.md)
  provides a default scalar estimate. If an `e`-dependent rate is
  explicitly requested when every unknown category has count zero, `e`
  may be omitted.

- rate:

  an optional character vector specifying the rates to be calculated. If
  `NULL` (the default), all rates available for the supplied value of
  `e` are returned.

- weight:

  an optional numeric vector that specifies the weight of each element
  in 'x' if x is a character vector or factor. For AAPOR weighted rates,
  use base weights (inverse selection probabilities); two-phase designs
  should also account for subsampling. If none is provided (the
  default), an unweighted estimate is returned. Individual zero weights
  are permitted, as required for phase-2-eligible cases that are not
  subsampled. Weights cannot be supplied with an already-aggregated
  named vector or table.

- return_nd:

  a logical to switch to having the function return the numerator and
  denominator instead of the rate. Defaults to FALSE.

## Value

If `return_nd = FALSE`, a named numeric vector containing the requested
outcome rates. If `return_nd = TRUE`, a numeric matrix with one row per
requested rate and columns `NUM` and `DEN` containing its numerator and
denominator. Names for weighted rates have a `w` suffix.

## Details

Survey and public opinion research often categorizes interview attempts
for a survey according to a set of outcome codes as follows:

- I = Complete interview

- P = Partial interview

- R = Refusal and break-off

- NC = Non-contact

- O = Other eligible non-interview (2.30, 2.90)

- UH = Unknown if household/occupied housing unit (3.10)

- UR = Unknown if sampled unit is eligible/housing unit contains an
  eligible respondent (3.20)

- UO = Unknown, other (3.90)

- NE = Not eligible (4.0)

`UR` is the 10th-edition aggregate symbol for 3.20, which the 9th
edition included under `UO`. Legacy `UO` inputs remain supported. With a
scalar `e`, moving a 3.20 case from `UO` to `UR` does not alter a rate.
With category-specific estimates it can, so new 3.20 cases should be
coded `UR` for standards conformance.

These high-level classes are used to calculate outcome rates that
provide some measure of quality over the fieldwork. These outcome rates
are defined here as follows:

The formulas below show the traditional scalar form `e(UH + UR + UO)`.
If `e` is supplied by category, that term is evaluated as
`e["UH"] * UH + e["UR"] * UR + e["UO"] * UO`. Each value is the
conditional probability that a case in that unknown category is
ultimately eligible for the survey, following the companion guidance in
(Amaya et al. 2025) . Calculate `e` separately for each frame. One
vector applies to the cases in one call; combining frames requires a
scientifically justified aggregation. Other design components, modes, or
phases may also require separate estimates when their mechanisms differ.
Document the scientific basis for every estimate.

**AAPOR Response Rate**

The proportion of sampled cases that yield a complete or partial
interview, depending on the selected definition.

- RR1 = I / ((I + P) + (R + NC + O) + (UH + UR + UO))

- RR2 = (I + P) / ((I + P) + (R + NC + O) + (UH + UR + UO))

- RR3 = I / ((I + P) + (R + NC + O) + e(UH + UR + UO))

- RR4 = (I + P) / ((I + P) + (R + NC + O) + e(UH + UR + UO))

- RR5 = I / ((I + P) + (R + NC + O))

- RR6 = (I + P) / ((I + P) + (R + NC + O))

RR5 and RR6 are appropriate only when no unknown cases are eligible or
no cases have unknown eligibility.

**AAPOR Cooperation Rates**

The proportion of all interviewed cases among eligible units ever
contacted. These printed formulas are AAPOR's household-level rates.

- COOP1 = I / ((I + P) + R + O)

- COOP2 = (I + P) / ((I + P) + R + O)

- COOP3 = I / ((I + P) + R)

- COOP4 = (I + P) / ((I + P) + R)

**AAPOR Refusal Rates**

The proportion of the sample that refuses to participate in the survey.

- REF1 = R / ((I + P) + (R + NC + O) + (UH + UR + UO))

- REF2 = R / ((I + P) + (R + NC + O) + e(UH + UR + UO))

- REF3 = R / ((I + P) + (R + NC + O))

As with RR5 and RR6, excluding unknown cases from REF3 must be justified
by the study's actual eligibility situation.

**AAPOR Contact Rates**

The proportion of cases in which a responsible member of the housing
unit is reached. These printed formulas are AAPOR's household-level
rates.

- CON1 = ((I + P) + (R + O)) / ((I + P) + (R + NC + O) + (UH + UR + UO))

- CON2 = ((I + P) + (R + O)) / ((I + P) + (R + NC + O) + e(UH + UR +
  UO))

- CON3 = ((I + P) + (R + O)) / ((I + P) + (R + NC + O))

**Location Rate**

The proportion of cases that could be located for an interview.

The location rate is not defined in AAPOR's Standards, but can be found
in (Valliant et al. 2013) . Note: depending on how the located cases are
encoded, this may or may not be the correct formula.

- LOC1 = ((I + P) + (R + O + NC)) / ((I + P) + (R + NC + O) + (UH + UR +
  UO))

- LOC2 = ((I + P) + (R + O + NC)) / ((I + P) + (R + NC + O) + e(UH +
  UR + UO))

## References

Amaya A, Marlar J, English N (2025). “Estimating the Eligibility Status
of Cases with Unknown Eligibility.” American Association for Public
Opinion Research.
<https://aapor.org/wp-content/uploads/2025/10/Estimating-the-Eligibility-Status-of-Cases-with-Unknown-Eligibility_FINAL.pdf>.  
  
The American Association for Public Opinion Research (2023). “Standard
Definitions: Final Dispositions of Case Codes and Outcome Rates for
Surveys.”
<https://aapor.org/wp-content/uploads/2024/03/Standards-Definitions-10th-edition.pdf>.  
  
Valliant R, Dever JA, Kreuter F (2013). *Practical Tools for Designing
and Weighting Survey Samples*, Statistics for Social and Behavioral
Sciences. Springer New York.

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
x <- c("I", "P", "I", "NC", "UH", "I", "R", "NE",
      "UR", "UO", "I", "O", "P", "I")

# calculate all rates
elr <- eligibility_rate(x)
outcomerate(x, e = elr)
#>        RR1        RR2        RR3        RR4        RR5        RR6      COOP1 
#> 0.38461538 0.53846154 0.39285714 0.55000000 0.50000000 0.70000000 0.55555556 
#>      COOP2      COOP3      COOP4       REF1       REF2       REF3       CON1 
#> 0.77777778 0.62500000 0.87500000 0.07692308 0.07857143 0.10000000 0.69230769 
#>       CON2       CON3       LOC1       LOC2 
#> 0.70714286 0.90000000 0.76923077 0.78571429 

# use separate eligibility estimates for each unknown category
e_by_class <- c(UH = 0.4, UR = 0.7, UO = 0.2)
outcomerate(x, e = e_by_class, rate = c("RR3", "REF2", "CON2"))
#>        RR3       REF2       CON2 
#> 0.44247788 0.08849558 0.79646018 

# return only one rate
outcomerate(x, rate = "COOP1")
#>     COOP1 
#> 0.5555556 

# calculate weighted rates using illustrative base weights
w <- seq(0.5, 1.8, length.out = length(x))
outcomerate(x, e = elr, weight = w)
#>       RR1w       RR2w       RR3w       RR4w       RR5w       RR6w     COOP1w 
#> 0.36912752 0.52348993 0.37741734 0.53524641 0.48672566 0.69026549 0.52380952 
#>     COOP2w     COOP3w     COOP4w      REF1w      REF2w      REF3w      CON1w 
#> 0.74285714 0.61797753 0.87640449 0.07382550 0.07548347 0.09734513 0.70469799 
#>      CON2w      CON3w      LOC1w      LOC2w 
#> 0.72052402 0.92920354 0.75838926 0.77542109 

# alternatively, provide input as counts
freq <- c(I = 6, P = 2, NC = 3, R = 1)
outcomerate(freq, e = elr)
#>        RR1        RR2        RR3        RR4        RR5        RR6      COOP1 
#> 0.50000000 0.66666667 0.50000000 0.66666667 0.50000000 0.66666667 0.66666667 
#>      COOP2      COOP3      COOP4       REF1       REF2       REF3       CON1 
#> 0.88888889 0.66666667 0.88888889 0.08333333 0.08333333 0.08333333 0.75000000 
#>       CON2       CON3       LOC1       LOC2 
#> 0.75000000 0.75000000 1.00000000 1.00000000 
```
