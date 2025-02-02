
<!-- README.md is generated from README.Rmd. Please edit that file -->
<!-- badges: start -->

[![R build
status](https://github.com/JanEngelstaedter/grow96/workflows/R-CMD-check/badge.svg)](https://github.com/JanEngelstaedter/grow96/actions)
<!-- badges: end -->

# grow96

This R package was written to facilitate the import, wrangling and
analysis and bacterial growth data produced by microplate spectrometers.
Currently, this package is closely aligned with the specific needs and
instruments of the Engelstaedter/Letten group and hence intended for
internal use only.

## Installation

To install the package, you can run the following two lines of code in
R:

``` r
install.packages("devtools")
devtools::install_github("JanEngelstaedter/grow96")
```

## Use

Load the package like any other R package:

``` r
library(grow96)
```

The package provides functions for subsequent steps in a standard work
flow. First, we can define a “spec-file” that specifies variables for
rows and columns of a 96-well plate. For example, if we want to have 8
strains across the rows (“strainA” through “strainH”) and 12 drug
concentrations (0 to 0.55) across the columns, we could specify this as
follows:

``` r
makeSpec_fullFact(plateName = "Experiment1",
                  rowName = "strain", 
                  columnName = "drugConcentration",
                  rows = paste0("strain", LETTERS[1:8]), 
                  columns = 0.05* 0:11,
                  specPath = "specs",
                  plotPath = "plots")
```

This will generate a single spec file in the designated folder, and also
a pdf showing the plate design in another folder. The
`makeSpec_fullFact` function also supports empty borders, blank wells,
several replicate plates (including with randomised rows and/or
columns), and a few other features - check out its documentation.

After the OD raw data has been obtained and file(s) been given names
corresponding to the spec file name(s), they can be imported and
processed:

``` r
data <- processODData(specPath="specs", dataPath="data")
```

Next, can run a quality control analysis on the data:

``` r
qcODData(data, path = "qc")
```

This will generate a pdf file in the folder “qc” containing information
about temperature fluctuations through time as well as OD data from
blank wells (if there are any).

We can also plot the data in an interactive shiny app:

``` r
shinyPlate(data)
```

If we are happy with the quality of the data, we can proceed to blank
all the reads:

``` r
data <- blankODs(data, method = "fixed", value = 0.05)
```

In this case we have used a single fixed value for blanking, but the
`blankODs` function also allows us to blank use designated blank wells
on a plate, or use different blanks depending on the variables defined
for a plate.

Finally, we can analyse the data in order to extract statistics such as
the maximum growth rate and the maximum OD, and to summarise the
statistics across replicate plates:

``` r
growthAnalysis <- analyseODData(data)
```

The resulting object is a list containing all these statistics as
tibbles, which should enable easy downstream analyses and plotting.

Note that we can also use a pipe for the three main steps in the work
flow:

``` r
growthAnalysis <- processODData(specPath="specs", dataPath="data") |>
  blankODs(method = "fixed", value = 0.05) |>
  analyseODData()
```
