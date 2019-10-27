---
title: "Exploring Geohash"
date: 2019-10-26
categories:
  - blog
tags:
  - spatial
---
## Geohash
Recently, I've been investigating different ways to break up datasets into grids in order to join and aggregate data that falls within each part of the grid. There are three geometries that work for tiling: rectangle, triangle, and hexagon. I'm going to post about hexagon's later. In the meantime, let's look at `rectangles`. Let's say you started with a polygon outlining the City of Seattle (KC data source). <br />
<img src="/assets/images/seattle_boundary_no_basemap.PNG"/><br />
<em>Seattle boundary without a basemap</em>
One way to split up data is using a [geohash](https://en.wikipedia.org/wiki/Geohash). I think a really helpful website for understanding geohashing is this [interactive-geohash](https://www.movable-type.co.uk/scripts/geohash.html).
Here's a static image from that site of the earth broken down into geohashes: <br />
<img src="/assets/images/geohash.jpg" width="350">
