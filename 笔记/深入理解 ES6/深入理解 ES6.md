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

在 ES5 的**非严格模式下**中，`arguments` 对象会反映出参数的变化

```javascript
function foo(name) {
  console.log(name === arguments[0]) // true
  name = 'bar'
  console.log(name === arguments[0]) // true
}
```

而在严格模式下得到的结果与上述相反，在 ES6 中和 ES5 严格模式下一致，所以永远可以通过 `arguments` 对象来获取初始的传入参数

### 函数剩余参数 `...`

```javascript
function foo(name, ...keys) {}
```

- 函数只能有一个剩余参数，并且必须放在最后，否则会报语法错误
- 剩余参数不能再对象字面量的 `setter` 属性中使用，否则会报语法错误

```javascript
let obj = {
  // 语法错误
  set name(...keys) {}
}
```

### 函数构造器

虽然 `Function` 构造器并不常用，但是他也增强了不少能力，允许使用默认参数以及剩余参数

```javascript
let arr1 = new Function('first', 'second', 'return first + second')
let arr2 = new Function('first', 'second = first', 'return first + second')
let pickFirst = new Function('...args', 'return args[0]')
```

### ES6 的名称属性

ES6 给所有函数添加了 name 属性

```javascript
function foo() {}
let bar = function() {}
console.log(foo.name) // foo
console.log(bar.name) // bar
```

> 匿名函数的名称属性在火狐和 Edge 中支持度不好，值为空字符串

```javascript
let bar = function foo() {}
let person = {
  get firstName() {},
  sayName() {}
}
console.log(bar.name) // foo
console.log(person.sayName.name) // sayName

let descriptor = Object.getOwnPropertyDescriptor(person, 'firstName')
console.log(descriptor.get.name) // get firstName
```

从上面的例子可以看出来

- 函数表达式的名称优先级要高于赋值目标的变量名
- 对象字面量指定的函数其名称就为函数名
- `person.fristName` 是个 `getter` 函数，因此他的名称为 `get firstName`，`setter` 函数也是相同的情况（`getter` 和 `setter` 函数要用 `Object.getOwnPropertyDescriptor()` 来检索）

```javascript
function bar() {}
let foo = bar.bind()
console.log(foo.name) // bound bar
console.log(new Function().name) // anonymous
```

- 使用 `bind()` 创建的函数，在获取其名称时会带有 `"bound"` 前缀
- 使用 `Function` 构造器创建的函数，其名称则会有 `"anonymous"` 前缀

> 函数的 name 属性大都用在调试时获取有用信息

### 明确函数的双重用途

```javascript
function Foo(bar) {
  this.bar = bar
}
let one = new Foo('bar')
let two = Foo('bar')
console.log(one) // [Object object]
console.log(two) // undefined
```

在如上的例子中，`one` 使用 `new` 来构造，函数内部的 `this` 是一个新对象，并作为函数的返回值，`two` 没有使用 `new` 来调用，输出了 `undefined`（并且在非严格模式下给全局对象添加了 `name` 属性）

JS 为函数提供了两个不同的内部方法 `[[Call]]` `[[Construct]]`

- `[[Call]]`
  - 当函数未使用 `new` 进行调用时会使用该方法，运行的是代码中显示的函数体
- `[[Construct]]`
  - 当函数使用 `new` 进行调用时，该方法会被执行，负责创建一个被称为新目标的新的对象，且使用该新目标作为 `this` 去执行函数体，拥有 `[[Construct]]` 方法的函数被称为构造器

> 不是所有的函数都拥有 `[[Construct]]` 方法，比如箭头函数

### 判断函数如何被调用

```javascript
function Foo(bar) {
  if (this instanceof Foo) {
    this.bar = bar
  } else {
    throw new Error('must use new')
  }
}
```

但是该方法不绝对可靠，只要使用 `apply` 或者 `call` 改变其 `this` 指向就无法正确判断

```javascript
let one = new Foo('bar')
let two = Foo(one, 'two') // 能够正常执行
```

为了解决上述问题，ES6 引入了 `new.target` 元属性

> 元属性：指的是非对象（例如 new）上的一个属性

当函数的 `[[Construct]]` 方法被调用时，`new.target` 会被填入 `new` 运算符的作用目标，该目标通常是新创建的对象实例的构造器，而 `[[Call]]` 方法被调用时，`new.target` 的值会是 `undefined`

```javascript
function Foo(bar) {
  // 或者如下也可实现
  // if (new.target === Person) {
  if (typeof new.target !== 'undefined') {
    this.bar = bar
  } else {
    throw new Error('must use new')
  }
}
let one = new Foo('bar')
let two = Foo(one, 'two') // 报错
```

### 块级函数

在 ES5 的严格模式下，若在代码块内使用**函数声明**声明函数则会抛出语法错误（更好的选择是使用函数表达式）

而在 ES6 的严格模式中会将函数声明视为块级声明，块级函数会被提升到定义所在的代码块的顶部，并允许他在定义所在的代码块内部被访问，一旦代码块执行完毕函数也不复存在

块级函数和 `let` 函数相似，区别在于块级函数会被提升到代码块的顶部，而 `let` 的函数表达式则不会

ES6 的非严格模式下，行为有细微不同，块级函数的作用于会被提升到函数或全局函数的顶部，而不是代码块的顶部

### 箭头函数

特性

- 没有 `this`、`super`、`arguments`，也没有 `new.target` 绑定，这些值都由其所在的非箭头函数来决定
- 不能使用 `new` 调用，箭头函数没有 `[[Construct]]` 方法，因此不能当作构造函数使用
- 没有原型，也就是没有 `prototype` 属性
- 不能更改 `this`（也就是不能使用 `call()`、`apply()`、`bind()` 来改变其 `this` 值，虽然使用时也不会报错），`this` 的值在函数的整个生命周期都保持不变
- 没有 `arguments` 对象，所以必须依赖于具名参数或剩余参数来访问函数的参数
- 不允许重复的具名参数，箭头函数不允许拥有重复的具名参数，无论是否在严格模式下（传统函数只有在严格模式下才会禁止这种行为）

> 为什么会有这些特性，在嵌套的函数中有时会因为调用方式不同导致 `this` 指向改变
> 使用单一的 `this` 值来执行代码，JS 引擎可以更容易对代码进行优化

> 箭头函数也拥有 `name` 属性

### 尾调用优化

> 尾调用：调用函数的语句是另一个函数的最后语句

```javascript
function foo() {
  return bar() // 尾调用
}
```

ES5 中实现的尾调用：一个新的栈帧被创建并被推到调用栈之上，用于表示该次函数调用，这意味着之前每个栈帧都被保留在内存中，当调用栈太大时会出问题

ES6 在严格模式下力图为特定尾调用减少调用栈的大小（非严格模式下的尾调用则保持不变），当满足如下条件，尾调用优化会清除当前栈帧并再次利用它，而不是为尾调用创建新的栈帧

1. 尾调用不能引入当前栈帧中的变量（意味着该函数不能是闭包）
2. 进行尾调用的函数在尾调用返回结果后不能做额外操作
3. 尾调用的结果作为当前函数的返回值

```javascript
'use strict'
function foo() {
  // 被优化
  return bar()
}

function foo() {
  // 未被优化，缺少 return
  bar()
}

function foo() {
  // 未被优化，在返回之后还要执行加法
  return 1 + bar()
}

function foo() {
  // 未被优化，调用并不在尾部
  let result = bar()
  return result
}

function foo() {
  let num = 1
  let bar = () => num
  // 未被优化，此函数是闭包
  return bar()
}
```

### 尾调用优化实例

尾调用优化大都在递归的时候需要考虑，比如求阶乘

```javascript
function factorial(n) {
  if (n <= 1) {
    return 1
  } else {
    // 未被优化，在返回之后还要执行乘法
    return n * factorial(n - 1)
  }
}
```

如上的做法在 n 很大的时候会导致堆栈溢出

优化如下

```javascript
function factorial(n, p = 1) {
  if (n <= 1) {
    return 1 * p
  } else {
    let result = n * p
    // 被优化
    return factorial(n - 1, result)
  }
}
```

## 扩展的对象功能

### 对象类别

- 普通对象：拥有 JS 对象所有默认的内部行为
- 奇异对象：其内部行为在某些方面有别于默认行为
- 标准对象：在 ES6 中被定义的对象，例如 `Array`、`Date` 等等，标准对象可以是普通的也可以是奇异的
- 内置对象：在脚本开始运行时由 JS 运行环境提供的对象，所有的标准对象都是内置对象

### 对象字面量语法

```javascript
let person = {
  name: 'king',
  say: function() {
    console.log('hello')
  }
}

let person = {
  name: 'king',
  // 可省略 :function
  say() {
    console.log('hello')
  }
}
```

如上速记语法称为简写语法，也是在 `person` 中创建了一个方法

> 简写语法创建的对象有一点区别，方法简写能使用 `super`，而非简写的方法则不能使用 `super`

对象字面量内定义 key 也可以使用方括号，表示该属性名需要计算，其结果是一个字符串

```javascript
let bar = 'Bar'
let person = {
  ['one' + bar]: 'one',
  ['two' + bar]: 'two'
}
```

### 新的方法

`Object.is()` 方法

比 `===` 更准确的方法

`===` 怪异点

- 认为 `+0` 和 `-0` 相等，即使这两者在 JS 引擎中有不同的表示
- 认为 `NaN` !== `NaN`，因此还需要使用 `isNaN()` 函数来检测 `NaN`

```javascript
console.log(+0 == -0) // true
console.log(+0 === -0) // true
console.log(Object.is(+0, -0)) // false

console.log(NaN == NaN) // false
console.log(NaN === NaN) // false
console.log(Object.is(NaN, NaN)) // true

console.log(5 == 5) // true
console.log(5 === 5) // true
console.log(Object.is(5, 5)) // true

console.log(5 == '5') // true
console.log(5 === '5') // false
console.log(Object.is(5, '5')) // false
```

`Object.assign()` 方法

`Object.assign()` 接受一个接收者和多个供应者，最后返回接收者，当有属性值相同时，会按照顺序依次覆盖

```js
let foo = Object.assign({}, { type: 'js', name: 'index.js' }, { type: 'css', name: 'index.css' })
console.log(foo)
```

操作访问器属性

```js
let receiver = {}
let supplier = {
  get name() {
    console.log('get name')
    return function() {
      return 'king'
    }
  }
}
// 会输出 get name
Object.assign(receiver, supplier)
let descriptor = Object.getOwnPropertyDescriptor(receiver, 'name')
console.log(descriptor.value) // function() {return "king"}
console.log(descriptor.get) // undefined
```

如上，在使用 `Object.assign()` 之后，receiver.name 就作为一个属性存在了，其值就为 supplier.name，相当于在执行 Object.assign 的时候访问了一次 supplier.name
