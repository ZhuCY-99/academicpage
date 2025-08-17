---
layout: archive
title: "Interest"
permalink: /interest/
author_profile: true
---
<div id="map" style="width: 100%; height: 600px;"></div>

<!-- 引入 Mapbox GL JS -->
<link href="https://api.mapbox.com/mapbox-gl-js/v2.14.1/mapbox-gl.css" rel="stylesheet">
<script src="https://api.mapbox.com/mapbox-gl-js/v2.14.1/mapbox-gl.js"></script>
<script src="https://unpkg.com/@mapbox/mapbox-sdk/umd/mapbox-sdk.min.js"></script>
<script src="https://unpkg.com/@mapbox/togeojson"></script>

<script>
  mapboxgl.accessToken = '你的Mapbox Token';  // ⚠️换成你自己的 Mapbox Access Token

  const map = new mapboxgl.Map({
    container: 'map',
    style: 'mapbox://styles/mapbox/outdoors-v12',
    center: [116.4, 39.9],  // 默认北京
    zoom: 10
  });

  // 加载 academicpage/gpx/ 文件夹下的 gpx
  const gpxFiles = [
    "https://zhucy-99.github.io/academicpage/gpx/20250814not_outdoor_run_class_0.gpx",
    "https://zhucy-99.github.io/academicpage/gpx/20250811not_outdoor_run_class_0.gpx"
  ];

  gpxFiles.forEach(file => {
    fetch("/academicpage/" + file)
      .then(response => response.text())
      .then(gpxText => {
        const parser = new DOMParser();
        const gpxDoc = parser.parseFromString(gpxText, "application/xml");
        const geojson = toGeoJSON.gpx(gpxDoc);

        map.on('load', () => {
          map.addSource(file, {
            "type": "geojson",
            "data": geojson
          });

          map.addLayer({
            "id": file,
            "type": "line",
            "source": file,
            "layout": {
              "line-join": "round",
              "line-cap": "round"
            },
            "paint": {
              "line-color": "#ff0000",
              "line-width": 3
            }
          });
        });
      });
  });
</script>
