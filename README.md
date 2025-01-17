# Evolutionary dynamics of the escape of SARS-CoV-2 from hundreds of antibodies

This repository contains all input data, code, and results for the paper "Clonal interference and changing selective pressures shape the escape of SARS-CoV-2 from hundreds of antibodies" by the Matsen group.

As input, we use data from multiple sources, including:
* Deep mutational scanning from [Cao et al.](https://doi.org/10.1038/s41586-022-05644-7).
* Neutralization data from Cao et al. and [Wang et al](https://www.nature.com/articles/s41586-022-04466-x).
* SARS-CoV-2 sequences obtained using [Nextstrain](https://nextstrain.org/).

## Organization of this repository

* `data/`: a directory with input data
* `notebooks/`: a directory with Jupyter notebooks, which contain most of the code to perform the analyses
* `scripts/`: a directory with auxiliary scripts
* `ncov-dmsa/`: a submodule that uses Nextstrain to curate SARS-CoV-2 sequences
* `environment.yml`: encodes the conda environment that we used to run the notebooks in `notebooks/`

## Step #1: using Nexstrain to curate a set of SARS-CoV-2 sequences sampled roughly uniformly over the first 3.5 years of the pandemic and from many countries from around the globe

The code to run this step is in the submodule `ncov-dmsa/`.
The submodule comes from [a repository](https://github.com/matsengrp/ncov-dmsa) that we forked from the [Nextstrain ncov repository](https://github.com/nextstrain/ncov).
Our forked repository includes code to compute escape scores from DMS data and to color the output Nextstrain trees based on those scores.

The code to run this step can be carried out by running the `Snakemake` command in file `run_build.sh` in the submodule's root directory.

## Step #2: computing an escape score for each SARS-CoV-2 sequence



## Running the analysis

* Run the Nextstrain build
* Then, in `notebooks/` run `compute_escape_scores.ipynb` followed by `analyze_escape_scores.ipynb`

, processed by the [Bloom lab's escape calculator](https://github.com/jbloomlab/SARS2-RBD-escape-calc)

## TODO
* update `ncov-dmsa` submodule to include new config file, results?, etc.
* clean up `environment.yml` file
* add submodule for computing fitness effects?