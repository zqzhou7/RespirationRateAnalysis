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
This section processes the cleaned data to estimate oxygen consumption (respiration) across a user-defined measurement window.

Step-by-step explanation:

#### a. Convert relevant columns to numeric format:
```r
d$Seconds     = as.numeric(d$Seconds)
d$Channel1    = as.numeric(d$Channel1)
d$Channel2    = as.numeric(d$Channel2)
d$Channel3    = as.numeric(d$Channel3)
d$Channel4    = as.numeric(d$Channel4)
d$Temperature = as.numeric(d$Temperature)
```

#### b. Define the measurement window:

In this example, respiration is calculated between 15 and 75 minutes of incubation (i.e., 900 to 4500 seconds).
```r
ctrl_begin = subset(d, Seconds >= 900 & Seconds < 901)    # Start time
ctrl_end   = subset(d, Seconds >= 4500 & Seconds < 4501)  # End time
```

#### c. Calculate oxygen depletion:

We compute the difference in O₂ levels between the start and end points for each channel.
```r
ctrl_orig = data.frame(rbind(ctrl_begin, ctrl_end, NA), row.names = c("begin", "end", "dm"))
ctrl_dift = ctrl_orig["begin", 4:7] - ctrl_orig["end", 4:7]
ctrl_orig[3, 4:7] = ctrl_dift
ctrl_orig[3, 1]   = ctrl_orig[1, 1]  # Date
ctrl_orig[3, 3]   = ctrl_orig["end", 3] - ctrl_orig["begin", 3]  # Duration (in seconds)
ctrl_orig[3, 8]   = mean(d$Temperature[1:900])  # Mean starting temperature
```

#### d. Extract and save the output:

Format the results into a clean summary table and export to a CSV file.
```r
respirationRate = ctrl_orig[3, -2]
names(respirationRate) = c("Date", "Duration(s)", "Channel1", "Channel2", "Channel3", "Channel4", "Mean temperature")
write.csv(respirationRate, "respirationRate.csv", row.names = FALSE)
```

### Example Output (respirationRate.csv)
- Date: Timestamp of the experiment
- Duration(s): Total incubation time in seconds
- Channel1–4: Oxygen consumption (change in concentration over time)
- Mean temperature: Average temperature during the first 15 minutes


### Notes
- This script assumes the FireSting output format is consistent across files.
- You may need to adjust the subset() timing depending on your experimental design.
- Temperature is averaged across the full dataset, assuming ambient stability during incubation.
