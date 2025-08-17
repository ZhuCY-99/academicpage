---
layout: archive
title: "My Outdoor Adventures"
permalink: /interest/
author_profile: true
---

<div class="map-container">
    <div class="map-controls">
        <h3>轨迹控制</h3>
        <div id="track-list">
            <!-- 轨迹列表将在这里动态生成 -->
        </div>
        <button id="fit-bounds" class="btn">查看全部轨迹</button>
        <button id="toggle-all" class="btn">显示/隐藏全部</button>
    </div>
    <div id="map"></div>
</div>

<div class="stats-container">
    <div class="stats-grid">
        <div class="stat-item">
            <h4>总轨迹数</h4>
            <span id="total-tracks">0</span>
        </div>
        <div class="stat-item">
            <h4>总距离</h4>
            <span id="total-distance">0 km</span>
        </div>
        <div class="stat-item">
            <h4>总时间</h4>
            <span id="total-time">0h 0m</span>
        </div>
    </div>
</div>

<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
     integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY="
     crossorigin=""/>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"
     integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo="
     crossorigin=""></script>

<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet-gpx/1.7.0/gpx.min.js"></script>

<script>
    // 地图初始化
    var map = L.map('map').setView([39.9042, 116.4074], 10);

    // 添加多个地图图层选项
    var osm = L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '© OpenStreetMap contributors'
    });

    var satellite = L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}', {
        attribution: 'Tiles © Esri'
    });

    var topo = L.tileLayer('https://{s}.tile.opentopomap.org/{z}/{x}/{y}.png', {
        attribution: 'Map data: © OpenStreetMap contributors, SRTM | Map style: © OpenTopoMap'
    });

    // 默认添加OSM图层
    osm.addTo(map);

    // 图层控制
    var baseMaps = {
        "街道地图": osm,
        "卫星图": satellite,
        "地形图": topo
    };

    // GPX配置
    var gpxConfig = [
        {
            url: '/gpx/20250814not_outdoor_run_class_0.gpx',
            name: '香山徒步',
            type: '徒步',
            color: '#e74c3c',
            date: '2024-03-15'
        },
        {
            url: '/gpx/20250811not_outdoor_run_class_0.gpx',
            name: '环奥森骑行',
            type: '骑行', 
            color: '#3498db',
            date: '2024-04-20'
        },
        {
            url: '/gpx/20250810not_outdoor_run_class_0.gpx',
            name: '晨跑路线',
            type: '跑步',
            color: '#2ecc71',
            date: '2024-05-10'
        }
    ];

    // 轨迹管理
    var trackLayers = {};
    var allBounds = L.latLngBounds();
    var totalDistance = 0;
    var totalDuration = 0;
    var loadedTracks = 0;

    // 创建轨迹控制面板
    function createTrackControls() {
        var trackList = document.getElementById('track-list');
        
        gpxConfig.forEach(function(config, index) {
            var trackItem = document.createElement('div');
            trackItem.className = 'track-item';
            trackItem.innerHTML = `
                <div class="track-header">
                    <input type="checkbox" id="track-${index}" checked>
                    <label for="track-${index}">
                        <span class="track-color" style="background-color: ${config.color}"></span>
                        <span class="track-name">${config.name}</span>
                    </label>
                </div>
                <div class="track-meta">
                    <span class="track-type">${config.type}</span>
                    <span class="track-date">${config.date}</span>
                </div>
                <div class="track-stats" id="stats-${index}">
                    <span class="loading">加载中...</span>
                </div>
            `;
            trackList.appendChild(trackItem);

            // 添加事件监听
            document.getElementById(`track-${index}`).addEventListener('change', function(e) {
                if (e.target.checked) {
                    map.addLayer(trackLayers[index]);
                } else {
                    map.removeLayer(trackLayers[index]);
                }
            });
        });
    }

    // 加载GPX文件
    function loadGPXFiles() {
        gpxConfig.forEach(function(config, index) {
            var gpx = new L.GPX(config.url, {
                async: true,
                marker_options: {
                    startIconUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.7.1/images/marker-icon-green.png',
                    endIconUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.7.1/images/marker-icon.png',
                    shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.7.1/images/marker-shadow.png',
                },
                polyline_options: {
                    color: config.color,
                    weight: 4,
                    opacity: 0.8
                }
            }).on('loaded', function(e) {
                console.log(`Loaded: ${config.name}`);
                
                var distance = (this.get_distance() / 1000).toFixed(2);
                var duration = this.get_duration_string();
                
                // 更新轨迹信息
                document.getElementById(`stats-${index}`).innerHTML = `
                    <span>${distance} km</span>
                    <span>${duration}</span>
                `;

                // 添加弹窗信息
                this.bindPopup(`
                    <div class="gpx-popup">
                        <h4>${config.name}</h4>
                        <p><strong>类型:</strong> ${config.type}</p>
                        <p><strong>日期:</strong> ${config.date}</p>
                        <p><strong>距离:</strong> ${distance} km</p>
                        <p><strong>时间:</strong> ${duration}</p>
                    </div>
                `);

                // 存储图层
                trackLayers[index] = this;
                map.addLayer(this);
                
                // 扩展边界
                allBounds.extend(this.getBounds());
                
                // 更新统计
                totalDistance += parseFloat(distance);
                totalDuration += this.get_duration();
                loadedTracks++;
                
                // 如果所有轨迹都加载完成
                if (loadedTracks === gpxConfig.length) {
                    updateStats();
                    // 自动调整视图
                    setTimeout(() => {
                        if (allBounds.isValid()) {
                            map.fitBounds(allBounds, {padding: [20, 20]});
                        }
                    }, 1000);
                }
                
            }).on('error', function(e) {
                console.error(`Error loading ${config.name}:`, e);
                document.getElementById(`stats-${index}`).innerHTML = '<span class="error">加载失败</span>';
            });
        });
    }

    // 更新统计信息
    function updateStats() {
        document.getElementById('total-tracks').textContent = loadedTracks;
        document.getElementById('total-distance').textContent = totalDistance.toFixed(2) + ' km';
        
        var hours = Math.floor(totalDuration / 3600);
        var minutes = Math.floor((totalDuration % 3600) / 60);
        document.getElementById('total-time').textContent = `${hours}h ${minutes}m`;
    }

    // 控制按钮事件
    document.getElementById('fit-bounds').addEventListener('click', function() {
        if (allBounds.isValid()) {
            map.fitBounds(allBounds, {padding: [20, 20]});
        }
    });

    var allVisible = true;
    document.getElementById('toggle-all').addEventListener('click', function() {
        Object.keys(trackLayers).forEach(function(index) {
            var checkbox = document.getElementById(`track-${index}`);
            checkbox.checked = !allVisible;
            
            if (checkbox.checked) {
                map.addLayer(trackLayers[index]);
            } else {
                map.removeLayer(trackLayers[index]);
            }
        });
        allVisible = !allVisible;
    });

    // 初始化
    L.control.layers(baseMaps).addTo(map);
    L.control.scale().addTo(map);
    
    createTrackControls();
    loadGPXFiles();
</script>

<style>
    .map-container {
        display: grid;
        grid-template-columns: 300px 1fr;
        gap: 20px;
        margin: 20px 0;
    }
    
    .map-controls {
        background: #f8f9fa;
        padding: 20px;
        border-radius: 8px;
        border: 1px solid #dee2e6;
    }
    
    .map-controls h3 {
        margin-top: 0;
        margin-bottom: 15px;
        color: #495057;
    }
    
    #map {
        height: 600px;
        border-radius: 8px;
        border: 1px solid #dee2e6;
    }
    
    .track-item {
        margin-bottom: 15px;
        padding: 10px;
        background: white;
        border-radius: 5px;
        border: 1px solid #e9ecef;
    }
    
    .track-header {
        display: flex;
        align-items: center;
        margin-bottom: 5px;
    }
    
    .track-header input[type="checkbox"] {
        margin-right: 8px;
    }
    
    .track-header label {
        display: flex;
        align-items: center;
        cursor: pointer;
        font-weight: 500;
    }
    
    .track-color {
        width: 16px;
        height: 16px;
        border-radius: 50%;
        margin-right: 8px;
        border: 2px solid white;
        box-shadow: 0 0 0 1px rgba(0,0,0,0.1);
    }
    
    .track-meta {
        display: flex;
        justify-content: space-between;
        font-size: 0.85em;
        color: #6c757d;
        margin-bottom: 5px;
    }
    
    .track-stats {
        font-size: 0.85em;
        color: #495057;
    }
    
    .track-stats span {
        margin-right: 10px;
    }
    
    .btn {
        background: #007bff;
        color: white;
        border: none;
        padding: 8px 16px;
        border-radius: 4px;
        cursor: pointer;
        margin: 5px 5px 5px 0;
        font-size: 0.9em;
    }
    
    .btn:hover {
        background: #0056b3;
    }
    
    .stats-container {
        margin: 20px 0;
    }
    
    .stats-grid {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
        gap: 20px;
    }
    
    .stat-item {
        text-align: center;
        padding: 20px;
        background: #f8f9fa;
        border-radius: 8px;
        border: 1px solid #dee2e6;
    }
    
    .stat-item h4 {
        margin: 0 0 10px 0;
        color: #495057;
        font-size: 0.9em;
        text-transform: uppercase;
        letter-spacing: 0.5px;
    }
    
    .stat-item span {
        font-size: 1.5em;
        font-weight: bold;
        color: #007bff;
    }
    
    .gpx-popup h4 {
        margin: 0 0 10px 0;
        color: #495057;
    }
    
    .gpx-popup p {
        margin: 5px 0;
        font-size: 0.9em;
    }
    
    .loading {
        color: #6c757d;
        font-style: italic;
    }
    
    .error {
        color: #dc3545;
    }
    
    @media (max-width: 768px) {
        .map-container {
            grid-template-columns: 1fr;
        }
        
        .map-controls {
            order: 2;
        }
        
        #map {
            height: 400px;
            order: 1;
        }
    }
</style>

## 关于我的户外活动

这个互动地图展示了我的各种户外运动轨迹。每条轨迹都有不同的颜色代表不同的活动类型：

- 🔴 **红色** - 徒步路线
- 🔵 **蓝色** - 骑行路线  
- 🟢 **绿色** - 跑步路线

你可以：
- 点击左侧的复选框来显示/隐藏特定轨迹
- 点击地图上的轨迹查看详细信息
- 使用右上角的图层控制切换地图类型
- 使用控制按钮快速查看所有轨迹或切换显示状态

### 如何添加你的轨迹

1. 将GPX文件上传到 `files/gpx/` 目录
2. 在页面代码中的 `gpxConfig` 数组里添加新的轨迹信息
3. 自定义轨迹的名称、颜色、类型和日期

轨迹数据来源于我使用GPS设备或手机应用记录的真实运动路线。
