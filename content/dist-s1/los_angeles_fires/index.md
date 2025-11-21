+++
author = "Richard West, Charlie Marshak, Talib Oliver-Cabrera, and Grace Bato"
title = "The Los Angeles Fires 2025"
date = "2025-11-02"
description = "Los Angeles Wildfires as seen in DIST-S1"
+++

![Toastt21](https://upload.wikimedia.org/wikipedia/commons/1/1a/PalisadesFire_fromDowntown.png) Image attribution [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0), via Wikimedia Commons

The Los Angeles wildfires in January 2025 devasted our city including many at JPL.
It's a hazard that many on the OPERA team were personally impacted.
Due to it's felt proximity, we were eager to see how well the DIST-S1 product fared.
The OPERA DIST-S1 product provides disturbance alerts and accumulates changes over time.
It is far from perfect, but it is able to provide all-weather, all-illumination observations.
We will see how the burn areas faired against hand drawn maps.
We will also see how the changes accumulated evolved over time.


## Status Layer

Let's first examine what the product looks like 2 months after the fire.
The most representative layer is the `DIST-GEN-STATUS` layer, which represents the disturbance delineations detected by our product. 
A description of this layer, it's labels, and color map can be found [here](https://opera-adt.github.io/dist-s1/test/pages/product_documentation/disturbance_labels/):

![dist_labels](dist-labels.png)

We overlay the perimeters extracted from the [Wildland Fire Interagency Geospatial Services](https://data-nifc.opendata.arcgis.com/datasets/nifc::wfigs-current-interagency-fire-perimeters/about), a massive database of burn perimeters within the United States. 
To get all the relevant perimeters (Eaton, Pallisades, Kenneth, Sunset), we examined +/- 20 days from January 4th, 2025 within MGRS tile 11SLT. 
Again, the status layer shown is actually 2 months after the event.
This allows us to see the full burn area and the accumulated changes detected by the product.

{{< rawhtml >}}
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script src="https://unpkg.com/pmtiles@3.0.7/dist/pmtiles.js"></script>

<div id="fire-map" style="height: 600px; width: 100%; margin: 20px 0;"></div>

<script>
(async () => {
  // Initialize map centered on Los Angeles
  const map = L.map('fire-map').setView([33.85, -118.75], 9);

  // Add base ESRI imagery tiles
  const esriImagery = L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}', {
    attribution: 'Tiles &copy; Esri &mdash; Source: Esri, i-cubed, USDA, USGS, AEX, GeoEye, Getmapping, Aerogrid, IGN, IGP, UPR-EGP, and the GIS User Community',
    maxZoom: 19
  }).addTo(map);

  // Add OpenStreetMap as alternative basemap
  const osmLayer = L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '© OpenStreetMap contributors',
    maxZoom: 19
  });

  // Load PMTiles - detect path based on current location
  const pathPrefix = window.location.pathname.includes('/dist-s1-blog/')
    ? (window.location.pathname.includes('/test/') ? '/dist-s1-blog/test' : '/dist-s1-blog')
    : '';
  const pmtilesUrl = `${pathPrefix}/map_data/los_angeles/los_angeles_2025.pmtiles`;
  const p = new pmtiles.PMTiles(pmtilesUrl);

  // Debug: Check if PMTiles loads
  p.getHeader().then(h => {
    console.log('PMTiles header:', h);
  }).catch(e => {
    console.error('Error loading PMTiles:', e);
  });

  // Custom GridLayer for PMTiles raster
  const PMTilesLayer = L.GridLayer.extend({
    createTile: function(coords, done) {
      const tile = document.createElement('img');

      // Fetch tile from PMTiles
      p.getZxy(coords.z, coords.x, coords.y).then(data => {
        if (data) {
          const blob = new Blob([data.data], { type: 'image/png' });
          const url = URL.createObjectURL(blob);
          tile.src = url;

          // Clean up blob URL after image loads
          tile.onload = () => {
            URL.revokeObjectURL(url);
            done(null, tile);
          };
          tile.onerror = () => {
            done(new Error('Tile load error'), tile);
          };
        } else {
          done(new Error('No tile data'), tile);
        }
      }).catch(err => {
        console.error('Error fetching tile:', err);
        done(err, tile);
      });

      return tile;
    }
  });

  // Add PMTiles layer to map
  const pmtilesLayer = new PMTilesLayer({
    opacity: 0.8,
    attribution: 'JPL/Caltech',
    maxZoom: 16,
    minZoom: 0
  });

  pmtilesLayer.addTo(map);

  // Load and add GeoJSON layer
  const geojsonResponse = await fetch(`${pathPrefix}/map_data/los_angeles/los_angeles_fires.geojson`);
  const geojsonData = await geojsonResponse.json();

  const geojsonLayer = L.geoJSON(geojsonData, {
    style: {
      color: '#ff0000',
      weight: 2,
      opacity: 0.8,
      fillOpacity: 0.3
    },
    onEachFeature: function(feature, layer) {
      if (feature.properties) {
        let popupContent = '<div>';
        for (const [key, value] of Object.entries(feature.properties)) {
          popupContent += `<strong>${key}:</strong> ${value}<br>`;
        }
        popupContent += '</div>';
        layer.bindPopup(popupContent);
      }
    }
  }).addTo(map);

  // Add layer control with clickable basemaps
  const baseLayers = {
    "ESRI Imagery": esriImagery,
    "OpenStreetMap": osmLayer
  };

  const overlays = {
    "DIST-S1 Status (2025-03-03)": pmtilesLayer,
    "Fire Perimeters": geojsonLayer
  };

  L.control.layers(baseLayers, overlays).addTo(map);
})();
</script>
{{< /rawhtml >}}

It's also helpful to see how the status layers change over time and what to expect.
Los Angeles is well represented by Sentinel-1 imaging, meaning that the available images are released by the European Space Agency (ESA) and we can process them.
In the time-series below, you often see images taken twice in 1 day because there is an ascending and descending track over LA.

![status-ts](status.gif)

For example, SAR is highly sensitive to rain.
Los Angeles had an unusually wet February including an [atmospheric river](https://cw3e.ucsd.edu/cw3e-event-summary-12-14-february-2025/) on
Feburary 14th on 2024.
You can see erroneous changes associated to this that are marked as alert/provisional signal and then are removed over time with confirmation process.

## Eaton Fire Zoom In

Let's take a closer look at the Eaton Fire with additional external data layers including damage indicators and as well as the DIST-S1 metric.
The **D**amage **Ins**pection (DINS) data from CalFire (here are the links for the [complete CA DINS database](https://hub-calfire-forestry.hub.arcgis.com/datasets/cal-fire-damage-inspection-dins-data/explore) and just for [Eaton](https://gis.data.ca.gov/datasets/CALFIRE-Forestry::dins-2025-eaton-public-view/about)).
One thing to note is that the status layer confirms/accumulates changes through all viewing geometries and over time.
Our confirmed layers do not capture the entire burn perimeter.
However, if we look at the metric (coming from January 21st), we can see good visual alignment with the DINS data.
The metric is a way to quantitatively measure how disturbed a pixel is.
Higher values indicate higher statistical likelihood of disturbance and lower, less. 
Visually, we scaled the metric to be between 2 and 5, so that values below 2 are set to the visual min (blue) and values above 5 are set to the visual max (yellow).


{{< rawhtml >}}
<div id="eaton-fire-map" style="height: 600px; width: 100%; margin: 20px 0;"></div>

<script>
(async () => {
  // Initialize map centered on Eaton Fire area
  const eatonMap = L.map('eaton-fire-map').setView([34.18, -118.08], 12);

  // Add base ESRI imagery tiles
  const esriImagery = L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}', {
    attribution: 'Tiles &copy; Esri &mdash; Source: Esri, i-cubed, USDA, USGS, AEX, GeoEye, Getmapping, Aerogrid, IGN, IGP, UPR-EGP, and the GIS User Community',
    maxZoom: 19
  }).addTo(eatonMap);

  // Add OpenStreetMap as alternative basemap
  const osmLayer = L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '© OpenStreetMap contributors',
    maxZoom: 19
  });

  // Load PMTiles - detect path based on current location
  const pathPrefix = window.location.pathname.includes('/dist-s1-blog/')
    ? (window.location.pathname.includes('/test/') ? '/dist-s1-blog/test' : '/dist-s1-blog')
    : '';

  // Load Eaton Fire Status PMTiles
  const eatonStatusUrl = `${pathPrefix}/map_data/los_angeles/eaton_fire_2025_status.pmtiles`;
  const eatonStatusPMTiles = new pmtiles.PMTiles(eatonStatusUrl);

  // Load Metric PMTiles
  const metricUrl = `${pathPrefix}/map_data/los_angeles/metric_20250121_subset_eaton.pmtiles`;
  const metricPMTiles = new pmtiles.PMTiles(metricUrl);

  // Custom GridLayer for PMTiles raster
  const PMTilesLayer = L.GridLayer.extend({
    initialize: function(pmtilesInstance, options) {
      L.GridLayer.prototype.initialize.call(this, options);
      this.pmtiles = pmtilesInstance;
    },
    createTile: function(coords, done) {
      const tile = document.createElement('img');

      // Fetch tile from PMTiles
      this.pmtiles.getZxy(coords.z, coords.x, coords.y).then(data => {
        if (data) {
          const blob = new Blob([data.data], { type: 'image/png' });
          const url = URL.createObjectURL(blob);
          tile.src = url;

          // Clean up blob URL after image loads
          tile.onload = () => {
            URL.revokeObjectURL(url);
            done(null, tile);
          };
          tile.onerror = () => {
            done(new Error('Tile load error'), tile);
          };
        } else {
          done(new Error('No tile data'), tile);
        }
      }).catch(err => {
        console.error('Error fetching tile:', err);
        done(err, tile);
      });

      return tile;
    }
  });

  // Add Eaton Status PMTiles layer
  const eatonStatusLayer = new PMTilesLayer(eatonStatusPMTiles, {
    opacity: 0.8,
    attribution: 'JPL/Caltech',
    maxZoom: 16,
    minZoom: 0
  });

  eatonStatusLayer.addTo(eatonMap);

  // Add Metric PMTiles layer (initially hidden)
  const metricLayer = new PMTilesLayer(metricPMTiles, {
    opacity: 0.8,
    attribution: 'JPL/Caltech',
    maxZoom: 16,
    minZoom: 0
  });

  // Load Eaton Perimeter GeoJSON
  const eatonPerimeterResponse = await fetch(`${pathPrefix}/map_data/los_angeles/eaton_perimeter.geojson`);
  const eatonPerimeterData = await eatonPerimeterResponse.json();

  const eatonPerimeterLayer = L.geoJSON(eatonPerimeterData, {
    style: {
      color: '#ff0000',
      weight: 2,
      opacity: 0.8,
      fillOpacity: 0.1
    },
    onEachFeature: function(feature, layer) {
      if (feature.properties) {
        let popupContent = '<div>';
        for (const [key, value] of Object.entries(feature.properties)) {
          popupContent += `<strong>${key}:</strong> ${value}<br>`;
        }
        popupContent += '</div>';
        layer.bindPopup(popupContent);
      }
    }
  }).addTo(eatonMap);

  // Load DINS Eaton GeoJSON (now in EPSG:4326)
  const dinsEatonResponse = await fetch(`${pathPrefix}/map_data/los_angeles/dins_eaton_formatted.geojson`);
  const dinsEatonData = await dinsEatonResponse.json();

  const dinsEatonLayer = L.geoJSON(dinsEatonData, {
    pointToLayer: function(feature, latlng) {
      // Color based on damage level
      let color = '#00ff00'; // Default green for No Damage
      const damage = feature.properties.Damage || '';
      if (damage.includes('Destroyed')) {
        color = '#ff0000'; // Red
      } else if (damage.includes('Major') || damage.includes('Minor')) {
        color = '#ff8800'; // Orange
      } else if (damage.includes('Affected')) {
        color = '#ffff00'; // Yellow
      }

      return L.circleMarker(latlng, {
        radius: 4,
        fillColor: color,
        color: '#000',
        weight: 1,
        opacity: 0.8,
        fillOpacity: 0.7
      });
    },
    onEachFeature: function(feature, layer) {
      if (feature.properties) {
        let popupContent = '<div>';
        for (const [key, value] of Object.entries(feature.properties)) {
          popupContent += `<strong>${key}:</strong> ${value}<br>`;
        }
        popupContent += '</div>';
        layer.bindPopup(popupContent);
      }
    }
  }).addTo(eatonMap);

  // Add layer control
  const baseLayers = {
    "ESRI Imagery": esriImagery,
    "OpenStreetMap": osmLayer
  };

  const overlays = {
    "Eaton Fire Status (2025-03-03)": eatonStatusLayer,
    "Metric (2025-01-21)": metricLayer,
    "Eaton Fire Perimeter": eatonPerimeterLayer,
    "Damage Indicators (DINS)": dinsEatonLayer
  };

  L.control.layers(baseLayers, overlays).addTo(eatonMap);

  // Add legend for DINS damage indicators
  const legend = L.control({ position: 'bottomright' });

  legend.onAdd = function(map) {
    const div = L.DomUtil.create('div', 'info legend');
    div.style.backgroundColor = 'white';
    div.style.padding = '10px';
    div.style.border = '2px solid rgba(0,0,0,0.2)';
    div.style.borderRadius = '5px';
    div.style.fontSize = '12px';

    const grades = [
      { label: 'Destroyed (>50%)', color: '#ff0000' },
      { label: 'Major/Minor (10-50%)', color: '#ff8800' },
      { label: 'Affected (1-9%)', color: '#ffff00' },
      { label: 'No Damage', color: '#00ff00' }
    ];

    div.innerHTML = '<strong>DINS Damage Level</strong><br>';

    for (let i = 0; i < grades.length; i++) {
      div.innerHTML +=
        '<div style="display: flex; align-items: center; margin-top: 5px;">' +
        '<i style="background:' + grades[i].color + '; width: 18px; height: 18px; opacity: 0.7; border: 1px solid #000; border-radius: 50%; margin-right: 8px; flex-shrink: 0;"></i>' +
        '<span>' + grades[i].label + '</span>' +
        '</div>';
    }

    return div;
  };

  legend.addTo(eatonMap);
})();
</script>
{{< /rawhtml >}}

