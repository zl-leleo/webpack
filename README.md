

1. 每次打包前，删除之前webpack打包好的文件


npm i clean-webpack-plugin --save-dev


webpack.config.js

const CleanWebpackPlugin = require('clean-webpack-plugin')
 
{
  plugins: [
    new CleanWebpackPlugin(paths [, {options}])
  ]
}





2.生成html文件插件

npm i --save-dev html-webpack-plugin


webpack.config.js


const HtmlWebpackPlugin = require('html-webpack-plugin')


webpackconfig = {

    entry: './src/index.js',    // 输入文件，打包前js的路径 （不配置就直接默认路径./src/index.js）
    output: {
       path: path.resolve(__dirname, 'dist'),   // 获取路径 (不配置就直接默认路径./dist/main.js)
       filename: 'js/bundle.js'    // 输出文件，打包后的js文件名字和路径
    },
	
    ...

    plugins: [
        new HtmlWebpackPlugin()
    ]
}

连接更多：
https://www.cnblogs.com/wonyun/p/6030090.html



3. 文件识别vue模板中的html文件<template>


vue-loader V15的版本中,需要在webpack.config.js中加入相应的插件


在webpack.config.js的头部引入插件:

const VueLoaderPlugin = require('vue-loader/lib/plugin');

在导出的配置中添加插件:

module.export = {
  ... //之前配置不变
  plugins: [
    new VueLoaderPlugin()
  ]
}

如果你使用commonjs规范引入vue，即使用require语法导入vue模块，那么就需要vue的构建版本设置为vue.commonjs.js。

module:{

    ... //之前配置不变


    // 还需要添加以下内容
    resolve: {
        alias: {
            //确定vue的构建版本
            'vue$':'vue/dist/vue.esm.js'
        },
        extensions: ['*','.js','.vue','.json']
    }

}



4. 文件识别vue模板中的行内样式<style>,并独立出来一个样式文件


const MiniCssExtractPlugin = require("mini-css-extract-plugin");

 module: {
            rules: [
                {
                    test: /\.css$/,
                    use: [
                      {
                        loader: MiniCssExtractPlugin.loader
                      },
                      "css-loader"
                    ]
                }
            ]
        }

        plugins: [
            new MiniCssExtractPlugin({
                filename: "[name].css",            // 输出文件的路径，可修改
                chunkFilename: "[id].css"
            })
        ],

5. js插件和业务代码分离


optimization:{
            splitChunks:{
                //对entry进行拆分
                chunks: 'all',
                minSize: 30000,
                cacheGroups:{
                    //比如你要单独把vue等官方库文件打包到一起，可以使用这个缓存组，如果要具体到库文件，可以单独给库文件写一个缓存组
                    vendor:{
                        test: /node_modules/,
                        priority: 10,
                        name: "vendor",
                        enforce: true
                    },
                    //这里定义分离前被引用过两次的文件，将其一同打包到common.js,最小为30kb
                    common:{
                        name: "common",
                        minChunks: 2,
                        minSize: 20000
                    }
                }
            }
        }

了解更多：
https://blog.csdn.net/bubbling_coding/article/details/81585412



6. 图片打包，html里面的图片和css背景图片


module: {
            rules: [
                {
                  test: /\.(jpe?g|png|gif|svg|woff|woff2|eot|ttf|otf)$/i,
                  use: [
                    {
                      loader: 'file-loader',
                      options: {
                        name: '[name].[ext]',
                        outputPath: 'images/'
                      }
                    },
                  ]
                },
                // 下面几行才是 html-loader 的配置内容
                {
                  test: /\.html$/,
                  use: [ {
                    loader: 'html-loader',
                    options: {
                      minimize: true
                    }
                  }],
                }
            ]
        }




































