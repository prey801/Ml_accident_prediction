# Ml_accident_prediction
Here's the README with the code included:

---

# Accident Heatmap Visualization

This project creates an interactive accident heatmap on Google Maps using Python, Folium, and Google Colab. It visualizes the distribution of accident locations on a Google Maps base layer, offering a geographical overview of accident densities and hotspots.

## Table of Contents
1. [Features](#features)
2. [Requirements](#requirements)
3. [Getting Started](#getting-started)
4. [Usage](#usage)
5. [Configuration](#configuration)
6. [Code](#code)
7. [Output](#output)
8. [Troubleshooting](#troubleshooting)
9. [License](#license)

## Features
- Interactive accident location heatmap over Google Maps
- Inline display of the heatmap in Google Colab for easy access and exploration
- Customizable map settings (radius, blur, zoom)
- Statistical summary of accident data by location

## Requirements
- **Python Libraries**:
  - pandas
  - folium
  - IPython.display (for Colab inline display)

- **Google Maps API Key**: Needed to display Google Maps tiles.

## Getting Started

### Step 1: Obtain a Google Maps API Key
1. Go to the [Google Cloud Console](https://console.cloud.google.com/).
2. Create a new project or select an existing one.
3. Enable the **Google Maps JavaScript API**.
4. Generate an API key and make note of it, as it will be needed in the code.

### Step 2: Set up the Google Colab Environment
1. Open Google Colab and create a new notebook.
2. Install any missing libraries if necessary (although Folium and IPython should be pre-installed in Colab).

### Step 3: Upload the Dataset
Upload your accident dataset (CSV file) to the Colab environment. The dataset should have columns for `Latitude`, `Longitude`, and optional accident details such as `Hour`, `Collision Type`, etc.

Example of dataset structure:
| Latitude | Longitude | Hour | Collision Type |
|----------|-----------|------|----------------|
| 42.12345 | -83.98765 | 14   | Rear-end      |
| 42.54321 | -83.12345 | 18   | Side-swipe    |

## Usage

### Run the Code
1. Copy the code from `main.py` into a Colab cell or clone the repository if available.
2. Replace `'YOUR_GOOGLE_MAPS_API_KEY'` with your actual Google Maps API key.
3. Set the `file_path` variable to point to your uploaded dataset.
4. Execute the cell to display the heatmap.

```python
import pandas as pd
import folium
from folium.plugins import HeatMap
from IPython.display import display, IFrame

def create_google_accident_heatmap(file_path, api_key):
    """
    Create an interactive Folium heatmap of accident locations using Google Maps tiles and display it inline in Colab.
    """
    try:
        # Read the data
        df = pd.read_csv(file_path, encoding='latin1')
        
        # Remove rows where latitude or longitude is 0 or null
        df = df[df['Latitude'] != 0]
        df = df[df['Longitude'] != 0]
        df = df.dropna(subset=['Latitude', 'Longitude'])
        
        print(f"Total accidents with valid coordinates: {len(df)}")
        
        # Calculate the center point for the map (mean of latitudes and longitudes)
        center_lat = df['Latitude'].mean()
        center_lon = df['Longitude'].mean()
        
        # Create base map centered at the average location, zoomed in to 12
        m = folium.Map(location=[center_lat, center_lon], zoom_start=12)
        
        # Add Google Maps tile layer (Roadmap)
        google_tile = f"https://mt1.google.com/vt/lyrs=m&x={{x}}&y={{y}}&z={{z}}&key={api_key}"
        folium.TileLayer(tiles=google_tile, attr="Google", name="Google Maps").add_to(m)
        
        # Prepare the location data for the heatmap (latitude, longitude pairs)
        locations = df[['Latitude', 'Longitude']].values.tolist()
        
        # Add the heatmap layer to the base map
        HeatMap(locations, radius=15, blur=10, max_zoom=13).add_to(m)
        
        # Save the map as an HTML file
        map_file = 'google_accident_heatmap_interactive.html'
        m.save(map_file)
        
        # Display the map inline in Colab
        display(IFrame(map_file, width=700, height=500))
        
    except Exception as e:
        print(f"Error creating heatmap: {str(e)}")
        raise

if __name__ == "__main__":
    try:
        # Replace with your Google Maps API key
        api_key = 'YOUR_GOOGLE_MAPS_API_KEY'
        
        # File path to the dataset
        file_path = '/content/monroe county car crash 2003-2015.csv'
        
        # Create heatmap and display it inline
        create_google_accident_heatmap(file_path, api_key)
        
    except Exception as e:
        print(f"Error in main execution: {str(e)}")
```

## Configuration

You can adjust the heatmap’s **radius**, **blur**, and **max zoom** parameters within the `HeatMap` function:
```python
HeatMap(locations, radius=15, blur=10, max_zoom=13).add_to(m)
```
- **radius**: Controls the size of each point in the heatmap (default: 15).
- **blur**: Controls the blurriness of each point (default: 10).
- **max_zoom**: Sets the maximum zoom level for the heatmap to appear.

## Output
The output is an interactive heatmap displayed inline in Colab, which shows accident hotspots based on location data in your dataset.

## Troubleshooting
- **Google Maps not displaying**: Ensure your API key is valid and you’ve enabled billing on your Google Cloud account if necessary.
- **Dataset Errors**: Ensure your dataset contains accurate and valid `Latitude` and `Longitude` columns.
- **Display Issues**: If the map doesn't display inline, try re-running the cell or clearing the Colab outputs and running it again.

## License
This project is licensed under the MIT License.

---

Let me know if you'd like any further adjustments!
