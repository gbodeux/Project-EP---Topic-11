# Project-EP-Topic-11
# Part 1: Task 1-5

import numpy as np
import matplotlib.pyplot as plt
import rasterio

# -------------------------------------------------------------------------------
# Task 1: Open the natural hazard, slope, and stream network maps as numpy arrays
natural_hazard_path = '/content/NH_EVENT_DETECTION.tif'
slope_path = '/content/Slope.tif'
stream_network_path = '/content/Stream_Network.tif'

# Function to open a GeoTIFF file and return its data as a numpy array
def open_geotiff(file_path):
    with rasterio.open(file_path) as src:
        return src.read(1)  # Assuming a single band GeoTIFF

# Open the maps as numpy arrays
natural_hazard_map = open_geotiff(natural_hazard_path)
slope_map = open_geotiff(slope_map_path)
stream_network_map = open_geotiff(stream_network_path)

# Print the numpy array results
print("Natural Hazard Array:")
print(natural_hazard_map)
print("\nSlope Array:")
print(slope_map)
print("\nStream Network Array:")
print(stream_network_map)

# ------------------------------------------------------------------------------------
# Task 2: Visualize the slope and stream network map and export them as 600 dpi images
def visualize_and_export(image, output_path):
    plt.figure(figsize=(10, 10))
    plt.imshow(image, cmap='viridis')
    plt.colorbar()
    plt.axis('off')
    plt.savefig(output_path, dpi=600, bbox_inches='tight')
    plt.show()

visualize_and_export(slope, 'slope_map.png')
visualize_and_export(stream_network, 'stream_network_map.png')


# ------------------------------------------------------------------------------------------------------------------
# Task 3: Create a function that ingests the NH map and makes a binary map (0/1) for non-affected and affected areas
# Create a binary numpy array with 0/1 values 
def binary_map(natural_hazard_map, threshold):
    binary_map = np.where(natural_hazard_map >= threshold, 1, 0)
    return binary_map
user_threshold = 0.5  # Replace with the user's chosen threshold
binary_map_result = binary_map(natural_hazard_map, user_threshold)
print(binary_map_result)

# Create binary map 
plt.imshow(binary_map_result, cmap='binary', interpolation='nearest')
plt.title('Binary Map')
plt.xlabel('Pixel number X-direction')
plt.ylabel('Pixel number Y-direction')
plt.show()


# --------------------------------------------------------
# Task 4: Discriminate landslide and flash flood locations
def discriminate_locations(nh_map, slope_map, stream_network_map, landslide_threshold, flood_threshold):
    landslide_area = create_binary_map(slope_map, landslide_threshold)
    flood_area = create_binary_map(stream_network_map, flood_threshold)


    nh_binary_map = create_binary_map(nh_map, 0.5)  # Choose a threshold for natural hazard map


    landslide_locations = np.logical_and(nh_binary_map, landslide_area)
    flood_locations = np.logical_and(nh_binary_map, flood_area)


    return landslide_locations, flood_locations


# ---------------------------------------------------------------------
# Task 5: Create and export maps showing only landslide and flood areas
landslide_map, flood_map = discriminate_locations(natural_hazard, slope, stream_network, 0.2, 4)

visualize_and_export(landslide_map, 'landslide_area_map.png')
visualize_and_export(flood_map, 'flash_flood_area_map.png')


# Part 2: Task 6-9

import os
from rasterio.plot import show
from rasterio.windows import Window
from rasterio.enums import Resampling
import matplotlib.pyplot as plt

# ----------------------------------------- 
# Task 6: Read time series of rainfall maps
rainfall_folder = '/content/Rainfall/'
 
# List all rainfall files in the folder
rainfall_files = [f for f in os.listdir(rainfall_folder) if f.endswith('.tif')]
rainfall_files.sort()  # Sort files to ensure correct temporal order
 
# Read the first rainfall map to get metadata
with rasterio.open(os.path.join(rainfall_folder, rainfall_files[0])) as src:
    meta = src.meta
    rain_data_shape = src.shape

# Initialize an array to store all rainfall maps
rainfall_data = np.zeros((len(rainfall_files), rain_data_shape[0], rain_data_shape[1]), dtype=np.float32)
 
# Read all rainfall maps into the array
for i, rainfall_file in enumerate(rainfall_files):
    with rasterio.open(os.path.join(rainfall_folder, rainfall_file)) as src:
        resampled_data = src.read(out_shape=(rain_data_shape[0], rain_data_shape[1]), resampling=Resampling.bilinear)
        rainfall_data[i, :, :] = resampled_data # /!\ ERROR name 'rainfall_data' is not defined
    
    
# -----------------------------------------------------------------
# Task 7: Plot the spatially averaged daily rainfall as time series
# Calculate spatially averaged daily rainfall
spatially_averaged_rainfall = np.mean(rainfall_data, axis=(1, 2))

# Plot time series
plt.figure(figsize=(12, 6))
plt.plot(spatially_averaged_rainfall, marker='o', linestyle='-', color='b')
plt.title('Spatially Averaged Daily Rainfall Time Series')
plt.xlabel('Day')
plt.ylabel('Rainfall (mm)')
plt.grid(True)
plt.show()

