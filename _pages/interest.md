---
layout: archive
title: "My Interests"
permalink: /interest/
author_profile: true
---

## 地图加载测试

<div id="debug-area">
    <p>JavaScript支持测试: <span id="js-test">未测试</span></p>
    <p>Leaflet库测试: <span id="leaflet-test">未测试</span></p>
    <p>地图容器测试: <span id="container-test">未测试</span></p>
</div>

<div id="map-container">
    <div id="map" style="height: 400px; width: 100%; border: 2px solid #333; background-color: #e6e6e6;">
        <p style="text-align: center; padding-top: 180px; margin: 0;">地图加载区域</p>
    </div>
</div>

<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

<script>
// 基础JavaScript测试
document.getElementById('js-test').innerHTML = '<span style="color: green;">✓ JavaScript正常</span>';

// 等待页面完全加载
window.onload = function() {
    console.log('页面加载完成');
    
    // 测试Leaflet是否加载
    if (typeof L !== 'undefined') {
        document.getElementById('leaflet-test').innerHTML = '<span style="color: green;">✓ Leaflet已加载</span>';
        console.log('Leaflet版本:', L.version);
        
        // 测试地图容器
        var mapElement = document.getElementById('map');
        if (mapElement) {
            document.getElementById('container-test').innerHTML = '<span style="color: green;">✓ 地图容器存在</span>';
            
            try {
                // 初始化简单地图
                console.log('开始初始化地图...');
                var map = L.map('map').setView([39.9042, 116.4074], 10);
                
                // 添加图层
                L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
                    attribution: '© OpenStreetMap contributors'
                }).addTo(map);
                
                // 添加一个标记测试
                L.marker([39.9042, 116.4074]).addTo(map)
                    .bindPopup('测试标记')
                    .openPopup();
                
                console.log('地图初始化成功！');
                
                // 如果基础地图成功，再尝试加载GPX
                setTimeout(loadGPXTest, 2000);
                
            } catch (error) {
                console.error('地图初始化错误:', error);
                document.getElementById('map').innerHTML = '<p style="color: red; text-align: center; padding-top: 180px;">地图初始化失败: ' + error.message + '</p>';
            }
        } else {
            document.getElementById('container-test').innerHTML = '<span style="color: red;">✗ 地图容器未找到</span>';
        }
    } else {
        document.getElementById('leaflet-test').innerHTML = '<span style="color: red;">✗ Leaflet未加载</span>';
    }
};

// GPX加载测试函数
function loadGPXTest() {
    console.log('开始GPX测试...');
    
    // 先加载GPX插件
    var script = document.createElement('script');
    script.src = 'https://cdnjs.cloudflare.com/ajax/libs/leaflet-gpx/1.7.0/gpx.min.js';
    script.onload = function() {
        console.log('GPX插件加载成功');
        
        // 测试你的GPX文件
        if (typeof L.GPX !== 'undefined') {
            var gpx = new L.GPX('/files/gpx/20241201not_outdoor_run_class_0.gpx', {
                async: true,
                marker_options: {
                    startIconUrl: null,
                    endIconUrl: null,
                    shadowUrl: null,
                },
                polyline_options: {
                    color: 'red',
                    weight: 4,
                    opacity: 0.8
                }
            }).on('loaded', function(e) {
                console.log('GPX文件加载成功！');
                map.fitBounds(e.target.getBounds());
            }).on('error', function(e) {
                console.error('GPX文件加载失败:', e);
            }).addTo(map);
        }
    };
    script.onerror = function() {
        console.error('GPX插件加载失败');
    };
    document.head.appendChild(script);
}
</script>

## 故障排除步骤

1. **检查上面的测试结果** - 看看哪一步出现了问题
2. **打开浏览器开发者工具** (F12) 查看Console标签页的错误信息
3. **检查Network标签页** 确认所有资源都正确加载了

## 可能的解决方案

### 方案1：如果JavaScript被限制
某些Jekyll配置可能会限制JavaScript执行。

### 方案2：如果是文件路径问题
你的GPX文件路径应该是：`/files/gpx/20241201not_outdoor_run_class_0.gpx`

### 方案3：使用HTML页面替代
如果Markdown页面有问题，可以创建纯HTML页面。
