---
layout: archive
title: "Interest"
permalink: /interest/
author_profile: true
---

<div id="map" style="height:600px; margin-bottom:20px;"></div>

<!-- 引入 Leaflet -->
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

<!-- 引入 leaflet-gpx 插件 -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet-gpx/1.7.0/gpx.min.js"></script>

<script>
// 初始化地图
var map = L.map('map').setView([39.9, 116.4], 11);  // 默认北京中心，可改为你常骑的地方坐标

// 添加底图
L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap'
}).addTo(map);

// 假设你把 gpx 文件都放在 /gpx/ 文件夹下
var gpxFiles = [
  "20241201not_outdoor_run_class_0.gpx",
  // 未来可以继续往这里加
];

// 循环加载每个轨迹
gpxFiles.forEach(function(file) {
  new L.GPX(file, {
      async: true,
      marker_options: {
        startIconUrl: null,
        endIconUrl: null,
        shadowUrl: null
      }
  }).on('loaded', function(e) {
      map.fitBounds(e.target.getBounds()); // 自动缩放
  }).on('addline', function(e) {
      var gpx = e.target;
      var name = gpx.get_name() || file;
      var distance = (gpx.get_distance() / 1000).toFixed(2) + " km";
      var time = (gpx.get_total_time() / 3600).toFixed(2) + " h";
      var speed = (gpx.get_average_speed() * 3.6).toFixed(2) + " km/h";
      gpx.bindPopup("<b>" + name + "</b><br>距离: " + distance + "<br>时间: " + time + "<br>均速: " + speed);
  }).addTo(map);
});
</script>
