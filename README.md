# Evolutionary dynamics of the escape of SARS-CoV-2 from hundreds of antibodies

A study by the Matsen lab that investigates the evolutionary dynamics of SARS-CoV-2 antibody escape from hundreds of antibodies.

As input, we use data from multiple sources, including:
* Deep mutational scanning from [Cao et al.](https://doi.org/10.1038/s41586-022-05644-7), processed by the [Bloom lab's escape calculator](https://github.com/jbloomlab/SARS2-RBD-escape-calc).
* Neutralization data from Cao et al. and Wang et al.
* SARS-CoV-2 sequences obtained through Nextstrain.

## Organization of this repository

* `data/`: a directory with input data
* `notebooks/`: a directory with Jupyter notebooks, which contain most of the code to perform the analyses
* `scripts/`: a directory with auxiliary scripts
* `ncov-dmsa/`: a fork of the [Nextstrain ncov repository](https://github.com/nextstrain/ncov) that includes code to compute escape scores from DMS data and color the output Nextstrain trees based on those scores
* `environment.yml`: encodes the conda environment that we used to run the notebooks in `notebooks/`

## Running the analysis

* Run the Nextstrain build
* Then, in `notebooks/` run `compute_escape_scores.ipynb` followed by `analyze_escape_scores.ipynb`