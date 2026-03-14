<template>
  <div id="earth-container" >
    <div id="cesiumContainer" ref="cesiumContainer"></div>
    <div class="control-panel">
            <h3>位置导航</h3>
            <div class="input-group">
                <label>经度:</label>
                <input type="number" v-model.number="longitude" step="0.000001" min="-180" max="180" placeholder="经度 (-180 到 180)">
            </div>
            <div class="input-group">
                <label>纬度:</label>
                <input type="number" v-model.number="latitude" step="0.000001" min="-90" max="90" placeholder="纬度 (-90 到 90)">
            </div>
            <div class="input-group">
                <label>高度(米):</label>
                <input type="number" v-model.number="height" step="1" placeholder="高度">
            </div>
            <button @click="flyToLocation">跳转</button>
        </div>

    <div class="control-panel">
        <p>查询面板</p>
    </div>
  </div>
</template>
<style scoped>
#earth-container {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  overflow: hidden;
  background-color: #000000;
}
#cesiumContainer {
  width: 100%;
  height: 100%;
}
.page-viewer {
  position: center;
  top: 10px;
  background-color: rgba(255, 255, 255, 0.8);
  padding: 5px 10px;
  border-radius: 4px;
}

.control-panel {
  position: absolute;
  top: 10px;
  right: 10px;
  background-color: rgba(255, 255, 255, 0.8);
  padding: 15px;
  border-radius: 4px;
  z-index: 100;
  width: 200px;
}

.control-panel h3 {
  margin-top: 0;
  margin-bottom: 10px;
  font-size: 16px;
}

.input-group {
  margin-bottom: 10px;
}

.input-group label {
  display: block;
  margin-bottom: 5px;
  font-size: 12px;
}

.input-group input {
  width: 100%;
  padding: 5px;
  box-sizing: border-box;
  border: 1px solid #ccc;
  border-radius: 3px;
}

.control-panel button {
  width: 100%;
  padding: 8px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 3px;
  cursor: pointer;
}

.control-panel button:hover {
  background-color: #45a049;
}

</style>
<script >
import * as Cesium from 'cesium';
import 'cesium/Build/Cesium/Widgets/widgets.css';


export default {
  data() {
        return {
            viewer: null,
            defaultAccessToken: null,
            viewer: null,
            defaultAccessToken: null,
            longitude: 116.397428, // 默认经度 (北京)
            latitude: 39.90923,   // 默认纬度 (北京)
            height: 300000,         // 默认高度
            clickedLongitude: null,
            clickedLatitude: null,
        }
    },
    methods:{
        initCesiumViewer() {
            // 确保容器存在
            const container = document.getElementById('cesiumContainer');
            if (!container) {
                console.error('Cesium容器不存在');
                return;
            }

            try {
                this.viewer = new Cesium.Viewer('cesiumContainer', {
                    infoBox: false,
                    shouldAnimate: true,
                    contextOptions: {
                        webgl: {
                            alpha: true,
                            depth: true,
                            stencil: true,
                            antialias: true,
                            premultipliedAlpha: true,
                            preserveDrawingBuffer: true,
                            failIfMajorPerformanceCaveat: true
                        }
                    },
                    geocoder: false,
                    homeButton: false,
                    sceneModePicker: false,
                    baseLayerPicker: false,
                    navigationHelpButton: false,
                    animation: false,
                    timeline: false,
                    fullscreenButton: false,
                    imageryProvider: false,
                });

                // 隐藏Cesium的logo
                this.viewer.cesiumWidget.creditContainer.style.display = 'none';
                
                // 添加自定义地图图层
                this.viewer.scene.imageryLayers.addImageryProvider(
                    new Cesium.TileMapServiceImageryProvider({
                        url: "/map/{z}/{x}/{reverseY}.jpg",
                        fileExtension: "jpg",
                        tilingScheme: new Cesium.GeographicTilingScheme(),
                    })
                );
                
                console.log('Cesium地图初始化成功');
                console.log('Cesium版本:', Cesium.VERSION);
            } catch (error) {
                console.error('Cesium地图初始化失败:', error);
            }

            const handler = new Cesium.ScreenSpaceEventHandler(this.viewer.scene.canvas);
            handler.setInputAction((click) => {
            // 获取点击位置
            const cartesian = this.viewer.camera.pickEllipsoid(click.position, this.viewer.scene.globe.ellipsoid);
            if (cartesian) {
            // 调用处理函数
            this.destinationToLocation(cartesian);
            }
            }, Cesium.ScreenSpaceEventType.LEFT_CLICK);  
        },

        flyToLocation() {
        if (!this.viewer) {
            console.error('Cesium查看器未初始化');
            return;
        }
        
        // 验证输入
        if (isNaN(this.longitude) || isNaN(this.latitude) || isNaN(this.height)) {
            alert('请输入有效的经纬度和高度');
            return;
        }
        
        if (this.longitude < -180 || this.longitude > 180) {
            alert('经度必须在 -180 到 180 之间');
            return;
        }
        
        if (this.latitude < -90 || this.latitude > 90) {
            alert('纬度必须在 -90 到 90 之间');
            return;
        }
        
        // 使用Cesium的flyTo方法跳转到指定位置
        this.viewer.camera.flyTo({
            destination: Cesium.Cartesian3.fromDegrees(this.longitude, this.latitude, this.height),
            orientation: {
                heading: Cesium.Math.toRadians(0.0),
                pitch: Cesium.Math.toRadians(-90.0),
                roll: 0.0
            },
            duration: 2 // 飞行时间（秒）
        });
        
        console.log(`跳转到位置: 经度 ${this.longitude}, 纬度 ${this.latitude}, 高度 ${this.height}`);
        },

        destinationToLocation(destination) {
            const cartographic = Cesium.Cartographic.fromCartesian(destination);

            this.clickedLongitude = Cesium.Math.toDegrees(cartographic.longitude).toFixed(6);
            this.clickedLatitude = Cesium.Math.toDegrees(cartographic.latitude).toFixed(6);

            console.log(`点击位置: 经度 ${this.clickedLongitude}, 纬度 ${this.clickedLatitude}`);
            },

        mounted() {
        // 确保Cesium的访问令牌已设置
        if (!this.defaultAccessToken) {
            console.warn('Cesium访问令牌未设置，某些功能可能无法正常工作');
        } else {
            Cesium.Ion.defaultAccessToken = this.defaultAccessToken;
        }
        
        // 设置Cesium基础路径
        window.CESIUM_BASE_URL = "/node_modules/cesium/Build/Cesium/";
        
        // 初始化Cesium查看器
        this.$nextTick(() => {
            this.initCesiumViewer();
            });
        },
    }
}

</script>