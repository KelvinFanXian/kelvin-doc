# 修改静态文件目录

```js
// config/index.js
const assetsPublicPath = '/ten-ui/'
```

- [ref](https://blog.csdn.net/oopxiajun2011/article/details/108219283)



# css优化

```js
// build/webpack.prod.conf.js
new OptimizeCSSPlugin({
  cssProcessorOptions: config.build.productionSourceMap
    ? { safe: true, map: { inline: false } }
    : { safe: true }
}),
```

