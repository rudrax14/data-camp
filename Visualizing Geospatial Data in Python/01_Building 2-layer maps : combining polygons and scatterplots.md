### Plotting a scatterplot from longitude and latitude

'Latitude is plotted as x on the horizontal axis.'

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
