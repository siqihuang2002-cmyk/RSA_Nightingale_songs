# RSA_Nightingale_songs

Analysis of rhythmic patterns from Thrush Nightingale song interval data using cycle duration, rhythm ratios, and multiple clustering methods (DBSCAN, HDBSCAN, GMM), with cluster transition visualization in R.

## Table of Contents
- [Installation](#installation)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)

## Installation

Install the required R packages:

```r
install.packages("dbscan")
install.packages("ggplot2")
install.packages("tidyverse")
install.packages("dplyr")
install.packages("R.matlab")
install.packages("ggtern")
install.packages("mclust")
```

## Usage

### Input Data

The script expects a `.mat` file containing song interval data (in milliseconds):

```r
da <- readMat("~/Downloads/IntervalData_ThrushNightingale_3corpora.mat")
song_intervals <- da$ThrushNightingaleSongs.intervals.ms
```

To use a different file or variable name, update:

```r
da <- readMat("your/path/to/file.mat")
song_intervals <- da$YourVariableName
```

### Key Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| BPM | `68` | Change in `sixteen = 60/68/4` to match your reference tempo |
| `eps` (DBSCAN) | `0.05` | Neighbourhood radius for DBSCAN clustering |
| `minPts` (DBSCAN) | `25` | Minimum cluster size for DBSCAN |
| `minPts` (HDBSCAN) | `50` | Minimum cluster size for HDBSCAN |
| `G` (GMM) | `1:6` | Range of mixture components to evaluate |
| `rr` sensitivity multiplier | `2.5` / `4.0` | Horizontal axis weight for DBSCAN / HDBSCAN respectively |
| `count >= 100` | `100` | Transition frequency threshold for arrow display |

### Clustering Methods

The script provides three interchangeable clustering approaches. 
```r
# Option 1 — DBSCAN
cl <- dbscan(data_for_cl, eps = 0.05, minPts = 25)

# Option 2 — HDBSCAN
cl <- hdbscan(data_for_cl, minPts = 50)

# Option 3 — Gaussian Mixture Models (GMM)
fit <- Mclust(data_for_cl, G = 1:6, modelNames = "VVV")
```

Make sure to also switch the corresponding `final` data frame construction to match your chosen method:

```r
# For DBSCAN / HDBSCAN
final <- data.frame(rr, cd = data$cd, cluster = cl$cluster)

# For GMM
final <- data.frame(rr, cd = data$cd, cluster = fit$classification)
```

### Output

Running the script produces one plot:

**`p1` — 2D Pattern-Duration Plot**
- x-axis: rhythm ratio (`rr`), ranging from 0 to 1
- y-axis: cycle duration in 16th note units, ranging from 0 to 1500
- Coloured points: cluster-assigned song intervals (cluster 0 / noise shown in grey)
- Black arrows: high-frequency consecutive cluster transitions (filtered by `count >= 100`)
- Arrow width: scaled to transition frequency

<img width="513" height="450" alt="fig rsa_night_dbscan" src="https://github.com/user-attachments/assets/e978789a-1823-4c8b-a4ea-e416b2da3501" />
<img width="513" height="450" alt="fig rsa_night_hdbscan(4)" src="https://github.com/user-attachments/assets/7eab80dd-6e56-435c-9756-a6d2b8c9ea89" />
<img width="513" height="450" alt="fig rsa_night_gmm" src="https://github.com/user-attachments/assets/8ca1fc3a-3559-4a95-b00b-00ebc1fab1f1" />


