# UML

[文档](http://plantuml.com/zh/index)

## 时序图

### 示例

```uml
@startuml
participant "用户" as User #red
box "终端"
participant "终端1" as Box1
participant "终端2" as Box2
endbox

activate User
User -> Box1: Hello
...
Box1 --> User: Hi
deactivate User
@enduml
```

### 注释

- 可以使用 `'`（单引号）对单行进行注释
- 可以使用 `/'` 和 `'/` 作为注释的开始和结束

### 顶部声明

- title
  - 大标题
  - 可以使用 `**bold**` 等语法
  - 可以使用 `\n` 来进行分行
  - 还可以使用 `title` 和 `end title` 来定义多行标题
- header
  - 页标题
  - 可以使用 `center` 和 `left` 和 `right` 设置对齐方式
- footer
  - 页尾标题
  - 可以使用 `center` 和 `left` 和 `right` 设置对齐方式
- 分页
  - `newpage`
  - 当前页（`%page%`）和总页数（`%lastpage%`）
- autonumber
  - 自动添加消息编号
  - 设置初始值和步长（15 和 10）
    - `autonumber 15 10`
  - 表示暂停
    - `autonumber stop`
  - 表示继续
    - `autonumber resume`
- hide footbox
  - 移除脚注
  - 这会让参与者只有一个块（底部的块消失）
- scale
  - 缩放
  - `scale 1.5`
  - `scale 2/3`
  - `scale 200 width`
  - `scale 200*100`
  - `scale max 300*200`
  - `scale max 1024 width`
- skinparam
  - 样式定义
  - [文档](http://plantuml.com/zh/skinparam)

### 声明参与者

- **actor**
- **participant**
- **database**
- boundary
- control
- entity
- collections

甚至还可以使用 `box` 和 `end box` 来包裹参与者

```uml
@startuml
participant "用户" as User #red
box "终端"
participant "终端1" as Box1
participant "终端2" as Box2
end box

User -> Box1: 发送请求
Box1 -> Box2: 发送请求
Box2 --> Box1: 返回数据
Box1 --> User: 返回数据
@enduml
```

#### 扩展

- `as` 设置短语
  - `actor "用户" as User`
- `#red` 设置颜色
  - `actor User #red`
- `order` 设置排序（数字越小排位越前）
  - `participant User1 order 1`

### 修改箭头

#### 样式

- 末尾加 `x`
  - 表示一条丢失的消息
  - `User ->x Box: Hello`
- 将 `<` 或者 `>` 替换成 `\` 或者 `/`
  - 箭头只有上半部分或者下半部分
  - `User -/ Box: Hello`
- 将箭头标记写两次，如 `>>` 或者 `//`
  - 细箭头
  - `User ->> Box: Hello`
- 用 `--` 替代 `-`
  - 显示为虚线箭头
  - `User --> Box: Hello`
- 末尾加 `o`
  - 显示为末尾加圈
  - `User ->o Box: Hello`
- 开头加 `<`
  - 显示为双向箭头
  - `User <-> Box: Hello`

#### 颜色

- `User -[#red]> Box: Hello`
- `User -[#eee]> Box: Hello`

### 组合消息

- alt/else
- opt
- loop
- par
- break
- critical
- **group**

用 `end` 来结束分组

```uml
@startuml
participant "用户" as User
participant "终端" as Box

User -> Box: 发起请求
alt 请求成功
  Box --> User: 请求成功
else 请求失败
  Box --> User: 请求失败
  group 重新发起请求
    User -> Box: 重新发送请求
    loop 每隔 10 秒发送请求
      User -> Box: 重复发送请求
    end
    Box --> User: 返回请求
  end
else 其他情况
  Box --> User: 其他情况
end
@enduml
```

### 消息备注

在消息后面添加 `note left: xxx` 或者 `note right: xxx` 来给消息添加备注，还可以用 `end note 来添加多行备注

```uml
@startuml
participant "用户" as User
participant "终端" as Box

User -> Box: 发起请求
note left: 记得带上 token
Box --> User: 返回请求
note right: 会带上 cookie
User -> Box: 继续发送请求
note left
  后续的所有的请求
  都会自动带上 cookie
  无需其他操作
end note
@enduml
```

还可以使用 `note left of` 或者 `note right of` 或者 `note over` 在 participant 的相对位置设置备注

甚至可以设置备注的背景色 `note over User #ffaaaa`

甚至可以改变备注框的形状 `hnote` 或者 `rnote`

```uml
@startuml
participant "用户" as User
participant "终端" as Box

User -> Box: 发起请求
note left of User: 记得带上 token
Box --> User: 返回请求
note right of Box: 会带上 cookie
User -> Box: 继续发送请求
note over User, Box #ffaaaa
  后续的所有的请求
  都会自动带上 cookie
  无需其他操作
end note
@enduml
```

更甚至，还可以改变文字的样式

```uml
@startuml
participant "用户" as User
participant "终端" as Box

User -> Box: 发送请求
note over User, Box
  This is **bold**
  This is //italics//
  This is ""monospaced""
  This is --stroked--
  This is __underlined__
  This is ~~waved~~
  '某些环境波浪线不太好使
end note
@enduml
```

### 分隔符

使用 `== 分隔符 ==` 来让你的时序图看起来更有条理

```uml
@startuml
participant "用户" as User
participant "终端" as Box

User -> Box: 发起请求
== 发起了请求 ==
Box --> User: 返回请求
== 返回了请求 ==
@enduml
```

### 引用

```uml
@startuml
participant "用户" as User
participant "终端" as Box

ref over User, Box: init
User -> Box: 发起请求
== 发起了请求 ==
ref over User, Box
  load
end ref
Box --> User: 返回请求
== 返回了请求 ==
@enduml
```

### 延迟

可以使用 `...` 来表示延迟，还可以使用 `... 5 minutes ...` 来给延迟添加备注

```uml
@startuml
participant "用户" as User
participant "终端" as Box

== 发起了请求 ==
User -> Box: 发起请求
...
== 返回了请求 ==
... 5 minutes ...
Box --> User: 返回请求
@enduml
```

### 空间

可以使用 `|||` 来增加每一步的空间，还可以使用 `||45||` 来指定增加的像素的数量

```uml
@startuml
participant "用户" as User
participant "终端" as Box

User -> Box: 发起请求
|||
Box --> User: 返回请求
||100||
@enduml
```

### 生命周期

可以使用 `activate` 和 `deactivate` 和 `destroy` 来表示参与者的生命周期

甚至可以给生命周期添加颜色 `activate User #red`

再甚至可以嵌套生命周期

```uml
@startuml
participant "用户" as User
participant "终端" as Box

activate User #red
  User -> Box: 发起请求
  activate Box
    Box --> User: 返回数据
  deactivate Box
  activate User #blue
    User -> Box: 发送结束请求
  deactivate User
  activate Box
    Box --> User: 收到结束请求
  destroy Box
deactivate User
@enduml
```

### 创建参与者

这个语法可以使某些 participant 在某一步骤的时候被创建

```uml
@startuml
participant "用户" as User
participant "终端" as Box

activate User
create Box
User -> Box: 发送请求
activate Box
  Box --> User: 返回数据
deactivate Box
@enduml
```

### 进入和发出消息

该语法适合某些作为中间件的示例，使用 `[` 和 `]` 来创建

这里面也可以使用箭头的样式语法

```uml
@startuml
participant "终端" as Box

[-> Box: 发送请求
note right
  post 请求，并且 cookie 中携带了登录信息
end note
Box -->[: 返回数据
]-> Box: 其他请求
Box -->]: 返回其他数据
@enduml
```

### 构造类型和圈圈

可以使用 `<<` 和 `>>` 来给参与者添加构造类型

在构造类型中，还可以使用 `(X, color)` 来给参与者添加圈圈和颜色

```uml
@startuml
participant "用户" as User << user >>
participant "终端" as Box << (B, #ffaaaa) box >>

activate User
User -> Box: 发送请求
Box --> User: 返回数据
@enduml
```

### 图片标题

可以使用 `caption` 给图片增添标题

```uml
@startuml
participant "用户" as User << user >>
participant "终端" as Box << (B, #ffaaaa) box >>

caption UserStart
User -> Box: 发送请求
Box --> User: 返回数据
@enduml
```

### 图例说明

可以使用 `legend` 和 `end legend` 用于设置图例说明，还可以使用 `left` 和 `right` 和 `center` 作为图例对齐方式

```uml
@startuml
participant "用户" as User << user >>
participant "终端" as Box << (B, #ffaaaa) box >>

legend center
  简单请求
end legend
User -> Box: 发送请求
Box --> User: 返回数据
@enduml
```
