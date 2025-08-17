---
layout: archive
title: "My Interests"
permalink: /interest/
author_profile: true
---

{% include base_path %}

## 我的户外轨迹地图

<div id="map-container">
    <div id="map" style="height: 500px; width: 100%; border: 1px solid #ccc; margin: 20px 0;"></div>
</div>

<div id="track-info" style="background: #f8f9fa; padding: 15px; margin: 20px 0; border-radius: 5px;">
    <h4>轨迹信息</h4>
    <div id="status">正在加载地图...</div>
    <div id="gpx-details"></div>
</div>

<!-- 引入必要的CSS和JS -->
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" 
      integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY=" 
      crossorigin=""/>

<script>
// 动态加载脚本的函数
function loadScript(src, callback) {
    var script = document.createElement('script');
    script.type = 'text/javascript';
    script.src = src;
    script.onload = callback;
    script.onerror = function() {
        document.getElementById('status').innerHTML = '脚本加载失败: ' + src;
    };
    document.head.appendChild(script);
}

// 等待DOM完全加载
function initializeMap() {
    var statusDiv = document.getElementById('status');
    
    function updateStatus(message) {
        console.log(message);
        statusDiv.innerHTML = message;
    }

    updateStatus('正在加载Leaflet库...');
    
    // 动态加载Leaflet
    loadScript('https://unpkg.com/leaflet@1.9.4/dist/leaflet.js', function() {
        updateStatus('Leaflet加载成功，正在初始化地图...');
        
        try {
            // 创建地图
            var map = L.map('map').setView([39.9042, 116.4074], 13);
            
            // 添加OSM图层
            L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
                maxZoom: 19,
                attribution: '© <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
            }).addTo(map);
            
            // 添加测试标记
            var marker = L.marker([39.9042, 116.4074]).addTo(map);
            marker.bindPopup('<b>测试标记</b><br>地图加载成功！').openPopup();
            
            updateStatus('基础地图加载成功！正在加载GPX支持...');
            
            // 加载GPX插件
            loadScript('https://cdnjs.cloudflare.com/ajax/libs/leaflet-gpx/1.7.0/gpx.min.js', function() {
                updateStatus('GPX插件加载成功，正在加载轨迹文件...');
                loadGPXTrack(map);
            });
            
        } catch (error) {
            updateStatus('地图初始化失败: ' + error.message);
            console.error('Map initialization error:', error);
        }
    });
}

// 加载GPX轨迹
function loadGPXTrack(map) {
    var gpxUrl = '{{ base_path }}/gpx/20241201not_outdoor_run_class_0.gpx';
    
    try {
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
            document.getElementById('status').innerHTML = '✅ GPX轨迹加载成功！';
            
            // 获取轨迹信息
            var distance = (this.get_distance() / 1000).toFixed(2);
            var duration = this.get_duration_string();
            var elevationGain = this.get_elevation_gain().toFixed(0);
            var elevationMax = this.get_elevation_max().toFixed(0);
            
            // 显示轨迹详情
            document.getElementById('gpx-details').innerHTML = 
                '<h5>跑步轨迹详情</h5>' +
                '<p><strong>总距离:</strong> ' + distance + ' km</p>' +
                '<p><strong>总时间:</strong> ' + duration + '</p>' +
                '<p><strong>爬升:</strong> ' + elevationGain + ' m</p>' +
                '<p><strong>最高海拔:</strong> ' + elevationMax + ' m</p>';
            
            // 调整地图视图
            map.fitBounds(e.target.getBounds(), {padding: [20, 20]});
            
            // 添加轨迹点击事件
            this.bindPopup(
                '<div style="text-align: center;">' +
                '<h4>🏃‍♂️ 跑步轨迹</h4>' +
                '<p><strong>距离:</strong> ' + distance + ' km</p>' +
                '<p><strong>用时:</strong> ' + duration + '</p>' +
                '</div>'
            );
        });
        
        gpx.on('error', function(e) {
            document.getElementById('status').innerHTML = '❌ GPX文件加载失败';
            console.error('GPX loading error:', e);
            
            // 提供调试信息
            document.getElementById('gpx-details').innerHTML = 
                '<p style="color: red;">GPX文件加载失败，可能的原因：</p>' +
                '<ul>' +
                '<li>文件路径不正确: ' + gpxUrl + '</li>' +
                '<li>文件不存在或无法访问</li>' +
                '<li>文件格式有问题</li>' +
                '</ul>' +
                '<p>请检查文件是否存在于 <code>gpx/</code> 目录下</p>';
        });
        
        gpx.addTo(map);
        
    } catch (error) {
        document.getElementById('status').innerHTML = '❌ GPX处理出错: ' + error.message;
        console.error('GPX processing error:', error);
    }
}

// 使用多种方式确保在适当时机初始化
if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', initializeMap);
} else {
    // DOM已经加载完成
    setTimeout(initializeMap, 100);
}

// 备用初始化方法
window.addEventListener('load', function() {
    // 如果地图还没有初始化，再次尝试
    if (document.getElementById('status').innerHTML === '正在加载地图...') {
        setTimeout(initializeMap, 500);
    }
});
</script>

## 关于这个地图

这个地图展示了我记录的户外运动轨迹。目前展示的是一条跑步路线，包含了以下信息：

- **实时轨迹数据**: 从GPS设备记录的真实路径
- **详细统计**: 距离、时间、海拔变化等
- **交互功能**: 可以点击轨迹查看详情，缩放查看不同区域

### 技术说明

- 使用 **Leaflet.js** 作为地图库
- 基于 **OpenStreetMap** 提供地图数据
- 支持 **GPX格式** 的轨迹文件
- 完全兼容 **GitHub Pages** 静态托管

如果地图没有正确显示，请检查浏览器的开发者工具(F12)查看具体错误信息。
