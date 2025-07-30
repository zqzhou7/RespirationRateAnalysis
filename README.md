# R Script for the Respiration Rate Calculation

![R Markdown](https://img.shields.io/badge/built_with-RMarkdown-blue.svg)

## Overview

This repository provides a **step-by-step R Markdown tutorial** for calculating **oxygen consumption (respiration rate)** using raw data from a **FireSting optical oxygen sensor**. The script processes `.txt` sensor output files, extracts relevant measurement intervals, and computes respiration rates across four channels.

This tutorial is ideal for researchers working with aquatic respirometry or conducting physiological experiments with oxygen sensors.

> 🔗 Access this notebook on GitHub: [RespirationRateAnalysis](https://github.com/zqzhou7/RespirationRateAnalysis.git)

---

## Files

- `respiration_template.Rmd` — R Markdown notebook with step-by-step instructions.
- `d5_control.txt` — Example raw data file from the FireSting sensor.
- `respirationRate.csv` — Output CSV file containing calculated respiration rates and temperature.

---

## Requirements

### Software

- R (version ≥ 4.0)
- RStudio (recommended)

### R Packages

This tutorial only uses base R functions. No additional packages are required.

---

## Workflow

The script is organized into three main steps:

### 1. Load Data

```r
d = read.table("d5_control.txt", fill = TRUE)
# Loads FireSting raw data file.
# Adds padding where fields are missing using fill = TRUE.

```
### 2. Sort Data
```r
d = d[22:length(d$V3), 1:8] # Removes header rows and non-numeric entries.
names(d) = c('Date', 'Time', 'Seconds', 'Channel1', 'Channel2', 'Channel3', 'Channel4', 'Temperature')
# Renames columns for clarity.
# Prepares for numeric conversion and analysis.
```

### 3. Calculate Respiration Rates

```csv
Date,Duration(s),Channel1,Channel2,Channel3,Channel4,Mean temperature
2025-07-01,3600,0.42,0.38,0.47,0.41,25.3
```

### Notes
	•	This script assumes the FireSting output format is consistent across files.
	•	You may need to adjust the subset() timing depending on your experimental design.
	•	Temperature is averaged across the full dataset, assuming ambient stability during incubation.












