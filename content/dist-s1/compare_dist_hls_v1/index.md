+++
author = "Talib Oliver Cabrera, Charlie Marshak, Amy Pickens, and Richard West"
title = "Comparing DIST-S1 and DIST-HLS over Non-Hazards"
date = "2026-02-11"
description = "Comparing the OPERA optical and SAR disturbance product"
+++

This blog post qualitiatively compares the OPERA Sentinel-1 disturbance product with the OPERA DIST-HLS products across a few specific sites.
In performing this comparison, we provide a perspective on the capabilities, strengths, and limits of the Sentinel-1 product.
The DIST-HLS has two disturbance products: the generic product (`GEN-STATUS`) and vegetation product (`VEG-STATUS`).
The DIST-S1 is a generic change product and we refer to it as `DIST-S1` hereafter.
The DIST-HLS `VEG-STATUS` first models "vegetation cover" and then delineates changes when a pixel undergoes *vegetation cover loss*.
We highlight that vegetation *gain* is not considered.
The DIST-HLS `GEN-STATUS` is the *statistical* deviation of the recent acquisition with respect to the baseline of the images capturing *generic* change.
While both DIST-HLS disturbance products are validated carefully, there are no formal requirements for the `GEN-STATUS`.
The `DIST-S1` product delineates disturbances based on *statistical* variations in a very similar fashion to DIST-HLS's `GEN-STATUS`.
When comparing the above products (`DIST-S1`, `VEG-STATUS`, `GEN-STATUS`), we share all three product layers.

# Important Thematic Differences betwee DIST-S1 and DIST-HLS

Before jumping in to the interactive maps, we want to highlight a few items that will be prevelant across all comparisons.

- Statistical measures of disturbance (i.e. `DIST-S1` and `GEN-STATUS`) will see any deviation from the mean as a disturbance. *Vegetation loss*, however, measures a specific type of disturbance. For example, generic changes should capture vegetation *growth* or structural changes within a cleared area. However, vegetation loss delineations (e.g. `VEG-STATUS`) will not delineate these disturbances.
- The High/Low alerts for statistical measures of disturbance (i.e. `DIST-S1` and `GEN-STATUS`) indicate the likelihood of disturbance. That is to say, a "high confidence" alert is much less likely than a "low confidence" alert, where we are analyzing these probabilities using the baseline images. Reframing using the complement, a "high confidence" alert is more likely to be an anomaly than a "low confidence" alert. For the `VEG-STATUS`, there is a thematic view of "high" and "low": "High" indicates over >50% vegetation cover loss in a pixel and low indicates  <50%. In particular, statistical "high"/"low" have different meainings than those in `VEG-STATUS`. For example, a disturbance that removes <50% vegetation cover could be viewed as a highly unlikely with respect to a statistical measure.
- The `DIST-S1` product uses a transformer model to estimate the mean and standard deviation of the set of baseline imagery. At a high level, the DIST-S1 model is accounting for the temporal ordering of the baseline imagery and therefore more recent images are more likely to contribute to accurate parameter estimates than those images acquired further in the past. The `VEG-STATUS` estimates the baseline vegetation cover by compositing all of the baseline images together and estimating the vegetation cover from the composite. Similarly, the `GEN-STATUS` estimates per-pixel normal parameters via standard sample estimation. In particular, the DIST-HLS products do not consider temporal ordering.
- While the disturbance delineations may align in the different scenarios, the acquisition mode of sidelooking SAR fundamentally contrasts the down-looking optical data and a disturbance area in SAR can appear to be "smoothed" compared to HLS.
- DIST-HLS has a sampling rate of 2-3 days whereas DIST-S1 has a sampling rate of about 1 week. However, Sentinel-1 does not regularly distribute all the data that it can, except over Europe and parts of North America. That said, there are instances of high latitude where scenes are too dark for HLS to distribute. Additionally, when comparing and confirming disturbances delineations from Sentinel-1 over time via different acquisition geometries (i.e. ascending and descending passes), it is harder to confirm (i.e. verify over time) disturbances particularly if disturbances are visible in one acquisition geometry and not the other. 
- If there is alignment for a particular disturbance, HLS is able to detect "finer" features than SAR, i.e. features that are 1-2 pixels in width like a clearing for a road or path in a forest.
- SAR is sensitive to structure as opposed to spectral changes (like "green"-ness). For example, sparse green vegetation that is cleared will *not* appear as disturbance in SAR and steep inclines will sometimes prevent accurate delineations from being identified on such terrain.
- SAR is sensitive to soil moisture, rain, snow, and floating vegetation/sediment. If these appear in a recent acquisition and represent deviations from the baseline, these will show up as provisional changes. If they are transient disturbances, these delineations will be filtered out over time.
- The confirmation process provides a means to "filter" out changes that are not consistently observed in time. However, this process all allows for resets of changes if a change is "confirmed", then returns to it's baseline as "finished", and then new changes are detected. If this cycle occurs, then changes that were confirmed are not accumulated. For `DIST-S1`, where regrowth, urban development, and moisture sensitivities are detected, there can appear changes that are confirmed but not carried over in the most recent `DIST-S1` product.

[Dr. Amy Pickens](https://geog.umd.edu/facultyprofile/pickens/amy) selected mant of the sites below for informal comparisons except those related to a specific hazard. Most of the data represents changes detected in products generated on or before August 1st, 2025. We used 4 months of data products prior to a given date to confirm confirm the changes in the products shown.


# Examples 

## Logging

### Central Sweeden

Below is a portion of a managed forest in Gävleborg County, Sweeden.

{{< pmtiles-map id="sweden-map" >}}
[ 
  {"url": "/map_data/compare_dist_hls_v1/sweeden_logging_example/OPERA_L3_DIST-ALERT-S1_T33VWJ_20250729T162213Z_20251005T203603Z_S1A_30_v0.1_GEN-DIST-STATUS_subset.pmtiles", "label": "DIST-S1 (2025-07-29)"},
  {"url": "/map_data/compare_dist_hls_v1/sweeden_logging_example/OPERA_L3_DIST-ALERT-HLS_T33VWJ_20250801T104041Z_20250814T031730Z_S2A_30_v1_VEG-DIST-STATUS_subset.pmtiles", "label": "DIST-HLS Vegetation (2025-08-01)"},
  {"url": "/map_data/compare_dist_hls_v1/sweeden_logging_example/OPERA_L3_DIST-ALERT-HLS_T33VWJ_20250801T104041Z_20250814T031730Z_S2A_30_v1_GEN-DIST-STATUS_subset.pmtiles", "label": "DIST-HLS Generic (2025-08-01)"}
]
{{< /pmtiles-map >}}

Things to note:

- There is a lot of alignment between `VEG-STATUS` and `DIST-S1`. We note that the large "high" confidence alerts in `DIST-S1` are "low" alerts in `VEG-STATUS`. This means that this was "unlikely" with respect to the baseline collected from S1, but the modeled vegetation cover within said pixels is <50%. 
- The larger disturbances delineated in `DIST-S1` not present in `VEG-STATUS` appear over bare areas with respect to the base satellite imagery. We speculate such areas corresponds to recent vegetation growth that is by definition not in `VEG-STATUS`.
- The DIST-HLS products contain some finer features like trimming forestands at the edges.


## Mining

### Indonesia

Below is a nickel mine on Weda Bay in Indonesia.

{{< pmtiles-map id="indonesia-map" >}}
[
  {"url": "/map_data/compare_dist_hls_v1/indonesia_mining_example/OPERA_L3_DIST-ALERT-S1_T52NCF_20250728T210950Z_20260205T183920Z_S1A_30_v0.1_GEN-DIST-STATUS_subset.pmtiles", "label": "DIST-S1 (2025-07-28)"},
  {"url": "/map_data/compare_dist_hls_v1/indonesia_mining_example/OPERA_L3_DIST-ALERT-HLS_T52NCF_20250730T015641Z_20250815T152112Z_S2A_30_v1_VEG-DIST-STATUS_subset.pmtiles", "label": "DIST-HLS Vegetation (2025-07-30)"},
  {"url": "/map_data/compare_dist_hls_v1/indonesia_mining_example/OPERA_L3_DIST-ALERT-HLS_T52NCF_20250730T015641Z_20250815T152112Z_S2A_30_v1_GEN-DIST-STATUS_subset.pmtiles", "label": "DIST-HLS Generic (2025-07-30)"}
]
{{< /pmtiles-map >}}

Things to note:

- The process of mining in this area undergoes the cycle: vegetation clearing and then structural build-up. We note that statistical/generic measures will detect both, but vegetation loss delineations (`VEG-STATUS`) will only detect the first.
- We can see build up of various mining areas switching between the basemaps (ESRI and Google Satellite).

## Development and Road Expansion

### Northeastern China

This scene is capturing some of the recent development that is going on throughout China on a regular basis.
This scene shows the conversion of green areas to buildings with solar paneled roofs as well as road expansion.

{{< pmtiles-map id="china-map" >}}
[
  {"url": "/map_data/compare_dist_hls_v1/china_road_expansion_example/OPERA_L3_DIST-ALERT-S1_T50SQE_20250720T100431Z_20260205T184044Z_S1A_30_v0.1_GEN-DIST-STATUS_subset.pmtiles", "label": "DIST-S1 (2025-07-20)"},
  {"url": "/map_data/compare_dist_hls_v1/china_road_expansion_example/OPERA_L3_DIST-ALERT-HLS_T50SQE_20250729T024611Z_20250815T053457Z_S2C_30_v1_VEG-DIST-STATUS_subset.pmtiles", "label": "DIST-HLS Vegetation (2025-07-29)"},
  {"url": "/map_data/compare_dist_hls_v1/china_road_expansion_example/OPERA_L3_DIST-ALERT-HLS_T50SQE_20250729T024611Z_20250815T053457Z_S2C_30_v1_GEN-DIST-STATUS_subset.pmtiles", "label": "DIST-HLS Generic (2025-07-29)"}
]
{{< /pmtiles-map >}}

Things to note:

- you can see some of the development at high resolution looking at the Google Maps and then switching to ESRI Imagery
- The DIST-HLS product shows the creation of a narrow road or railway being built. However, it seems like this is ongoing and the changes are not confirmed in either `VEG-STATUS` nor `GEN-STATUS`.
- The `DIST-S1` product is very good at seeing the construction of buildings with solar panelled roofs.
