### Plotting a scatterplot from longitude and latitude
```
Latitude is plotted as x on the horizontal axis.
```

### Styling a scatterplot
```
# Import matplotlib.pyplot
import matplotlib.pyplot as plt
 
# Scatterplot 3
plt.scatter(father_son.fheight, father_son.sheight,  c = 'darkred', edgecolor = 'darkblue',marker='s')
plt.show()
plt.xlabel('father height (inches)')
plt.ylabel('son height (inches)')
plt.title('Son Height as a Function of Father Height')
 
plt.grid()
 
# Show your plot
plt.show()
```

### Extracting longitude and latitude
```
# print the first few rows of df 
print(df.head())

# extract latitude to a new column: lat
df['lat'] = [loc[0] for loc in df.Location]
 
# extract longitude to a new column: lng
df['lng'] = [loc[1] for loc in df.Location]
 
# print the first few rows of df again
print(df.head())
```
### Plotting chicken locations
```
# Import pandas and matplotlib.pyplot using their customary aliases
import pandas as pd
import matplotlib.pyplot as plt

# Load the dataset
chickens = pd.read_csv(chickens_path)
 
# Look at the first few rows of the chickens DataFrame
print(chickens.head())
 
# Plot the locations of all Nashville chicken permits
plt.scatter(x = chickens.lng, y = chickens.lat)
 
# Show the plot
plt.show()
```
### Creating a GeoDataFrame & examining the geometry
```
# Import geopandas
import geopandas as gpd 
 
# Read in the services district shapefile and look at the first few rows.
service_district = gpd.read_file(shapefile_path)
print(service_district.head())
 
# Print the contents of the service districts geometry in the first row
print(service_district.loc[0, 'geometry'])
```
### Plotting shapefile polygons
```
# Import packages
import geopandas as gpd
import matplotlib.pyplot as plt
 
# Plot the Service Districts without any additional arguments
service_district.head()
service_district.plot()
plt.show()
 
 
# Plot the Service Districts, color them according to name, and show a legend
service_district.plot(column = 'name', legend = True)
plt.show()
```
### Geometry
```
polygons, rectangles, points
```
### Plotting points over polygons - part 1
```
# Plot the service district shapefile
service_district.plot(column='name')
 
# Add the chicken locations
plt.scatter(x=chickens.lng, y=chickens.lat, c = 'black')
 
# Show the plot
plt.show()
```
### Plotting points over polygons - part 2
```
# Plot the service district shapefile
service_district.plot(column='name', legend=True)
 
# Add the chicken locations
plt.scatter(x=chickens.lng, y=chickens.lat, c='black', edgecolor = 'white')
 
 
# Add labels and title
plt.title('Nashville Chicken Permits')
plt.xlabel('longitude')
plt.ylabel('latitude')
 
# Add grid lines and show the plot
plt.grid()
plt.show()
```
### Working with GeoJSON
```
The file is human readable, so you can open it in a text editor and understand the contents.
```
### Colormaps
```
# Set legend style
lgnd_kwds = {'title': 'School Districts',
               'loc': 'upper left', 'bbox_to_anchor': (1, 1.03), 'ncol': 1}
 
# Plot the school districts using the tab20 colormap (qualitative)
school_districts.plot(column = 'district', cmap = 'tab20', legend = True, legend_kwds  = lgnd_kwds)
plt.xlabel('Longitude')
plt.ylabel('Latitude')
plt.title('Nashville School Districts')
plt.show();
```
```
# Set legend style
lgnd_kwds = {'title': 'School Districts',
               'loc': 'upper left', 'bbox_to_anchor': (1, 1.03), 'ncol': 1}

# Plot the school districts using the summer colormap (sequential)
school_districts.plot(column = 'district', cmap = 'summer', legend = True, legend_kwds = lgnd_kwds)
plt.xlabel('Longitude')
plt.ylabel('Latitude')
plt.title('Nashville School Districts')
plt.show();
```
```
# Set legend style
lgnd_kwds = {'title': 'School Districts',
               'loc': 'upper left', 'bbox_to_anchor': (1, 1.03), 'ncol': 1}

# Plot the school districts using Set3 colormap without the column argument
school_districts.plot(cmap = 'Set3', legend = True, legend_kwds = lgnd_kwds)
plt.xlabel('Longitude')
plt.ylabel('Latitude')
plt.title('Nashville School Districts')
plt.show();
```
### Map Nashville neighborhoods
```
import geopandas as gpd
import matplotlib.pyplot as plt
 
# Read in the neighborhoods geojson file
neighborhoods = gpd.read_file(neighborhoods_path)
 
# Print the first few rows of neighborhoods
print(neighborhoods.head())
 
# Plot the neighborhoods, color according to name and use the Dark2 colormap
neighborhoods.plot(column = 'name', cmap = 'Dark2')
 
# Show the plot.
plt.show()
```
### Changing coordinate reference systems
```
# Print the first row of school districts GeoDataFrame and the crs
print(school_districts.head(1))
print(school_districts.crs)
 
# Convert the crs to epsg:3857
school_districts.geometry = school_districts.geometry.to_crs(epsg = 3857)
                         
# Print the first row of school districts GeoDataFrame and the crs again
print(school_districts.head(1))
print(school_districts.crs)
```
### Construct a GeoDataFrame from a DataFrame
```
import pandas as pd
import geopandas as gpd
from shapely.geometry import Point
import matplotlib.pyplot as plt
 
# Print the first few rows of the art DataFrame
print(art.head())
 
# Create a geometry column from lng & lat
art['geometry'] = art.apply(lambda x: Point(float(x.lng), float(x.lat)), axis=1)
 
# Create a GeoDataFrame from art and verify the type
art_geo = gpd.GeoDataFrame(art, crs = neighborhoods.crs, geometry = art.geometry)
print(type(art_geo))
```
### Spatial join practice
```
# Spatially join art_geo and neighborhoods 
art_intersect_neighborhoods = gpd.sjoin(art_geo, neighborhoods, op = 'intersects')
 
# Print the shape property of art_intersect_neighborhoods
print(art_intersect_neighborhoods.shape)
```
```
# Create art_within_neighborhoods by spatially joining art_geo and neighborhoods
art_within_neighborhoods = gpd.sjoin(art_geo, neighborhoods, op = 'within')
 
# Print the shape property of art_within_neighborhoods
print(art_within_neighborhoods.shape)
```
```
# Spatially join art_geo and neighborhoods and using the contains op
art_containing_neighborhoods = gpd.sjoin(art_geo, neighborhoods, op = 'contains')
 
# Print the shape property of art_containing_neighborhoods
print(art_containing_neighborhoods.shape)
# (0, 13)
```
### Finding the neighborhood with the most public art
```
# import packages
import geopandas as gpd
import pandas as pd

# Spatially join neighborhoods with art_geo
neighborhood_art = gpd.sjoin(art_geo, neighborhoods, op = "within")
 
# Print the first few rows
print(neighborhood_art.head())
```
### Aggregating points within polygons
```
# Get name and title from neighborhood_art and group by name
neighborhood_art_grouped = neighborhood_art[['name', 'title']].groupby('name')
 
# Aggregate the grouped data and count the artworks within each polygon
print(neighborhood_art_grouped.agg('count').sort_values(by = 'title', ascending = False))
```
### Plotting the Urban Residents neighborhood and art
```
# Create urban_art from neighborhood_art where the neighborhood name is Urban Residents
urban_art = neighborhood_art.loc[neighborhood_art.name == 'Urban Residents']
 
# Get just the Urban Residents neighborhood polygon and save it as urban_polygon
urban_polygon = neighborhoods.loc[neighborhoods.name == "Urban Residents"]
 
# Plot the urban_polygon as ax 
ax = urban_polygon.plot(color = 'lightgreen')
 
# Add a plot of the urban_art and show it
urban_art.plot( ax = ax, column = 'type', legend = True);
plt.show()
```
### Find the area of the Urban Residents neighborhood
```
# Print the head of the urban polygon 
print(urban_polygon.head())
 
# Create a copy of the urban_polygon using EPSG:3857 and print the head
urban_poly_3857 = urban_polygon.to_crs(epsg = 3857)
print(urban_poly_3857.head())
 
# Print the area of urban_poly_3857 in kilometers squared
area = urban_poly_3857.geometry.area / 10**6
print('The area of the Urban Residents neighborhood is ', area[0], ' km squared')
 

```
### The center of the Urban Residents neighborhood
```
# Create downtown_center from urban_poly_3857
downtown_center = urban_poly_3857.geometry.centroid
 
# Print the type of downtown_center 
print(type(downtown_center))
 
# Plot the urban_poly_3857 as ax and add the center point
ax = urban_poly_3857.plot(color = 'lightgreen')
downtown_center.plot(ax = ax, color = 'black')
plt.xticks(rotation = 45)
 
# Show the plot
plt.show()
```
### Prepare to calculate distances
```
# Import packages
from shapely.geometry import Point
import geopandas as gpd
import pandas as pd
 
# Create art_dist_meters using art and the geometry from art
art_dist_meters = gpd.GeoDataFrame(art, geometry = art.geometry, crs = {'init': 'epsg:4326'})
print(art_dist_meters.head(2))
 
# Set the crs of art_dist_meters to use EPSG:3857
art_dist_meters.geometry = art_dist_meters.geometry.to_crs(epsg = 3857)
print(art_dist_meters.head(2))
 
# Add a column to art_meters, center
art_dist_meters['center'] = center_point
```
### Art distances from neighborhood center
```
# Import package for pretty printing
import pprint
 
# Build a dictionary of titles and distances for Urban Residents art
art_distances = {}
for row in art_dist_meters.iterrows():
    vals = row[1]
    key = vals['title']
    ctr = vals['center']
    art_distances[key] = vals['geometry'].distance(other=ctr)
 
# Pretty print the art_distances
pprint.pprint(art_distances)
```
### Create a folium location from the urban centroid
```
# Print the head of the urban_polygon
print(urban_polygon.head())
 
# Create urban_center from the urban_polygon center
urban_center = urban_polygon.center[0]
 
# Print urban_center
print(urban_center)
 
# Create array for folium called urban_location
urban_location = [urban_center.y, urban_center.x]
 
# Print urban_location
print(urban_location)
```
### Create a folium map of downtown Nashville
```
# Construct a folium map with urban_location
downtown_map = folium.Map(location = urban_location, zoom_start = 15)
 
# Display the map
display(downtown_map)
```
### Folium street map of the downtown neighborhood
```
# Create array for called folium_loc from the urban_polygon center point
point = urban_polygon.centroid[0]
folium_loc = [point.y, point.x]
 
# Construct a map from folium_loc: downtown_map
downtown_map = folium.Map(location = folium_loc, zoom_start = 15)
 
# Draw our neighborhood: Urban Residents
folium.GeoJson(urban_polygon.geometry).add_to(downtown_map)
 
# Display the map
display(downtown_map)
```
### Adding markers for the public art
```
# Iterate through the urban_art and print each part of tuple returned
for row in urban_art.iterrows():
  print('first part: ', row[0])
  print('second part: ', row[1])
 
# Create a location and marker with each iteration for the downtown_map
for row in urban_art.iterrows():
    row_values = row[1] 
    location = [row_values['lat'], row_values['lng']]
    marker = folium.Marker(location = location)
    marker.add_to(downtown_map)
 
# Display the map
display(downtown_map)
```
### Troubleshooting data issues
```
# Print the urban_art titles
print(urban_art.title)
 
#Print the urban_art descriptions
print(urban_art.desc)
 
# Replace Nan and ' values in description
urban_art.desc.fillna('', inplace = True)
urban_art.desc = urban_art.desc.str.replace("'", "`")
 
#Print the urban_art descriptions again
print(urban_art.desc)
```
### A map of downtown art
```
# Construct downtown map
downtown_map = folium.Map(location = nashville, zoom_start = 15)
folium.GeoJson(urban_polygon).add_to(downtown_map)

# Create popups inside the loop you built to create the markers
for row in urban_art.iterrows():
    row_values = row[1] 
    location = [row_values['lat'], row_values['lng']]
    popup = (str(row_values['title'])).replace("'", "`")
    marker = folium.Marker(location = location, popup = popup)
    marker.add_to(downtown_map)

# Display the map.
display(downtown_map)
```
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
