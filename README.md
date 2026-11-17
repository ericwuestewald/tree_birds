# Declining Bird Populations and Forest Loss in the American South

*Capstone project for the YSE Environmental Data Science Certificate.*

*Author: [Eric Wuestewald] Last Updated: [Nov 2026]*

## **Overview**
North America has lost approximately one-third of its total bird population (nearly 3 billion birds) since the 1970s. Though there are numerous reasons for the decline of these populations, one of the largest is habitat destruction and fragmentation caused by agriculture, urban development, logging, and forest management. From 2000 to 2025, the American South experienced a significant population and development boom, adding over 7.5 million people at a 6% growth rate (nearly double the national average). During that time, Southern states were responsible for 70% of permanent deforestation across America.

This project seeks to better understand the potential connection between habitat loss and breeding bird populations, so that developing population centers can make more informed decisions about how and where to develop while protecting local wildlife and managing conservation goals.

## **Research Questions**
Specifically, this project seeks to investigate the following questions:

1) How closely are forest habitat loss and loss of breeding bird populations correlated? 
2) Which birds are most affected by forest habitat loss?
3) Is temporary forest loss also correlated to declines in bird populations?
4) Which locations are most negatively affected by forest habitat loss and loss of breeding bird populations?

## **Data Sets**
To answer these questions, this research heavily utilizes the [United States Geological Survey's Breeding Bird Survey](https://www.usgs.gov/data/north-american-breeding-bird-survey-analysis-results-1966-2025), which has tracked over 420 bird populations along 4,100 survey routes across North America from 1966 to 2025. Each survey route is conducted in June by participants skilled in avian identification along a 24.5 miles long route with stops half a mile apart. At each step, participants count every bird they can hear or see with a .25 mile radius. Once analyzed, USGS provides raw data and an index of population abundance that can be used to estimate population trends and relative abundance at various geographic scales. These are yearly indexes of abundance estimated using a hierarchical model analysis for regions corresponding to the long-term ratio trend estimates, representing the expected average birds seen on a route in that region by an average BBS observer.

This research also uses [Global Nature Watch's annual tree cover loss](https://www.globalforestwatch.org/dashboards/country/USA/) and [drivers of tree cover loss](https://datasets.wri.org/datasets/dominant-drivers-of-tree-cover-loss-at-1km_) (2001-2025) datasets. This data from University of Maryland's GLAD lab captures any disturbances to tree cover (defined as woody vegetation at least five meters tall) at 30-meter resolution. Information from Global Nature Watch is then divided by human and natural causes and permanent or temporary causes. 

This project also plans to use GIS and eBird data to better understand and illustrate changing forest makeup and shifting trends in bird populations.

## Repository structure
```
tree_birds/
├── README.md ← you are here
├── raw_data/ ← original data, never edited
│ ├── bbs_regions.csv
│ └── bird_families.csv
│ └── USA.xlsx
│ └── bbs_data
│ │ └── 2026_dataset
│ │ │ └── SpeciesList.csv
│ │ │ └── 2026_analysis
│ │ │ │ └── BBS_Core_Indices_1996-2025.csv
├── clean_data/ ← output of 1_bbs_data_cleaning.ipynb and 2_tc_loss_data_cleaning.ipynb
│ └── amended_specieslist.csv
│ └── north_american_bird_abundance_1966-2025_clean.csv
│ └── tree_loss_data_2001-2025.csv
│ └── us_state_bird_abundance_by_forest_loss_2001-2025.csv
├── figures/ ← plots and tables (coming in later scripts)
└── scripts/ ← notebooks, numbered in the order they run
├── 0_project_setup.ipynb
└── 1_bbs_data_cleaning.ipynb
└── 2_tc_loss_data_cleaning.ipynb
└── 3_bbs_exploratory_analysis.ipynb
```

## How to run the code
All scripts are Google Colab notebooks written in R. Run them in number order:
1. `scripts/0_project_setup.ipynb` creates the project folder and subfolders in Google Drive.
2. `1_bbs_data_cleaning.ipynb` reads, joins, binds, preprocesses, and cleans raw files from the bbs_data folder, as well as bbs_regions.csv, bird_families.csv, and SpeciesList.csv, and writes amended_specieslist.csv and north_american_bird_abundance_1966-2025_clean.csv.
3. `2_tc_loss_data_cleaning.ipynb` reads, joins, binds, reshapes, preprocesses, and cleans information from Global Nature Watch's tree cover loss and drivers spreadsheets (USA.xlsx) and writes tree_loss_data_2001-2025.csv and us_state_bird_abundance_by_forest_loss_2001-2025.csv.
4. `3_bbs_exploratory_analysis.ipynb` loads in the .csvs from step 3. and begins processing them for sums, medians, correlations, and trends. This will soon be updated to build graphs and save figures to 'figures/'.
5. Each notebook must first mount to Google Drive through Python, then switches to R runtime. 

## Data cleaning steps
`1_bbs_data_cleaning`
1. **Created data frame and used `rbind` to amend SpeciesList.csv with missing AOU values** AOU values are numbers representing specific bird species. Some number values for common bird species occurred in the BBS_Core_Indices_1996-2025.csv, but did not match up to values in the original SpeciesList.csv.
2. **Dropped french_comnmon_name, renamed english_common_name to common_name** to remove bloat and match with other data sets.
3. **Created data frames reflecting common names for bird families and orders and combined them with species list using `left_join` by AOU** to ensure easy filtering by common names for hawks, woodpeckers, perching birds, and etc.
4. **Used `rename` on BBS_Core_Indices_1996-2025.csv columns to match with amended_specieslist.csv, common order, and bird_families.csv data sets, then used `left_join` by AOU** to combine the four data sets.
5. **Read in common_regions data set and combined it with `left_join` by region** to translate geographical regions, states, and country codes for easy filtering and contextual understanding.
6. **Used `relocate` on columns** to arrange them in a more visually understandable order.
7. **Removed duplicates**.
8. **Used `filter` to remove NA values** from relative_abundance (index) and common_name columns and create a clean data set.

`2_tc_loss_data_cleaning.ipynb`
1. **Pulled US and US state tree cover loss databases from Global Nature Watch excel spreadsheet, renamed columns to match, and combined them with `rbind`**.
2. **Filtered data by 30 percent threshold tree canopy density** to match driver data set and commonly used thresholds for measuring tree cover loss.
3. **Removed remaining threshold information, state size columns, and extent_2010_ha column**, the latter of which represented a different measure of tree density that doesn't match up with the remaining methods or measurements.
4. **Reshaped columns with `pivot_long`** to combine years into one column and yearly loss values into another.
5. **Pulled US and US state tree cover loss driver databases from Global Nature Watch excel spreadsheet, renamed columns to match, and combined them with `rbind`**.
6. **reshaped columns with `pivot_wide`** to separate six individual reasons for tree cover loss and their respective loss amounts by state and year.
7. **Used `mutate` to add two new columns combining temporary and permanent tree cover loss drivers by state by year**.
8. **Used `relocate` to move columns for easier visual grouping**.
9. **Used is.na to convert NAs in newly created columns into 0s** so unreported values don't disturb other reported data.
10. **Nulled unnecessary threshold and country columns of the tree cover loss driver data set**.
11. **Used `left_join` by location and year to combine tree cover loss data with tree cover loss driver data**.
12. **Renamed and standardized columns**.
13. **Used `group_by` and `cum_sum` to create cumulative columns for yearly tree cover loss, permanent tree cover loss, and temporary tree cover loss by common_region and year.** Now the data reflects the individual yearly loss for these categories as well as the cumulative total loss for multiple points of analysis.
14. **Used `left_join` by common_region and year to combine the tree cover loss data from step 13. with the final product from `1_bbs_data_cleaning`**.
15. **Removed duplicates**.
16. **Used `filter` to remove NA values** from yearly_loss_ha, perm_tc_loss_ha, and temp_tc_loss_ha and create clean .csv file.

## **Known data issues:**
-BBS indices are representative of whole integers but have been processed to average survey results across multiple routes, as well as adjustments for different types of bird behavior, expertise of the surveyor, and other expressions of uncertainty, making some numbers fractional and hard to immediately understand.
-Global Nature Watch's tree cover loss data is measured in hectares and must be significantly scaled down to match the small BBS indices.
-Global Nature Watch's breakdowns of permanent and temporary data do not perfectly add up to total yearly losses. That small gap comes from tree cover loss that their model did not classify. The model is only run on areas where loss makes up at least .5% of a 1 kilometer cell, as very small loss areas are hard to classify accurately. This leaves some small, scattered areas of loss that are not assigned to any driver category. That area is best classified as an 'unknown' driver.

## Status and next steps
- [x] Set up the project folder and GitHub repository
- [x] Clean and combine the raw data
- [ ] Exploratory plots (started, but needs more work to establish trends)
- [ ] Correlation plots of tree cover loss and bird indices
- [ ] Maps of common sites with shifting bird indices 
      
## AI Disclosure
I used Google's Gemini models to search names of functions and refine formatting. AI was also used occasionally to debug error messages.
