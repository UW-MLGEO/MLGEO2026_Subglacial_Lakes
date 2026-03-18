# MLGEO2026_Subglacial_Lakes
Project group classifying spatial distribution of subglacial lakes

## Members

**ESS 569:** Ashley Howard

**ESS 469:** Cameron Boyd, Olivia Murdock, Sofia Suhinin

# Overview
The code in this repository implements a U-Net model to classify the presence of subglacial lakes in CryoSat-2 elevation data. You can find our code in the Notebooks folder. See below for instructions on downloading data and a summary of the code files.
# How to Run Our Code
## Dependancies
You can run this command


```
pip install torch numpy pandas xarray netCDF4 matplotlib scikit-learn rioxarray rasterio cartopy pyproj
```

in your terminal to download necessary dependancies.

## Downloading Data
### REMA 
The REMA dataset is available in full 2 meter resolution from the Polar Geospatial Center. The 100 meter gridded version we used for this project is available at this link: https://data.pgc.umn.edu/elev/dem/setsm/REMA/mosaic/v2.0/100m/
### CryoSat-2
The CryoSat-2 data was downloaded through an API, and detailed instructions can be found inside the Notebooks folder in the file 1.2_CryoSatDownload.ipynb. We used all available data between 2010 and 2020. You will need an Earth Online username and password to access this data.

### Wilson et al. (2025)
The validation dataset and existing lake locations are open access as part of the Wilson et al. (2025) Nature Communications publication:

Wilson, S.F., Hogg, A.E., Rigby, R. et al. Detection of 85 new active subglacial lakes in Antarctica from a decade of CryoSat-2 data. Nat Commun 16, 8311 (2025). https://doi.org/10.1038/s41467-025-63773-9

## Pre-processing Data
In order to transform the data to a useable input, we regridded the CryoSat-2 data onto the REMA grid and subtracted REMA from CryoSat-2. This gave us an elevation anomaly output indicative of change before or since the time of the REMA DEM assembly (mostly 2015). We then generated 50km x 50km square tiles for each timestep centered on each lake coordinate from the Wilson et al. (2025) dataset and saved them as netCDF files. We also generated a null dataset that we know contains no subglacial lake activity. Boolean masks of the same shape were created from the Wilson et al. (2025) geojson files. We split our data into 70% training, 15% validation, and 15% test.

## Model Training
The model received the mean and standard deviation of each lake tile and the boolean lake mask for training. We found that 15 epochs worked best for our dataset. More detailed information about our model training can be found in the Notebooks folder. The second iteration of our model incorporated transformations to the lake tiles and masks to decenter the lakes and augment the dataset to prevent the model from learning to put lakes in the center every time.

# Descriptions of Each Notebook
+ 1.1 - Download and preliminary visualization of the ATL-11 dataset for IceSat-2. We ended up using CryoSat-2 data due to the overlap with our validation dataset, so this notebook can be safely ignored.
+ 1.2 - CryoSat-2 download and preliminary visualization
+ 2.1 - Statistics and other data characterization for IceSat-2 and CryoSat-2
+ 2.2 - Data visualization for the Wilson et al. (2025) dataset
+ 3.1 - Loads in REMA data and subtracts it from CryoSat-2. Shows the process we used to design the first iteration of our elevation anomaly tile.
+ 3.2 - Puts the process from 3.1 into a loop that generates tiles and gifs for all the lakes
+ 4.1 - Identifies null locations without lakes and visualizes the spatial distribution of the dataset
+ 5.1 - Splits the data into training, validation, and test sets and generates the boolean lake masks
+ 6.2 - Contains the first functioning iteration of our U-Net CNN
+ 6.3 - Implements data augmentation and new loss functions
