# Pathloom Data Science/GIS Technical Questions

Applicant Name: Summerlyn Turner            Date Received: 11/07/2023

1. Please send a piece of code (DATA Processing or Backend Related) in which you are proud of and willing to stamp your name to. Then briefly describe your code in a short paragraph.

  Solution is located HERE (Pathloom-GIS-Technical-Assessment/Data-Processing-Example.md)


## 2. Answer the following questions:

Create a Mapbox tileset that consists of live data obtained from NOAA. This data will require you to pull the images from an API in order to get what you need to create the tileset.

Example: https://digital.weather.gov/?zoom=6&lat=45.9413&lon=-112.04565&layers=00BTFFTT&region=0&element=0&mxmz=false 

For this project we want to show the Max Temperature Graphical Forecast in deg F.

NOAA gives directions on how to pull this data here: https://digital.weather.gov/staticpages/mapservices.php
To retrieve the data I recommend using python as it is a very easy language to learn.

Once the data has been collected you should create a tile set called “Max Temperature” and the date for the temperature shown should be for the date you completed this project.

Also ensure that when you are zoomed out at the US level only a few temperatures are shown and as the user zooms in more temperatures begin to appear over cities in the area. 

Your final result should look similar to this with numbers showing the temperature throughout the us.



 Again once you have completed the tile, please share your map and code required to gather data so we can review.

Using the NPS website (https://public-nps.opendata.arcgis.com/)  find the trails data for Rocky Mountain National Park. 

The tile should be named “Rocky Mountain National Park - Trails,” download the shapefile.

Make a duplicate copy of the file and combine both the files into one dataframe. Using Python, find duplicate geometries in your dataset. Share the code and output.

Using ArcGIS, QGIS, or Python GDAL, derive the following properties for all records in your dataset: elevation profile (x-axis should be distance, y-axis should be elevation), min elevation, max elevation, and total elevation gain. Export it as a shapefile and csv, and provide a screenshot of your output dataframe and an example of your elevation profile plot. Also, list what DEM and Coordinate Systems you used in your analysis (share links where available).





