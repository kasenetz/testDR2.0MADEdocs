# Input Data

HBCD-MADE takes raw, BIDS formatted HBCD data in .set file format. Please visit the [HBCD Data Release Docs](https://docs.hbcdstudy.org/latest/) for more information once the data are released. 

- [HBCD EEG Overview](https://docs.hbcdstudy.org/latest/instruments/eeg/#overview-eeg-protocols)

- [Contents of Raw HBCD data folders](https://docs-hbcd-made.readthedocs.io/en/latest/data_requirements.html#contents-of-raw-hbcd-data-folders)

- [Required Processing Inputs](https://docs.hbcdstudy.org/latest/instruments/eeg/#overview-eeg-protocols)

## Required Porcessing Inputs

The following files are required inputs to the HBCD-MADE pipeline:

Channel locations file: `./0_2AverageNet128_v1.sfp`

Metadata in .set files: `./SUBSES_task-<label>_acq-eeg_eeg.set`

EEG data matrix in .fdt: `./SUBSES_task-<label>_acq-eeg_eeg.fdt`

Processing settings .json: `proc_settings_HBCD.json`

## HBCD EEG Task Information

Task descriptions are available on the centralized HBCD Data Release Docs, and HBCD EEG Task files can be downloaded from our [GitHub repository](https://github.com/ChildDevLab/Tasks)


