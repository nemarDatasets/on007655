# MorseEEG-ATP companion Matlab code

This folder contains the Matlab analysis code accompanying the OpenNeuro dataset.

## Scope

These scripts are intended to be distributed together with the dataset in the `code/` directory and to run on the corresponding BIDS/OpenNeuro dataset structure.

The code implements a six-step workflow:

1. **Behavior analysis and grouping** (`step1_behavior_analysis.m`)
2. **EEG preprocessing** (`step2_preprocess.m`)
3. **PSD feature extraction** (`step3_extract_PSD.m`)
4. **ERP analysis and visualization** (`step4_ERP_analysis.m`)
5. **PSD group-difference analysis** (`step5_PSD_ranksum_FDR.m`)
6. **LOSO PSD classification** (`step6_PSD_LOSO_classification.m`)

Outputs are written into subdirectories inside `derivatives/`.

## Tested software environment

The code has been tested only in the following environment:

- **Operating system:** Windows
- **MATLAB:** R2022b
- **EEGLAB:** v2024.0

Other environments have not been tested in this project.

For toolbox requirements, see `ENVIRONMENT_DEPENDENCIES.md`.

## Expected dataset structure

Set `project_root` in each script to the dataset root, for example:

```matlab
project_root = 'D:\BIDS\MorseEEG-ATP';
```

The dataset root is expected to contain at least:

```text
project_root/
├── participants.tsv
├── sub-01/
│   ├── beh/
│   │   ├── sub-01_task-dotcount_beh.tsv
│   │   └── sub-01_task-firstlast_beh.tsv
│   └── eeg/
│       ├── sub-01_task-dotcount_eeg.vhdr
│       ├── sub-01_task-dotcount_events.tsv
│       ├── sub-01_task-firstlast_eeg.vhdr
│       └── sub-01_task-firstlast_events.tsv
├── sub-02/
├── ...
├── derivatives/
└── code/
    ├── step1_behavior_analysis.m
    ├── step2_preprocess.m
    ├── step3_extract_PSD.m
    ├── step4_ERP_analysis.m
    ├── step5_PSD_ranksum_FDR.m
    ├── step6_PSD_LOSO_classification.m
    ├── eloc62stroop.loc
    ├── topoplot_addparameter.m
    ├── README_Code.md
    └── ENVIRONMENT_DEPENDENCIES.md
```

## Required files in the `code/` directory

Please distribute the following files together:

- `step1_behavior_analysis.m`
- `step2_preprocess.m`
- `step3_extract_PSD.m`
- `step4_ERP_analysis.m`
- `step5_PSD_ranksum_FDR.m`
- `step6_PSD_LOSO_classification.m`
- `eloc62stroop.loc`
- `topoplot_addparameter.m`
- `README_Code.md`
- `ENVIRONMENT_DEPENDENCIES.md`

`eloc62stroop.loc` is required by Step4 and Step5 for scalp topography plots.  
`topoplot_addparameter.m` is required by Step5.

## Recommended execution order

Run the scripts in the following order:

```text
Step1 -> Step2 -> Step3 -> Step4 -> Step5 -> Step6
```

## Step-by-step summary

### Step1: Behavior analysis and grouping
Input:
- `participants.tsv`

Output:
- `derivatives/behavior/`
- grouping files used by Step5 and Step6

Main saved files include:
- `top_week_sub_ACC_RT.mat`
- `commonSubIDs.mat`
- `labelinf_ACC_RT.mat`

### Step2: EEG preprocessing
Input:
- subject behavioral TSV files
- BrainVision EEG files
- subject events TSV files

Output:
- `derivatives/preprocessed/`
- minimally preprocessed EEG `.mat` files
- `derivatives/preprocessed/dataset_description.json`

### Step3: PSD feature extraction
Input:
- `derivatives/preprocessed/*.mat`

Output:
- `derivatives/PSD_features/*.mat`

### Step4: ERP analysis
Input:
- `derivatives/preprocessed/*.mat`
- `eloc62stroop.loc`

Output:
- `derivatives/ERP/`

### Step5: PSD group-difference analysis
Input:
- `derivatives/PSD_features/*.mat`
- Step1 grouping outputs
- `eloc62stroop.loc`
- `topoplot_addparameter.m`

Output:
- `derivatives/stats/PSD_ranksum_FDR/`

### Step6: LOSO PSD classification
Input:
- `derivatives/PSD_features/*.mat`
- Step1 grouping outputs

Output:
- `derivatives/classification/`

## Grouping used in Step5 and Step6

Step5 reads the Step1 grouping outputs and uses the **4th grouping solution** in `top_week_sub_ACC_RT.mat`.

The corrected `step6_PSD_LOSO_classification.m` is aligned with this behavior:
- it reads Step1 outputs from `derivatives/behavior/`
- it uses `GROUP_SELECTION_INDEX = 4` by default
- it converts Step1 row indices into actual subject IDs using `commonSubIDs.mat`

If you want to use another grouping solution from Step1, change:

```matlab
GROUP_SELECTION_INDEX = 4;
```

inside `step6_PSD_LOSO_classification.m`.

## Running the code

Before running the scripts:

1. Open MATLAB.
2. Add EEGLAB to the Matlab path.
3. Open the dataset `code/` directory.
4. Set `project_root` at the top of each script.

## Reproducibility notes

- Intermediate derivative files are stored as Matlab `.mat` files.
- `step6_PSD_LOSO_classification.m` sets a fixed random seed with `rng(42)`.
- The tested environment is Windows + MATLAB R2022b + EEGLAB v2024.0.
- Using different software versions may produce different behavior and has not been tested here.

## Citation / usage note

If you use this dataset or the companion code in your work, please cite the corresponding dataset record and manuscript.
