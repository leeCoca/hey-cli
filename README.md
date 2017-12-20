# hey-cli
webpack脚手架，hot-dev-server，build。
不需要理解webpack，只需要知道如何配置就可以使用，摆脱繁琐重复的webpack配置。   


## 特性
- 一次全局安装，所有的开发项目都支持，不需要每个项目都安装配置webpack。    
- 支持<code>ES6</code>
- 支持热替换
- 支持反向代理
- 默认支持<code>vue2.0</code>，支持<code>react</code>
- 构建UMD模式的代码。
- 只需要配置<code>hey.js</code>配置文件即可使用

## 安装

```sh
npm install -g hey-cli
```

## 配置

在项目根目录下添加hey.js配置文件。 
```js
module.exports = {
	//端口号
  "port": 9002,
  "dist": 'dist', //生成文件的根目录
  "timestamp": false, //build生成的static文件夹是否添加时间戳
  "react": true, //支持react项目
	//webpack相关配置    
  "webpack": {
    "console": false, //打包压缩是否保留console，默认为false
    "sourceMap" : false, //打包的时候要不要保留sourceMap, 默认为false
    //公开path
    "publicPath": "/", 

    "output": {
      //输出哪些文件，主要是html，默认会加载和html文件名一样的js文件为入口。支持定义公用包。
      "./*.html": {
        "entry":"./src/index.js", //默认加载js文件，并且html自动引用。如果没有配置，则自动加载与html文件名同样的js文件。
        "commons": [
          "common"
        ]
      }
    },

    //公共包定义，可以定义多个
    "commonTrunk": {
      "common": [
        "jquery",
        "vue",
        "vuex",
        "manba",
        "jsoneditor"
      ]
    },

    //定义假名
    "resolve": {
      "alias": {}
    },

    //定义全局变量
    "global": {
      "Vue": "vue",
      "$": "jquery",
      "log": "./js/common/log"
    },

    //定义反向代理服务器
    "devServer": {
      "proxy": {
        //设定/api开头的url向定义的接口请求
        "/api": {
          "target": "http://yoda:9000"
        }
      },
      historyApiFallback: true
    },
    //定义外部引用
    "externals":{

    },

    //定义全局less参数定义，可以在任意less中使用参数
    globalVars: './static/css/var.less',
  },

  //未做关联引用的文件在build的时候复制到打包的文件夹中
  "copy": [
    "./images/**/*",
    "./help/**/*",
    "./template/**/*"
  ]
};
```

### 扩展配置
可以在hey.js中webpack配置项中扩张配置以下属性：
- plugins
- module
- node
- externals
- devServer
具体使用，请参照[webpack](https://webpack.js.org/)文档.

## 示例

### 加载vue,vue-router

```json
"hey": {
  "port": 9008,
  "timestamp": true,
  "dist": "gen",
  "webpack": {
    "publicPath": "/",
    "output": {
      "./*.html": {
        "entry":"./src/app",
        "common":["common"]
      }
    },
    "commonTrunk": {
      "common":["vue","vue-router"]
    },
    "global": {
      "Vue": "vue"
    },
    "devServer": {
      "historyApiFallback":true
    }
  },
  "copy":["./static/images/**/*"]
}
```
### 外部加载vue,vue-router  

```json
"hey": {
  "port": 9008,
  "timestamp": true,
  "dist": "gen",
  "webpack": {
    "publicPath": "/",
    "output": {
      "./*.html": {
        "entry":"src/app"
      }
    },
    "global": {
      "Vue": "vue"
    },
    "devServer": {
      "historyApiFallback":true
    },
    "externals": {
      "Vue": "window.Vue",
      "VueRouter": "window.VueRouter"
    }
  },
  "copy":["./static/images/**/*"]
}
```

### 构建UMD模式的公用代码
主要用于构建一些的公用代码，简单配置即可使用。  
*由于是打包成UMD模式的公用包，请不要使用import模式。*

```js
module.exports = {
  dist: "build",
  webpack: {
    umd: {
      entry: "./src/index.js",
      library: "Validator",
      filename: 'validator.js' //build后将生成/build/validator.js
    },
    externals: {
      "manba": "manba"  //该依赖包将不会打包进源码中
    }
  }
};
```

## 执行

启动webpack服务器

```sh
hey dev
```

打包项目，支持hash文件，按需加载。  
配置文件中使用<code>timestamp</code>属性，可以生成static[hash]命名的文件夹，这样可以防止所有版本的文件汇聚在一个文件夹。

```sh
hey build
```

## 注意事项

对于vue的项目，项目本身需要安装与vue版本对应的 <code>vue-template-compiler</code> 包。  
如果您的项目使用的是最新版本的vue，则请使用以下命令执行安装  

```sh
npm install --save vue
npm install --save-dev vue-template-compiler
```
如果您的项目使用的是固定版本的vue，则请使用以下命令执行安装  
```sh
npm install --save vue@版本号
npm install --save-dev vue-template-compiler@版本号
```
