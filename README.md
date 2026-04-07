---
title: "README: MTS-CAR and MTS Baseline Models"
output: github_document
---

# Overview

This repository contains the R Markdown implementation of the following models:

1. **MTS-CAR**: a restricted VAR model with conditional autoregressive (CAR) spatial effects.
2. **MTS Baseline**: a baseline restricted VAR model without the CAR structure.

The main model in this repository is **MTS-CAR**. It is designed for panel data observed across multiple areas or units, where temporal dependence and spatial dependence are both important.

The repository is intended for users who want to:

- fit the proposed MTS-CAR model,
- generate leave-one-out forecasts,
- and evaluate predictive performance using the same transformed dataset and forecast metrics.

# Repository contents

The repository currently contains two main R Markdown files:

- `MTS Baseline.Rmd`
- `MTS-CAR.Rmd`

These files are organized as follows:

- **MTS Baseline.Rmd**: the baseline model.
- **MTS-CAR.Rmd**: full model with spatial random effects and CAR prior structure.


# Model summary

## MTS-CAR

The MTS-CAR model is the main contribution of this repository. It models each area-specific series using a dynamic regression with spatially structured random effects. The general form is

$$
y_{it} = \theta_0 + \nu_{0i} + (\theta_1 + \nu_{1i}) y_{i,t-1} + \varepsilon_{it},
\qquad
\varepsilon_{it} \sim \mathcal{N}(0,\sigma^2),
$$

where the random effects are assigned a CAR-based prior structure.

This model is appropriate when:

- the data are observed over time,
- there are multiple related spatial units or areas,
- and neighboring areas are expected to share information.

## MTS Baseline

The MTS Baseline provides a benchmark without the full spatial CAR structure:

$$
y_{it} \mid \mu_i,b_i,\sigma^2, y_{i,t-1} \sim \mathcal{N}\left(\mu_i + b_i y_{i,t-1}, \sigma^2 \right), \qquad t=1,\dots,T,\quad i=1,\dots,\ell.
$$

It is included for model comparison.

## SS Baseline

The SS Baseline is a univariate AR(1) model fitted separately to each series:

$$
y_t = \beta_0 + \beta_1 y_{t-1} + \varepsilon_t,
\qquad
\varepsilon_t \sim \mathcal{N}(0,\sigma^2).
$$

This serves as the simplest benchmark. (Code not included here)

# Data requirements

The code assumes that the same dataset is used across all models.

The following objects are expected in the working environment or created during preprocessing:

- `MSA_data`: square-root transformed and second differenced series used for estimation
- `mdata`: square-root transformed and first differenced series used for back-transformation
- `msa_data`: square-root transformed only series
- `msa_data_level`: original series in level form
- `Dat2.csv`: adjacency matrix used for the spatial structure in the MTS-CAR model

## Important note on preprocessing

The forecasting code in this repository assumes that the data have already undergone the same preprocessing pipeline used in the paper:

1. square-root transformation,
2. first and second differencing,
3. construction of lagged series,
4. and alignment of the transformed and level datasets.

In particular, the leave-one-out forecast code assumes that:

- the final row of the transformed series is held out,
- the model is fit on rows `1, ..., T-1`,
- the omitted final transformed value is then forecast using the fitted model,
- and the forecast is back-transformed to the original level.
- Finally, the prediction performance and error diagnostics are performed on the back-transformed forecasts compared to the original level data.

## Output produced by the code

The code will typically return:

- posterior parameter draws,
- posterior summary tables,
- trace plots and density plots for MCMC-based models,prediction tables, and relative-error summary tables.

Typical output objects may include:

- fitted model objects,
- prediction summary tables,
- error summary tables,
- parameter summary tables.

## Required R packages

Depending on the model file, the following packages may be required:

`install.packages(c(
  "Matrix",
  "coda",
  "ggplot2",
  "gridExtra",
  "reshape2",
  "dplyr"
))`

## Graphs
- The Figures in the paper were produced manually by collecting the output from the code and plotting in a separate script. 
- The code in this repository does not automatically generate the same figures, but it produces the underlying data and summary tables that can be used to create similar visualizations. 
- Users can customize their own plots based on the output from the model fitting and forecasting code.