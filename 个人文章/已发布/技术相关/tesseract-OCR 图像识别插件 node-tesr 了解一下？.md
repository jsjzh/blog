# tesseract-OCR 图像识别插件 node-tesr

![node](https://i.loli.net/2019/03/14/5c89be9f64bac.jpg)

## 前言

该项目诞生于一次爬虫事件，当时一时兴起想把某租房网信息爬下来，前面进行的还是挺顺畅的，但是在租房价格信息上被摆了一道，房屋的价格信息为一个数字图片为底加上偏移量来显示的，和雪碧图一样的实现方式，当然，其中加上了一点小算法，具体如下。

- 获取数字图片信息和 `offset` 信息
  - ![数字图片](https://i.loli.net/2019/03/14/5c89c3750eae9.png '直接放出来会不会不太好？侵删')
  - `{ "offset": [ [1, 4, 2, 8], [5, 1, 7, 8], [5, 1, 3, 8], ... ] }`
- 由 offset 信息加上一点算法得出 `position` 信息
  - `（background-position: xxx px）`
- 以数字图片为背景，加上偏移，`append` 到价格信息他应该在地方

略一思索，倒也不是什么大事儿，只要加个识别的过程再辅以算法即可。

在实行图像识别的过程中借助到了 `google` 的开源软件 `tesseract-OCR`，因为爬虫环境是 `node`，遂写了一个适用于 `tesseract-OCR` 最新版本的 `node` 插件，后续还添加了命令行使用的功能。

## 演示

### 命令行使用 --- 1

![cmd-line-1.jpg](https://i.loli.net/2019/03/14/5c89b9278d9d5.jpg)

### 命令行使用 --- 2

![cmd-line-2.jpg](https://i.loli.net/2019/03/14/5c89b9296e4d5.jpg)

### 模块使用 --- 1

![module-1.jpg](https://i.loli.net/2019/03/14/5c89b927b22a5.jpg)

## 项目在这里

如果觉得我对你有帮助，不妨给我个 star 吧，蟹蟹~

[github node-tesr](https://github.com/jsjzh/node-tesr)

## 正文

### 命令行使用

想要使用图像识别首先要确保电脑中已经安装了 [`tesseract-OCR` 点击下载](https://github.com/tesseract-ocr/tesseract/wiki/Downloads)。

想要使用命令行建议全局安装

```cmd
npm install node-tesr -g
```

```cmd
tesr --from=./test/output.jpg --to=./output.txt
```

参数说明

```
--from 需要识别的图片路径（必须）
--to 若传入此参数会将识别的文字输出到该文件下（非必须，默认会将识别内容输出到命令行）
--l 识别语言，对中文稍微做了点处理，识别简体 --l=chs，识别繁体 --l=cht（非必须，默认为 eng）
--p 见 lib/config.js 里的说明（非必须，默认为 3 自动模式）
--o 见 lib/config.js 里的说明（非必须，默认为 3 自动模式）
```

### 模块引入使用

```cmd
npm install node-tesr
```

```javascript
const tesseract = require('node-tesr')

tesseract('./output.jpg', { l: 'eng', oem: 3, psm: 3 }, function(err, data) {
  // 此处获得识别内容
  console.log(data)
})

// 或者如下也可
tesseract('./output.jpg', function(err, data) {
  // 此处获得识别内容
  console.log(data)
})
```

## 后语

### 效果

经测试效果还是不错的，但是有一点需要注意一下，上面提到该网站的数字图片是透明底的，测试发现 `tesseract-OCR` 对透明底的似乎无解，这个时候就需要结合一下 `images` 这个 `node` 插件

```javascript
let images = require('images')
images(500, 100)
  .fill(0xff, 0xff, 0xff, 1)
  .draw(images('demo.png'), 10, 10)
  .save('output.jpg', {
    quality: 100
  })
```

将透明底填充为白底即可正常识别

### 如何提高我的图像识别准确率

老板！我的图像识别率很低怎么破！

来，看这里，这个可以提高图像识别率。

[识别算法学习](https://blog.csdn.net/xiaojun111111/article/details/54377154)

## 待办

- 增加网络地址图片也可识别的功能
- 使用 `then` 来处理回调

## 页脚

代码即人生，我甘之如饴。

我在这里 [gayhub@jsjzh](https://github.com/jsjzh) 欢迎大家来找我玩儿。

欢迎小伙伴们直接加我，拉你进群一起学习前端呀，记得备注一下你来自哪里哦。

![wechat](https://i.loli.net/2019/03/11/5c867208cc9c0.jpg)

![wechat](https://i.loli.net/2019/03/17/5c8e0dfc3dadf.jpg)
