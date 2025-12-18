+++
author = "Eric Fielding and M. Grace Bato"
title = "Tracking Mauna Loa displacements with OPERA DISP-S1"
date = "2025-12-18"
description = "Tracking Tracking Mauna Loa displacements with OPERA DISP-S1"
+++

The OPERA Surface Displacement from Sentinel-1 (DISP-S1) products measure ground motion in the radar line-of-sight direction, providing insight into both natural and human-driven changes across Earth’s surface.

Here we highlight the application of DISP-S1 products to the summit area of Mauna Loa volacano in Hawai'i. There was a significant eruption of Mauna Loa in December 2022, and the DISP-S1 products show the surface expression of a magma intrusion at shallow depths beneath the summit. The magma intruded along a dike or vertical crack and pushed the sides of the volcano apart in a short time at the beginning of the eruption. The eruption also caused lava to fill the summit crater and flow down the north side of the volcano. The lava flow shows ongoing surface displacement for almost a year after the eruption, likely a combination of slow downhill movement of the lava and contraction as the lava fills.

This map shows the location of Mauna Loa summit in the middle of the Big Island of Hawai'i.

![Map showing the Big Island of Hawai'i. A black box outlines the area of the summit of Mauna Loa shown in the displacement maps below.](Hawaii-MaunaLoa-box.png)

The two images below show views from the [Alaska Satellite Facility (ASF) Displacement Portal](https://displacement.asf.alaska.edu/#/?dispOverview=VEL&zoom=11.123&center=-155.541,19.287&flightDirs=DESCENDING&series=POINT(-155.5594093453547%2019.514142878603707)--1--Point--1b396fb4-8f31-443d-be30-03b7d25ff6c7--Series::POINT(-155.51075417202367%2019.493268696859957)--2--Point--af1bde3d-416f-470d-bad8-bedd8965c9ac--Series::POINT(-155.50216185796532%2019.602812814102023)--3--Point--b642813e-c8aa-48ba-8b4b-b3f068ff1e71--Series&start=2016-07-20T23:15:49Z&end=2024-12-30T00:16:44Z), which enables quick viewing and analysis of DISP-S1 products. We selected three points on Mauna Loa to illustrate how different parts of the volcano moved during and after the eruption in December 2023. The same three points are shown on both maps and the two maps show DISP-S1 measurements from the two Sentinel-1 satellite tracks over Mauna Loa. The two tracks have opposite look directions. The DISP-S1 measurements are in the direction between the ground and the satellite, which is up and west (slightly south) for the ascending track but up and east (also slightly south) for the descending track. The maps of the displacements show largely opposite patterns on the two tracks because the horizontal motion due to the magma intrusion. The intrusion pushed the east side of the volcano summit to the east, which is towards the satellite (positive) on the descending track and away from the satellite (negative) on the ascending track. Conversely, the intrusion pushed the west side of the volcano summit to the west, which is negative (blue) on the descending track and positive (red) on the ascending track.

Another feature looks similar on the two maps with a dark blue color. This is where a large lava flow flowed north from the summit during the eruption for more than a week. The DISP-S1 measurements show the surface of the lava flow moved away from the satellite on both tracks, so it moved down and probably a little north.

The time series plots show the how the three points moved over time from the DISP-S1 dataset. Point 1, with blue dots on the graph, shows how the west side of the summit moved from 2016 to 2024. There is a sudden westward motion in December 2022 at the time of the Mauna Loa eruption. Point 2, with orange dots on the graph, shows how the east side of the summit moved over the same time interval, with a sudden eastward movement at the time of the eruption. 

The time series for Point 3 has a different temporal pattern. This point is on the top of the large lava flow that flowed north from the summit during the eruption, and it shows that the surface of the flow moved gradually for many months after the eruption in a direction that is away from the satellite on both tracks. This is likely a combination of downward motion due to cooling of the lava and slow motion of the viscous lava to the north on the steep slope of Mauna Loa.

This is the map and time series plots for the ascending track, where the direction from the ground to the satellite is up and west.
![Screenshot of Displacement portal showing Mauna Loa displacements on the ascending track, with time series graphs for three points near the summit.](MaunaLoa-Asc.jpg)

This is the map and time series plots for the descending track, where the direction from the ground to the satellite is up and east.
![Screenshot of Displacement portal showing Mauna Loa displacements on the descending track, with time series graphs for three points near the summit.](MaunaLoa-Desc.jpg)

Check this link to the Displacement Portal for the interactive map and [time series](https://displacement.asf.alaska.edu/#/?dispOverview=VEL&zoom=11.123&center=-155.541,19.287&flightDirs=DESCENDING&series=POINT(-155.5594093453547%2019.514142878603707)--1--Point--1b396fb4-8f31-443d-be30-03b7d25ff6c7--Series::POINT(-155.51075417202367%2019.493268696859957)--2--Point--af1bde3d-416f-470d-bad8-bedd8965c9ac--Series::POINT(-155.50216185796532%2019.602812814102023)--3--Point--b642813e-c8aa-48ba-8b4b-b3f068ff1e71--Series&start=2016-07-20T23:15:49Z&end=2024-12-30T00:16:44Z).

These products are available to download at [ASF Vertex](https://search.asf.alaska.edu/#/?dataset=OPERA-S1&productTypes=DISP-S1&resultsLoaded=true&zoom=8.413&center=-118.067,32.803&granule=OPERA_L3_DISP-S1_IW_F18904_VV_20240701T135248Z_20241228T135245Z_v1.0_20250418T055222Z&polygon=POINT(-118.3657%2033.7423)) or [NASA Earthdata](https://search.asf.alaska.edu/#/?dataset=OPERA-S1&productTypes=DISP-S1&resultsLoaded=true&zoom=8.413&center=-118.067,32.803&granule=OPERA_L3_DISP-S1_IW_F18904_VV_20240701T135248Z_20241228T135245Z_v1.0_20250418T055222Z&polygon=POINT(-118.3657%2033.7423)).
