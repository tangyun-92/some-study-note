### 一、路由懒加载

#### 1、作用

提升用户体验，提升首屏组件加载速度，解决白屏问题

#### 2、代码示例

##### 2.1 未使用路由懒加载

```vue.js
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'

Vue.use(Router)
export default new Router({
  routes: [
    {
    	path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    }
  ]
})
```

##### 2.2 使用路由懒加载

```vue
import Vue from 'vue'
import Router from 'vue-router'

Vue.use(Router)
export default new Router({
  routes: [
    {
    	path: '/',
      name: 'HelloWorld',
      // 方法一：vue异步组件实现
      // component: resolve => (require(['@/components/HelloWorld'], resolve))
      // 方法二：import方法(常用)
      component: () => import('@/components/HelloWorld')
    }
  ]
})
```

### 二、组价懒加载

#### 1、代码示例

##### 1.1 原本写法

```vue
<template>
  <div class="hello">
    <hello-world></hello-world>
    111
  </div>
</template>

<script>
  import HelloWorld from './HelloWorld'
  export default {
    components: {
      HelloWorld
    },
    data() {
      return {
        msg: 'this is Vue App'
      }
    }
  }
</script>
```

##### 1.2 组件懒加载写法

```vue
<template>
  <div class="hello">
    <hello-world></hello-world>
    111
  </div>
</template>

<script>
export default {
  components: {
  	// 方法一
    'HelloWorld': () => import('./HelloWorld'),
    // 方法二
    // HelloWorld': resolve => (['./HelloWorld'], resolve)
  }
}
</script>
```

### 三、通过线上引入CDN链接

#### 1、找到项目中的index.html文件

```html
<script src='https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.min.js'></script>
```

#### 2、配置webpack.base.conf.js

在module.exports中加入以下代码

```js
externals: {
  // 键：表示导入包语法from后面跟着的名称
  // 值：表示script引入js文件时，在全局环境中的变量名称
  vue: 'Vue',
  axios: 'axios',
  'vue-router': 'Router'
}
```

### 四、gzip暴力压缩

#### 1、nginx开启gzip模式

```txt
server {
  listen 8103;
  server_name ***;
  # 开启gzip
  gzip on;
  # 进行压缩的文件类型
  gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/png image/gif;
  # 是否在http header中添加Vary: Accept-Encofing,建议开启
  gzip_vary on;
}
```

#### 2、Vue开启gzip

##### 2.1 安装依赖

npm install compression-webpack-plugin@1.1.12 --save-dev

##### 2.2 配置gzip

config --> index.js

```js
build: {
  productionGzip: true,
  productionGzipExtensions: ['js', 'css']
}
```

### 五、拓展：分析文件大的原因

利用webpack-bundle-analyzer分析器，分析项目依赖关系

build --> webpack.prod.conf.js

```js
const BunldeAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

plugin: [
	new BunldeAnalyzerPlugin(),
]
```

然后运行npm run build打包命令，浏览器会出现如下页面，此时我们就能看到哪些文件里面到底包含了什么东西

### 一、路由懒加载

#### 1、作用

提升用户体验，提升首屏组件加载速度，解决白屏问题

#### 2、代码示例

##### 2.1 未使用路由懒加载

```vue.js
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'

Vue.use(Router)
export default new Router({
  routes: [
    {
    	path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    }
  ]
})
```

##### 2.2 使用路由懒加载

```vue
import Vue from 'vue'
import Router from 'vue-router'

Vue.use(Router)
export default new Router({
  routes: [
    {
    	path: '/',
      name: 'HelloWorld',
      // 方法一：vue异步组件实现
      // component: resolve => (require(['@/components/HelloWorld'], resolve))
      // 方法二：import方法(常用)
      component: () => import('@/components/HelloWorld')
    }
  ]
})
```

### 二、组价懒加载

#### 1、代码示例

##### 1.1 原本写法

```vue
<template>
  <div class="hello">
    <hello-world></hello-world>
    111
  </div>
</template>

<script>
  import HelloWorld from './HelloWorld'
  export default {
    components: {
      HelloWorld
    },
    data() {
      return {
        msg: 'this is Vue App'
      }
    }
  }
</script>
```

##### 1.2 组件懒加载写法

```vue
<template>
  <div class="hello">
    <hello-world></hello-world>
    111
  </div>
</template>

<script>
export default {
  components: {
  	// 方法一
    'HelloWorld': () => import('./HelloWorld'),
    // 方法二
    // HelloWorld': resolve => (['./HelloWorld'], resolve)
  }
}
</script>
```

### 三、通过线上引入CDN链接

#### 1、找到项目中的index.html文件

```html
<script src='https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.min.js'></script>
```

#### 2、配置webpack.base.conf.js

在module.exports中加入以下代码

```js
externals: {
  // 键：表示导入包语法from后面跟着的名称
  // 值：表示script引入js文件时，在全局环境中的变量名称
  vue: 'Vue',
  axios: 'axios',
  'vue-router': 'Router'
}
```

### 四、gzip暴力压缩

#### 1、nginx开启gzip模式

```txt
server {
  listen 8103;
  server_name ***;
  # 开启gzip
  gzip on;
  # 进行压缩的文件类型
  gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/png image/gif;
  # 是否在http header中添加Vary: Accept-Encofing,建议开启
  gzip_vary on;
}
```

#### 2、Vue开启gzip

##### 2.1 安装依赖

npm install compression-webpack-plugin@1.1.12 --save-dev

##### 2.2 配置gzip

config --> index.js

```js
build: {
  productionGzip: true,
  productionGzipExtensions: ['js', 'css']
}
```

### 五、拓展：分析文件大的原因

利用webpack-bundle-analyzer分析器，分析项目依赖关系

build --> webpack.prod.conf.js

```js
const BunldeAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

plugin: [
	new BunldeAnalyzerPlugin(),
]
```

然后运行npm run build打包命令，浏览器会出现如下页面，此时我们就能看到哪些文件里面到底包含了什么东西

![image](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1cf80b550bb045329a259d0e83c186a9~tplv-k3u1fbpfcp-zoom-1.image "image")

