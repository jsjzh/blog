- import
- 注释
  - //
- 箭头函数
  - =>
- class
- extends
- var
  - 声明变量而不指定其类型
  - 以下划线 \_ 开头的类或成员变量是私有的
- String
  - 在声明变量的时候就可以指定具体类型
- '' 或 ""
- \$variableName 和 \${variableName.xxx}

## 变量声明

### var

```dart
var content = "Dart";
```

不需要指定变量的数据类型，Dart 会自动推断数据类型，可以用 var 来定义任何变量

**值得注意的是，`var` 一旦赋值后就不能改变数据类型了**

> 因为 `var` 并不直接存储值，而是存储值的对象的引用  
> 例如 `var content = "Dart"`，是因为名字为 `content` 的 `var` 变量存储了值为 Dart 的 `String` 对象的引用

### 明确数据类型

```dart
String content = "Dart";
int count = 0;
```

| 类型   | 说明                                                      | 例子                                             |
| ------ | --------------------------------------------------------- | ------------------------------------------------ |
| int    | 整数，范围从 -2^63 到 2^63-1                              | int x = 1;                                       |
| double | 浮点数，64 位（双精度）浮点数                             | double x1 = 1.1;                                 |
| num    | 数字，可以是整数也可以是浮点数                            | num x2 = 1;                                      |
| String | 字符串，采用 UTF-16 编码，用单引号或者双引号来创建        | String str = "Dart";                             |
| bool   | 布尔值                                                    | bool isTrue = true;                              |
| List   | List\<E\>，E 表示 List 里面的类型，用 [] 表示             | List<int> x4 = [1, 2, 3];                        |
| Set    | Set\<E\>，E 表示 Set 里面的类型，用 {} 表示               | Set<int> x6 = {1, 2, 3};                         |
| Map    | Map<K, V>，K 表示 key 的数据类型，V 表示 value 的数据类型 | Map<String, int> x8 = {"first": 1, "second": 2}; |
| Runes  | 表示采用 UTF-32 编码的字符串，用于显示 Unicode            | Runes input = new Runes('\u{1f600}');            |

### 数据类型可变

```dart
dynamic example = "example";
```

和 `var` 的区别就是，`dynamic` 赋值后还可以改变数据类型

**一般用在 native 和 flutter 交互，native 传来的数据**

### Object

```dart
Object index = 100;
```

Dart 里所有东西都是对象，是因为 Dart 的所有东西都继承自 `Object`，因此 `Object` 可以定义任何变量，而且赋值后类型也可以更改

**不要滥用 `dynamic`，一般情况下都用 `Object` 来替代 `dynamic`**

## 常量

- 使用时可以吧 `var` 省略
- `final` 和 `const` 只能赋值一次，而且只能在声明的时候就赋值
- `const` 是隐式的 `final`
- 使用 `const` 时，如果变量是类里的变量，必须加 `static`

```dart
final content = "Dart";
static const bool isTrue = true;
```

**他们的区别是：`const` 是编译时常量，在编译的时候就初始化了，`final` 是当类创建的时候才初始化**

## 函数

在 Dart 中函数也是对象，类型是 `Function`

```dart
返回类型 函数名(函数参数) {}
```

```dart
String transString(int count) {
  return count.toString();
}
```

### 必选参数

```dart
bool isString(String content, int count) {
  print(content + count.toString());
  print(content is String);
  return content is String;
}
```

**必选参数必须在可选参数的前面**

### 可选参数

#### 可选命名参数

使用 `{}` 抱起来的参数是可选命名参数

```dart
bool say(String msg, {@required String from, int clock = 250}) {
  print(msg + " from " + from.toString() + " at " + clock.toString());
  return true;
}

say('Hello Flutter', from: 'home');
```

#### 可选位置参数

使用 `[]` 抱起来的参数是可选命名参数

```dart
bool say(String msg, [String from, int clock]) {
  print(msg + " from " + from.toString() + " at " + clock.toString());
  return true;
}

say('Hello Flutter', 'home', 1024);
```

#### 默认值

```dart
bool say(String msg, {@required String from, int clock = 250}) {
  print(msg + " from " + from.toString() + " at " + clock.toString());
  return true;
}
```

```dart
bool say(String msg, [String from, int clock = 250]) {
  print(msg + " from " + from.toString() + " at " + clock.toString());
  return true;
}
```

## 箭头语法

```dart
void main() => runApp(MyApp());
```

等价于

```dart
void main() {
  return runApp(MyApp());
}
```

## 类

默认的构造函数就是使用类名作为函数名的构造函数，就是下面的 `Point(num x, num y) { ... }`

```dart
class Point {
  num x, y;
  Point(num x, num y) {
    this.x = x;
    this.y = y;
  }
}
```

还可以如下写

```dart
class Point {
  num x, y;
  Point(this.x, this.y);
}
```

### 创建实例

不需要使用 `new` 来创建实例

通过 `.` 来引用实例变量或方法

```dart
class Point {
  num x, y;
  Point(this.x, this.y);
}

Point point = Point(0, 0);

print(point.x);
```

## 操作符

### 算术运算操作符

| 操作符 | 说明 | 例子                              |
| ------ | ---- | --------------------------------- |
| +      | 加   | num a = 1 + 2;                    |
| -      | 减   | num a1 = 1 - 2;                   |
| -num   | 负数 | num a2 = -1;                      |
| \*     | 乘   | num a3 = 1 \* 2;                  |
| /      | 除   | num a4 = 5 / 2; // 2.5            |
| ~/     | 整除 | num a5 = 5 ~/ 2; // 2             |
| %      | 取余 | num a6 = 5 % 2; // 1              |
| ++num  |      | num a7 = 1;num b = ++a7; // 2 2   |
| num++  |      | num a8 = 1;num b = a8++; // 2 1   |
| --num  |      | num a9 = 1;num b = --a9; // 0 0   |
| num--  |      | num a10 = 1;num b = a10--; // 0 1 |

### 比较操作符

| 操作符 | 说明       | 例子            |
| ------ | ---------- | --------------- |
| ==     | 相等       | assert(2 == 2); |
| !=     | 不等于相等 | assert(1 != 2); |
| >      | 大于       | assert(3 > 2);  |
| <      | 小于       | assert(1 < 2);  |
| >=     | 大于等于   | assert(3 >= 3); |
| <=     | 小于等于   | assert(3 <= 3); |

### 类型判断操作符

| 操作符 | 说明                                       | 例子                           |
| ------ | ------------------------------------------ | ------------------------------ |
| as     | 类型转换，如果为 null 的话会抛出异常       | (demo as Point).x = 0;「TODO」 |
| is     | 判断是否是某类型，如果为 null 返回 false   | isTrue is Function             |
| is!    | 判断是否不是某类型，如果为 null 返回 false | usTrue is! Function            |

### 赋值操作符

| 操作符 | 说明                                 | 例子                                           |
| ------ | ------------------------------------ | ---------------------------------------------- |
| =      |                                      |                                                |
| ??=    | 当被赋值对象位空才赋值，否则不会赋值 | num a,b;a = 1;b ??= 2; // b 为空才会被赋值为 2 |
| +=     | 先进行操作再赋值                     | a += b; // a = a + b;                          |
| -=     | 先进行操作再赋值                     |                                                |
| \*=    | 先进行操作再赋值                     |                                                |
| /=     | 先进行操作再赋值                     |                                                |
| ~/=    | 先进行操作再赋值                     |                                                |
| >>=    | 先进行操作再赋值                     |                                                |
| <<=    | 先进行操作再赋值                     |                                                |
| ^=     | 先进行操作再赋值                     |                                                |
| &=     | 先进行操作再赋值                     |                                                |
| \|=    | 先进行操作再赋值                     |                                                |

### 逻辑运算操作符

| 操作符 | 说明       | 例子                    |
| ------ | ---------- | ----------------------- |
| !(...) | 反转表达式 | !(2 == 3);              |
| \|\|   | 逻辑或     | (2 == 2) \|\| (2 == 3); |
| &&     | 逻辑与     | !(2 == 3) && (2 == 2);  |

### 按位与移位运算符

| 操作符 | 说明     | 例子 |
| ------ | -------- | ---- |
| &      | 按位与   |      |
| \|     | 按位或   |      |
| ^      | 按位异或 |      |
| ~      | 按位非   |      |
| <<     | 左移     |      |
| >>     | 右移     |      |

### 条件运算符

1. `demo ? function1 : function2;`
   1. 三元运算符
2. `demo1 ?? demo2;`
   1. 如果 demo1 为 null，就返回 demo2

### 级联操作符

`..`

允许对同一对象进行一系列的操作，除了函数调用，还可访问同一对象上的字段

```dart
class Point {
  num x, y;
  Point(this.x, this.y);
  add(int count) {}
}

Point point = Point(0, 0)
  ..x = 10
  ..y = 20
  ..add(250);
```

### 其他操作符

| 操作符 | 说明               | 例子     |
| ------ | ------------------ | -------- |
| ()     | 函数调用           |          |
| []     | 访问列表           |          |
| .      | 访问成员变量       |          |
| ?.     | 有条件访问成员变量 | foo?.bar |

### ?. ?? ??=

1. ?.
   1. `var yourName = user?.name`
      1. 如果 `user.name` 存在则返回 `user.name`，如果不存在则返回 `null`

```js
var yourName;
if (user == null) {
  yourName = null;
} else {
  yourName = user.name;
}
```

2. ??
   1. `var yourName = name ?? "Bob";`
      1. 如果 `name` 为 `null`，就返回 `Bob`;

```js
var yourName;
if (name == null) {
  yourName = "Bob";
} else {
  yourName = name;
}
```

3. ??=
   1. `user ??= User();`
      1. 如果 `user` 为 `null`，`user` 就等于 `User()`;
      2. 等于 `user = user ?? User();`

```js
if (user == null) {
  user = User();
}
```
