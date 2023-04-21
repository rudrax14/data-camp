## 

How do you show all of your data while making sure that viewers don't miss an important point or points? Here we discuss how to guide your viewer through the data with color-based highlights and text. We also introduce a dataset on common pollutant values across the United States.

<br>

### Hardcoding a highlight

```
houston_pollution = pollution[pollution.city  ==  'Houston']

# Make array orangred for day 330 of year 2014, otherwise lightgray
houston_colors = ['orangered' if (day  ==  330) & (year  ==  2014) else 'lightgray' 
                  for day,year in zip(houston_pollution.day, houston_pollution.year)]

sns.regplot(x = 'NO2',
            y = 'SO2',
            data = houston_pollution,
            fit_reg = False, 
            # Send scatterplot argument to color points 
            scatter_kws = {'facecolors': houston_colors, 'alpha': 0.7})
plt.show()
```

### Programmatically creating a highlight

```
houston_pollution = pollution[pollution.city  ==  'Houston'].copy()

# Find the highest observed O3 value
max_O3 = houston_pollution.O3.max()

# Make a column that denotes which day had highest O3
houston_pollution['point_type'] = ['Highest O3 Day' if O3  ==  max_O3 else 'Others' for O3 in houston_pollution.O3]

# Encode the hue of the points with the O3 generated column
sns.scatterplot(x = 'NO2',
                y = 'SO2',
                hue = 'point_type',
                data = houston_pollution)
plt.show()
```

### Comparing with two KDEs

```
# Filter dataset to the year 2012
sns.kdeplot(pollution[pollution.year == 2012].O3, 
            # Shade under kde and add a helpful label
            shade = True,
            label = '2012')

# Filter dataset to everything except the year 2012
sns.kdeplot(pollution[pollution.year != 2012].O3, 
            # Again, shade under kde and add a helpful label
            shade = True,
            label = 'other years')
plt.show()
```

### Correcting string errors

```
sns.distplot(pollution[pollution.city == 'Vandenberg Air Force Base'].O3, 
             label = 'Vandenberg', 
             # Turn of the histogram and color blue to stand out
             hist = False,
             color = 'steelblue', 
             # Turn on rugplot
             rug = True)

sns.distplot(pollution[pollution.city != 'Vandenberg Air Force Base'].O3, 
             label = 'Other cities',
             # Turn off histogram and color gray
             hist = False,  
             color = 'gray')
plt.show()

```
###Beeswarms
```
# Filter data to just March
pollution_mar = pollution[pollution.month == 3]

# Plot beeswarm with x as O3
sns.swarmplot(y = "city",
              x = 'O3', 
              data = pollution_mar, 
              # Decrease the size of the points to avoid crowding 
              size = 3)

# Give a descriptive title
plt.title('March Ozone levels by city')
plt.show()
```

### A basic text annotation

```
# Draw basic scatter plot of pollution data for August
sns.scatterplot(x = 'CO', y = 'SO2', data = pollution[pollution.month  ==  8],color="orange")

# Label highest SO2 value with text annotation
plt.text(0.57,41,
         'Cincinnati had highest observed\nSO2 value on Aug 11, 2013', 
         # Set the font to large
         fontdict = {'ha': 'left', 'size': 'large'},color="blue")
plt.show()      
```

### Arrow annotations

```
# Query and filter to New Years in Long Beach
jan_pollution = pollution.query("(month  ==  1) & (year  ==  2012)")
lb_newyears = jan_pollution.query("(day  ==  1) & (city  ==  'Long Beach')")

sns.scatterplot(x = 'CO', y = 'NO2',
                data = jan_pollution)

# Point arrow to lb_newyears & place text in lower left 
plt.annotate('Long Beach New Years',
             xy = (lb_newyears.CO, lb_newyears.NO2),
             xytext = (2, 15), 
             # Shrink the arrow to avoid occlusion
             arrowprops = {'facecolor':'gray', 'width': 3, 'shrink': 0.03},
             backgroundcolor = 'white')
plt.show()
```
### Combining annotations and color

```
is_lb = ['orangered' if city  ==  'Long Beach' else 'lightgray' for city in pollution['city']]
```
### Getting rid of unnecessary color

```
# Hard to read scatter of CO and NO2 w/ color mapped to city
# sns.scatterplot('CO', 'NO2',
#                 alpha = 0.2,
#                 hue = 'city',
#                 data = pollution)

g = sns.FacetGrid(data = pollution,
                  col = 'city',
                  col_wrap = 3)

# Map sns.scatterplot to create separate city scatter plots
g.map(sns.scatterplot, 'CO', 'NO2', alpha = 0.2,color="orange")
plt.show()
```
### Fixing Seaborn's bar charts

```
import numpy as np

sns.barplot(y = 'city', x = 'CO', 
              estimator = np.mean,
            ci = False,
              data = pollution,
              # Add a border to the bars
            edgecolor ='black')
plt.show()
```
### Making a custom continuous palette

```
# Filter the data
cinci_2014 = pollution.query("city  ==  'Cincinnati' & year  ==  2014")

# Define a custom continuous color palette
color_palette = sns.light_palette('orangered',
                         as_cmap=True)

# Plot mapping the color of the points with custom palette
sns.scatterplot(x = 'CO',
                y = 'NO2',
                hue = 'O3', 
                data = cinci_2014,
                palette = color_palette)
plt.show()
```
### Customizing a diverging palette heatmap

```
# Define a custom palette
color_palette = sns.diverging_palette(250, 0, as_cmap = True)

sns.heatmap(nov_2015_CO,
            cmap=color_palette,
            center=0,
            vmin=-4,
            vmax=4
           );
plt.yticks(rotation=0);
plt.show()
```
### Adjusting your palette according to context

```
# Dark plot background
plt.style.use("dark_background")

# Modify palette for dark background
color_palette = sns.diverging_palette(250, 0,
                                      center = 'dark',
                                      as_cmap = True)

# Pass palette to plot and set center
sns.heatmap(oct_2015_o3,
            cmap = color_palette,
            center = 0);
plt.yticks(rotation = 0);

plt.show()
```
### Using a custom categorical palette

```
# Filter our data to Jan 2013
pollution_jan13 =pollution.query('year == 2013 & month == 1')

# Color lines by the city and use custom ColorBrewer palette
sns.lineplot(x='day', y='CO', hue='city', palette='Set2', linewidth=3, data=pollution_jan13);
plt.show()
```
### Dealing with too many categories

```
# Choose the combos that get distinct colors
wanted_combos = ['Vandenberg Air Force Base NO2', 'Long Beach CO', 'Cincinnati SO2']

city_pol_month['color_cats'] = [x if x in wanted_combos else 'other' for x in city_pol_month['city_pol']]

# Plot lines with color driven by new column and lines driven by original categories
sns.lineplot(x = "month",
             y = "value",
             hue = 'color_cats',
             units = 'city_pol',
             estimator = None,
             palette = 'Set2',
             data = city_pol_month);
plt.show()
```
### Coloring ordinal categories

```
# Divide CO into quartiles
pollution['CO quartile'] = pd.qcut(pollution['CO'], q = 4, labels = False)

des_moines = pollution.query("city  ==  'Des Moines'")

# Color points with by quartile and use ColorBrewer palette
sns.scatterplot(x = 'SO2',
                y = 'NO2',
                hue = 'CO quartile', 
                  data = des_moines,
                palette = 'GnBu');
plt.show()
```
### Choosing the right variable to encode with color

```
cities = ['Fairbanks', 'Long Beach', 'Vandenberg Air Force Base', 'Denver']

# Filter data to desired cities
city_maxes = max_pollutant_values[max_pollutant_values.city.isin(cities)]

# Swap city and year encodings
sns.catplot(x = 'city', hue = 'year',
              y = 'value', row = 'pollutant',    
              # Change palette to one appropriate for ordinal categories
              data = city_maxes, palette = 'BuGn',
              sharey = False, kind = 'bar');
plt.show()
```
### Basic confidence intervals

```
# Construct CI bounds for averages
average_ests['lower'] = average_ests['mean'] - 1.96*average_ests['std_err']
average_ests['upper'] = average_ests['mean'] + 1.96*average_ests['std_err']
print(average_ests)
# Setup a grid of plots, with non-shared x axes limits
g = sns.FacetGrid(average_ests, row = 'pollutant', sharex = False)

# Plot CI for average estimate
g.map(plt.hlines, 'y', 'lower', 'upper')

# Plot observed values for comparison and remove axes labels
g.map(plt.scatter, 'seen', 'y', color = 'orangered').set_ylabels('').set_xlabels('') 

plt.show()
```
### Annotating confidence intervals

```
plt.hlines(y='year', xmin='lower', xmax='upper', 
          linewidth=5, color='steelblue', alpha=0.7,
          data=diffs_by_year);

# Point estimates
plt.plot('mean', 'year', 'k|', data=diffs_by_year);

# Add a 'null' reference line at 0 and color orangered
plt.axvline(x=0, color='orangered', linestyle='--');

# Set descriptive axis labels and title
plt.xlabel('95% CI');
plt.title('Avg SO2 differences between Cincinnati and Indianapolis');
plt.show()
```
### Making a confidence band

```
vandenberg_NO2['lower'] = vandenberg_NO2['mean'] - 2.58 * vandenberg_NO2['std_err']
vandenberg_NO2['upper'] = vandenberg_NO2['mean'] + 2.58 * vandenberg_NO2['std_err']

# Plot mean estimate as a white semi-transparent line
plt.plot('day', 'mean', data=vandenberg_NO2, color='white', alpha=0.4);

# Fill between the upper and lower confidence band values
plt.fill_between(x='day', y1='lower', y2='upper', data=vandenberg_NO2);

plt.show()
```
### Separating a lot of bands

```
# Setup a grid of plots with columns divided by location
g = sns.FacetGrid(eastern_SO2, col='city', col_wrap=2);

# Map interval plots to each cities data with coral colored ribbons
g.map(plt.fill_between, 'day', 'lower', 'upper', color='coral');

# Map overlaid mean plots with white line
g.map(plt.plot, 'day', 'mean', color='white');

plt.show()
```
### Cleaning up bands for overlaps

```
for city, color in [('Denver', '#66c2a5'), ('Long Beach', '#fc8d62')]:
    # Filter data to desired city
    city_data = SO2_compare[SO2_compare.city == city]
    
    # Set city interval color to desired and lower opacity
    plt.fill_between(x='day', y1='lower', y2='upper', data=city_data, color=color, alpha=0.4);
    
    # Draw a faint mean line for reference and give a label for legend
    plt.plot('day', 'mean', data=city_data, label=city, color=color, alpha=0.25);
    
plt.legend();
plt.show()
```
### 90, 95, and 99% intervals

```
# Add interval percent widths
alphas = [     0.01,  0.05,   0.1] 
widths = [ '99% CI', '95%', '90%']
colors = ['#fee08b','#fc8d59','#d53e4f']

for alpha, color, width in zip(alphas, colors, widths):
    # Grab confidence interval
    conf_ints = pollution_model.conf_int(alpha)
    
    # Pass current interval color and legend label to plot
    plt.hlines(y = conf_ints.index, xmin = conf_ints[0], xmax = conf_ints[1],
               colors = color, label = width, linewidth = 10) 

# Draw point estimates
plt.plot(pollution_model.params, pollution_model.params.index, 'wo', label = 'Point Estimate')

plt.legend(loc = 'upper right')
plt.show() 
```
### 90 and 95% bands

```
int_widths = ['90%', '99%']
z_scores = [1.67, 2.58]
colors = ['#fc8d59', '#fee08b']

for percent, Z, color in zip(int_widths, z_scores, colors):
    
    # Pass lower and upper confidence bounds and lower opacity
    plt.fill_between(
        x = cinci_13_no2.day, alpha = 0.4, color = color,
        y1 = cinci_13_no2['mean'] - Z * cinci_13_no2['std_err'],
        y2 = cinci_13_no2['mean'] + Z * cinci_13_no2['std_err'],
        label = percent);
    
plt.legend();
plt.show()
```
### Using band thickness instead of coloring

```
# Decrase interval thickness as interval widens
sizes =      [    15,  10,  5]
int_widths = ['90% CI', '95%', '99%']
z_scores =   [    1.67,  1.96,  2.58]

for percent, Z, size in zip(int_widths, z_scores, sizes):
    plt.hlines(y = rocket_model.pollutant, 
               xmin = rocket_model['est'] - Z * rocket_model['std_err'],
               xmax = rocket_model['est'] + Z * rocket_model['std_err'],
               label = percent, 
               # Resize lines and color them gray
               linewidth = size, 
               color = 'gray'); 
    
# Add point estimate
plt.plot('est', 'pollutant', 'wo', data = rocket_model, label = 'Point Estimate');
plt.legend(loc = 'center left', bbox_to_anchor = (1, 0.5));
plt.show()
```
### The bootstrap histogram

```
cinci_may_NO2 = pollution.query("city  ==  'Cincinnati' & month  ==  5").NO2

# Generate bootstrap samples
boot_means = bootstrap(cinci_may_NO2, 1000)

# Get lower and upper 95% interval bounds
lower, upper = np.percentile(boot_means, [2.5, 97.5])

# Plot shaded area for interval
plt.axvspan(lower, upper, color = 'gray', alpha = 0.2);

# Draw histogram of bootstrap samples
sns.distplot(boot_means, bins = 100, kde = False);

plt.show()
```
### Bootstrapped regressions

```
sns.lmplot('NO2', 'SO2', data = no2_so2_boot,
           # Tell seaborn to a regression line for each sample
           hue = 'sample', 
           # Make lines blue and transparent
           line_kws = {'color': 'steelblue', 'alpha': 0.2},
           # Disable built-in confidence intervals
           ci = None, legend = False, scatter = False);

# Draw scatter of all points
plt.scatter('NO2', 'SO2', data = no2_so2);

plt.show()
```
### Lots of bootstraps with beeswarms

```
city_boots = pd.DataFrame()

for city in ['Cincinnati', 'Des Moines', 'Indianapolis', 'Houston']:
    # Filter to city
    city_NO2 = pollution_may[pollution_may.city  ==  city].NO2
    # Bootstrap city data & put in DataFrame
    cur_boot = pd.DataFrame({'NO2_avg': bootstrap(city_NO2, 100), 'city': city})
    # Append to other city's bootstraps
    city_boots = pd.concat([city_boots,cur_boot])

# Beeswarm plot of averages with citys on y axis
sns.swarmplot(y = "city", x = "NO2_avg", data = city_boots, color = 'coral');
plt.show()
```
### Looking at the farmers market data

```
# Print first three rows of data and transpose
first_rows = markets.head(3).transpose()
print(first_rows)

# Get descriptions of every column
col_descriptions = markets.describe(include = 'all',
                                percentiles = [0.5]).transpose()
print(col_descriptions)
```
### Scatter matrix of numeric columns

```
# Select just the numeric columns (exluding individual goods)
numeric_columns = ['lat', 'lon', 'months_open', 'num_items_sold', 'state_pop']

# Make a scatter matrix of numeric columns
pd.plotting.scatter_matrix(markets[numeric_columns], 
                             # Make figure large to show details
                             figsize = (15,10), 
                           # Lower point opacity to show overlap
                           alpha = 0.5)
plt.show()
```
### Digging in with basic transforms

```
markets['log_pop'] = np.log(markets['state_pop'])

# Draw a scatterplot of log-population to # of items sold
sns.scatterplot(x='log_pop',
                y='num_items_sold',
                # Reduce point opacity to show overlap
                alpha=0.25,
                data=markets
               );

plt.show()
```
### Is latitude related to months open?

```
sns.regplot(x = 'lat', 
            y = 'months_open', 
            # Set scatter point opacity & color
           scatter_kws={'alpha':0.1, 'color':'gray'},
            # Disable confidence band
              ci=False, 
            data = markets)

plt.show()
```
### What state is the most market-friendly?

```
g = sns.regplot(
    "log_markets", "log_pop", 
    ci = False,
    # Shrink scatter plot points
    scatter_kws = {'s':2},
    data = markets_and_pop)

# Iterate over the rows of the data
for _, row in markets_and_pop.iterrows():
    state, _, _, log_markets, log_pop = row
    # Place annotation and reduce size for clarity
    g.annotate(state,  (log_markets, log_pop), size=10);
plt.show()
```
### Popularity of goods sold by state

```
fig, ax = plt.subplots(figsize=(10, 10))
# Subset goods to interting ones
to_plot = ['Cheese', 'Maple', 'Fruits', 'Grains', 'Seafood', 'Plants', 'Vegetables']
goods_by_state_small = goods_by_state.query('good in ' + str(to_plot))

g = sns.scatterplot('good','prop_selling', data = goods_by_state_small,
                    # Hide scatter points by shrinking to nothing
                    s = 0, ax=ax)

for _,row in goods_by_state_small.iterrows():
    g.annotate(row['state'], (row['good'], row['prop_selling']), 
             # Center annotation on axis
             ha = 'center', 
             size = 10)
plt.show()
```
### Stacking to find trends

```
_, (ax1, ax2) = plt.subplots(2, 1, figsize=(10, 10))

# Draw location scatter plot on first plot
sns.scatterplot("lon", "lat", "months_open",
               palette=sns.light_palette("orangered", n_colors=12),
               legend=False, data=markets,
               ax=ax1);

# Plot a regression plot on second plot
sns.regplot("lon", "months_open",
           scatter_kws={'alpha':0.2, 'color':'gray', 'marker': '|'},
           lowess=True,
           marker='|', data=markets,
           ax=ax2);

plt.show()
```
### Using a plot as a legend

```
# Set up two side-by-side plots
f, (ax1, ax2) = plt.subplots(1, 2, figsize=(15, 15))

# Map the column for selected states to the bar color
sns.barplot('people_per_market', 'state', hue='is_selected',
           # Disable dodge so bars are full size
           dodge=False,
           data=markets_by_state, ax=ax1);

# Map selected states to point color
sns.scatterplot('log_pop', 'log_markets', hue='is_selected',
               data=markets_by_state, ax=ax2, s=100);

# Remove the legend for both plots
ax1.legend_.remove()
ax2.legend_.remove()
plt.show() 
```
### Cleaning up the background

```
fig, ax = plt.subplots(figsize=(15, 15))

# Set background to white with grid
sns.set_style('whitegrid')

plt.scatter('good','prop selling', marker = '_', alpha = 0.7, data = goods_by_state);

# Draw lines across goods for highlighted states
highlighted = goods_by_state.query("state in ['New Mexico','North Dakota','Vermont']")
sns.lineplot('good','prop selling', 'state', data = highlighted, legend = False, ax=ax);

# Draw state name at end of lines
last_rows = highlighted.groupby('state', as_index = False).agg('first')
for _,row in last_rows.iterrows():
    plt.annotate(row['state'], (row['good'], row['prop selling']),
                 ha = 'right', xytext = (5,0), textcoords = 'offset pixels')

# Remove all borders
sns.despine(left=True, bottom=True);
plt.show()
```
### Remixing a plot

```
fig, ax = plt.subplots(figsize=(10, 10))
# Decrease font size so state names are less crowded
sns.set(font_scale = 0.85)

# Switch to an appropriate color palette
blue_pal = sns.light_palette("steelblue", as_cmap = True)

# Order states by latitude
g = sns.heatmap(markets_by_month.reindex(state_by_lat), 
            # Add gaps between cells
            linewidths = 0.1, 
            # Set new palette and remove color bar 
            cmap = blue_pal, cbar = False,
            yticklabels = True, ax=ax);

# Rotate y-axis ticks 
g.set_yticklabels(g.get_yticklabels(), rotation = 0);
plt.title('Distribution of months open for farmers markets by latitude');
plt.show()
```
### Enhancing legibility

```
f, (ax1, ax2) = plt.subplots(1, 2, figsize=(15, 15))

# Draw barplot w/ colors mapped to state_colors vector
sns.barplot('people_per_market', 'state', palette = state_colors,
            data = markets_by_state, ax = ax1)

# Map state colors vector to the scatterplot as well
p = sns.scatterplot('population', 'num_markets', c = state_colors,
                    data = markets_by_state, s = 60, ax = ax2)

# Log the x and y scales of our scatter plot so it's easier to read
ax2.set(xscale = "log", yscale = 'log')

# Increase annotation text size for legibility
ax2.annotate(tx_message, xy = (26956958,230), 
             xytext = (26956958, 450),ha = 'right', 
             size = 15, backgroundcolor = 'white',
             arrowprops = {'facecolor':'black', 'width': 3})
sns.set_style('whitegrid')
plt.show()
```
