---
layout: archive
title: "Interest"
permalink: /interest/
author_profile: true
---

<div id="map" style="width:100%; height:600px; margin-bottom:20px;"></div>

<!-- Leaflet CSS & JS -->
<link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>

<!-- Leaflet-GPX 插件 -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet-gpx/1.7.0/gpx.min.js"></script>

<script>
  // 初始化地图
  var map = L.map('map').setView([39.9, 116.4], 11); // 北京为默认中心

  // 添加 OSM 底图
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      maxZoom: 19,
      attribution: '© OpenStreetMap'
  }).addTo(map);

  // GPX 文件列表
  var gpxFiles = [
    '/gpx/20241201not_outdoor_run_class_0.gpx'
    // 后续可以继续添加新的 GPX 文件
  ];

  // 循环加载 GPX
  gpxFiles.forEach(function(file){
    new L.GPX(file, {
        async: true,
        marker_options: { startIconUrl: '', endIconUrl: '', shadowUrl: '' }
    })
    .on('loaded', function(e){
        map.fitBounds(e.target.getBounds()); // 自动缩放到轨迹范围
    })
    .on('addline', function(e){
        var gpx = e.target;
        var distance = (gpx.get_distance() / 1000).toFixed(2); // km
        var time = (gpx.get_total_time() / 3600).toFixed(2);   // 小时
        var speed = (gpx.get_speed() * 3.6).toFixed(2);        // km/h
        gpx.bindPopup("<b>" + file.split('/').pop() + "</b><br>距离: " + distance + " km<br>时间: " + time + " h<br>速度: " + speed + " km/h");
    })
    .addTo(map);
  });
</script>
