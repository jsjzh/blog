## 块级作用域

```javascript
// 即使是最安全的 typeof 也会引用错误
// 这说明使用 let const 并不会将其提升到函数顶部
console.log(typeof value)
let value = 'value'
```

`const` 阻止的是对于变量绑定的修改，而不是阻止对成员值的修改

> 需要重点了解的是： let 声明在循环内部的行为是在规范中特别定义的，而与不提升变量声明的特征没有必然联系
> 事实上，在早期 let 的实现中并没有这种行为，它是后来才添加的

### 循环中的块级作用域

在 `for` 循环中，在初始化时使用 `const` 定义变量，在第一次迭代之后会抛出错误，而在 `for-in` 或者 `for-of` 循环中丁页则不会，因为在 `for-in` 与 `for-of` 循环内工作时，每次迭代创建了一个新的变量绑定，而不是试图去修改已绑定的变量的值

### 全局作用域中的块级作用域

在全局作用于上使用 `var` 时，在浏览器环境中会被定义到 `window` 对象上，而使用 `let` 和 `const` 则不会导致此结果

> 若想让代码能从全局对象中被访问，你仍然需要使用 var
> 在浏览器中跨越帧或窗口去访问代码时，这种做法非常普遍

### 块级作用域的最佳实践

最佳实践，在默认情况下使用 `const`，在知道变量值需要被修改的情况下才使用 `let`，依据是大部分变量在初始化之后都不应当被修改

## 字符串与正则表达式

## normalize()

### TODO 正则表达式 u 标志

### 识别子字符串的方法

- `includes()`
  - 字符串若存在于给定文本中，返回 `true`，否则返回 `false`
- `startsWith()`
  - 字符串若存在于给定文本起始处，返回 `true`，否则返回 `false`
- `endsWith()`
  - 字符串若存在于给定文本末尾处，返回 `true`，否则返回 `false`

以上方法都接受两个参数，子字符串，搜索起始位置索引（可选）

当提供第二个参数， `includes()` 和 `startsWith()` 会从该索引开始匹配，`endsWith()` 会将字符串长度减去该参数

> 如果传入参数为正则表达式会抛出错误，但在 indexOf 和 lastIndexOf 会将正则表达式转换为字符串并搜索它

### repeat()

接受一个参数作为字符串的重复次数，返回一个新字符串

```javascript
console.log('hello'.repeat(2))
```

```javascript
// 需要产生所进的代码格式化工具中
let indent = ' '.repeat(4)
let indentLevel = 0
// 每当增加缩进
let newIndent = indent.repeat(++indentLevel)
```

### TODO 正则表达式 y 标志

### 复制正则表达式

将正则表达式传递给 `RegExp` 构造器可以复制该正则

```javascript
let re = /\s/i
let cloneRe = new RegExp(re)
```

如果传入第二个参数在 ES5 中会报错，在 ES6 中修改了次设定，当传入第二个标志参数后会覆盖原先的标志

### flags 属性

ES5 中，可以用 `source` 获取正则的字符串，在 ES6 中又添加了一个 `flags` 用来获取标志字符串

### 模板字面量

- 多行字符串
- 基本的字符串格式化
- HTML 转义

多行字符串在 ES5 中可以通过反斜线 `\` 来实现

```javascript
let message =
  'hello \n\
world'
```

或者使用数组进行拼接

```javascript
let message = ['hello', 'world'].join('\n')
```

模板字符串中的所有空白符都是字符串的一部分，包括换行，包括缩进

```javascript
let html = `
<div>
  <h1>hello world</h1>
</div>`.trim()
```

模板字面量本身也是 JS 表达式，可以使用多重嵌套

```javascript
let name = 'king'
let deep = `hello ${`my name is ${name}`}`
```

### 标签化模板

```javascript
let name = 'king'
let message = tag`hello my name is ${name}`
function tag(literals, ...substitutions) {
  let result = ''
  // 仅使用 substitution 的元素数量来进行循环
  for (let i = 0; i < substitutions.length; i++) {
    result += literals[i]
    result += substitutions[i]
  }
  // 添加最后一个字面量
  result += literals[literals.length - 1]
  return result
}
```

使用模板字面量中的原始值

```javascript
let message2 = String.raw`Multiline\nstring`
console.log(message2) // "Multiline\\nstring"
```

## 函数

在 ES5 的**非严格模式下**中，arguments 对象会反映出参数的变化

```javascript
function foo(name) {
  console.log(name === arguments[0]) // true
  name = 'bar'
  console.log(name === arguments[0]) // true
}
```

而在严格模式下得到的结果与上述相反，在 ES6 中和 ES5 严格模式下一致，所以永远可以通过 arguments 对象来获取初始的传入参数

### 函数剩余参数 `...`

```javascript
function foo(name, ...keys) {}
```

- 函数只能有一个剩余参数，并且必须放在最后，否则会报语法错误
- 剩余参数不能再对象字面量的 setter 属性中使用，否则会报语法错误

```javascript
let obj = {
  // 语法错误
  set name(...keys) {}
}
```

### 函数构造器

```js
let arr1 = new Function('first', 'second', 'return first + second')
let arr2 = new Function('first', 'second = first', 'return first + second')
let pickFirst = new Function('...args', 'return args[0]')
```
