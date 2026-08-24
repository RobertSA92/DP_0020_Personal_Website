---
title: "Acquiring Sentinel‑2 Imagery using Vessel AIS Data"
date: 2026-08-23T13:45:49+07:00
slug: /space-explorations-next-frontier/
description: Python script that retrieves AIS vessel event locations from Global Fishing Watch and uses Google Earth Engine to download matching Sentinel‑2 satellite images.
image: images/Disney_Dream_20260822.jpg
caption: Satellite Images are from Sentinal-2 via Google Earth Engine, Disney Dream Photo by user JamesHills on Pixabay
categories:
  - Geospatial
tags:
  - feature
  - Google Earth Engine 
  - Global FIshing Watch 
  - Python
  - API
  - Sentinel-2
draft: false
---

<!--more-->

## 1. Importing Libraries and Setting Up Parameters

Libraries used: Google Earth Engine, geemap, pandas, cartopy, PIL, cv2, and others. Parameters: the vessel name, MMSI, date range, and a base directory for storing outputs.

The notebook includes values such as:

- `vessel_mmsi = '311042900'`
- `max_downloads = '1000'`

This gives a clean, reproducible setup.

## 2. Authenticating and Querying the Global Fishing Watch API

Loaded GFW API access token from an environment file and created the client. A function is used to resolve the MMSI into GFW vessel IDs. The API returned:

"Disney Dream”

With the vessel IDs, port‑visit, encounter, and loitering events within the date range. Geometry was flattened into simple `lat` and `lon` columns and produced a clean DataFrame of AIS events.

## 3. Preparing Earth Engine and Downloading Sentinel‑2 Imagery

For each AIS event, a buffered bounding box was created around its coordinates.

The COPERNICUS/S2_SR_HARMONIZED collection was queried, filtered by date and cloud cover, selected RGB bands, and downloaded all matching images as JPEGs.

Example log entry:

“Row 2: downloading image 1/2 → row2_20260119T160201…jpg”

## 4. Generating Geospatial Sidecar Files

For each downloaded JPEG, sidecar files were generated:

- a `.jgw` world file  
- a `.prj` projection file (EPSG:4326)  
- a `.geojson` metadata file containing bounding box, acquisition date, cloud percentage, and AIS event metadata  

This ensured every image remained geospatially referenced.

## 5. Image Upscaling

Each JPEG was upscaled by 4× using Lanczos resampling. After resizing, the world‑file was recalculated with adjusted pixel size to maintain correct geospatial alignment.

Example log:

“Upscaled row10… to (840, 896)”

## 6. Image Sharpening (Unsharp Mask)

I applied an unsharp mask with:

- radius 2  
- percent 150  
- threshold 3  

Sidecar files were copied unchanged.

Example log:

“Sharpened row39_20260705T155129…jpg”

## 7. Gamma Correction

Gamma correction was adjusted (γ = 0.8) to brighten midtones. Geospatial metadata remained untouched.

Example log:

“Gamma corrected row41… (gamma=0.8)”

## 8. Additional Enhancements (Exploratory)

I experimented with vibrance, saturation, contrast, and white balance adjustments, but decided they didn’t meaningfully improve the imagery.

## 9. Final Output Structure

Sentinel‑2 imagery aligned to AIS vessel events was organised into:

- raw downloads  
- upscaled images  
- sharpened images  
- gamma‑corrected images  
- full geospatial metadata for each file  

This workflow provides a reproducible pipeline for linking AIS events to satellite imagery and enhancing the results for analysis or visualisation.