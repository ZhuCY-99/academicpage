---
layout: archive
title: "My Interests"
permalink: /interest/
author_profile: true
---

<div id="map" style="height: 500px; width: 100%; border: 1px solid #ccc; margin: 20px 0;"></div>

<div id="track-info" style="background: #f8f9fa; padding: 15px; margin: 20px 0; border-radius: 5px;">
    <h4>轨迹信息</h4>
    <div id="status">正在加载地图...</div>
    <div id="gpx-details"></div>
</div>

<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet-gpx/1.7.0/gpx.min.js"></script>

<script>
function initMap() {
    var statusDiv = document.getElementById('status');
    
    function updateStatus(message) {
        console.log(message);
        statusDiv.innerHTML = message;
    }

    updateStatus('正在初始化地图...');
    
    try {
        // 创建地图
        var map = L.map('map').setView([39.9042, 116.4074], 13);
        
        // 添加OSM图层
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '© <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
        }).addTo(map);
        
        updateStatus('基础地图加载成功！正在加载GPX轨迹...');
        
        // 使用你提供的正确路径
        var gpxUrl = 'https://zhucy-99.github.io/academicpage/gpx/20241201not_outdoor_run_class_0.gpx';
        
        var gpx = new L.GPX(gpxUrl, {
            async: true,
            marker_options: {
                startIconUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.7.1/images/marker-icon-green.png',
                endIconUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.7.1/images/marker-icon.png',
                shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.7.1/images/marker-shadow.png',
            },
            polyline_options: {
                color: '#e74c3c',
                weight: 4,
                opacity: 0.8
            }
        });
        
        gpx.on('loaded', function(e) {
            updateStatus('✅ GPX轨迹加载成功！');
            
            // 获取轨迹信息
            var distance = (this.get_distance() / 1000).toFixed(2);
            var duration = this.get_duration_string();
            var elevationGain = this.get_elevation_gain().toFixed(0);
            var elevationMax = this.get_elevation_max().toFixed(0);
            var startTime = this.get_start_time();
            var endTime = this.get_end_time();
            
            // 显示轨迹详情
            document.getElementById('gpx-details').innerHTML = 
                '<h5>🏃‍♂️ 跑步轨迹详情</h5>' +
                '<div style="display: grid; grid-template-columns: 1fr 1fr; gap: 10px;">' +
                '<p><strong>总距离:</strong> ' + distance + ' km</p>' +
                '<p><strong>总时间:</strong> ' + duration + '</p>' +
                '<p><strong>爬升:</strong> ' + elevationGain + ' m</p>' +
                '<p><strong>最高海拔:</strong> ' + elevationMax + ' m</p>' +
                '<p><strong>开始时间:</strong> ' + (startTime ? new Date(startTime).toLocaleString() : '未知') + '</p>' +
                '<p><strong>结束时间:</strong> ' + (endTime ? new Date(endTime).toLocaleString() : '未知') + '</p>' +
                '</div>';
            
            // 调整地图视图到轨迹范围
            map.fitBounds(e.target.getBounds(), {padding: [20, 20]});
            
            // 添加轨迹点击事件
            this.bindPopup(
                '<div style="text-align: center; min-width: 200px;">' +
                '<h4>🏃‍♂️ 户外跑步</h4>' +
                '<p><strong>距离:</strong> ' + distance + ' km</p>' +
                '<p><strong>用时:</strong> ' + duration + '</p>' +
                '<p><strong>平均配速:</strong> ' + (duration && distance > 0 ? 
                    (this.get_duration() / 60 / parseFloat(distance)).toFixed(2) + ' min/km' : '未知') + '</p>' +
                '</div>'
            );
        });
        
        gpx.on('error', function(e) {
            updateStatus('❌ GPX文件加载失败');
            console.error('GPX loading error:', e);
            
            document.getElementById('gpx-details').innerHTML = 
                '<div style="color: red;">' +
                '<h5>GPX文件加载失败</h5>' +
                '<p>尝试加载的文件: <br><code>' + gpxUrl + '</code></p>' +
                '<p>可能的原因：</p>' +
                '<ul>' +
                '<li>CORS跨域访问限制</li>' +
                '<li>文件格式不正确</li>' +
                '<li>网络连接问题</li>' +
                '</ul>' +
                '</div>';
        });
        
        gpx.addTo(map);
        
    } catch (error) {
        updateStatus('❌ 地图初始化失败: ' + error.message);
        console.error('Map initialization error:', error);
    }
}

// 确保在DOM和所有脚本加载完成后初始化
window.addEventListener('load', function() {
    setTimeout(initMap, 500);
});
</script>

## 我的跑步轨迹

这里展示了我在2024年12月1日记录的一次户外跑步轨迹。地图会显示：

- 🔴 **红色轨迹线**: 完整的跑步路径
- 🟢 **绿色标记**: 起点
- 🔴 **红色标记**: 终点
- 📊 **详细数据**: 距离、时间、海拔等信息

点击轨迹线可以查看更多详细信息，包括平均配速等数据。

### 地图功能
- **缩放**: 使用鼠标滚轮或地图控件进行缩放
- **平移**: 拖拽地图查看不同区域  
- **信息**: 点击轨迹线查看跑步详情
