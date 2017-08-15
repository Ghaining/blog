---
title: JavaScript之正则表达式入门(基本语法与应用案例)
tags:
  - tags
categories: categories
date: 2017-08-15 09:20:09
---

## 简述

正则表达式的实现最早来自 Unix 系统, 并不是 JS 一家独有, 而是一套通用语法, 各家实现略有不同.

虽说目前 JS 技术发展迅速, 但很多部分都是借鉴其他语言的特性和框架发展而来的.

很多人对正则头痛, 常常写匹配时忘记语法如何写了. 😂

复杂度较高的的组合使用, 阅读性也很低, 例如 `/^[-a-z0-9~!$%^&*_=+}{\'?]+(\.[-a-z0-9~!$%^&*_=+}{\'?]+)*@([a-z0-9_][-a-z0-9_]*(\.[-a-z0-9_]+)*\.(aero|arpa|biz|com|coop|edu|gov|info|int|mil|museum|name|net|org|pro|travel|mobi|[a-z][a-z])|([0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}))(:[0-9]{1,5})?$/i` , 这是一段邮箱认证的正则.

正则出现的频率并不高, 出现时却有`尚方宝剑`的效果, 能一招见效, 达成目的, 正则是我们拿来精准匹配和替换的利器.

本篇旨在探索基本语法在实际中的应用.

## 基本语法


|     字符    |                                       含义                                      |
|-------------|---------------------------------------------------------------------------------|
| .           | 任意一个字符,不包括 `\n\r`                                                      |
| ^           | 以什么开始                                                                      |
| $           | 以什么结束                                                                      |
| *           | 元字符出现0或多次                                                               |
| +           | 元字符出现1或多次                                                               |
| ?           | 元字符出现0或1次                                                                |
| {n}         | 出现n次                                                                         |
| {n,}        | 出现最少n次                                                                     |
| {n,m}       | 出现n到m次之间                                                                  |
| [  ]        | 匹配`\n`以外的单字字符                                                          |
| [ab]        | 匹配a或b                                                                        |
| [a-d]       | 匹配一个a到d之间的小写字母                                                      |
| %[^a-zA-Z]% | 两个百分号之间不应该出现字母, `^`                                               |
| \b          | 单词边界                                                                        |
| \d          | 匹配一个数字                                                                    |
| \D          | 匹配一个非数字                                                                  |
| \s          | 匹配一个空白字符                                                                |
| \S          | 匹配一个非空白字符                                                              |
| \w          | 匹配一个非特殊字符, 包括_                                                       |
| \W          | 匹配一个特殊字符                                                                |
| \           | 转义字符, 正则语法中`^.$()¦*+?{}\`如需要匹配字符串中对应字符需要使用转义字符`\` |
| g           | 全局替换                                                                        |
| i           | 忽略大小写                                                                      |


## 相关属性和方法

### 正则实例的属性

![image.png](http://upload-images.jianshu.io/upload_images/4434201-4c96ce1a603b302a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

实例拥有5个属性:

- global：是否全局搜索，默认是false
- ignoreCase：是否大小写敏感，默认是false
- multiline：多行搜索，默认值是false
- lastIndex：是当前表达式模式首次匹配内容中最后一个字符的下一个位置，每次正则表达式成功匹配时，lastIndex属性值都会随之改变。
- source：正则表达式的文本字符串

### 正则原型的方法

我们可以用下面的方法创造一个正则表达式,

```js
// new 一个
let pattern1 = new RegExp('abc');
// 字面量生成
let pattern2 = /^\S/;
```

这两种创造正则表达式的效果是相同的.

可以用 new 来生成实例, 可见 RegExp 是一个构造函数, 那我们就去他的原型上看看都有哪些方法可用.

```js
Object.prototype.toString.call(RegExp)
// [object Function]
```

![image.png](http://upload-images.jianshu.io/upload_images/4434201-87c2a40e83bbae47.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们可以看到, 有下面3个可用的方法:

- test
- exec
- compile

#### test()

test 用于匹配字符串中是否存在符合正则匹配规则的字符, 存在返回 true, 不存在返回 false.

```js
let part = /o/g;
let ret = part.test('Hello World');
console.log(ret);
// true
```

#### exec()

exec 用于匹配字符串中是否存在符合正则匹配规则的字符, 存在返回一个数组, 包含匹配到的字符, 不存在返回 null .

```js
let part = /o/g;
let ret = part.exec('Hello World');
console.log(ret);
// ["o", index: 4, input: "Hello World"]
```

如果全局匹配, 多次运行, 会返回后面新匹配到的索引.

```js
let part = /e/g;
do {
result=part.exec("The best things in life are free");
console.log(result);
} while (result!=null)
// ["e", index: 2, input: "The best things in life are free"]
// ["e", index: 5, input: "The best things in life are free"]
// ["e", index: 22, input: "The best things in life are free"]
// ["e", index: 26, input: "The best things in life are free"]
// ["e", index: 30, input: "The best things in life are free"]
// ["e", index: 31, input: "The best things in life are free"]
```

#### compile()

compile 用于替换正则表达式的匹配规则, 编译正则为接近计算机底层编码格式.

```js
let part = /e/;

// 方法 1
part.compile('abc','g');
console.log(part);
// /abc/g

// 方法 2
part.compile(/def/);
console.log(part);
// /def/
```

到这里有人可能有疑问了, 这和我重新赋值一个新的正则表达式有什么区别?

区别还是有的, 我们使用的编程语言是经由机器码多次编译而来的, compile 可以编译为便于计算机理解的代码, 提升运行速度.

简单的匹配时没有明显速度差异的, 如果是大数据, 数据体量极大, 正则的执行速度差异就体现出来了.

### 字符串和正则相关的方法

字符串原型上有4个方法和正则相关联.

- String.prototype.search
- String.prototype.match
- String.prototype.replace
- String.prototype.split

#### String.prototype.search

search 用于检索字符串的制定子字符串, 返回索引位置(Number类型), 检索不成功返回 `-1`, 参数可用字符串与正则表达式匹配.

不能全局检索, 只能匹配一次, 忽略 `g`, 匹配成功立即返回.

```js
let str = 'Hello World';
console.log(str.search('H')); // 0
console.log(str.search('h')); // -1

console.log(str.search(/World/)); // 6
```

#### String.prototype.match

```js
let str = 'Hello World';
```

单次匹配, 返回一个数组, 包含 index 属性 和 input 属性, index 为匹配到的索引位置, input 为被匹配的父字符串, 返回值与 exec 相同.

![image.png](http://upload-images.jianshu.io/upload_images/4434201-2285affda4cfb35d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

全局匹配, 返回一个数组, 不包含 index 属性 和 input 属性, 匹配到的子字符串会以数组形式放置, 返回值与 exec 不同.

![image.png](http://upload-images.jianshu.io/upload_images/4434201-099a43019f059498.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### String.prototype.replace

replace 方法并不改变字符串本身, 而是返回替换后的新字符串.

```js
let str = 'Hello World';
str.replace('o','aaa') // "Hellaaa World"

str.replace(/o/gi, 0) // "Hell0 W0rld"
```

replace 的第二个参数也可以是函数, 参数有如下这些

| 参数       | 代表的数值                                                                                                      |
| match      | 匹配的子字符串                                                                                                  |
| p1,p2, ... | 匹配的每个字符串                                                                                                |
| offset     | 匹配到的子字符串在原字符串中的偏移量。（比如，如果原字符串是“abcd”，匹配到的子字符串是“bc”，那么这个参数将是1） |
| string     | 被匹配的原字符串                                                                                                |

```js
function replacer(match, p1, p2, p3, offset, string) {
  // p1 is nondigits, p2 digits, and p3 non-alphanumerics
  return [p1, p2, p3].join(' - ');
}
var newString = 'abc12345#$*%'.replace(/([^\d]*)(\d*)([^\w]*)/, replacer); // 'abc - 12345 - #$*%'：
```

已字符串作为参数, 可以使用以下特殊语句:

| 特殊字符 |                                             含义                                             |
|----------|----------------------------------------------------------------------------------------------|
| `$$`       | 插入一个 "$"。                                                                               |
| `$&`       | 插入匹配的子串。                                                                             |
| `$``       | 插入当前匹配的子串左边的内容。                                                               |
| `$' `      | 插入当前匹配的子串右边的内容。                                                               |
| `$n `      | 假如第一个参数是 RegExp对象，并且 n 是个小于100的非负整数，那么插入第 n 个括号匹配的字符串。 |

```js
// 交换字符串中的两个单词
var re = /(\w+)\s(\w+)/;
var str = "John Smith";
var newstr = str.replace(re, "$2, $1");
console.log(newstr);
// Smith, John
```



#### String.prototype.split

split 的参数可以用字符串和正则表达式匹配

```js
let str = 'HellosWorld';
str.split(/s/); // ["Hello", "World"]
```


## 应用案例

### 表单验证

待补充




## 参考资料

- W3C
- MDN