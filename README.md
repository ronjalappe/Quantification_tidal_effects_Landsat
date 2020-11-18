# Quantification of tidal effects on Landsat imagery

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
![Status: in_progress](https://img.shields.io/badge/Status-in_progress-red.svg)

This project aims on calculating the impact of tidal range on shoreline detection results when working with Landsat imagery at 30 meters spatial resolution. A fully automatic approach using the Google Earth Engine Python API has been chosen to guarantee transferability to other study areas. For exploring coastline dynamics it is import to know about the effects of tidal variantions on the detection resuluts and to evetually correct for them, especially when working with time series.
The here presented tool only requires the area of interest (which can be selected interactively), an hourly sea level data csv, the sea level station coordinates and user paths as input. Sea level data can be obtained from the [UHSLC Legacy Data Portal](http://uhslc.soest.hawaii.edu/data/?rq#uh381http://uhslc.soest.hawaii.edu/data/csv/rqds/pacific/hourly/h381a.csv).

The area around Da Nang in Central Vietnam has been chosen as study area. Central Vietnam belongs to a mesotidal environment.

**Workflow**
1. Identification of low and high tide dates at Landsat aquisition time within the selected AOI
    - Cleaning of sea level data, extraction of valid years, calculation of Landsat overpass time for each year, filtering of sea level data by Landsat aqusition hour, identification of low and high tide peaks (+/1 1 day), creation of low and high tide date lists for each available year) 
    - ![Sea level data at Landsat aquisition time](https://github.com/ronjalappe/Quantification_tidal_effects_Landsat/images/)
    - Identification of Landsat images at low and hight tides 
    - Shoreline detection on each of the identified images
    - creation 
    - ***Output: low and high tide date lists***

2. Selection of nearly cloud-free images at low and high tides
    - Filtering [Landsat SR archive](https://developers.google.com/earth-engine/datasets/catalog/landsat) by 3-day date ranges
    - Calcualtion of cloud cover within AOI 
    - Filtering of selected images by cloud cover (< 20%)
    - Cloud-masking on selected images (using sr_cloud_qa band, for LS8 pixel_qa band)
    - Creation of daily mosaics 
    - Removal of mosaics covering < 2/3 of the AOI
    - Creation of low and high tide image pairs (each high tide images receives the closest available low tide image or vice versa)
    - ***Output: list of image pairs***
    - ![Image pair](https://github.com/ronjalappe/Quantification_tidal_effects_Landsat/images/)

3. Shoreline detection for each image pair
    - Calculation of the modified Difference Normalized Water Index (MNDWI) 
    - Binarization using Otsu's threshold [1]
    - Download rasters on local maschine 
    - Contour extraction using the [skimage.measure.find_contours](https://scikit-image.org/docs/0.8.0/api/skimage.measure.find_contours.html) Python function 
    - Clipping of contours to buffered OSM shoreline
    - ***Output: shorelines geojson for each image pair***
    - ![Shoreline detection workflow](https://github.com/ronjalappe/Quantification_tidal_effects_Landsat/images/)


4. Calculation of shoreline displacement between low and high tide 
    - Creation of transects perpendicular to OSM shoreline (100 m spacing) with landwards transect origin
    - Calculation of shoreline/ transect intersections
    - Measuring distance of each intersection to transect origin (difference between distance of high tide intersection and distance low tide intersection equals landwards shoreline displacement from high to low tide) 
    - ***Output: transects geojson with shoreline displacement information***
    - ![Image with colored transects](https://github.com/ronjalappe/Quantification_tidal_effects_Landsat/images/)

5. Statistics
    - Calculation of median shoreline displacement and standard deviation for each image pairs
    - Calculation of overall median shoreline displacement and standard deviation for all image pairs
    - ***Output: bar plot with error bars***
    - ![bar plot](https://github.com/ronjalappe/Quantification_tidal_effects_Landsat/images/)


How users can get started with the project 

Where users can get help with your project 

who maintains and contributes to the project 


**References**
[1] Nobuyuki Otsu. A Threshold Selection Method from Gray-Level Histograms. IEEE Transactions on Systems, Man, and Cybernetics, 9(1):62–66, January 1979.
