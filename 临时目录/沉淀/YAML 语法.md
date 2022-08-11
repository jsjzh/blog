# YAML 语法

[测试语法](https://nodeca.github.io/js-yaml/)

## 基本语法

- 大小写敏感
- 使用缩进表示层级关系
- 缩进时不允许使用 tab 键，只允许使用空格
- 缩进的空格数目不重要，只要相同层级的元素左侧对齐即可

## 对象

使用 `: ` 结构表示对象的一组键值对

```yaml
language: chinese
```

```js
const data = {
  language: "chinese";
}
```

## 数组

使用 `- ` 结构表示数组

```yaml
- chinese
- english
```

```js
const data = ["chinese", "english"];
```

如果数组包含数组，可以用如下方式

```yaml
- - chinese
  - english
```

```js
const data = [["chinese", "english"]];
```

## 复合结构

```yaml
languages:
  - Ruby
  - Perl
  - Python
websites:
  YAML: yaml.org
  Ruby: ruby-lang.org
  Python: python.org
  Perl: use.perl.org
```

```js
const data = {
  languages: ["Ruby", "Perl", "Python"],
  websites: {
    YAML: "yaml.org",
    Ruby: "ruby-lang.org",
    Python: "python.org",
    Perl: "use.perl.org",
  },
};
```

## 纯量

纯量是最基本不可再分的值

```yaml
number: 12.30
isTrue: true
parent: -
iso8601: 2001-12-14t21:59:43.10-05:00
date: 1976-07-31
e: !!str 123
f: !!str true
```

```js
const data = {
  number: 12.3,
  isTrue: true,
  parent: null,
  iso8601: new Date('2001-12-14t21:59:43.10-05:00'),
  date: new Date('1976-07-31')
  e: "123",
  f: "true"
}
```

## 字符串

字符串默认不使用引号表示

```yaml
str: "string"
```

```js
const data = {
  str: "string";
}
```

如果字符串中有空格或者特殊字符，需要放在引号内

```yaml
str: "内容: 字符串"
```

```js
const data = {
  str: "内容: 字符串";
}
```

单引号会对特殊字符转义，双引号则不会

```yaml
s1: '内容\n字符串'
s2: "内容\n字符串"
```

```js
const data = {
  s1: "内容\\n字符串",
  s2: "内容\n字符串",
};
```

字符串可以写成多行，用 `|` 保留换行符，或者用 `>` 把换行符当成空格

```yaml
this: |
  foo
  bar
that: >
  foo
  bar
```

```js
const data = {
  this: "foo\nbar\n",
  that: "foo bar\n",
};
```

`+` 表示保留字符串末尾的换行，`-` 表示删除字符串末尾的换行

```yaml
s1: |
  foo

s2: |+
  foo


s3: |-
  foo
```

```js
const data = {
  s1: "foo\n",
  s2: "foo\n\n\n",
  s3: "foo",
};
```

## 引用

锚点 `&` 和别名 `*`，可以用来引用，`<<` 用来合并到当前数据

```yaml
defaults: &defaults
  adapter: postgres
  host: localhost

development:
  database: myapp_development
  <<: *defaults

test:
  database: myapp_test
  <<: *defaults
```

```yaml
defaults:
  adapter: postgres
  host: localhost

development:
  database: myapp_development
  adapter: postgres
  host: localhost

test:
  database: myapp_test
  adapter: postgres
  host: localhost
```

```yaml
- &default Steve
- Clark
- Brian
- Oren
- *default
```

```yaml
- Steve
- Clark
- Brian
- Oren
- Steve
```
