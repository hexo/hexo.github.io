---
title: webpack学习笔记
date: 2016-10-17 11:00:54
tags:
categories: 前端
---
### webpack的优势
webpack是以commonJS的形式来书写脚本的，但对AMD/CMD的支持也很全面，方便旧项目进行代码迁移
开发便捷，能替代部分grunt/gulp的工作，比如打包，压缩混淆，图片转base64等
扩展性强，插件机制完善，特别是支持react热插拔（react-hot-loader）

### 安装和配置

#### 安装
```
npm install webpack -g
```

#### 配置
每个项目下都必须有一个webpack.config.js，它的作用类似常规的gulpfile.js/gruntfile.js,就是一个配置项，告诉webpack它需要做什么
```
var webpack = require('webpack');
var commonsPlugin = new webpack.optimize.CommonsChunkPlugin('common.js');

module.exports = {
    //插件项
    plugins: [commonsPlugin],
    //页面入口文件配置
    entry: {
        index : './src/js/page/index.js'
    },
    //入口文件输出配置
    output: {
        path: 'dist/js/page',
        filename: '[name].js'
    },
    module: {
        //加载器配置
        loaders: [
            { test: /\.css$/, loader: 'style-loader!css-loader' },
            { test: /\.js$/, loader: 'jsx-loader?harmony' },
            { test: /\.scss$/, loader: 'style!css!sass?sourceMap'},
            { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192'}
        ]
    },
    //其它解决方案配置
    resolve: {
        root: 'E:/github/flux-example/src', //绝对路径
        extensions: ['', '.js', '.json', '.scss'],
        alias: {
            AppStore : 'js/stores/AppStores.js',
            ActionType : 'js/actions/ActionType.js',
            AppAction : 'js/actions/AppAction.js'
        }
    }
};

(1) plugins 是插件项，这里我们使用了一个 CommonsChunkPlugin 的插件，它用于提取多个入口文件的公共脚本部分，然后生成一个 common.js 来方便多页面之间进行复用。

(2) entry 是页面入口文件配置，output 是对应输出项配置（即入口文件最终要生成什么名字的文件、存放到哪里），其语法大致为：

(3) module.loaders 是最关键的一块配置。它告知 webpack 每一种文件都需要使用什么加载器来处理：

    module: {
        //加载器配置
        loaders: [
            //.css 文件使用 style-loader 和 css-loader 来处理
            { test: /\.css$/, loader: 'style-loader!css-loader' },
            //.js 文件使用 jsx-loader 来编译处理
            { test: /\.js$/, loader: 'jsx-loader?harmony' },
            //.scss 文件使用 style-loader、css-loader 和 sass-loader 来编译处理
            { test: /\.scss$/, loader: 'style!css!sass?sourceMap'},
            //图片文件使用 url-loader 来处理，小于8kb的直接转为base64
            { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192'}
        ]
    }

如上，"-loader"其实是可以省略不写的，多个loader之间用“!”连接起来。

注意所有的加载器都需要通过 npm 来加载，并建议查阅它们对应的 readme 来看看如何使用。

拿最后一个 url-loader 来说，它会将样式中引用到的图片转为模块来处理，使用该加载器需要先进行安装：

npm install url-loader -save-dev

配置信息的参数“?limit=8192”表示将所有小于8kb的图片都转为base64形式（其实应该说超过8kb的才使用 url-loader 来映射到文件，否则转为data url形式）。

(4) 最后是 resolve 配置，这块很好理解，直接写注释了：

    resolve: {
        //查找module的话从这里开始查找
        root: 'E:/github/flux-example/src', //绝对路径
        //自动扩展文件后缀名，意味着我们require模块可以省略不写后缀名
        extensions: ['', '.js', '.json', '.scss'],
        //模块别名定义，方便后续直接引用别名，无须多写长长的地址
        alias: {
            AppStore : 'js/stores/AppStores.js',//后续直接 require('AppStore') 即可
            ActionType : 'js/actions/ActionType.js',
            AppAction : 'js/actions/AppAction.js'
        }
    }
```

### 运行webpack
```
webpack —display-error-details:方便出错时能查阅详细的信息
webpack —-config XXX.js:使用另外的一份配置文件来打包
webpack —watch:监听变动并自动打包
webpack -p:压缩混淆脚本，这个很重要
webpack -d:生成map映射文件，告知哪些模块最终被打包到哪里去了
```

### 模块引入
直接在页面中引入webpack最终生成的页面脚本即可
```
<!DOCTYPE html>
<html>
<head lang="en">
  <meta charset="UTF-8">
  <title>demo</title>
</head>
<body>
  <script src="dist/js/page/common.js"></script>
  <script src="dist/js/page/index.js"></script>
</body>
</html>
```

自定义公共模块提取
```
var CommonsChunkPlugin = require("webpack/lib/optimize/CommonsChunkPlugin");
module.exports = {
    entry: {
        p1: "./page1",
        p2: "./page2",
        p3: "./page3",
        ap1: "./admin/page1",
        ap2: "./admin/page2"
    },
    output: {
        filename: "[name].js"
    },
    plugins: [
        new CommonsChunkPlugin("admin-commons.js", ["ap1", "ap2"]),
        new CommonsChunkPlugin("commons.js", ["p1", "p2", "admin-commons.js"])
    ]
};
// <script>s required:
// page1.html: commons.js, p1.js
// page2.html: commons.js, p2.js
// page3.html: p3.js
// admin-page1.html: commons.js, admin-commons.js, ap1.js
// admin-page2.html: commons.js, admin-commons.js, ap2.js
```
独立打包样式文件:有时候希望的项目的样式不要被打包到脚本中，而是独立出来作为css，然后在页面中以<link>标签引入，这时候我们需要extract-text-webpack-plugin插件来帮忙
```
var webpack = require('webpack');
    var commonsPlugin = new webpack.optimize.CommonsChunkPlugin('common.js');
    var ExtractTextPlugin = require("extract-text-webpack-plugin");

    module.exports = {
        plugins: [commonsPlugin, new ExtractTextPlugin("[name].css")],
        entry: {
                //...省略其他配置
```
使用CDN/远程文件：有时候我们希望某些模块走CDN并以</script>的形式挂载到页面上来加载，但是又希望能在webpack的模块上使用，这时可以再配置文件里使用externals属性来帮忙
```
{
    externals: {
        // require("jquery") 是引用自外部模块的
        // 对应全局变量 jQuery
        "jquery": "jQuery"
    }
}
```
### webpack 模块加载器
在webpack中,JavaScript,css,less,Typescript,jsx,coffeescript,图片等静态文件都是模块,不同模块的加载是通过加载器(webpack-loader)来统一管理的.Loaders之间是可以串联的,一个加载器的输出可以作为下一个加载器的输入,最终返回到javascript上,loader的配置可以写在在配置文件中,通过正则表达式的方式进行匹配,计提可以参考下面的示例:
```
module: {
  loaders: [{
    test: /\.less/,
    loader: 'style-loader!css-loader!less-loader'
    }, {
      test: /\.(png|jpg)$/,
      loader: 'url-loader?limit=10000&name=build/[name].[ext]'
      }]
}
```
loader也支持在js文件中通过require的方式进行加载,只需要在require资源path的前面制定loader,用!来串联不同的loader和资源即可.
