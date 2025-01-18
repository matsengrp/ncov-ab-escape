# Evolutionary dynamics of the escape of SARS-CoV-2 from hundreds of antibodies

This repository contains input data, code, and results for the paper "Clonal interference and changing selective pressures shape the escape of SARS-CoV-2 from hundreds of antibodies" by the Matsen group.

As input, we use data from multiple sources, including:
* Deep mutational scanning from [Cao et al.](https://doi.org/10.1038/s41586-022-05644-7)
* Neutralization data from Cao et al. and [Wang et al.](https://www.nature.com/articles/s41586-022-04466-x)
* SARS-CoV-2 sequences obtained using [Nextstrain](https://nextstrain.org/)

## Organization of this repository

* `data/`: a directory with input data
* `notebooks/`: a directory with Jupyter notebooks, which contain most of the code to perform the analyses
* `scripts/`: a directory with auxiliary scripts
* `ncov-dmsa/`: a submodule that uses Nextstrain to curate SARS-CoV-2 sequences
* `environment.yml`: encodes the conda environment that we used to run the notebooks in `notebooks/`
* `results/`: a directory with key results files

The below subsections break down the analysis into five steps.
For each step, we describe the code used to run that step and the location of key output files.

## Step #1: using Nexstrain to curate a set of globally circulating SARS-CoV-2 sequences sampled roughly uniformly over the first 3.5 years of the pandemic

The code to run this step is in the submodule `ncov-dmsa/`.
This submodule is a custom version of the [Nextstrain ncov repository](https://github.com/nextstrain/ncov), which contains code to compute escape scores from DMS data and to color the output Nextstrain trees based on those scores.

To carry out this step, we ran the following snakemake command in the submodule's root directory:

```snakemake --use-conda --cores <n_cpus> --configfile my_profiles/configs/global_build.yaml --conda-frontend conda```

Key results files:
* `results/viral_metadata.csv`: many of the output files from the above pipeline are large in size. So, we manually created this light-weight file that reports key data relevant to the below analyses. In this file, each row corresponds to a viral sequence sampled in the above pipeline. The following columns report:
    * `strain`: the name of the sequence.
    * `all_aa_substitutions`: the set of amino-acid mutations in that sequence's spike protein relative to the Wuhan-Hu-1 reference strain (see the column ), the year the sequence was sampled, and the Nextstrain clade associated with the sequence.
    * `date`: the date the sequence was sampled. The `year` column gives the date as a decimal in years.
    * `clade`: gives the clades we used in our analysis. The column `Nextstrain_clade` gives the raw clade names generated from the Nextstrain pipeline. These names differ from the ones in the `clade` column in a few minor ways (we merged three clades associated with Delta, and updated the paranthetical names to be more descriptive).
* Additional files that are larger in size, such as the multiple sequence alignment of spike sequences used to compute viral escape scores, are available upon request.
* `ncov-dmsa/auspice/`: this folder contains the pylogenetic tree generated from the above Nextstrain pipeline. The tree can be vizualized by dragging and dropping the files onto https://auspice.us/, or by running the following command in the `ncov-dmsa` directory using the [Nextstrain software package](https://docs.nextstrain.org/en/latest/): ```nextstrain view auspice/```. The tree can be colored using different options present in the `color by` dropdown menu.

## Step #2: process the input DMS data from Cao et al.

The notebook `notebooks/process_data.ipynb` processes the raw data from Cao et al. in the following ways:
* Of the 3,333 antibodies with data, this notebook curates a set of 1,603 using the following filters: i) the antibody was strongly escaped in the DMS experiments, defined as the $e_{a,r,m}$ values for that antibody summing to $>$2 for at least one site, ii) the antibody was isolated from a human exposed to SARS-CoV-2 (rather than SARS-CoV-1), and iii) the antibody had pseudovirus neutralization data for all variants from the Cao et al.\ study. There were 2,326 antibodies that passed the first filter, 1,616 antibodies that passed the first two filters, and 1,603 antibodies that passed all three filters.
* The notebook also computes site-level escape scores from the DMS data for each antibody. It does so using the strategy described in the Results section of our paper.

The code in this notebook is adapted from [Bloom lab's escape calculator](https://github.com/jbloomlab/SARS2-RBD-escape-calc).

Key results files:
* `results/processed_input_data/escape.csv`: gives site-level antibody escape scores. Each row gives an escape score from a given antibody for a given RBD site. The following columns report:
    * `antibody`: the name of the antibody
    * `site`: the RBD site number
    * `normalized_escape`: the site-level escape score

## Step #3: computing viral escape scores

The notebook `notebooks/compute_escape_scores.ipynb` performs this step.
It does so using the strategy described in the Results section of our paper.

## Step #4: estimating fitness effects of amino-acid mutations in nature

We computed fitness effects using the computational pipeline from https://github.com/neherlab/SARS2-mut-fitness-v2 described in [Haddox et al.](https://doi.org/10.1101/2025.01.07.631013).

To do so, we cloned the above repository (commit: `65fcdd3c0252ca2c3caaad8e79b6af03d697d318`), replaced the default `config.yaml` file with a custom file (see `scripts/config.yaml`), and then ran the pipeline using the following snakemake command:

```snakemake -c <n_cpus>```

Key results files:
* The above pipeline generates a series of CSV files that report inferred fitness effects, generating one file per clade. We make these files available in the `results/aamut_fitness/` directory. In each file, each row reports data for a given amino-acid mutation, with the `delta_fitness` column giving the mutation's estimated fitness effect, and the `uncertainty` column giving the level of uncertainty corresponding to each estimate.

## Step #5: analyzing escape scores

The notebook `notebooks/analyze_escape_scores.ipynb` takes raw data from above and performs various analyses from the paper, including all plots from the paper. These analyses include computing antibody-escape trajectories, clustering trajectories, analyzing antibody epitopes and neutralization potencies, validating predicted escape scores, analyzing clade-displacement events, and analyzing fitness effects to show evidence of clonal interference and changing selective pressures.

Key results files:
* `results/trajectories.csv`: each row in this file gives the value of the escape trajectory for a given antibody at a given point in time. The following columns report:
    * `antibody`: the name of the antibody.
    * `year`: the timepoint in the trajectory.
    * `mean_escape`: the value of the trajectory at the given timepoint.
    * `epitope_cluster`: the antibody's epitope, as defined by Cao et al.
    * `escape_cluster`: the antibody's escape cluster from our paper (1-8)
    * `kmeans_cluster`: the antibody's cluster from k-means clustering into 20 clusters before we manually consolidated these 20 cluster into 8 clusters
    * `log_IC50`: the $\log_{10} \text{IC}_{50}$ of the antibody against the D614G variant (data from Cao et al.)
    * `source`: the source of the antibody, indicating the exposure history of the host the antibody was isolated from (metadata from Cao et al.)