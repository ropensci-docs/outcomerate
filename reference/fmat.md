# outcomerate Formula Matrix (Internal Data)

The `fmat` object is the internal dataset used by the `outcomerate`
package. It holds all definitions for the outcome rates. With the
exception of location rates, these are taken from the AAPOR Standard
Definitions (2023).

## Details

The data is a 3-dimensional binary array consisting of:

- outcome: codes `I`, `P`, `R`, `NC`, `O`, `UH`, `UR`, `UO`, `eUH`,
  `eUR`, `eUO`, and `NE`

- rate: the shorthand name for the rate (e.g. RR1)

- side: numerator (NUM) and denominator (DEN)

Given these three dimensions, each outcome rate can be defined as a
rational number (i.e. a fraction) consisting of a summation of
frequencies of outcome codes (where the matrix entries are nonzero).

The input parameters given by the user are `I`, `P`, `R`, `NC`, `O`,
`UH`, `UR`, `UO`, and `NE`. The scalar or category-specific values in
`e` are multiplied by `UH`, `UR`, and `UO` internally to produce the
estimated eligible counts `eUH`, `eUR`, and `eUO`.

The reason for this implementation is:

a\) It conforms to a DRY (don't repeat yourself) philosophy by holding
all definitions in one place. These definitions can be used as upstream
inputs to functions/test suites requiring them.

b\) It makes it easier to use intermediate steps in the formula
calculations. For instance, it may be of use to a researchers to want to
obtain the numerator/denominators of calculations, instead of only the
output.

c\) it makes it easy to compare the output

d\) It is easier to maintain

## References

<https://aapor.org/wp-content/uploads/2024/03/Standards-Definitions-10th-edition.pdf>

## Examples

``` r
fmat <- outcomerate:::fmat

# Print the dimensions
dimnames(fmat)
#> $outcome
#>  [1] "I"   "P"   "R"   "NC"  "O"   "UH"  "UR"  "UO"  "NE"  "eUH" "eUR" "eUO"
#> 
#> $rate
#>  [1] "RR1"   "RR2"   "RR3"   "RR4"   "RR5"   "RR6"   "COOP1" "COOP2" "COOP3"
#> [10] "COOP4" "REF1"  "REF2"  "REF3"  "CON1"  "CON2"  "CON3"  "LOC1"  "LOC2" 
#> 
#> $side
#> [1] "NUM" "DEN"
#> 

# Say we want to know the definition of Response Rate 2, RR2. We see
# below that the numerator (NUM) column is defined by the entries with a 1,
# or (I + P). Likewise, the denominator (DEN) is defined as
# (I + P + R + NC + O + UH + UR + UO)
fmat[, "RR2", ]
#>        side
#> outcome NUM DEN
#>     I     1   1
#>     P     1   1
#>     R     0   1
#>     NC    0   1
#>     O     0   1
#>     UH    0   1
#>     UR    0   1
#>     UO    0   1
#>     NE    0   0
#>     eUH   0   0
#>     eUR   0   0
#>     eUO   0   0


# To use linear algebra, we define a zero-one numerator matrix 'N'
# and a zero-one denominator matrix 'D'. Our count of disposition codes
# is given here manually as 'x' (in the same order as N and D).
N = fmat[ , , 1]
D = fmat[ , , 2]
x <- c(I = 5, P = 2, R = 1, NC = 7, O = 3,
      UH = 4, UR = 2, UO = 8, NE = 1,
      eUH = 3, eUR = 1.5, eUO = 6)

# Return all rates
(x %*% N) / (x %*% D)
#>       rate
#>            RR1     RR2       RR3      RR4       RR5       RR6     COOP1
#>   [1,] 0.15625 0.21875 0.1754386 0.245614 0.2777778 0.3888889 0.4545455
#>       rate
#>            COOP2 COOP3 COOP4    REF1       REF2       REF3    CON1      CON2
#>   [1,] 0.6363636 0.625 0.875 0.03125 0.03508772 0.05555556 0.34375 0.3859649
#>       rate
#>             CON3   LOC1      LOC2
#>   [1,] 0.6111111 0.5625 0.6315789


# The same thing can be achieved with the apply family of functions
numden <- apply(x * fmat, 2:3, sum)
numden[, 1] / numden[, 2]
#>        RR1        RR2        RR3        RR4        RR5        RR6      COOP1 
#> 0.15625000 0.21875000 0.17543860 0.24561404 0.27777778 0.38888889 0.45454545 
#>      COOP2      COOP3      COOP4       REF1       REF2       REF3       CON1 
#> 0.63636364 0.62500000 0.87500000 0.03125000 0.03508772 0.05555556 0.34375000 
#>       CON2       CON3       LOC1       LOC2 
#> 0.38596491 0.61111111 0.56250000 0.63157895 
```
