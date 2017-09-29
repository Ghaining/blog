---
title: JavaScript之正则表达式入门(基本语法与应用)
tags:
  - 正则
  - 正则表达式
categories: JavaScript
date: 2017-08-15 09:20:09
---

## 简述

正则出现的频率并不高, 出现时却有`尚方宝剑`的效果, 能一招见效, 达成目的, 是我们拿来精准匹配、替换文本的利器.

正则表达式的实现最早来自 Unix 系统, 并不是 JS 一家独有, 而是一套通用语法, 各家实现略有不同.

虽说目前 JS 技术发展迅速, 但很多部分都是借鉴其他语言的特性和框架发展而来的.

很多人对正则头痛, 常常写匹配时忘记语法如何写了, 我也经常这样. 😂

掌握语法并不难, 难在灵活运用啊. 🤔

复杂度较高的正则, 阅读性很低, 例如 `/^[-a-z0-9~!$%^&*_=+}{\'?]+(\.[-a-z0-9~!$%^&*_=+}{\'?]+)*@([a-z0-9_][-a-z0-9_]*(\.[-a-z0-9_]+)*\.(aero|arpa|biz|com|coop|edu|gov|info|int|mil|museum|name|net|org|pro|travel|mobi|[a-z][a-z])|([0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}))(:[0-9]{1,5})?$/i` , 这是一段邮箱认证的正则, 我是看的要吐血了 🙄

本文来探讨下正则语法的使用和相关的 Api.

## 基本语法

提供正则匹配表供阅览, 掌握规则是起步.

|   字符   |                                       含义                                      |
|----------|---------------------------------------------------------------------------------|
| .        | 匹配不包括 `\n` 的任意一个字符                                                  |
| ^        | 匹配以什么字符开始的字符串                                                      |
| $        | 匹配以什么字符结束字符串                                                        |
| *        | 字符出现0或多次                                                                 |
| +        | 字符出现1或多次                                                                 |
| ?        | 字符出现0或1次                                                                  |
| {n}      | 出现n次                                                                         |
| {n,}     | 出现最少n次                                                                     |
| {n,m}    | 出现n到m次之间                                                                  |
| (hello?) | 匹配一个字符串或一段正则表达式                                                  |
| [a]      | 用来匹配单个字符                                                                |
| [abcd]   | 匹配任意一个单字字符,从左至右,返回先匹配的                                      |
| [a-d]    | 匹配一个a到d之间的一个小写字母                                                  |
| [^x]     | 匹配字符 x 以外的任意一个字符                                                   |
| \b       | 单词边界, 匹配一个位置, 0宽度, 一侧是单词(\w的匹配范围), 一侧是非单词           |
| \B       | 不是单词边界                                                                    |
| \d       | 匹配一个数字                                                                    |
| \D       | 匹配一个非数字                                                                  |
| \s       | 匹配一个空白字符                                                                |
| \S       | 匹配一个非空白字符                                                              |
| \w       | 匹配一个非特殊字符, 包括_ , 等效于 [a-zA-Z0-9_]                                 |
| \W       | 匹配一个特殊字符, 等效于 [^a-zA-Z0-9_]                                          |
| \        | 转义字符, 正则语法中`^.$()¦*+?{}\`如需要匹配字符串中对应字符需要使用转义字符`\` |
| g        | 全局替换                                                                        |
| i        | 忽略大小写                                                                      |
| m        | 多行匹配                                                                        |


正则用一些语法去匹配字符串, 如果我们想匹配字符串中与语法字符串相同的字符,
需要使用转义符 `\`, 需要转移的字符包括 `* . ? + $ ^ [ ] ( ) { } | \ /`.

常用的匹配写几个例子大致就理解了, 下面会有介绍在 JavaScript 中正则的相关方法, 不好理解的是 `\b` 单词边界和修饰符 `m` 多行匹配.

- 单词边界

`\b` 并不是匹配字符的, 它匹配的是一个位置, 一侧是单词(大小英文、数字、下划线组成的单词), 一侧是非单词, 本身是 0 宽度的.

```js
let str = `hello, welcome to xingchuan's home!`;
let re = /xingchuan\b'\bs/;
console.log(str.match(re)); // ["xingchuan's"]
```
字符串的开始和结束位置也是存在单词边界的, `hello` 之前就存在.

```js
let str = `hello, welcome to xingchuan's home!`;
let re = /\bhe/;
console.log(str.match(re)); // ["he", index: 0, input: "hello, welcome to xingchuan's home!"]
```
- 多行匹配

正常一个字符串只有一个开始和一个结束位置, 但用了 `m` 修饰符后可以改变 `^` 和 `$` 的匹配模式.

```js
let str = `hello, welcome to xingchuan's home!`;
let re = /^xingchuan/;
console.log(str.match(re)); // null
```

加入换行符 `\n` 之后就可以匹配到了

```js
let str = `hello, welcome to \nxingchuan's home!`;
let re = /^xingchuan/m;
console.log(str.match(re)); // ["xingchuan", index: 19, input: "hello, welcome to ↵xingchuan's home!"]
```

## 相关属性和方法

### 正则实例的属性

![image.png](http://upload-images.jianshu.io/upload_images/4434201-4c96ce1a603b302a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

实例拥有5个属性:

- global：是否全局搜索，默认是false
- ignoreCase：是否大小写敏感，默认是false
- multiline：多行搜索，默认值是false
- lastIndex：是当前表达式模式首次匹配内容中最后一个字符的下一个位置，每次正则表达式成功匹配时，lastIndex属性值都会随之改变。
- source：正则表达式的匹配规则字符串

### 正则原型的方法

我们可以用下面的方法创造一个正则表达式,

```js
// new 一个
let re1 = new RegExp('hello');
// 字面量生成
let re2 = /^\S/;
```

这两种创造正则表达式的效果是相同的.


![image.png](http://upload-images.jianshu.io/upload_images/4434201-87c2a40e83bbae47.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们可以看到, 有下面3个可用的方法:

- test
- exec
- compile

#### RegExp.prototype.test()

test 用于匹配字符串中是否存在符合正则匹配规则的字符, 存在返回 true, 不存在返回 false.

```js
let part = /xingchuan/g;
let re = part.test(`hello, welcome to xingchuan's home!`);
console.log(re); // true
```

#### RegExp.prototype.exec()

exec 用于匹配字符串中是否存在符合正则匹配规则的字符, 存在返回一个数组, 包含匹配到的字符, 不存在返回 null .

|  属性  |                 属性值                 |
|--------|----------------------------------------|
| 索引 0 | 正则表达式匹配到的字符串               |
| 索引 1 | 正则表达式中`()`和`[]`中匹配到的字符串 |
| ...    | ...                                    |
| 索引 n | 正则表达式中`()`和`[]`中匹配到的字符串 |
| index  | 索引 0 匹配到字符串首字符的索引        |
| input  | 被匹配的原始字符串                     |

```js
let re = /hello\sworld/g;
let str = 'This is hello world!This is hello world!This is hello!';
console.log(re.exec(str).index); // 8
console.log(re.exec(str).index); // 28

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

#### RegExp.prototype.compile()

compile 用于替换正则表达式的匹配规则, 编译正则为接近计算机底层编码格式.

PS: 👇 该特性已经从 Web 标准中删除，虽然一些浏览器目前仍然支持它，但也许会在未来的某个时间停止支持，请尽量不要使用该特性


### 字符串和正则相关的方法

字符串原型上有4个方法和正则相关联.

- search
- match
- replace
- split

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

|    参数    |                                                   代表的数值                                                    |
|------------|-----------------------------------------------------------------------------------------------------------------|
| match      | 匹配的子字符串                                                                                                  |
| p1,p2, ... | 匹配的每个字符串                                                                                                |
| offset     | 匹配到的子字符串在原字符串中的偏移量。（比如，如果原字符串是“abcd”，匹配到的子字符串是“bc”，那么这个参数将是1） |
| string     | 被匹配的原字符串                                                                                                |

```js
function replacer(match, p1, p2, p3, offset, string) {
  // p1 is nondigits, p2 digits, and p3 non-alphanumerics
  reurn [p1, p2, p3].join(' - ');
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
var str = "xing chuan";
var newstr = str.replace(re, "$2, $1");
console.log(newstr);
// chuan, xing
```

#### String.prototype.split

split 的参数可以用字符串和正则表达式匹配

```js
let str = `hello, welcome to     xingchuan's home!`;
let arr = str.split(/[\s]+/);
console.log(arr);
```

## 一些应用

推荐几个在线调试正则表达式的网址:

1. http://tools.jb51.net/regex/javascript
2. http://www.regexpal.com/
3. http://www.rubular.com/

- 判断是否为IOS移动端

```js
/(iPod)|(iPhone)|(iPad)/.test('User-Agent,Mozilla/5.0 (iPhone; U; CPU iPhone OS 4_3_3 like Mac OS X; en-us) AppleWebKit/533.17.9 (KHTML, like Gecko) Version/5.0.2 Mobile/8J2 Safari/6533.18.5')
```

- 判断当前IOS系统版本

```js
var us = 'User-Agent,Mozilla/5.0 (iPhone; U; CPU iPhone OS 10_1_3 like Mac OS X; en-us) AppleWebKit/533.17.9 (KHTML, like Gecko) Version/5.0.2 Mobile/8J2 Safari/6533.18.5';

console.dir(us.match(/os\s(\d+)/i)[1] - 0);
console.dir(/os\s(\d+)/i.exec(us)[1] - 0);
```

- 判断是否为安卓

```js
let us = 'User-Agent, Opera/9.80 (Android 2.3.4; Linux; Opera Mobi/build-1107180945; U; en-GB) Presto/2.8.149 Version/11.10';
let isAndroid = /android/i.test(us);
```

- 判断安卓系统版本

```js
let us = 'User-Agent, Opera/9.80 (Android 2.3.4; Linux; Opera Mobi/build-1107180945; U; en-GB) Presto/2.8.149 Version/11.10';
let isAndroid = /android\s(\d+)/i.exec(us)[1] - 0;
```
各种浏览器的UserAgent, [点这里](http://tools.jb51.net/table/useragent)

- 匹配中文

```js
let re = /[\u4e00-\u9fa5]/; // 目前网络通用的繁简体中文映射表区间
let str = "行川草庐";
console.log(re.test(str));
```
- 邮箱验证

```js
let re = /\w[-\w.+]*@([A-Za-z0-9][-A-Za-z0-9]+\.)+[A-Za-z]{2,8}/; //域名允许用 xc-cl.cc 中划线连接
let str = "xccl@xccl.cc";
console.log(re.test(str));
```

- URL判断

```js
let re = /^((https|http|ftp)?:\/\/)[^\s]+/;
let str = "http://xccl.cc/";
console.log(re.test(str));
```

正则可以做的事太多了, 大家慢慢研究吧 🌝

## 参考资料

- W3C
- MDN

