### Finding counts from a spatial join
```
from shapely.geometry import Point
 
# Create a shapely Point from lat and lng
permits['geometry'] = permits.apply(lambda x: Point((x.lng , x.lat)), axis = 1)
 
# Build a GeoDataFrame: permits_geo
permits_geo = gpd.GeoDataFrame(permits, crs = council_districts.crs, geometry = permits.geometry)
 
# Spatial join of permits_geo and council_districts
permits_by_district = gpd.sjoin(permits_geo, council_districts, op = 'within')
print(permits_by_district.head(2))
 
# Create permit_counts
permit_counts = permits_by_district.groupby(['district']).size()
print(permit_counts)
```
### Council district areas and permit counts
```
# Create an area column in council_districts
council_districts['area'] = council_districts.geometry.area
print(council_districts.head(2))
```
```
# Convert permit_counts to a DataFrame
permits_df = permit_counts.to_frame()
print(permits_df.head(2))
```
```
# Reset index and column names
permits_df.reset_index(inplace=True)
permits_df.columns = ['district', 'bldg_permits']
print(permits_df.head(2))
```
```
# Merge council_districts and permits_df: 
districts_and_permits = pd.merge(council_districts, permits_df, on = 'district')
print(districts_and_permits.head(2))
```
### Calculating a normalized metric
```
# Print the type of districts_and_permits
print(type(districts_and_permits))
 
# Create permit_density column in districts_and_permits
districts_and_permits['permit_density'] = districts_and_permits.apply(lambda row: row.bldg_permits / row.area, axis = 1)
 
# Print the head of districts_and_permits
print(districts_and_permits.head())
```
### Geopandas choropleths
```
# Import packages
import matplotlib.pyplot as plt
import pandas as pd
import geopandas as gpd
```
```
# Simple plot of building permit_density
districts_and_permits.plot(column = 'permit_density', legend = True);
plt.show();
```
```
# Polished choropleth of building permit_density
districts_and_permits.plot(column = 'permit_density', cmap = 'BuGn', edgecolor = 'black', legend = True)
plt.xlabel('longitude')
plt.ylabel('latitude')
plt.xticks(rotation = 'vertical')
plt.title('2017 Building Project Density by Council District')
plt.show();
```
### Area in km squared, geometry in decimal degrees
```
# Change council_districts crs to epsg 3857
council_districts = council_districts.to_crs(epsg = 3857)
print(council_districts.crs)
print(council_districts.head())
 
# Create area in square km
sqm_to_sqkm = 10**6
council_districts['area'] = council_districts.geometry.area / sqm_to_sqkm
 
# Change council_districts crs back to epsg 4326
council_districts = council_districts.to_crs(epsg = 4326)
print(council_districts.crs)
print(council_districts.head())
```
### Spatially joining and getting counts
```
# Create permits_geo
permits_geo = gpd.GeoDataFrame(permits, crs = council_districts.crs, geometry = permits.geometry)
 
# Spatially join permits_geo and council_districts
permits_by_district = gpd.sjoin(permits_geo, council_districts, op = 'within')
print(permits_by_district.head(2))
 
# Count permits in each district
permit_counts = permits_by_district.groupby('district').size()
 
# Convert permit_counts to a df with 2 columns: district and bldg_permits
counts_df = permit_counts.to_frame()
counts_df.reset_index(inplace=True)
counts_df.columns = ['district', 'bldg_permits']
print(counts_df.head(2))
```
### Building a polished Geopandas choropleth
```
# Merge permits_by_district and counts_df
districts_and_permits = pd.merge(permits_by_district, counts_df, on = 'district')
 
# Create permit_density column
districts_and_permits['permit_density'] = districts_and_permits.apply(lambda row: row.bldg_permits / row.area, axis = 1)
print(districts_and_permits.head(2))
 
# Create choropleth plot
districts_and_permits.plot(column = 'permit_density', cmap = 'OrRd', edgecolor = 'black', legend = True)
 
# Add axis labels and title
plt.xlabel('longitude')
plt.ylabel('latitude')
plt.title('2017 Building Project Density by Council District')
plt.show()
```
### Folium choropleth
```
# Center point for Nashville
nashville = [36.1636,-86.7823]
 
# Create map
m = folium.Map(location=nashville, zoom_start=10)
```
```
folium.Choropleth(
   
    geo_data=districts_and_permits,
    name='geometry',
    data=districts_and_permits,
    columns=['district', 'permit_density'],
    key_on='feature.properties.district',
    fill_color='Reds',
    fill_opacity=0.5,
    line_opacity=1.0,
    legend_name='2017 Permitted Building Projects per km squared'
)
 
# Create LayerControl and add it to the map            
folium.LayerControl().add_to(m)
 
# Display the map
display(m)
```
```
# Create LayerControl and add it to the map            
folium.LayerControl().add_to(m)
 
# Display the map
display(m)
```
### Folium choropleth with markers and popups
```
# Create center column for the centroid of each district
districts_and_permits['center'] = districts_and_permits.geometry.centroid
 
# Build markers and popups
for row in districts_and_permits.iterrows():
    row_values = row[1] 
    center_point = row_values['center']
    location = [center_point.y, center_point.x]
    popup = ('Council District: ' + str(row_values['district']) +
             ';  ' + 'permits issued: ' + str(row_values['bldg_permits']))
    marker = folium.Marker(location = location, popup = popup)
    marker.add_to(m)
     
# Display the map
display(m)
```



