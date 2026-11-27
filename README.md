<h1 align="center">clever</h1>

<p align="center">
  Flags outlying timepoints in an fMRI run with the clever R package (PCA leverage, robust distance and DVARS) for scrubbing and quality control, and outputs an outlier plot and table.
</p>

<p align="center">
  <a href="https://github.com/brainlife/abcd-spec"><img src="https://img.shields.io/badge/ABCD_Spec-v1.1-green.svg" alt="ABCD Spec v1.1"></a>
  <a href="https://doi.org/10.25663/bl.app.71"><img src="https://img.shields.io/badge/Brainlife-bl.app.71-blue.svg" alt="Run on brainlife.io"></a>
</p>

## At a glance

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

> **Flags outlying timepoints in an fMRI run with the clever R package (PCA leverage, robust distance and DVARS) for scrubbing and quality control, and outputs an outlier plot and table.**

Masks the BOLD time series into a timepoints-by-voxels matrix, robustly normalises it, keeps high-variance and/or high-kurtosis principal components and measures leverage, robust distance and optionally DVARS for each timepoint, then thresholds them to flag outliers. Use it to choose volumes to scrub or to check fMRI data quality. Needs a 4D BOLD image and a brain mask in the same space. Note: main.R reads the images from undefined variables bold and mask (not input$bold / input$mask) and the parameter kurt_quantile is not in the list of names passed to clever (it expects kurt_quant), so the App may fail or ignore that parameter.

| | |
|---|---|
| **Inputs** | `neuro/func/task` · `neuro/mask` |
| **Outputs** | `raw` |
| **Container** | `brainlife/clever:2.0` |
| **Resources** | 8 CPUs · 31 GB memory · 15 h walltime |
| **Run it** | [doi.org/10.25663/bl.app.71](https://doi.org/10.25663/bl.app.71) |

#### Inputs

| Input | Datatype | Required | Description |
|---|---|---|---|
| `bold` | `neuro/func/task` | yes | The brain volume time-course to look for outlying timepoints in. |
| `mask` | `neuro/mask` | yes | The whole brain mask. Voxels to be retained for analysis should be 1; voxels to be discarded should be 0. |

#### Outputs

| Output | Datatype | Description |
|---|---|---|
| `clever` | `raw` | Outlier detection for high-dimensional data. |

<details>
<summary><b>Parameters</b> (16)</summary>

| Parameter | Type | Default | Description |
|---|---|---|---|
| `projection` | enum | `variance` | `clever` works by first choosing PCs of the data which are likely to contain information about outliers. `kurtosis` selects PCs of high kurtosis among the PCs which explain the top 90% of variance. It is based on the observation that the presence of outliers often induces high kurtosis. `variance` selects PCs of greater-than-average variance, and is based on the observation that the larger PCs often contain outlier information, whereas smaller PCs often represent typical variation in the data. We recommend the `kurtosis` method over the `variance` method. `PCATF` (PCA Trend Filtering) works differently from the other two projection methods: it computes a variation of PCA which minimizes the zero norm of first-differences in each PC score. This has the effect of making PC scores constant within neighborhoods of little change, thus highlighting regions of artifact presence. It is based on the observation that timepoints with artifacts tend to be adjacent (due to an artifact source persisting or otherwise affecting the fMRI across multiple scans). If multiple projections are chosen, each projection will be used with each compatible measure of outlyingness. Options: `kurtosis`, `variance`, `PCATF`, `kurtosis, variance`, `kurtosis, PCATF`, `variance, PCATF`, `kurtosis, variance, PCATF`. |
| `out_meas` | enum | `leverage` | After projecting the data, `clever` computes an outlyingness score for each observation. `leverage` is based on a regression model using the PC scores as a design matrix. `robdist` is a robust measure of the minimum covariance determinant (MCD) distance. It is currently being revamped; for now, we recommend the `leverage` option. Options: `leverage`, `robdist`, `leverage, robdist`. |
| `DVARS` | enum | `TRUE` | DVARS is another common measure of outlyingness for fMRI data based on the standard deviation of the voxels' change from the previous timepoint. Unlike the other methods, it is not based on first projecting the data. Options: `FALSE`, `TRUE`. |
| `detrend` | enum | `TRUE` | Should the PCs be detrended before measuring the outlyingness of each observation? Detrending is highly recommended for fMRI data for two reasons. First, temporal trends induce positive or negative kurtosis, contaminating the connection between high kurtosis and outlier presence. Second, trends tend to reduce the size of the in-MCD subset for the robust distance method causing many false positives. In addition to `TRUE` and `FALSE`, a third option `kurtosis` can be used to only detrend the PCs for the purpose of measuring kurtosis, and not for the actual outlyingness measurement. This option will not affect the PCATF projection, which is never detrended. Options: `TRUE`, `FALSE`, `kurtosis`. |
| `kurt_quantile` | string | `0.95` | A number between 0 and 1. Only applicable for the `kurtosis` projection method. PCs of kurtosis greater than this quantile will be retained. The quantile will be based on the sampling distribution of kurtosis for Normal, independent data (estimated by simulation or with the theoretical asymptotic distribution). The default value of 0.95 was chosen because the kurtosis of PCs with outliers should easily surpass this quantile, and 0.05 is a reasonable rate for false positives (retaining PCs without outlier information) under the null hypothesis of no outliers. Note that at least one PC will be retained in each projection, regardless of the value of `kurt_quantile`. |
| `id_outliers` | enum | `TRUE` | Should outliers be identified? Options: `TRUE`, `FALSE`. |
| `lev_cutoff` | string | `4` | The outlier cutoff value for leverage, as a multiple of the median leverage. |
| `rbd_cutoff` | string | `0.9999` | The outlier cutoff quantile for MCD distance. |
| `lev_images` | string | `FALSE` | Leverage images are currently not supported by the `clever` BrainLife app. |
| `verbose` | enum | `FALSE` | Should occasional updates be printed? Default is `FALSE`. Options: `TRUE`, `FALSE`. |
| `main` | string |  | Override title for output plot(s). |
| `sub` | string |  | Override subtitle for output plot(s). |
| `xlab` | string |  | Override x-axis label for output plot(s). |
| `ylab` | string |  | Override y-axis label for output plot(s). |
| `png` | string | `cleverPlot.png` | Name of file to save plot to. |
| `csv` | string | `cleverTable.csv` | Name of file to save table to. |

</details>

<!-- brainlife-app:end -->

## Running the App

### On brainlife.io

Open the App at [doi.org/10.25663/bl.app.71](https://doi.org/10.25663/bl.app.71), choose `neuro/func/task`, `neuro/mask` from your project, set the parameters and press **Execute**. The results are saved to your project as `raw`.

### Locally

You need [Singularity/Apptainer](https://apptainer.org/) (the App runs in `brainlife/clever:2.0`), [`jq`](https://jqlang.github.io/jq/).

1. Clone this repository.
2. Create `config.json` with the paths to your inputs and the parameters:

   ```json
   {
       "bold": "/path/to/bold/bold",
       "mask": "/path/to/mask/mask",
       "projection": "variance",
       "out_meas": "leverage",
       "DVARS": "TRUE",
       "detrend": "TRUE",
       "kurt_quantile": "0.95",
       "id_outliers": "TRUE",
       "lev_cutoff": "4",
       "rbd_cutoff": "0.9999",
       "lev_images": "FALSE",
       "verbose": "FALSE",
       "main": "",
       "sub": "",
       "xlab": "",
       "ylab": "",
       "png": "cleverPlot.png",
       "csv": "cleverTable.csv"
   }
   ```

3. Run `./main`.

## How it works

As input, `clever` takes an fMRI volume `"bold.nii.gz"` and brain mask `"mask.nii.gz"`, and applies the mask to form a ***T*** timepoints by ***V*** voxels matrix, ***Y***. In our case, ***Y*** represents an fMRI run: each row is a vectorized volume, and each column represents one timepoint. Next, the algorithm performs the following steps: 

1. Normalize ***Y*** by centering and scaling its columns robustly.

2. Perform PCA on the normalized ***Y*** matrix using the singular value decomposition (SVD) in order to obtain the ***T **x** T*** PC score matrix, ***U***.

3. Some PCs are removed from ***U*** to obtain the ***Q*** *x* ***T*** (***Q*** *<* ***T***) matrix, ***U'***. The PCs which are retained are those likley to contain outlier information: PCs with greater-than-average variance, or PCs with both greater-than-average variance and high kurtosis. (The removal of at least one PC is also a theoretical requirement for leverage; additionally, the robust distance method requires ***Q*** to be appropriately small relative to ***T***.)

4. Next, leverage and/or robust distance is measured. The output of each is a length ***T*** vector representing the "outlyingness" of each time point.  

5. The outlyingness measures are thresholded to identify the set of outlying timepoints. 

We also include the DVARS outlier detection method in `clever`. It normalizes the data as explained in step 1, but otherwise follows the algorithm as described in the [paper (Afyouni and Nichols, 2018)](doi.org/10.1016/j.neuroimage.2017.12.098) and implemented in the [MATLAB code](https://github.com/asoroosh/DVARS) provided by its authors.

## Citing

If you use this App, please cite it, the software it builds on and brainlife.io:

- clever. brainlife.io App. [doi:10.25663/bl.app.71](https://doi.org/10.25663/bl.app.71)
- Afyouni S, Nichols TE. Insight and inference for DVARS. NeuroImage (2018). [doi:10.1016/j.neuroimage.2017.12.098](https://doi.org/10.1016/j.neuroimage.2017.12.098)
- Hayashi S, Caron BA, Heinsfeld AS, et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. *Nature Methods* 21:809–813 (2024). [doi:10.1038/s41592-024-02237-2](https://doi.org/10.1038/s41592-024-02237-2)
- Avesani P, McPherson B, Hayashi S, et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. *Scientific Data* 6:69 (2019). [doi:10.1038/s41597-019-0073-y](https://doi.org/10.1038/s41597-019-0073-y)

## Authors

- Amanda Mejia
- John Muschelli
- Damon Pham

## Funding

[![Wellcome-226486/Z/22/Z](https://img.shields.io/badge/Wellcome-226486%2FZ%2F22%2FZ-purple.svg)](https://wellcome.org/)
[![NIH-NINDS-U24NS140384](https://img.shields.io/badge/NIH_NINDS-U24NS140384-green.svg)](https://reporter.nih.gov/project-details/11261037)
[![NIH-NINDS-UM1NS132207](https://img.shields.io/badge/NIH_NINDS-UM1NS132207-green.svg)](https://reporter.nih.gov/project-details/11411328)
[![NIH-NIBIB-R01EB029272](https://img.shields.io/badge/NIH_NIBIB-R01EB029272-green.svg)](https://reporter.nih.gov/project-details/10262925)
[![NIH-NIBIB-R01EB030896](https://img.shields.io/badge/NIH_NIBIB-R01EB030896-green.svg)](https://reporter.nih.gov/project-details/10428625)
[![NIH-NIMH-R01MH126699](https://img.shields.io/badge/NIH_NIMH-R01MH126699-green.svg)](https://reporter.nih.gov/project-details/10460628)
[![NIH-NIDCD-R01DC020088](https://img.shields.io/badge/NIH_NIDCD-R01DC020088-green.svg)](https://reporter.nih.gov/project-details/11140284)
[![NSF-BCS-1734853](https://img.shields.io/badge/NSF_BCS-1734853-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1734853)
[![NSF-BCS-2203524](https://img.shields.io/badge/NSF_BCS-2203524-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2203524)
[![NSF-IIS-1636893](https://img.shields.io/badge/NSF_IIS-1636893-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1636893)
[![NSF-OAC-2148729](https://img.shields.io/badge/NSF_OAC-2148729-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2148729)
[![NSF-IIS-1912270](https://img.shields.io/badge/NSF_IIS-1912270-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1912270)
[![NSF-IIS-2148700](https://img.shields.io/badge/NSF_IIS-2148700-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2148700)
[![NSF-OAC-1916518](https://img.shields.io/badge/NSF_OAC-1916518-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1916518)

<sub>brainlife.io, which runs this App, is publicly funded by Wellcome, the U.S. National Institutes of Health and the U.S. National Science Foundation; acknowledging that support helps keep it running.</sub>
