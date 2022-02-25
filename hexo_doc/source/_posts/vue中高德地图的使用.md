---
title: vue3中3D高德地图的使用
date: 2022-02-18 17:34:25
tags: [javascript,vue]
categories: vue
---

## vue3中3D高德地图的使用

#### 1、注册高德地图账号，并创建新应用

  {% asset_img 2.jpg %}

#### 2、创建应用后新建key，注意选择Web端(JS API)服务平台

  {% asset_img 3.jpg %}

#### 3、获取到key值和安全密钥

  {% asset_img 4.jpg %}

#### 4、下载高德地图插件的npm包@amap/amap-jsapi-loader

  ```bash
  npm i @amap/amap-jsapi-loader --save
  ```

#### 5、在你的index.vue文件中创建div容器

  ```vue
  <template>
       <div id="container"></div>
  </template>
  ```

#### 6、设置地图容器的css样式

  ```vue
  <style  scoped>
      #container{
          padding:0px;
          margin: 0px;
          width: 100%;
          height: 800px;
      }
  </style>
  ```

#### 8、在你的utils工具方法目录下的Amap.ts文件中封装地图实例的方法

  ```javascript
  import AMapLoader from '@amap/amap-jsapi-loader';
  
  export const getAMap = (id = 'container', config = {}) => {
    return new Promise((resolve, reject) => {
      AMapLoader.load({
          key:'你的key值', //需要设置您申请的key
          version:"1.4.15",
          plugins:['Map3D'],
      })
        .then((AMap) => {
          const map = new AMap.Map(id, {
              viewMode:"3D",
              rotation: 0,
              pitch: 70,
              mapStyle: '你自己的自定义样式的链接',
              zoom:17,
              zooms: [2, 20],
              center: [121.510909,31.233366],
              ...config
          })
          resolve({
            map,
            AMap
          });
        })
        .catch((e) => {
          console.log('高德地图错误', e)
          reject(e)
        })
    })
  }
  ```

#### 9、在你的pubilc目录下的index.html文件中设置安全密钥

  ```html
  <!DOCTYPE html>
  <html lang="">
    <head>
      <meta charset="utf-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width,initial-scale=1.0">
      <link rel="icon" href="<%= BASE_URL %>favicon.ico">
      <title><%= htmlWebpackPlugin.options.title %></title>
    </head>
    <body>
      <noscript>
        <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
      </noscript>
      <div id="app"></div>
      <!-- built files will be auto injected -->
    </body>
    <script>
      // 设置高德地图的安全密钥
      window._AMapSecurityConfig = { securityJsCode: '你的安全密钥' }
    </script>
  </html>
  ```

#### 10、在你的index.vue文件中调用封装的方法绘制地图

  ```vue
  <template>
    <div id="container" class="host-body">
    </div>
  </template>
  
  <script lang="ts">
  import {
    onMounted,
  } from 'vue'
  import { shallowRef } from '@vue/reactivity'
  import { getAMap } from '../../utils/Amap'
  
  export default defineComponent({
    setup() {
      const map = shallowRef<any>(null)
      // 生命周期
      onMounted(() => {
          initMap()
      })
  
      const initMap = async() => {
        let mymap: any = await getAMap()
        map.value = mymap.map
        // 后边这部分代码可以不需要，剩余代码为导入3D模型gltf文件
        let AMap = mymap.AMap
          var object3Dlayer = new AMap.Object3DLayer();
          map.value.add(object3Dlayer);
          // var druckMeshes;
          map.value.plugin(["AMap.GltfLoader"], () => {
              var urlDuck = 'https://a.amap.com/jsapi_demos/static/gltf-online/shanghai/scene.gltf';
              var paramDuck = {
                  position: new AMap.LngLat(121.510909,31.233366), // 必须
                  scale: 3580, // 非必须，默认1
                  height: 1800,  // 非必须，默认0
                  scene: 0, // 非必须，默认0
              };
              var gltfObj = new AMap.GltfLoader();
              gltfObj.load(urlDuck, (gltfDuck: {
                  setOption: (arg0: {
                      position: any; // 必须
                      scale: number; // 非必须，默认1
                      height: number; // 非必须，默认0
                      scene: number;
                  }) => void; rotateX: (arg0: number) => void; rotateZ: (arg0: number) => void;
              }) => {
                  // druckMeshes = gltfDuck;
                  gltfDuck.setOption(paramDuck);
                  gltfDuck.rotateX(90);
                  gltfDuck.rotateZ(120);
                  object3Dlayer.add(gltfDuck);
              });
        })
      }
  
      // return
      return {
        map
      }
    }
  })
  </script>
  
  <style lang="scss" scoped>
  @import '@/assets/scss/index.scss';
  #container {
      width: 100%;
      height: 100%;
      position: relative;
      z-index: 1;
      
  }
  </style>
  ```

#### 11、效果展示

  {% asset_img 1.jpg %}

