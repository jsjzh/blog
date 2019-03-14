## window 视图属性

`window.`

### `innerWidth` and `innerHeight`

表示 `window` 窗体的内部宽度，不包括用户界面元素，比如收藏栏、地址栏等等。

当浏览器大小发生改变的时候这些数值也会变化。

### `outerWidth` and `outerHeight`

表示 `window` 窗体整个的大小，包括收藏栏、地址栏等等。

当浏览器大小发生改变的时候这些数值也会变化。

### `pageXOffset` and `pageYOffset`

表示整个页面的滚动的像素值。

`pageXOffset` 最左侧为 `0`。

`pageYOffset` 最上侧为 `0`。

测试了一下，鼠标滚轮滚动一次移动的为 `100` 像素。

### `screenX` and `screenY`

表示浏览器窗口在显示器中的位置。

有两个显示屏的时候，会以主显示器为准轴，移到副显示器的时候 `screenX` 为负数。

## `screen` 视图属性

`screen.`

### `availWidth` and `availHeight`

显示器可用宽高，不包括任务栏之类的东西。

若把任务栏隐藏就会发现 `availHeight` 变大了一些。

### `colorDepth`

表示显示器的颜色深度。

### `pixelDepth`

基本表现和 `colorDepth` 类似。

### `width` and `height`

表示显示器屏幕的宽高，包括任务栏，大抵就是屏幕会发亮的所有的地方。

## 文档视图和元素视图方法

### `elmentFromPoint()`

`document.`

返回给定坐标处所在的元素。

```javascript
document.elementFromPoint(100, 100)
```

以上代码会返回距离文档左上角 `left 100px; top 100px` 的那个元素。

### `getBoundingClientRect()`

`element.`

得到矩形元素的界限，返回的是一个对象，包含 `top` `left` `right` `bottom` 四个属性值，大小都是相对文档视图左上角计算出来的。

看过一句话，出处已忘，说频繁使用该方法不是很好，每次调用浏览器都会把所有元素位置计算一边，会造成白屏。

### `getClientRects()`

`element.`

返回元素的数个矩形区域，返回的结果是个对象列表，具有数组特性。

这里的矩形选区只针对 `inline-block` 元素生效。

测试了最直观的结果就是有几行文字就会显示几个矩形。

### `scrollIntoView()`

`element.`

让元素滚动到可视区域。

## 元素视图属性

`element.`

### `clientLeft` and `clientTop`

表示元素的内容区域和该元素左上角的位置的距离。

元素由外到内为

- `margin`
- `border`
- `padding`
- `context`

所以大致可以理解成是 `border` 的宽度？

<!-- TODO 放一张图片表示盒子 -->

### `clientWidth` and `clientHeight`

表示内容区域的高度和宽度，包括 `padding` 大小，但是不包括边框和滚动条。

### `offsetLeft` and `offsetTop`

表示相对最近的祖先定位元素（`position` 被设置为 `relative`、`absolute` 或 `fixed` 的元素）的偏移量。

若父级没有定位元素，则得到的是和 `body` 的偏移量。

偏移量 = 父级元素的 `padding` + 该元素的 `margin`。

### `offsetParent`

得到的是一个最近的祖先定位元素。

得到的元素只可能是

- `<body>`
- `position` 不为 `static` 的元素
- `<th>` 、 `<td>` 或 `<tr>` 即使 `<table>` `position: static` 都为 `<table>`

`body` 元素没有 `offsetParent`，结果为 `null`

（chrome 浏览器下）`position: fixed` 的元素也没有 `offsetParent`，结果为 `null`

### `offsetWidth` and `offsetHeight`

整个元素的尺寸，包括边框。

### `scrollLeft` and `scrollTop`

表示元素滚动的像素大小，可以改写。

### `scrollWidth` and `scrollHeight`

表示整个内容区域的宽高，包括被隐藏的部分，如果元素没有隐藏的部分，该值等于 `clientWidth` 和 `clientHeight`

当向下滚动的时候，`scrollHeight` 应该等于 `scrollTop` + `clientHeight`

## 鼠标位置

`event.`

### `clientX` and `clientY`

值为鼠标处相对于 `window` 的左上角偏移量。

### `offsetX` and `offsetY`

表示鼠标相对于当前被点击元素的左上偏移量。

### `pageX` and `pageY`

为鼠标相对于 `document` 的坐标。

若有滚动条的话该值等于 `window.pageYOffset + event.clientY`

### `screenX` and `screenY`

鼠标相对于显示器屏幕的偏移坐标，手机中基本等于 `pageX` `pageY`。

双屏幕显示器中，若在副显示器点击，则 `screenX` 为负数。

### `x` and `y`

相当于 `clientX` `clientY`。
