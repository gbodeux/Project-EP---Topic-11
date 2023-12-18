# Project-EP---Topic-11
import rasterio
from rasterio.plot import show
import matplotlib.pyplot as plt
import numpy as np

# Load raster datasets (replace these with your actual file paths)
slope_filepath = '/content/Topic 11/Slope.tif'
stream_filepath = '/content/Topic 11/Stream_Network.tif'
NH_filepath = '/content/Topic 11/NH_EVENT_DETECTION.tif'

slope = rasterio.open(slope_filepath)
stream = rasterio.open(stream_filepath)
NH = rasterio.open(NH_filepath)

# Visualize slope and stream network
from rasterio.plot import show
show(slope)
show(stream)
show(NH) 
