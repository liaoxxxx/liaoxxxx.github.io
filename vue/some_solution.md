##各种错误的解决方案

###1.  运行: npm run dev ,报错误：  missing script: dev  ；dev 命令 不存在
````text
npm ERR! missing script: dev

npm ERR! A complete log of this run can be found in:
npm ERR!     /home/liaoxx/.npm/_logs/2020-01-16T09_13_16_773Z-debug.log
````

>解决方案： 在项目的根目录 的package.josn文件的 'scripts' 项注册 dev 命令

````text
{
  "scripts": {
    "dev": "vue-cli-service serve",
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
  },
}
````

****************************************************

###2. 读取/config/dev.env.js 或prod.env.js 等配置文件所配置的常量为 undefined， 

#####2.1 如 dev.env.js
````js   

'use strict'
const merge = require('webpack-merge')
const prodEnv = require('./prod.env')

module.exports = merge(prodEnv, {
  NODE_ENV: '"development"',
  API_HOST:'"http://localhost:8081/"',
  SERVER_HOST: '"http://localhost:8081"'  //刚配置的文件
})

````

#####2.2 在代码中 获得配置的常量  结果为： undefined

````js
	console.log(process.env.SERVER_HOST)   //结果为 undefined;
````


#####2.3 原因 在dev 开发环境中 ，修改 /config 目录下的文件并没有重新 引入编译，所以无法获取配置的常量值



#####2.4 解决方案 重新运行 npm run dev  或其他的相应的命令


************************************

### 3. 切勿使用内置或保留的HTML元素作为组件ID

````

	//不要使用内置或保留的HTML元素作为组件ID
   Do not use built-in or reserved HTML elements as component id: header
 
````


> 例如 

```
<template>
<div class="header-row-fixed">
</div>
</template>
<script>
export default {
  name: 'header',  //header 是内置保留的标签
  data () {
    return {
    }
  },

  methods: {
   
  }
}
</script>

```


### 4. vue 打包生产环境报错 `UnhandledPromiseRejectionWarning: CssSyntaxError:`

#### 报错信息

```

C:\coding\jiucheng\200520tianan\jinmantang-vue>npm run build

> jinmantang@1.0.0 build C:\coding\jiucheng\200520tianan\jinmantang-vue
> node build/build.js

| building for production...Error processing file: vue_static/css/app.4574041e9c9954f9292855c5f6d7a41f.css
(node:6496) UnhandledPromiseRejectionWarning: CssSyntaxError: C:\coding\jiucheng\200520tianan\jinmantang-vue\vue_static\css\app.4574041e9c9954f9292855c5f6d7a41f.css:13286:1: Unknown word

```

#### 解决方案
> 注释webpack 配置 /build/webpack.pro.conf.js

```

   // 是否允许 css 行内注释
   /* new OptimizeCSSPlugin({
      cssProcessorOptions: config.build.productionSourceMap
        ? { safe: true, map: { inline: false } }
        : { safe: true }
    }),*/
```