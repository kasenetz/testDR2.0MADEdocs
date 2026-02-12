# Expected Outputs from HBCD-MADE

The output structure of HBCD-MADE will mimic the input BIDS structure. If you have some EEG file that is found under `/bids_dir/sub-<label>/ses-<label>/eeg/`, then the output of HBCD-MADE will fall under `output_dir/sub-<label>/ses-<label>/eeg/`.

The output of HBCD-MADE will primarily be found in `.set` [EEGLAB](https://eeglab.org/) formatted data structures and `.csv` files. Different stages of data processing will be saved, but the final data elements to be used for subsequent analyses will be found in the ./processed_data folder. If you load an EEG file from this folder, its `data` field will have dimensions `<num_electrodes, num_samples, num_epochs>`, with the epochs placed around the events specified in the `proc_settings.json` file. The `.set/.fdt` files saved by HBCD-MADE can be loaded back into Matlab with EEGLAB’s `pop_loadset` function, or loaded in python using MNE.

The following output folders and files are created throughout processing within each subject’s output directory:

```
 |__ made/
        |__ sub-{ID}/
            |__ ses-{V0X}/
                |__ eeg/
                    |__ filtered_data/
                    |   |__ sub-{ID}_ses-{V0X}_task-<FACE|MMN|RS|VEP>_acq-eeg_run-{X}_desc-filtered_eeg.fdt
                    |   |__ sub-{ID}_ses-{V0X}_task-<FACE|MMN|RS|VEP>_acq-eeg_run-{X}_desc-filtered_eeg.set
                    |
                    |__ ica_data/
                    |   |__ sub-{ID}_ses-{V0X}_adjustReport.txt
                    |   |__ sub-{ID}_ses-{V0X}_desc-mergedICA_eeg.fdt
                    |   |__ sub-{ID}_ses-{V0X}_desc-mergedICA_eeg.set
                    | 
                    |__ merged_data/
                    |   |__ sub-{ID}_ses-{V0X}_desc-merged_eeg.fdt
                    |   |__ sub-{ID}_ses-{V0X}_desc-merged_eeg.json
                    |   |__ sub-{ID}_ses-{V0X}_desc-merged_eeg.set
                    | 
                    |__ processed_data/
                    |   |__ sub-{ID}_ses-{V0X}_task-FACE_desc-<F-TOPO>_topo.jpg
                    |   |__ sub-{ID}_ses-{V0X}_task-FACE_desc-oz_<diffERP|ERP>.jpg
                    |   |__ sub-{ID}_ses-{V0X}_task-MMN_desc-oz_<MMN-TOPO>_topo.jpg
                    |   |__ sub-{ID}_ses-{V0X}_task-MMN_desc-t7t8_<diffERP|ERP>.jpg
                    |   |__ sub-{ID}_ses-{V0X}_task-<FACE|MMN>_desc-{IMG}.jpg
                    |   |__ sub-{ID}_ses-{V0X}_task-RS_desc-allCh_PSD.jpg
                    |   |__ sub-{ID}_ses-{V0X}_task-RS_LogPowerSpectra.csv
                    |   |__ sub-{ID}_ses-{V0X}_task-RS_dbPowerSpectra.csv
                    |   |__ sub-{ID}_ses-{V0X}_task-RS_AbsPowerSpectra.csv
                    |   |__ sub-{ID}_ses-{V0X}_task-<FACE|MMN|VEP>_ERPSummaryStats.csv
                    |   |__ sub-{ID}_ses-{V0X}_task-<FACE|MMN|VEP>_ERPTrialMeasures.csv
                    |   |__ sub-{ID}_ses-{V0X}_task-VEP_<desc-oz_ERP|topo>.jpg
                    |   |__ sub-{ID}_ses-{V0X}_task-<FACE|MMN|VEP>_acq-eeg_run-{X}_ERP.mat
                    |   |__ sub-{ID}_ses-{V0X}_task-RS_spectra.mat
                    |   |__ sub-{ID}_ses-{V0X}_task-<FACE|MMN|RS|VEP>_acq-eeg_run-{X}_desc-filteredprocessed_eeg.fdt
                    |   |__ sub-{ID}_ses-{V0X}_task-<FACE|MMN|RS|VEP>_acq-eeg_run-{X}_desc-filteredprocessed_eeg.set
                    | 
                    |__ sub-{ID}_ses-{V0X}_acq-eeg_preprocessingReport.csv
                    |__ sub-{ID}_ses-{V0X}_task-<FACE|MMN|RS|VEP>_acq-eeg_run-{X}_MADEspecification.json

# Label Values Legend
<F-TOPO>: diffInvVsUpr, diffObjVsUp2, inverted, object, upright, upright2
<MMN-TOPO>: deviant, diffDevVsSta, diffDevVsPre, preDeviant, standard 
```

## HBCD-MADE output contains the following file types:

**JPEG**- topographic and ERP plots.

**MAT** - MATLAB data files contain processing output.

**CSV**- stores data in tabular format.

**JSON**- stores metadata and saves task-specific configuration settings in a format that is easy for users to read and edit.

**SET**- contains metadata and parameters for the EEG dataset, such as channel locations, sampling rate, and event information.

**FDT**- field data table (FDT) files contain EEG data resaved across different stages of processing.

## HBCD-MADE output folders: 
(expand for contents)

<details>


<summary> ./filtered_data </summary>


This folder contains all data saved early in the processing pipeline after filtering but prior to bad channel detection. Each EEG task administered will have a corresponding .set and .fdt file stored within this folder. These data have undergone the following operations:

* deletion of discontinuous data

* deletion of EKG channel(s) if present

* downsampling

* deletion of outer electrode ring

* filtering with 0.3 Hz high-pass with stopband of 0.1

* 60 Hz low-pass with 10 Hz transition band using a noncausal FIR filter


</details>


<details>


<summary> ./merged_data </summary>


Immediately after filtering, tasks are merged together into one file and re-saved into this folder. Tasks present in the merged .fdt file are listed in the corresponding .json file.


</details>



<details>


<summary> ./ica_data </summary>


After data are merged into one file, they undergo the following operations and are re-saved into `ica_data/` with ICA weights:

* apply FASTER algorithm for bad channel detection

* ICA preparation (dataset copied and 1Hz high-pass applied to the copy, broken into 1-s epochs for epoch-level rejection, bad channels rejected)

* ICA

* run adjustedADJUST to identify artifact-containing independent components- see [Leach et al., 2020](https://onlinelibrary.wiley.com/doi/10.1111/psyp.13566) for details. Output from adjustedADJUST is written to `SUBSES_adjust_report.csv` and contains IC labels assigned by the algorithm.  


</details>
 


 <details>


<summary> ./processed_data </summary>


The `processed_data` folder contains MADE derivatives and a preprocessing report.  See here (Whitney add link) for descriptions of MADE derivatives. 


</details>

# Output by task 

Click [here](https://github.com/Child-Development-Lab/HBCD-EEG-Utilities/blob/main/docs/csv_data_dictionary_derivatives.csv) for a data dictionary defining the fields in each .csv output file. 

### Faces task (FACE)

**1- FACE Trial Measures Output**: `SUBSES_task-FACE-ERPTrialMeasures.csv`

Trial-level derivatives for the FACE task include mean amplitude.

| Variable Name | Description                              |
|---------------|------------------------------------------|
| Condition | inverted, object, uprightInv, uprightObj |
| TrialNum | trial index |
| MeanAmplitude_<WindowStart-WindowEnd>_ROI | Mean amplitude within specified time window at specified ROI |


**2- FACE Summary Statistics Output**: `SUBSES_task-FACE-ERPSummaryStatistics.csv`

Subject-level derivatives for the FACE task include mean amplitude and SME.

| Variable Name | Description                              |
|---------------|------------------------------------------|
| Condition | inverted, object, uprightInv, uprightObj |
| NTrials | number of trials retained per condition |
| MeanAmplitude_WindowStart-WindowEnd_ROI | Mean amplitude within specified time window at specified ROI |
| SME_WindowStart-WindowEnd_ROI | Standard measurement error during specified time window at specified ROI |


### Mismatch Negativity (MMN)

**1- MMN Trial Measures Output**: `SUBSES_task-MMN-ERPTrialMeasures.csv`

Trial-level derivatives for the MMN task include mean amplitude.

| Variable Name | Description                              |
|---------------|------------------------------------------|
| Condition | deviant, predeviant, standard |
| TrialNum | trial index |
| MeanAmplitude_WindowStart-WindowEnd_ROI | Mean amplitude within specified time window at specified ROI |

**2- MMN Summary Statistics Output**: `SUBSES_task-MMN-ERPSummaryStatistics.csv`

Subject-level derivatives for the MMN task include mean amplitude and SME.

| Variable Name | Description                              |
|---------------|------------------------------------------|
| Condition | deviant, predeviant, standard |
| NTrials | number of trials retained per condition |
| MeanAmplitude_WindowStart-WindowEnd_ROI | Mean amplitude within specified time window at specified ROI |
| SME_WindowStart-WindowEnd_ROI | Standard measurement error during specified time window at specified ROI |


### Visual Evoked Potential (VEP)

**1- VEP Trial Measures Output**: `SUBSES_task-VEP-ERPTrialMeasures.csv`

Trial-level derivatives for the VEP task include mean amplitude, adaptive mean (peak), and latency.

| Variable Name | Description                              |
|---------------|------------------------------------------|
| Condition | VEP |
| TrialNum | trial index |
| MeanAmplitude_WindowStart-WindowEnd_ROI | Mean amplitude within specified time window at specified ROI |
| Peak_WindowStart-WindowEnd_ROI | Adaptive mean amplitude within specified time window at specified ROI |
| Latency_WindowStart-WindowEnd_ROI | Latency to peak within specified time window at specified ROI |

**2- VEP Summary Statistics Output**: `SUBSES_task-VEP-ERPSummaryStatistics.csv`

Subject-level derivatives for the VEP task include SME, mean amplitude, adaptive mean (peak), and latency.

| Variable Name | Description                              |
|---------------|------------------------------------------|
| Condition | VEP |
| NTrials | number of trials retained per condition |
| SME_WindowStart-WindowEnd_ROI | Standard measurement error during specified time window at specified ROI |
| MeanAmplitude_WindowStart-WindowEnd_ROI | Mean amplitude within specified time window at specified ROI |
| Peak_WindowStart-WindowEnd_ROI | Adaptive mean amplitude within specified time window at specified ROI |
| Latency_WindowStart-WindowEnd_ROI | Latency to peak within specified time window at specified ROI |

### Resting State 

- When interpreting the 50.0 Hz bin in RS output, please be aware that the data are low-pass filtered at 50Hz. 

**1- RS Absolute Power (μV²) Spectra Output**: `SUBSES_task-RS-AbsPowerSpectra.csv`

Subject-level absolute power values for RS.  
   
| Variable Name | Description                              |
|---------------|------------------------------------------|
| Row | Electrode | 
| 1.0 Hz | Sum of absolute power in μV² centered at 1 Hz (within the 0.5hz to 1.5hz freq range) at corresponding electrode site |
| 2.0 Hz | Sum of absolute power in μV² centered at 2 Hz (within the 1.5hz to 2.5hz freq range) at corresponding electrode site |
| 3.0 Hz | Sum of absolute power in μV² centered at 3 Hz (within the 2.5hz to 3.5hz freq range) at corresponding electrode site |
| 50.0 Hz | Sum of absolute power in μV² centered at 50 Hz (within the 49.5hz to 50.5hz freq range) at corresponding electrode site |

**2- RS Power (dB) Spectra Output**: `SUBSES_task-RS-dbPowerSpectra.csv`

Subject-level dB power values for RS.  
   
| Variable Name | Description                              |
|---------------|------------------------------------------|
| Row | Electrode |
| 1.0 Hz | Sum of power in dB centered at 1 Hz (within the 0.5hz to 1.5hz freq range) at corresponding electrode site |
| 2.0 Hz | Sum of power in dB centered at 2 Hz (within the 1.5hz to 2.5hz freq range) at corresponding electrode site |
| 3.0 Hz | Sum of power in dB centered at 3 Hz (within the 2.5hz to 3.5hz freq range) at corresponding electrode site |
| 50.0 Hz | Sum of absolute power in μV² centered at 50 Hz (within the 49.5hz to 50.5hz freq range) at corresponding electrode site |

**3- RS Log Power Spectra Output**: `SUBSES_task-RS-LogPowerSpectra.csv`

Subject-level log power values for RS.  
   
| Variable Name | Description                              |
|---------------|------------------------------------------|
| Electrode | Electrode label |
| 1.0 Hz | Sum of natural log power centered at 1 Hz (within the 0.5hz to 1.5hz freq range) at corresponding electrode site |
| 2.0 Hz | Sum of natural log power centered at 2 Hz (within the 1.5hz to 2.5hz freq range) at corresponding electrode site |
| 3.0 Hz | Sum of natural log power centered at 3 Hz (within the 2.5hz to 3.5hz freq range) at corresponding electrode site |
| 50.0 Hz | Sum of absolute power in μV² centered at 50 Hz (within the 49.5hz to 50.5hz freq range) at corresponding electrode site |

**4- RS Power Spectra .mat Output**: `SUBSES_task-RS_spectra.mat`

Epoch-level derivatives for RS.
   
| Variable Name | Description                              |
|---------------|------------------------------------------|
| avg_abs_pow | Average absolute power (μV²) across epochs |
| avg_log_pow | Average natural log power across epochs |
| avg_db_pow | Average power (dB) across epochs |
| all_abs_power | Channels x abs power x epochs |
| all_log_power | Channels x log power x epochs |
| all_db_pow | Channels x dB power x epochs |
| epoch_level_abs_pow | Absolute power (μV²) for each epoch |
| epoch_level_log_pow | Log power for each epoch |
| epoch_level_db_pow | Power (dB)  for each epoch |
| channel locations | ... |
| freqs | Frequency bins (1hz increments, 1-50hz) |
| n_epochs | Number of epochs |
| Fs | Sampling rate |
| num_channels | Number of channels |

**5- RS Power Spectra Figure** : `SUBSES_task-RS-desc-allCh_PSD.jpg`

Subject-level plot visualizing power spectral density for each channel across the 1-50Hz frequency range. Data for this plot originates from `SUBSES_task-RS-dbPowerSpectra.csv`. See below for an example:
 ![PSD plot](PSDplot.png)




