# Leaf Image Analysis: Feature Extraction & Statistical Analysis

I used R to analyze 375 leaf images from five tree species in the Swedish Leaf dataset. I wrote code to separate each leaf from its background, extract 15 numerical features describing its shape and color, and compare those measurements across species.

The goal was to understand which features are useful for describing differences between species and where those measurements fall short.

**[View the full analysis and visualizations](https://elijahchiu.github.io/leaf-image-analysis/reports/leaf_analysis.html)**

## Dataset

The project uses images from the Swedish Leaf Dataset, collected at Linköping University.

I selected five species, with 75 images per species:

- Ulmus carpinifolia
- Salix aurita
- Quercus
- Alnus incana
- Betula pubescens

Each image was processed individually, producing a dataset of 375 observations with 15 extracted numerical features.

**Source:** [Swedish Leaf Dataset](https://cvl.isy.liu.se/en/research/datasets/swedish-leaf/)

## How It Works

### 1. Image Processing

I used the `magick` and `EBImage` packages in R to process the original leaf images.

For each image, the code:

- Resizes the image to a standard size.
- Converts it to grayscale.
- Applies a threshold to separate the leaf from the light background.
- Uses connected-component labeling to identify the leaf.
- Keeps the largest connected component and fills holes to create a binary leaf mask.

This mask is used to calculate the shape measurements.

### 2. Feature Extraction

I extracted 15 numerical features from each image.

**Shape features (9):**

- Area
- Perimeter
- Length
- Width
- Aspect ratio
- Circularity
- Compactness
- Convex hull area
- Solidity

Length and width are calculated using principal component analysis (PCA) to find the main axes of the leaf.

**Color features (6):**

- Mean hue
- Mean saturation
- Mean brightness
- Standard deviation of hue
- Standard deviation of saturation
- Standard deviation of brightness

The color measurements are calculated using pixels inside the leaf mask, excluding the background.

The extracted features are saved in `data/leaf_features.csv`.

### 3. Exploratory Data Analysis

After processing the images, I compared the features across the five species using R and `ggplot2`.

The analysis includes:

- Summary statistics for each species
- Histograms of leaf area and circularity
- Empirical cumulative distribution functions (ECDFs) for perimeter
- Boxplots comparing aspect ratio and solidity
- Scatterplots comparing shape and color measurements

These comparisons helped identify which features varied most across species and where their distributions overlapped.

## Key Findings

Several patterns stood out in the analysis:

- **Leaf area:** Ulmus carpinifolia had the largest average area in pixels, while Alnus incana had the smallest.
- **Aspect ratio:** Salix aurita had the highest average aspect ratio (2.046), indicating a more elongated shape. Betula pubescens had the lowest (1.445).
- **Solidity:** Ulmus carpinifolia had the highest average solidity (0.940), while Alnus incana had the lowest (0.788).
- **Color:** Quercus had the highest average saturation (0.919), showing differences in color characteristics across species.

No single feature clearly separated all five species.

Area and perimeter provided information about size, while aspect ratio, circularity, and solidity described different aspects of leaf shape. HSV color measurements added information that could not be captured from the outlines alone.

### Limitations

The analysis also has several limitations:

- The fixed grayscale threshold works well for these scanned images but may not work as reliably with different backgrounds or lighting.
- Leaf stems and image orientation can affect shape measurements.
- Pixel-based area measurements do not represent actual physical leaf size.
- Color measurements can be influenced by scanning conditions.
- Some species have overlapping feature distributions, so individual measurements are not enough to reliably identify every species.

This project focuses on feature extraction and exploratory analysis rather than building a species classification model.

## Technologies Used

**Language:** R

**Libraries:**
- `tidyverse` — Data manipulation and analysis
- `magick` — Image reading and resizing
- `EBImage` — Image segmentation and mask processing
- `ggplot2` — Data visualization
- `rmarkdown` and `knitr` — Report generation

**Methods:** Image segmentation, feature engineering, PCA-based axis measurement, descriptive statistics, and exploratory data analysis.

## Repository Structure

```text
leaf-image-analysis/
├── README.md
├── .gitignore
├── leaf_analysis.Rmd
├── data/
│   └── leaf_features.csv
└── reports/
    └── leaf_analysis.html
```

- `leaf_analysis.Rmd` contains the image-processing code, feature extraction, analysis, and visualizations.
- `data/leaf_features.csv` contains the extracted features for all 375 images.
- `reports/leaf_analysis.html` contains the generated analysis report.

The original images are excluded from the repository. They can be downloaded separately from the Swedish Leaf Dataset.

## Running the Analysis

### Requirements

- R
- Pandoc (required for R Markdown)
- The R packages listed below

Install the required packages in R:

```r
install.packages(c("tidyverse", "magick", "rmarkdown", "knitr"))

if (!requireNamespace("BiocManager", quietly = TRUE)) {
  install.packages("BiocManager")
}

BiocManager::install("EBImage")
```

### Download the Images

Download the original images from the [Swedish Leaf Dataset](https://cvl.isy.liu.se/en/research/datasets/swedish-leaf/).

Place the following five folders inside `data/raw/`:

```text
data/
├── leaf_features.csv
└── raw/
    ├── leaf1/
    ├── leaf3/
    ├── leaf4/
    ├── leaf5/
    └── leaf6/
```

Each species folder should contain 75 `.tif` images.

### Generate the Report

From the project's root directory, run:

```bash
Rscript -e 'rmarkdown::render("leaf_analysis.Rmd", output_format = "html_document", output_dir = "reports")'
```

The script processes the 375 images, extracts their features, saves the results to `data/leaf_features.csv`, and generates `reports/leaf_analysis.html`.

Processing the images may take several minutes.

The processed CSV is also included in the repository, so the extracted measurements can be inspected without rerunning the image-processing pipeline.

## Acknowledgments

I originally completed this analysis for STA 160 at the University of California, Davis, and later organized the code and report for my GitHub portfolio.

The original dataset comes from Linköping University.

**Reference:**

Söderkvist, O. (2001). *Computer Vision Classification of Leaves from Swedish Trees*. Linköping University.