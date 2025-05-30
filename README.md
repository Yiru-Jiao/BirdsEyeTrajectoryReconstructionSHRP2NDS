# BirdsEyeTrajectoryReconstructionSHRP2NDS
This repository provides information and guidelines to use the reconstructed trajectories of naturalistic crashes and near-crashes in the SHRP2 NDS. The dataset itself is published by the Virginia Tech Transportation Institute (VITTI) at [https://doi.org/10.15787/VTT1/EFYEJR]. To legally use the data, one is required to obtain a SHRP2 Data Use License (DUL) since some original data are included in the processed dataset. Below you will find a concise overview of the dataset, a step‑by‑step DUL application guide, a structure description of the dataset, and pointers to method and documentation.

**Quick navigation**
1. [About the SHRP2 NDS](#About-SHRP2-NDS)
2. [What’s in the reconstructed dataset]()
3. How to get a Data‑Use License
4. Folder layout
5. Method summary & data dictionary
6. Citation

## About SHRP2 NDS
[SHRP2 NDS](https://highways.dot.gov/safety/data-analysis-tools/rsdp/rsdp-tools/strategic-highway-research-program-shrp2) is abbreviated from the Second Strategic Highway Research Program’s (SHRP2) Naturalistic Driving Study (NDS). It collected data with 3,300+ drivers' participation between 2010‑2013 across six U.S. states. The participants' personal vehicles were equipped with data acquisitation systems that recorded video footage, vehicle network data (e.g., speed, brake, and accelerator positions), and signals from additional sensors such as forward radar and accelerometers. A unique asset of SHRP2 NDS is the **manually annotated safety‑critical events**, i.e., crashes and near‑crashes, along with safe baseline segments for parallel comparison. For a full technical description, see [^1].

## Reconstructed data overview

|        Number of trips       | Originally recorded | Subject-vehicle reconstructed | Both subject and object reconstructed |
|------------------------------|------:|--------------------------:|---------------------:|
| **Safe baselines**           | 32,509 | 10,919 | 3,893 |
| **Safety-critical**          | 8,895 | 8,111 | 6,664 |
| &nbsp;&nbsp;-**Crashes**     | 1,942 | 1,807 | 1,042 |
| &nbsp;&nbsp;-**Near-crashes**| 6,953 | 6,304 | 5,262 |

This dataset contains reconstructed bird's eye view trajectories of 10,919 safe baseline trips and 8,111 trips involving safety-critical events (crashes and near-crashes). Among all the trips, 3,893 safe baseline trips have both the subject vehicle and at least one surrounding object reconstructed; and for safety-critical trips the number is 6,664, including 1,402 crashes and 5,262 near-crashes. The dataset is processed from two existing datasets that were derived from SHRP2 NDS [^2][^3].

## DUL application
> **Typical duration:** 1 week ~ 1 month

To access the data, 
1. **Initiate dataset application to VITTI**  
   Please send DUL inquiries to <datasharing@vtti.vt.edu>. In the email, you may mention this dataset's title and URL, as well as a brief description of your intended research topic. A staff in VITTI will contact you back.
2. **Fill in and sign application form**  
   The staff will send you a DUL application form, where you will fill in information about your intended use, and learn how to safely store the data. A DUL can be applied for a team by the PI.
3. **Submit the form back**
   Submit the form following the instructions in the form.
4. **Await approval**  
   Once VTTI approves your application, you will receive a link and credentials to download the dataset.
5. **Download files**  
   Log in dataset page, download all files, and store them safely.

## Structure preview of the dataset
The folder structure of the dataset you will access is as follows:
```
----- Parent folder
  |-- readme.md (this file)
  |-- ReconstructedTrajectories.zip
  |-- SafetyCriticalTestSet.zip
  |-- MethodExplanation.pdf
  |-- OverallDataDictionary.pdf
```
where `ReconstructedTrajectories.zip` contains the reconstructed trajectories of all the trips, and `SafetyCriticalTestSet.zip` contains the safety-critical test set. Within each of the zipped files, the folder structure and more detailed documentaion are provided in the `readme_data_structure.md` file. The `MethodExplanation.pdf` describes the methods used to process these data. The `OverallDataDictionary.pdf` summarises a complete data dictionary for the entire dataset.

## Method summary and data dictionary
In this repository we also provide the [`MethodExplanation.pdf`](MethodExplanation.pdf) and [`OverallDataDictionary.pdf`](OverallDataDictionary.pdf).

## Citation
```bibtex
@report{jiao2025shrpcrash,
author = {Jiao, Yiru and Calvert, Simeon},
publisher = {VTTI},
title = {Bird’s eye view trajectory reconstruction of naturalistic crashes and near-crashes in the SHRP2 NDS},
year = {2025},
version = {V1},
doi = {10.15787/VTT1/EFYEJR},
}
```

## References
[^1]: J. M. Hankey, M. A. Perez, and J. A. McClafferty. Description of the SHRP 2 Naturalistic Database and the Crash, Near-Crash, and Baseline Data Sets. Tech. rep. Virginia Tech Transportation Institute, 2016.
[^2]: E. Sears, M. A. Perez, K. Dan, T. Shimamiya, T. Hashimoto, M. Kimura, S. Yamada, and T. Seo. A Study on the Factors That Affect the Occurrence of Crashes and Near-Crashes. Version V2. 2019. URL: https://doi.org/10.15787/VTT1/FQLUWZ
[^3]: C. K. Layman, M. A. Perez, T. Sugino, and J. Eggert. Research of Driver Assistant System. Version V3. 2019. URL: https://doi.org/10.15787/VTT1/DEDACT
