# Quantification of tidal effects on Landsat imagery

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
![Status: work_in_progress](https://img.shields.io/badge/Status-work_in_progress-red.svg)

This project aims on calculating the impact of tidal range on shoreline detection results when working with Landsat imagery at 30 meters spatial resolution. A fully automatic approach using the Google Earth Engine Python API has been chosen to guarantee transferability to other study areas. For exploring coastline dynamics it is import to know about the effects of tidal variantions on the detection results and to eventually correct for them, especially when working with time series.
The presented tool only requires the area of interest (which can be selected interactively), an hourly sea level data csv, the sea level station coordinates and user paths as input. Sea level data can be obtained from the [UHSLC Legacy Data Portal](http://uhslc.soest.hawaii.edu/data/?rq#uh381http://uhslc.soest.hawaii.edu/data/csv/rqds/pacific/hourly/h381a.csv).

The area around Da Nang in Central Vietnam has been chosen as study area. Central Vietnam belongs to a mesotidal environment.

## Workflow
1. **Identification of low and high tide dates at Landsat acquisition time within the selected AOI**
    - Cleaning of sea level data/ extraction of valid years
    - Calculation of Landsat overpass time for each year
    - Filtering of sea level data by Landsat acquisition hour
    - Identification of low and high tide peaks (+/1 1 day)
    - creation of low and high tide date lists for each available year
    - ***Output: low and high tide date lists***
  <p align="center">
     <img src="https://github.com/ronjalappe/Quantification_tidal_effects_Landsat/blob/main/images/Sea_levels_qui-nong1_2000.png"/>
   </p>
   
2. **Selection of nearly cloud-free images at low and high tides**
    - Filtering [Landsat SR archive](https://developers.google.com/earth-engine/datasets/catalog/landsat) by 3-day date ranges
    - Calculation of cloud cover within AOI 
    - Filtering of selected images by cloud cover (< 20%)
    - Cloud-masking on selected images (using sr_cloud_qa band, for LS8 pixel_qa band)
    - Creation of daily mosaics 
    - Removal of mosaics covering < 2/3 of the AOI
    - Creation of low and high tide image pairs (each high tide images receives the closest available low tide image or vice versa)
    - ***Output: list of image pairs***
    ![Image pair](/images/workflow_ImagePairs.png)

3. **Shoreline detection for each image pair**
    - Calculation of the modified Difference Normalized Water Index (MNDWI) 
    - Binarization using Otsu's threshold [1]
    - Download rasters on local maschine 
    - Contour extraction using the [skimage.measure.find_contours](https://scikit-image.org/docs/0.8.0/api/skimage.measure.find_contours.html) Python function 
    - Clipping of contours to buffered OSM shoreline
    - ***Output: shorelines geojson for each image pair***
    ![Shoreline detection workflow](/images/worklow_ShorelineDetection.png)


4. **Calculation of shoreline displacement between low and high tide**
    - Creation of transects perpendicular to OSM shoreline (100 m spacing) with landwards transect origin
    - Calculation of shoreline/ transect intersections
    - Measuring distance of each intersection to transect origin (difference between distance of high tide intersection and distance low tide intersection equals landwards shoreline displacement from high to low tide) 
    - ***Output: transects geojson with shoreline displacement information***
   <p align="center">
     <img src="https://github.com/ronjalappe/Quantification_tidal_effects_Landsat/blob/main/images/qui-nong1_pair3.jpg" width="400"/>
   </p>


5. **Statistics**
    - Calculation of median shoreline displacement and standard deviation for each image pairs
    - Calculation of overall median shoreline displacement and standard deviation for all image pairs
    - ***Output: bar plot with error bars***
  <p align="center">
     <img src="https://github.com/ronjalappe/Quantification_tidal_effects_Landsat/blob/main/images/qui_nong_tidal_effects.png" width="800"/>
   </p>


## References
[1] Nobuyuki Otsu. A Threshold Selection Method from Gray-Level Histograms. IEEE Transactions on Systems, Man, and Cybernetics, 9(1):62–66, January 1979.
