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
<img src="/assets/images/tiling_uber.PNG" width=800><br/>
<em>Grid layouts from uber's [H3- hexagon tiling documentation](https://uber.github.io/h3/#/documentation/overview/use-cases)</em><br/>
<br/>
I'm going to post about hexagon's later. In the meantime, let's look at `rectangles`. Let's say you started with a polygon outlining the City of Seattle (KC data source). Below are two version of the Seattle outline. One is a screenshot of the raw file in QGIS, the other is a geopandas plot using the following:
```
city_df = gpd.read_file("/python_r_comp/admin_SHP/admin/city.shp")
fig, ax = plt.subplots(figsize=(5,12))
city_of_interest = 'Seattle' #there are multiple cities King County `city.shp` dataset
single_city = city_df[city_df['CITYNAME']==city_of_interest].copy()
single_city.to_crs({'init': 'epsg:4326'}, inplace=True) #change coordinate reference systems to be compatible with other datasets
single_city.plot(color='none', 
                   edgecolor='black', 
                   legend=True,
                  ax=ax)

_ = single_city.apply(lambda x: ax.annotate(s=x['CITYNAME'], 
                                           xy=(x.geometry.centroid.x, 
                                               x.geometry.centroid.y), ha='center'),axis=1)
```
<br/>
<table>
<tr>
  <td><img src="/assets/images/seattle_boundary_w_basemap.PNG" width=300></td>
  <td><img src="/assets/images/seattle_boundary_no_basemap.PNG" width=300></td>
</tr>
<tr>
  <td><em>Seattle boundary with a basemap</em></td>
  <td><em>Seattle boundary without a basemap</em></td>
</tr>
</table>
One way to split up data is using a [geohash](https://en.wikipedia.org/wiki/Geohash). I think a really helpful website for understanding geohashing is this [interactive-geohash](https://www.movable-type.co.uk/scripts/geohash.html).
Here's a static image from that site of the earth broken down into geohashes: <br />
<img src="/assets/images/geohash.jpg" width="350">
