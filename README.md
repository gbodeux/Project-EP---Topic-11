# Project-EP---Topic-11
import rasterio
from rasterio.plot import show
import matplotlib.pyplot as plt
import numpy as np

# Load raster datasets (replace these with your actual file paths)
slope_filepath = '/content/Topic 11/Slope.tif'
stream_filepath = '/content/Topic 11/Stream_Network.tif'
NH_filepath = '/content/Topic 11/NH_EVENT_DETECTION.tif'

R_april_13_filepath = 'Downloads\EP\Topic11\DATA\Rainfall_images/2020-04-13_RAINFALL.tif'
R_april_14_filepath = 'Downloads\EP\Topic11\DATA\Rainfall_images/2020-04-14_RAINFALL.tif'
R_april_15_filepath = 'Downloads\EP\Topic11\DATA\Rainfall_images/2020-04-15_RAINFALL.tif'
R_april_16_filepath = 'Downloads\EP\Topic11\DATA\Rainfall_images/2020-04-16_RAINFALL.tif'
R_april_17_filepath = 'Downloads\EP\Topic11\DATA\Rainfall_images/2020-04-17_RAINFALL.tif'
R_april_18_filepath = 'Downloads\EP\Topic11\DATA\Rainfall_images/2020-04-18_RAINFALL.tif'
R_april_19_filepath = 'Downloads\EP\Topic11\DATA\Rainfall_images/2020-04-19_RAINFALL.tif'
R_april_20_filepath = 'Downloads\EP\Topic11\DATA\Rainfall_images/2020-04-20_RAINFALL.tif'
R_april_21_filepath = 'Downloads\EP\Topic11\DATA\Rainfall_images/2020-04-21_RAINFALL.tif'
R_april_22_filepath = 'Downloads\EP\Topic11\DATA\Rainfall_images/2020-04-22_RAINFALL.tif' 
R_april_23_filepath = 'Downloads\EP\Topic11\DATA\Rainfall_images/2020-04-23_RAINFALL.tif'
R_april_24_filepath = 'Downloads\EP\Topic11\DATA\Rainfall_images/2020-04-24_RAINFALL.tif'
R_april_25_filepath = 'Downloads\EP\Topic11\DATA\Rainfall_images/2020-04-25_RAINFALL.tif'

slope = rasterio.open(slope_filepath)
stream = rasterio.open(stream_filepath)
NH = rasterio.open(NH_filepath)

# Visualize slope and stream network
from rasterio.plot import show
show(slope)
show(stream)
show(NH) 
