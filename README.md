# 🧪 Proteomics Analysis Pipeline

This repository provides a streamlined workflow for analyzing proteomics data using mzML files and [Sage](https://github.com/lazear/sage).

---

## 📦 Environment Setup

### 1. Create Conda Environment

Use the provided `environment.yml` file to create the analysis environment:

```bash
conda env create -f envs/environment.yml
conda activate mzml_env
```
### 📁 File Structure Overview
Make sure all scripts, raw files, and result folders are located at the same top-level directory, like this:

<img width="600" height="480" alt="image" src="https://github.com/user-attachments/assets/694b3987-9619-4b51-b496-515720e9c96c" />

📌 Note: All scripts (*.sh) and the sage_config.json should be placed in the same directory level as 00.raw and 01.mzml.

## 🔁 Workflow
The pipeline consists of the following steps:

### 0. Make the scripts executable

```
chmod +x *.sh
```


### 1. Convert RAW to mzML
Run the script to convert individual .raw files to .mzML format:

```bash
./convert_raws.sh
```

### 2. Merge mzML files per sample
Use the merge_mzmls.sh script to combine individual mzML files into one per sample:
```bash
./merge_mzmls.sh
```

### 2-1. Find Path Merged mzML Files
You can obtain the merged .mzML file paths required for the JSON file as a single string, where each path is quoted and separated by commas.
```bash
./Find FindPath_merged_mzML.sh
```
<img width="432" height="77" alt="image" src="https://github.com/user-attachments/assets/8e6320f1-330b-421c-bef8-0f9bfa7fbeb9" />

### 2-2. Modify Path in JSON files

For each analysis JSON file (e.g., for Proteome, GlycoProteome, PhosphoProteome),
update the following two fields using the output from grouped_mzml_paths.tsv:

- "mzml_paths": Use the comma-separated list of "merged.mzML" files for the corresponding type.

- "reference": Set the appropriate FASTA file (e.g., /human_contam_20240723.fasta).

Make sure the "type" in the TSV matches the dataset you are configuring in the JSON.


### 3. Run Sage
Execute Sage using the prepared JSON configuration:

⚠️ CPU usage spikes significantly during this step, so please monitor your system accordingly.

```bash
sage ./JSON/global_250715.json -o 02.sage/Proteome
sage ./JSON/glyco_250715.json -o 02.sage/GlycoProteome
sage ./JSON/phospho_250715.json -o 02.sage/PhosphoProteome

```

🛠 This step will be automated in a future update.

## 📁 Output Files

| File               | Description                                                                                                                           |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------- |
| `results.sage.tsv` | **Main identification result** from Sage. Contains peptide-spectrum matches (PSMs) with scores, q-values, and protein assignments.    |
| `tmt.tsv`          | **TMT quantification table**, with intensities for each peptide across TMT channels. Includes modification, uniqueness, and q-values. |
| `raw_list.txt`     | List of original `.raw` files used as input for conversion to mzML.                                                                   |
| `sample_dirs.txt`  | List of sample directories that were processed and merged during the merging step.                                                    |
