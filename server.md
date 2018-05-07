 ##### 有时候我们的项目可能在开发环境下正常，而在生产环境下报错，我们不可能改一下就推到测试环境去测，这样既浪费时间效率又低，这就需要我们有个静态资源服务器并且还能够代理或转发请求，通常可以使用nginx或者node，这理先介绍node的。


```js
// sever.js
const express = require('express');
const proxyMiddleware = require('http-proxy-middleware');

const config = {
  port: 8080,
  proxy: {
    '/rest': {
      target: 'http://www.example.com',
      changeOrigin: true,
    },
  },
  publicPath: '/', // 创建虚拟前缀
  static: './dist', // 静态资源目录
}

Object.keys(config.proxy).forEach((ctx) => {
  app.use(proxyMiddleware(ctx, proxy[ctx]));
});

app.use(config.publicPath, express.static(config.static));

const uri = `http://localhost:${port}${config.publicPath}`;

module.exports = app.listen(port, (err) => {
  if (err) console.log(err);
  console.log(`> Listening at ${uri}\n`);
});

```

```
$ node sever.js
```