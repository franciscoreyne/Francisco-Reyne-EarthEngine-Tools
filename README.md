# Francisco-Reyne Earth Engine Tools
Earth Engine Tools for Deeplearning and Generative AI Image Analysis 

### Explanation of Steps:
1. **Define the Area of Interest (AOI)**: You can define the AOI as a polygon (either by manually specifying coordinates or by uploading a shapefile/geojson).
2. **Select the Time Range**: The user can assign the time range (e.g., 1 year).
3. **Filter Sentinel-2 Images**: The code filters images for the specified period based on date and image quality (low cloud cover).
4. **Calculate Average RGB Colors**: For each time period, the average of the RGB bands (Red, Green, Blue) is calculated.
5. **Compare Average RGBs for Multiple Time Periods**: The code allows comparing the average RGB for different time periods.

Here’s the code:

```javascript
// Define the Area of Interest (AOI) (You can modify the polygon to your specific territory)
var aoi = ee.Geometry.Polygon([
  [[-75.5, 5.5], [-75.5, 5.0], [-75.0, 5.0], [-75.0, 5.5]]
]);

// Function to get Sentinel-2 images for a given date range
function getAverageImage(startDate, endDate, aoi) {
  var collection = ee.ImageCollection('COPERNICUS/S2')
    .filterBounds(aoi) // Filter by Area of Interest
    .filterDate(startDate, endDate) // Filter by date range
    .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE', 20)) // Filter images with less than 20% cloud cover
    .select(['B4', 'B3', 'B2']); // Select RGB bands (Red, Green, Blue)

  // Calculate the average image
  var averageImage = collection.mean();

  // Visualize the average image
  Map.addLayer(averageImage, {
    min: 0,
    max: 3000,
    bands: ['B4', 'B3', 'B2'],
    gamma: 1.4
  }, 'Average RGB ' + startDate + ' to ' + endDate);

  return averageImage;
}

// User-defined input parameters
var startDate1 = '2022-01-01';
var endDate1 = '2022-12-31';
var startDate2 = '2023-01-01';
var endDate2 = '2023-12-31';

// Get average images for different periods
var averageImage1 = getAverageImage(startDate1, endDate1, aoi);
var averageImage2 = getAverageImage(startDate2, endDate2, aoi);

// Compare the average images (you can do visual comparisons, statistics, etc.)
var diffImage = averageImage2.subtract(averageImage1);

// Visualize the difference between the two average images
Map.addLayer(diffImage, {
  min: -1000,
  max: 1000,
  palette: ['blue', 'white', 'red']
}, 'Difference between periods');

// Set map view
Map.centerObject(aoi, 10);
```

### Explanation of Each Part:
1. **Area of Interest (`aoi`)**: This defines your area of interest (AOI). You can either specify the coordinates for the AOI manually or load a shapefile/geojson.
2. **Function `getAverageImage`**: This function filters Sentinel-2 images by the AOI, date range, and image quality (cloud cover less than 20%). Then it calculates the mean RGB values over that period.
3. **Date Parameters**: The variables `startDate1`, `endDate1`, `startDate2`, and `endDate2` define the two periods. You can change these values based on the period the user wants to analyze.
4. **Visualization**: The `Map.addLayer` function adds the average images to the map for each period. The difference between the two average images is also visualized using a color palette that goes from blue (negative difference) to red (positive difference).
5. **Comparison of Images**: The code calculates the difference between the average images of the two periods and visualizes the result.

### How to Customize:
1. **Modify the AOI**: You can change the coordinates of the polygon or upload a geojson to define your AOI.
2. **Change the Dates**: Modify `startDate1`, `endDate1`, `startDate2`, and `endDate2` to reflect the periods of interest.
3. **Adjust Cloud Percentage**: The filter `filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE', 20))` limits the image collection to those with less than 20% cloud cover. You can adjust this value based on your needs.
4. **Add Additional Analysis**: If you want to conduct statistical analysis (e.g., calculating the mean or variance of differences between periods), you can add extra functions to process that data.

### Visualization:
The `Map.addLayer` function is used to visualize the average images on the Google Earth Engine map. Make sure you have access to a Google Earth Engine account to view the generated layers.

----------------------------------------------------
