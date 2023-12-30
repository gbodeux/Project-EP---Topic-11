# Project-EP-Topic-11
# Part 1: Task 1-5
#import the necessary packages
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
        rainfall_data[i, :, :] = resampled_data
    
    
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

# --------------------------
# Task 8: Create function that calculates rainfall stats for whole area: X-day average/median/max/sum 
# should be able to intake different X values, different starting dates
# user should be able to make a decision if the statistic is calculated over the rainfall data,
# prior/post this starting date -> If it is prior, the starting date should be included in the calculation

def rainfall_statistics(rainfall_data, x_days, date_index, before):

    # Extract relevant portion of the data based on date index 
    if before:
        data = rainfall_data[date_index - x_days + 1 : date_index + 1,:,:] # + 1 to include date_index in the calculation of x_days
    else:
        data = rainfall_data[date_index + 1 : date_index + 1 + x_days,:,:] # + 1 to remove date_index from the calculation

    # Calculate rainfall statistics on the x_day period 
    x_day_average = np.mean(data)
    x_day_median = np.median(data)
    x_day_max = np.max(data)
    x_day_sum = np.sum(data)

    # Store results into a dictionnary 
    x_days_stats = {'mean': x_day_average, 'median': x_day_median, 'max': x_day_max, 'sum': x_day_sum}
    return x_days_stats

# ------------------
# Task 9:  The event occurred on 17-04-2020 (index = 4, 5th day)
# Calculate the statistics for X = 1,2 and 3 prior and post event /!\ create a for loop to avoid redundance

X1_prior = rainfall_statistics(rainfall_data, 1, 4, True)
X2_prior = rainfall_statistics(rainfall_data, 2, 4, True)
X3_prior = rainfall_statistics(rainfall_data, 3, 4, True)

X1_post = rainfall_statistics(rainfall_data, 1, 4, False)
X2_post = rainfall_statistics(rainfall_data, 2, 4, False)
X3_post = rainfall_statistics(rainfall_data, 3, 4, False)

# Export all the results in one .csv file /!\ error: csv file (in user > isamo > results_all) shows same results for X1,2,3!

final_results = pd.DataFrame([X1_prior, X2_prior, X3_prior, X1_post, X2_post, X3_post])
csv_file_path = 'final_results.csv'
final_results.to_csv(csv_file_path, index=False)

