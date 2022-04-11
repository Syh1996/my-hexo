---
title: webpack学习计划来啦
date: 2022-01-10 18:11:09
tags: webpack
---
#### 开始

webpack学习计划终于来了

一直感觉学webpack有些困难，拖了好久，面试了头条和腾讯都同时问到了webpack，webpack也该提上日程了

---

#### 打包图片资源

* webpack5版本像url-loader，file-loder都是废弃不会直接使用的。我整了半天，虽然打包了图片，但是却打包了两次，并且无法显示图片了，
  如果想要使用这些废弃的旧功能，加上`type: javascript/auto`
* `esModule: false`,  打包图片时需要关闭ES modules
* `outputPath:'static/img',` 配置将资源文件输出的位置

``````js
module:{
  rules:[{
                 test:/\.(png|jpe?g|gif)$/,
                 loader:'url-loader',
                 options:{
                     limit:30*1024,
                     esModule: false,
                     outputPath:'static/img',
                 },
                 type: 'javascript/auto'
             }]
}

``````

#### 在webpack5中打包资源文件的修改

在 webpack 5 之前，通常使用：

- [`raw-loader`](https://v4.webpack.js.org/loaders/raw-loader/) 将文件导入为字符串
- [`url-loader`](https://v4.webpack.js.org/loaders/url-loader/) 将文件作为 data URI 内联到 bundle 中
- [`file-loader`](https://v4.webpack.js.org/loaders/file-loader/) 将文件发送到输出目录

资源模块类型(asset module type)，通过添加 4 种新的模块类型，来替换所有这些 loader：

- `asset/resource` 发送一个单独的文件并导出 URL。之前通过使用 `file-loader` 实现。
- `asset/inline` 导出一个资源的 data URI。之前通过使用 `url-loader` 实现。
- `asset/source` 导出资源的源代码。之前通过使用 `raw-loader` 实现。
- `asset` 在导出一个 data URI 和发送一个单独的文件之间自动选择。之前通过使用 `url-loader`，并且配置资源体积限制实现。url-loader与fileloader自动选择

  `Rule.parser.dataUrlCondition:{maxSize:4*1024}  可以配置文件大小 的选择方式`

当在 webpack 5 中使用旧的 assets loader（如 `file-loader`/`url-loader`/`raw-loader` 等）和 asset 模块时，你可能想停止当前 asset 模块的处理，并再次启动处理，这可能会导致 asset 重复，你可以通过将 asset 模块的类型设置为 `'javascript/auto'` 来解决。

generator：filename可以支持一个路径，将文件分类打包到不同的文件夹中

exclude  除开某个条件外的所有情况，都按此方法处理

``````js
 {
                 test:/\.(png|jpe?g|gif)$/,
                 type:'asset/resource',
                 generator:{
                    filename: 'static/img/[hash:10][ext]',
                 }
             },
             {
                exclude:/\.(png|jpe?g|gif|html|js|ts|css|less)$/,
                type:'asset/resource',
                generator:{
                    filename: 'static/media/[hash:10][ext]',
                 }
             }
``````

[原文参考：https://webpack.docschina.org/guides/asset-modules/](https://webpack.docschina.org/guides/asset-modules/)

**output.assetModuleFilename:'shao/[hash] [ext]'  可以将静态资源统一设置一个路径，当有generator中filename指定的路径时，优先于generator**

### plugins

* HtmlWebpackPlugin 简化了 HTML 文件的创建，以便为你的 webpack 包提供服务。这对于那些文件名中包含哈希值，并且哈希值会随着每次编译而改变的 webpack 包特别有用。你可以让该插件为你生成一个 HTML 文件，使用 lodash 模板提供模板，或者使用你自己的 loader。

  安装

  ```bash
  npm install --save-dev html-webpack-plugin
  ```



[相关链接:https://webpack.docschina.org/plugins/html-webpack-plugin/](https://webpack.docschina.org/plugins/html-webpack-plugin/)

* MiniCssExtractPlugin会将 CSS 提取到单独的文件中，为每个包含 CSS 的 JS 文件创建一个 CSS 文件，并且支持 CSS 和 SourceMaps 的按需加载。

  ```bash
  npm install --save-dev mini-css-extract-plugin
  ```

  [相关链接:https://webpack.docschina.org/plugins/mini-css-extract-plugin/](https://webpack.docschina.org/plugins/mini-css-extract-plugin/)

  * 本插件基于 webpack v5 的新特性构建，并且需要 webpack 5 才能正常工作
* CssMinimizerWebpackPlugin使用 [cssnano](https://cssnano.co/) 优化和压缩 CSS。

  ```bash
  npm install css-minimizer-webpack-plugin --save-dev
  ```

  [相关链接:https://webpack.docschina.org/plugins/css-minimizer-webpack-plugin/](https://webpack.docschina.org/plugins/css-minimizer-webpack-plugin/)

  * 仅在生产环境开启 CSS 优化。
  * 如果还想在开发环境下启用 CSS 优化，请将 `optimization.minimize` 设置为 `true`

### 抽离公共方法（测试只能抽离由npm安装的公共文件，自己写的不能抽离)

#### 通过entry入口配置

shared:[插件名称如jquery]

``````js
    entry:{
           dom1:{
               import:'./js/dom1.js',
               dependOn: 'shared',
           },
           dom2:{
               import:'./js/dom2.js',
               dependOn: 'shared',
           },
           shared:['jquery']
      },
``````

[参考https://webpack.docschina.org/configuration/entry-context/#dependencies](https://webpack.docschina.org/configuration/entry-context/#dependencies)

#### 通过优化项配置

``````js
optimization: {
        splitChunks: {
          chunks: 'all',
          //有效值为 all，async 和 initial。设置为 all 可能特别强大，因为这意味着 chunk 可以在异步和非异步 chunk 之间共享。
        },
      },
``````

[参考网址:https://webpack.docschina.org/plugins/split-chunks-plugin/](https://webpack.docschina.org/plugins/split-chunks-plugin/)

### 缓存

client（通常是浏览器）就能够访问此 server 的网站及其资源。而最后一步获取资源是比较耗费时间的，这就是为什么浏览器使用一种名为 [缓存](https://en.wikipedia.org/wiki/Cache_(computing)) 的技术。可以通过命中缓存，以降低网络流量，使网站加载速度更快，然而，如果我们在部署新版本时不更改资源的文件名，浏览器可能会认为它没有被更新，就会使用它的缓存版本。由于缓存的存在，当你需要获取新的代码时，就会显得很棘手。

此指南的重点在于通过必要的配置，以确保 webpack 编译生成的文件能够被客户端缓存，而在文件内容变化后，能够请求到新的文件。

``````js
 optimization: {
      runtimeChunk: 'single',
     splitChunks: {
       cacheGroups: {
         vendor: {
           test: /[\\/]node_modules[\\/]/,
           name: 'vendors',
           chunks: 'all',
         },
       },
     },
    },
``````

[原文参考https://webpack.docschina.org/guides/caching/#output-filenames](https://webpack.docschina.org/guides/caching/#output-filenames)


### 总结上述打包

```js

const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const HtmlWebpackPlugin = require('html-webpack-plugin');
const CssMinimizerPlugin = require("css-minimizer-webpack-plugin");
const TerserPlugin = require("terser-webpack-plugin");
const { resolve } = require("path");
module.exports = (env) => {
    return {
        entry: {
            app: {
                import: './app',
                /*   dependOn: 'shared', */
            },
            banner: {
                import: './js/banner-bar',
                /*    dependOn: 'shared', */
            },
            list: {
                import: './js/list',
                /* dependOn: 'shared', */
            },
            /*  shared: ['jquery'], */
        },
        output: {
            filename: 'js/[name].[contenthash].js',
            path: resolve(__dirname, 'build'),
            clean: true
        },
        module: {
            rules: [
                {
                    test: /\.css$/,
                    use: [MiniCssExtractPlugin.loader, "css-loader"],
                },
                {
                    test: /\.less$/,
                    use: [MiniCssExtractPlugin.loader, "css-loader", 'less-loader']
                },
                {
                    test: /\.(png|jpe?g|gif)$/i,
                    type: 'asset',
                    generator: {
                        filename: 'static/[hash][ext]',
                    },
                    parser: {
                        dataUrlCondition: {
                            maxSize: 10 * 1024,
                        },
                    },
                }
            ]
        },
        plugins: [
            new MiniCssExtractPlugin({
                filename: 'css/[name].css'
            }),
            new HtmlWebpackPlugin({
                template: './index.html'
            })],
        devServer: {
            static: {
                directory: resolve(__dirname, 'build'),
            },
            compress: true,
            port: 9000,
            /* open: true */
        },
        optimization: {
            runtimeChunk: 'single',
            splitChunks: {
                cacheGroups: {
                    vendor: {
                        test: /[\\/]node_modules[\\/]/,
                        name: 'vendors',
                        chunks: 'all',
                    },
                },
            },
            minimize: true,
            minimizer: [
                // 在 webpack@5 中，你可以使用 `...` 语法来扩展现有的 minimizer（即 `terser-webpack-plugin`），将下一行取消注释
                // `...`,
                new CssMinimizerPlugin(),
                new TerserPlugin()
            ],
        },
        //mode: 'development'
        mode: env.production ? 'production' : 'development'
    }
}
```

```js
 "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack --config webpack.config.prod.js  --env production --progress",
    "start": "webpack serve --config webpack.config.env.js"
  },
```

