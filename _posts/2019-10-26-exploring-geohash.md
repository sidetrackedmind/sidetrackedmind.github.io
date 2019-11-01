---
title: "Exploring Geohash"
date: 2019-10-26
categories:
  - blog
tags:
  - spatial
---
## Geohash
I've been investigating different ways to break up datasets into grids in order to join and aggregate data that falls within each part of the grid. There are three geometries that work for tiling: rectangle, triangle, and hexagon.<br/>
![tiling](/assets/images/tiling_uber.PNG)
<em>Grid layouts from uber's [H3- hexagon tiling documentation](https://uber.github.io/h3/#/documentation/overview/use-cases)</em><br/>
<br/>
I'm going to post about hexagon's later. In the meantime, let's look at `rectangles`. Let's say you started with a polygon outlining the City of Seattle (KC data source). Below are two version of the Seattle outline. One is a screenshot of the raw file in QGIS, the other is a geopandas plot using the following:
```python
city_df = gpd.read_file("/python_r_comp/admin_SHP/admin/city.shp")
fig, ax = plt.subplots(figsize=(5,12))
#there are multiple cities King County `city.shp` dataset
city_of_interest = 'Seattle'
single_city = city_df[city_df['CITYNAME']==city_of_interest].copy()
#change coordinate reference systems to be compatible with other datasets
single_city.to_crs({'init': 'epsg:4326'}, inplace=True)
single_city.plot(color='none', 
                   edgecolor='black', 
                   legend=True,
                  ax=ax)

_ = single_city.apply(lambda x: ax.annotate(s=x['CITYNAME'], 
                                           xy=(x.geometry.centroid.x, 
                                               x.geometry.centroid.y), ha='center'),axis=1)
```
![w_basemap](/assets/images/seattle_boundary_w_basemap.PNG) ![wo_basemap](/assets/images/seattle_boundary_no_basemap.PNG)
<br/>
<em>Above (left) Seattle boundary with a basemap and (right) Seattle boundary without a basemap from geopandas</em>
<br/>
<br/>
One way to split up the Seattle polygon into rectangles is using a [geohash grid](https://en.wikipedia.org/wiki/Geohash). A helpful website for understanding the levels of geohashing is this [interactive-geohash](https://www.movable-type.co.uk/scripts/geohash.html).
<br/>
Here's a static image from that site of the earth broken down into geohashes: <br />
<img src="/assets/images/geohash.jpg" width="350"><br/>
As you can see from the image above, there are various levels, or scales of rectangle, created in order to cover the whole globe. Each rectangle grid can be broken into smaller rectangles. There's a ton of information on the wikipedia page about the specifics of making the grid and the geohash characters. The benefits I want to call out are the following:
- consistency
- easy grouping without point-in-polygon function
<br/>
First, let's talk consistency. Using the python geohash library like 
```python
from geolib import geohash
``` 
you can choose a scale and create a rectangle grid that is <em>consistent</em>. 
<br/>
<img src="/assets/images/seattle_geohash_zoom_in.PNG" width="350"><br/>
<br/>
In the picture above, the `` geohash will <em>always</em> be at that location with those boundary coordinates. There are other ways to chop up polygons in python, for instance:<br/>
```python
# make the geometry a multipolygon if it's not already
geometry = single_city['geometry'].iloc[0]
if isinstance(geometry, Polygon):
    geometry = MultiPolygon([geometry])
geometry_cut = ox.quadrat_cut_geometry(geometry, quadrat_width=0.01)
seattle_cut = gpd.GeoDataFrame(crs={'init':'4326'}
                 ,geometry=[geom for geom in geometry_cut.geoms])
```
<br/>
<img src="/assets/images/polygon_cut_grid.png" width="350"><br/>
<br/>


If you chop up other data, you can <em>align</em> or <em>group</em> the datasets by geohash rectangles.   
