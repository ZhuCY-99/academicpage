---
layout: archive
title: "Interest"
permalink: /interest/
author_profile: true
---

<div id="map" style="height: 600px;"></div>

<!-- 引入 Leaflet 样式和脚本 -->
<link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
<script src="https://unpkg.com/leaflet-gpx"></script>

<script>
  // 初始化地图
  var map = L.map('map').setView([39.9, 116.4], 10); // 默认北京，可根据需要改

  // 加载底图（OpenStreetMap）
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '&copy; OpenStreetMap contributors'
  }).addTo(map);

  // 加载 GPX 文件
  var gpxFiles = [
    {% for file in site.static_files %}
      {% if file.path contains '/gpx/' and file.extname == '.gpx' %}
        "{{ file.path }}",
      {% endif %}
    {% endfor %}
  ];

  gpxFiles.forEach(function(gpx) {
    new L.GPX(gpx, {
      async: true,
      polyline_options: { color: 'blue', weight: 3, opacity: 0.75 }
    }).on('loaded', function(e) {
      var gpxLayer = e.target;
      map.fitBounds(gpxLayer.getBounds());
      // 添加属性 popup
      var info = `
        <b>轨迹文件:</b> ${gpx.split('/').pop()}<br/>
        <b>开始时间:</b> ${gpxLayer.get_start_time()}<br/>
        <b>总长度:</b> ${(gpxLayer.get_distance() / 1000).toFixed(2)} km<br/>
        <b>平均速度:</b> ${(gpxLayer.get_speed() * 3.6).toFixed(2)} km/h
      `;
      gpxLayer.bindPopup(info);
    }).addTo(map);
  });
</script>
