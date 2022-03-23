---
title: vue3+TS+vite脚手架优化
date: 2022-03-13 15:37:11
tags: [javascript,vue,vite]
categories: [vue,vite]
---

#### 1、vite快速创建vue3项目

```bash
yarn create vite
npm create vite@latest
// 用yarn或者npm创建项目都可，然后根据提示选择自己需要的框架
```

#### 2、启动项目

```bash
// yarn或者npm下载依赖包
npm install
yarn
// yarn或者npm启动项目
npm run dev
yarn dev
```

#### 3、sass预处理

- sass依赖包下载

```bash
yarn add sass -D
```

- vite.config.js配置

  ```javascript
  import { defineConfig } from 'vite'
  
  export default defineConfig({
    // ...表示省略
    ...
    css: {
      preprocessorOptions:{
        scss: {
          javascriptEnabled: true
        }
      }
    },
    ...
  })
  ```

#### 4、ant-design-vue按需引入

- 依赖包下载

  ```bash
  yarn add ant-design-vue
  yarn add unplugin-vue-components -D
  ```

- Vite.config.js配置

  ```javascript
  import { defineConfig } from 'vite'
  import Components from 'unplugin-vue-components/vite'
  import { AntDesignVueResolver } from 'unplugin-vue-components/resolvers'
  
  export default defineConfig({
    // ...表示省略
    ...
    plugins: [
      ...,
      Components({
        resolvers: [
          AntDesignVueResolver()
        ]
      }),
      ...
     ]
    ...
  })
  ```

- 在项目中使用

  ```vue
  <script lang="ts" setup>
    ...
  onMounted(() => {
      initMap()
  })
  </script>
  
  <template>
      <div id="map-container" class="host-body"></div>
      <div class="content-container">
          <div>{{ toNumber(1233445.1) }}</div>
          <img class="img" src="@/assets/1.jpg" />
        // 直接使用antd组件
          <a-card title="Default size card" style="width: 300px">
              <template #extra><a href="#">more</a></template>
              <p>card content</p>
              <p>card content</p>
              <p>card content</p>
          </a-card>
      </div>
  </template>
  
  <style lang="scss" scoped>
  #map-container {
      width: 100%;
      height: 100%;
      position: absolute;
      z-index: 1;
  }
  .content-container {
      width: 100%;
      height: 100%;
      position: absolute;
      z-index: 100;
      .img {
          width: 100px;
          height: 100px;
      }
  }
  </style>
  ```

- 每使用antd组件都会在根目录下生成components.d.ts文件声明组件

  {% asset_img 1.jpg %}

#### 5、类似lodash工具包按需引入

- 下载依赖包

  ```bash
  yarn add lodash
  yarn add vite-plugin-imp -D
  ```

- Vite.config.js配置

  ````javascript
  import { defineConfig } from 'vite'
  import VitePluginImp from 'vite-plugin-imp'
  
  export default defineConfig({
    // ...表示省略
    ...
    plugins: [
      ...,
      VitePluginImp({
        optimize: true,
        // 按需引入包
        libList: [
          {
            libName: 'lodash',
            libDirectory: '',
            camel2DashComponentName: false,
            style: () => {
              return false
            },
          }
        ]
      }),
      ...
     ]
    ...
  })
  ````

- 在项目中使用

  ```typescript
  import { isArray } from 'lodash'
  
  console.log(isArray([1,2,3]))
  ```

#### 6、图片压缩

- 注意：此插件会导致打包时间稍微过长，可根据自己的选择是否配置

- 下载依赖包

  ```bash
  yarn add lodash
  yarn add vite-plugin-imp -D
  ```

- Vite.config.js配置

  ````javascript
  import { defineConfig } from 'vite'
  import viteImagemin from 'vite-plugin-imagemin'
  
  export default defineConfig({
    // ...表示省略
    ...
    plugins: [
      ...,
      // 图片压缩
      viteImagemin({
        gifsicle: {
          optimizationLevel: 7,
          interlaced: false,
        },
        optipng: {
          optimizationLevel: 7,
        },
        mozjpeg: {
          quality: 20,
        },
        pngquant: {
          quality: [0.8, 0.9],
          speed: 4,
        },
        svgo: {
          plugins: [
            {
              name: 'removeViewBox',
            },
            {
              name: 'removeEmptyAttrs',
              active: false,
            },
          ],
        },
      }),
      ...
     ]
    ...
  })
  ````

- 打包效果

  {% asset_img 2.jpg %}

#### 7、gzip打包配置

- 下载依赖包

  ```bash
  yarn add vite-plugin-compression -D
  ```

- Vite.config.js配置

  ````javascript
  import { defineConfig } from 'vite'
  import ViteCompression from 'vite-plugin-compression'
  
  export default defineConfig({
    // ...表示省略
    ...
    plugins: [
      ...,
      // gzip
      ViteCompression(),
      ...
     ]
    ...
  })
  ````

- 打包效果

  {% asset_img 3.jpg %}

#### 8、路径别名配置

- Vite.config.js配置

  ```javascript
  import { defineConfig } from 'vite'
  import ViteCompression from 'vite-plugin-compression'
  
  export default defineConfig({
    // ...表示省略
    ...
    resolve: {
      // 路径别名
      alias: {
        "@": path.resolve(__dirname, "src")
      }
    },
    ...
  })
  ```

- 若希望有智能提示，需要在`tsconfig.json`文件中做配置，若未生效可查阅资料

  ```json
  {
    "compilerOptions": {
      "target": "esnext",
      "allowSyntheticDefaultImports": true,
      "useDefineForClassFields": true,
      "module": "esnext",
      "baseUrl": "./",
      // 路径别名配置
      "paths": {
        "@/*": ["./src/*"]
      },
      "moduleResolution": "node",
      "strict": true,
      "jsx": "preserve",
      "sourceMap": true,
      "isolatedModules": true,
      "resolveJsonModule": true,
      "esModuleInterop": true,
      "lib": ["esnext", "dom"]
    },
    "include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.tsx", "src/**/*.vue"],
    "references": [{ "path": "./tsconfig.node.json" }]
  }
  
  ```

#### 9、打包生产环境去除console.log()打印，去除debugger，输出文件目录分配

- vite.config.js配置

  ```java
  import { defineConfig } from 'vite'
  import ViteCompression from 'vite-plugin-compression'
  
  export default defineConfig({
    // ...表示省略
    ...
    build: {
      minify: 'terser',
      terserOptions: {
        compress: {
          // 生产环境删除console.log()
          drop_console: true,
          drop_debugger: true
        }
      },
      rollupOptions: {
        output: {
          // 输入文件目录配置
          chunkFileNames: 'static/js/[name]-[hash].js',
          entryFileNames: 'static/js/[name]-[hash].js',
          assetFileNames: 'static/source/[name]-[hash].[ext]'
          /*
          	请勿这样配置
          	assetFileNames: 'static/[ext]/[name]-[hash].[ext]'
          	这样会导致css这样 background-image: url('@/assets/pageBg.png');引用图片打包后找不到图片，从而产生bug
          */
        }
      }
    }
    ...
  })
  ```

- 打包效果

  {% asset_img 4.jpg %}

#### 10、vite.config.ts最终配置

```javascript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import path from 'path'
import VitePluginImp from 'vite-plugin-imp'
import Components from 'unplugin-vue-components/vite'
import { AntDesignVueResolver } from 'unplugin-vue-components/resolvers'
import ViteCompression from 'vite-plugin-compression'
import viteImagemin from 'vite-plugin-imagemin'

// https://vitejs.dev/config/
export default defineConfig({
  base: "./",
  css: {
    preprocessorOptions:{
      scss: {
        javascriptEnabled: true
      }
    }
  },
  plugins: [
    vue(),
    Components({
      resolvers: [
        AntDesignVueResolver()
      ]
    }),
    VitePluginImp({
      optimize: true,
      // 按需引入包
      libList: [
        {
          libName: 'lodash',
          libDirectory: '',
          camel2DashComponentName: false,
          style: () => {
            return false
          },
        }
      ]
    }),
    // 图片压缩
    viteImagemin({
      gifsicle: {
        optimizationLevel: 7,
        interlaced: false,
      },
      optipng: {
        optimizationLevel: 7,
      },
      mozjpeg: {
        quality: 20,
      },
      pngquant: {
        quality: [0.8, 0.9],
        speed: 4,
      },
      svgo: {
        plugins: [
          {
            name: 'removeViewBox',
          },
          {
            name: 'removeEmptyAttrs',
            active: false,
          },
        ],
      },
    }),
    // gzip
    ViteCompression(),
  ],
  resolve: {
    // 路径别名
    alias: {
      "@": path.resolve(__dirname, "src")
    }
  },
  build: {
    minify: 'terser',
    terserOptions: {
      compress: {
        // 生产环境删除console.log()
        drop_console: true,
        drop_debugger: true
      }
    },
    rollupOptions: {
      output: {
        // 输入文件目录配置
        chunkFileNames: 'static/js/[name]-[hash].js',
        entryFileNames: 'static/js/[name]-[hash].js',
        assetFileNames: 'static/source/[name]-[hash].[ext]'
      }
    }
  }
})
```

