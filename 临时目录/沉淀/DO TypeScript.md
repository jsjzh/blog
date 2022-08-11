# TypeScript

## 基本

```ts
// boolean
let flag: boolean = false;
// number
let num: number = 255;
// string
let str: string = "hello";
// number[]
let arr: number[] = [1, 2, 3];
// [string, number]
let arr2: [string, number] = ["a", 1];
// enum
enum Color {
  Red,
  Green,
  Blue
} // ==> {0: "Red", 1: "Green", 2: "Blue", Red: 0, Green: 1, Blue: 2}

let c: Color = Color.Green;
// any
let notSure: any = 256;
// void
function noRtn(): void {
  console.log("noRtn");
}
// never
function error(message: string): never {
  throw new Error(message);
}
// object
let obj: object = { name: "king" };
// 类型断言
let someValue: any = "some string";
let someLength: number = (<string>someValue).length;
// 于 JSX 一起使用时候仅允许 as 断言
let someLength2: number = (someValue as string).length;
```

## 接口

```ts
interface IConfig {
  color: string;
  width?: number;
  readonly x: number;
  [propName: string]: any;
}

interface IFunction {
  (str: string): boolean;
}
let myFunc: IFunction;
myFunc = function(str) {
  return str.length > 10;
};

// 当用 number 去索引 IArray 时会得到 string 类型的返回值
// ts 支持两种索引签名，string 和 number
interface IArray {
  [index: number]: string;
}
let myArray: IArray;
myArray = ["Bob", "Fred"];
class Animal {
  name: string;
}
class Dog extends Animal {
  breed: string;
}
// 但是数字索引的返回值，必须是字符串索引的返回值的子类型
// error，调换一下 Animal 和 Dog 即可
interface NotOkay {
  readonly [x: number]: Animal;
  [x: string]: Dog;
}

// 可以使用类来定义接口
class Timer {
  currentTime: Date;
}

// implements
interface IClockCtor {
  new (hour: number, mintue: number): IClockInterface;
}
interface IClockInterface {
  tick: () => void;
}
function createClock(ctor: IClockCtor, hour: number, mintue: number) {
  return new ctor(hour, mintue);
}
class OneClock implements IClockInterface {
  constructor(hour: number, mintue: number) {}
  tick() {
    console.log("oneClock");
  }
}
let one = createClock(OneClock, 10, 10);
// 还有就是用类表达式
const TwoClock: IClockCtor = class TwoClock implements IClockInterface {
  constructor(hour: number, mintue: number) {}
  tick() {
    console.log("twoClock");
  }
};
```

### 继承接口

```ts
interface Shape {
  color: string;
}
interface Two {
  width: string;
}
interface Square extends Shape, Two {
  name: string;
}
let square = <Square>{};
// 或者用 as
let square2 = {} as Square;
square.color = "red";
```

### 混合类型

```ts
interface Counter {
  (start: number): void;
  interval: number;
  reset(): void;
  reset2: () => void;
}
let counter = <Counter>function(start: number) {};
let counter2 = function(start: number) {} as Counter;
```

### 接口继承类

当接口继承了一个 `class` 类型时，他会继承成员但不包括其实现，这就意味着，如果创建了一个接口继承了一个拥有 `private` 和 `protected` 成员的类时，子类还应该实现这些成员

```ts
class Control {
  private state: any;
}
interface SelectControl extends Control {
  select: () => void;
}
// error，Image 缺少 state 属性
class Image implements SelectControl {
  select() {}
}
```

## 类

### 公有、私有与受保护的修饰词

默认，`public`，不显示的声明成员类型会默认为 `public`  
私有成员，`private`，当成员被标记成 `private`，它就不能在声明它的类的外部访问  
保护成员，`protected`，该成员在派生类中仍然可以访问，也就是子类可以访问基类的受保护成员，但在外部依旧不能访问。另外，构造函数也可以被标记成 `protected`，这意味着这个类不能在外部被实例化，但是能被继承

```ts
class Demo {
  public name: string;
  private foo: string;
  protected constructor(name: string) {
    this.name = name;
    this.foo = "demo";
  }
}

class ExDemo extends Demo {
  constructor(name: string) {
    super(name);
  }
}
// error，Demo 的构造函数是 protected 的，所以不能在外部被 new
let demo = new Demo("name");
let demo2 = new ExDemo("name");
demo2.name;
// error，foo 为私有成员
demo2.foo;
```

### readonly 修饰符

可以使用 readonly 将属性设置为只读，只读属性必须在声明时或构造函数里被初始化

```ts
class Demo {
  readonly name: string;
  readonly num: number = 8;
  constructor(theName: string) {
    this.name = theName;
  }
}
// 还可以使用简化版，这对于 public 和 protected 也是一样的
class Demo {
  readonly num: number = 8;
  constructor(readonly name: string) {}
}
```

### getter 和 setter

用该方法可以控制是否有权限更改一个属性

```ts
let flag = true;
class Demo {
  private _fullName: string;
  get fullName(): string {
    return this._fullName;
  }
  set fullName(newFullName): boolean {
    if (flag) this._fullName = newFullName;
    else consle.error("flag");
  }
}
```

另外，只带有 get 不带有 set 的属性，会被判断成 `readonly`

### 静态属性

```ts
class Demo {
  static firstName: string = "king";
  public getName(): string {
    return `${Demo.firstName} ${this.name}`;
  }
  constructor(public name: string) {}
}
let demo = new Demo("sun");
demo.getName(); // king sun
```

### 抽象类

`abstract` 被用于定于抽象类和在抽象类内部定义抽象方法，不同于接口，抽象类可以包含成员的实现细节，抽象类重的抽象方法不包含具体实现，**并且必须在派生类中实现**

```ts
abstract class AbsDemo {
  constructor(public name: string) {}
  getName(): string {
    return this.name;
  }
  abstract printName(): void;
}
class Demo extends AbsDemo {
  constructor(name: string) {
    // 在派生类中必须调用 super()
    super(name);
  }
  printName() {
    console.log(this.name);
  }
  getDemo() {
    return "demo";
  }
}
// error，不能创建一个抽象类的实例
let demo: AbsDemo = new AbsDemo("king");
let demo2: AbsDemo = new Demo("king");
demo2.getName(); // king
demo2.printName(); // king
// error，该方法在声明的抽象类中不存在
demo2.getDemo();
```

### typeof

```ts
class Demo {
  static names: string = "king";
  constructor(public firstName: string = "foo") {}
}
// Demo 是指实例的类型，这就无法读取到 names 属性，因为 names 是 Demo 的静态属性
let demo: Demo = new Demo();
// typeof Demo 意思是取 Demo 类的类型，而不是实例的类型
let Demo2: typeof Demo = Demo;
demo.firstName; // foo
Demo2.names = "sun";
let demo3: Demo = new Demo2();
Demo2.names; // sun
```

## 函数

### 可选参数和默认参数还有剩余参数

```ts
function demo(
  first: string,
  second: string = "sec",
  thr?: string,
  ...restNames: string[]
): string {
  return `${first} ${second} ${thr} ${restNames.join(" ")}`;
}
```

### this

```ts
interface UIElement {
  addClickListener(onclick: (this: void, e: Event) => void): void;
}
let uiElement: UIElement = {
  addClickListener(onclick) {}
};
class Handler {
  info: string = "";
  onClickGood = (e: Event) => {
    this.info = String(e.timeStamp);
  };
}
let h = new Handler();
uiElement.addClickListener(h.onClickGood);
```

### 重载

有的时候根据输入的不同，返回的值也会不同，就可以利用函数重载的写法

```ts
// 注意这不是声明一个函数，还是有些区别的
function getLength(x: any[]): number;
function getLength(x: string): number;
function getLength(x): any {
  if (typeof x == "object") {
    return x.length;
  } else {
    return x.length + 1;
  }
}
```

## 泛型

```ts
// 如下，给 identity 添加了类型变量 T，T 帮助我们捕获用户传入的类型
// identity 就叫做泛型
function identity<T>(arg: T): T {
  return arg;
}
// 以下两种都可以，第二种更加常见，编译器会根据传入的参数自动帮助我们确定 T 的类型，第一种情况只有在编译器无法确定 T 的类型的时候
let output = identity<string>("string");
let output2 = identity("string");

function getLength<T>(arg: T[]): number {
  console.log(arg.length);
  return arg.length;
}
```

```ts
interface IIdentity<T> {
  (arg: T): T;
}
function identity<T>(arg: T): T {
  return arg;
}
let myIdentity: IIDentity<number> = identity;
```

```ts
interface ILength {
  length: number;
}
function loggingIdentity<T extends ILength>(arg: T): T {
  console.log(arg.length);
  return arg;
}
```

```ts
function getProperty(obj: T, key: K) {
  return obj[key];
}
```

```ts
function create<T>(c: { new (): T }): T {
  return new c();
}
```

## 枚举

```ts
// 状态码的识别
const enum StatusDirection {
  SUCCESS,
  FAIL,
  PROCESS
}
// ===> {0: "SUCCESS", 1: "FAIL", 2: "PROCESS", SUCCESS: 0, FAIL: 1, PROCESS: 2}

// type LogLevelStrings = "SUCCESS" | "FAIL" | "PROCESS"
type LogLevelStrings = keyof typeof StatusDirection;
```

```ts
const enum FileAccess {
  // constant members
  None,
  Read = 1 << 1,
  Write = 1 << 2,
  ReadWrite = Read | Write,
  // computed member
  G = "123".length
}
// ===> {0: "None", 2: "Read", 3: "G", 4: "Write", 6: "ReadWrite", None: 0, Read: 2, Write: 4, ReadWrite: 6, G: 3}
```

## 高级类型

### 联合类型

用 `|` 分割，比如 `number | string | boolean`

### 类型保护

```ts
interface food {
  eatFood(): void;
}
interface drink {}
function Demo(pet: food | drink): pet is food {
  // 注意，这里要返回 boolean 的值才行
  return !!(pet as food).eatFood;
}
```

### 索引类型

以下就是通过 **索引类型查询** 和 **索引访问** 操作符

```ts
interface Person {
  name: string;
  age: number;
}

function pluck<T, K extends keyof T>(obj: T, names: K[]): T[K][] {
  return names.map(n => obj[n]);
}
let person: Person = {
  name: "Jarid",
  age: 35
};
let strings: string[] = pluck(person, ["name"]);
```

`keyof T` **索引类型查询操作符**，对于任何类型 `T`，`keyof T` 的结果为 `T` 上已知的公共属性名的联合，上面的示例中，可以把 `keyof` 理解成是 `name | age`

`T[K]` **索引访问操作符**，使用该操作符，只要保证 `K extends keyof T` 即可

### 映射类型

一个常见的需求是一个对象的所有属性都变成可选的或者是只读的

```ts
type IReadonly<T> = {
  readonly [P in keyof T]: T[P];
};
type IOptional<T> = {
  [P in keyof T]?: T[P];
};
interface Person {
  name: string;
  age: number | string;
}
type ReadonlyPerson = IReadonly<Person>;
type OptionalPerson = IOptional<Person>;
```

https://www.tslang.cn/docs/handbook/jsx.html
