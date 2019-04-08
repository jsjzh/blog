## Loader

Loader 的执行顺序是由后到前的

Loader 可以通过 URL querystring 的方式传入参数，例如 css-loader?minimize

css-loadder 用于读取 css 文件，style-loader 用于把 css 内容注入到 javascript 里

在载入某一 css 模块的时候，可以使用 require('style-loader!css-loader?minimize!./main.css')，来对其单独指定 loader

```js
module: {
  rules: [
    {
      test: /(css)$/,
      use: [
        'style-loader',
        {
          loader: 'css-loader',
          options: {
            minimize: true
          }
        }
      ]
    }
  ]
}
```

## Plugin
