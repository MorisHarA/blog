如今，webpack单页应用的脚手架太多了，但是有时候的确需要构建多页应用，怎么办呢？很简单，在单页应用的脚手架上稍作修改就可以了。
```
假设你的目录是这样子的，pages就是每个页面打包的入口
.
├── dist                                        // 上线项目文件
├── src                                         // 源码目录
│   ├── components                              // 公用组件
│   ├── pages                                   // 页面目录
│       ├── a.js                                // 页面 a
│       ├── b.js                                // 页面 b
│       ├── c.js                                // 页面 c
│—— webpack.config.js                           // webpack配置
```

我们修改webpack的entry
```js
const webpackConfig = {
  entry: {
    a: './src/pages/a.js',
    b: './src/pages/b.js',
    c: './src/pages/c.js',
  }
}
```

但是当page很多的时候我们不可能加一个页面就改下webpack的配置，那么我们可以借助node的fs模块去处理文件的引入问题
```js
function getAllEntry () {
  return require('fs').readdirSync('./src/pages/').reduce(function(memo, file) {
    return Object.assign(memo, { [key.match(/([^/]+)\.js$/)[1]]: file });
  }, {});
}
// 然后改下entry
module.exports = {
  entry: getAllEntry()
}

```

单页应用需要HtmlWebpackPlugin插件去构建html，以及处理html的依赖文件，那么多页应用怎么处理呢，只需要多次加入
HtmlWebpackPlugin插件就行
```js

const webpackConfig = {
  entry: {
    a: './src/pages/a.js',
    b: './src/pages/b.js',
    c: './src/pages/c.js',
  },
  plugins: [
    // 单页的操作
    new HtmlWebpackPlugin({
      filename: 'index.html'
      template: 'index.html',
      inject: true,
      minify: {
        removeComments: true,
        collapseWhitespace: true,
        removeAttributeQuotes: true,
        minifyJS: true
      },
      chunksSortMode: 'dependency'
    })
  ]
}

// 多页的操作，有几个页面就new几个
Object.keys(getAllEntry()).forEach(function (name) {
  const config = {
    filename: `${name}.html`,
    template: 'index.html',
    inject: true,
    minify: {
      removeComments: true,
      collapseWhitespace: true,
      removeAttributeQuotes: true,
      minifyJS: true
    },
    chunksSortMode: 'dependency'
  }
  webpackConfig.plugins.push(new HtmlWebpackPlugin(config));
})

```