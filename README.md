# ECMAScript 6 特性

文/[@啊啊啊啊啊豆豆](http://weibo.com/208083451)

2015/02/09

## 前言
ECMAScript 是浏览器脚本语言 JavaScript 的通用标准。该标准由 ECMA 协会在 1997 年以 ECMA-262 为代号被首次发布。此标准自发布至今共有 5 个官方发布版本，ECMAScript 6 则将成为第 6 个发布版本。


## 简介
ECMAScript 6 是下一代 ECMAScript 标准。此标准将在今年 6 月得到批准。本[草案](https://people.mozilla.org/~jorendorff/es6-draft.html)在 2013 年初被冻结并在当年 12 月发布，在此之后的一切改动被加入到 ECMAScript 7 版本中。ES6 是一次重大更新，并且是自 2009 年发布 ECMAScript 5 后的第一次更新。文章重点介绍 ECMAScript 6 大部分新增的特性。（本文后面将以“ES6”简称代替“ECMAScript 6”）


## 特性
ES6 包含如下特性：
- `let`和`const`操作符
- 箭头函数
- 类结构
- 增强的对象写法
- 字符串模板
- 多变量赋值
- 遍历器和`for...of`操作符
- 默认参数和 rest 运算符
- 生成器
- Unicode
- 模块
- Map 与 Set
- 符号
- Promises
- 新 API
- 二进制及八进制写法
- 尾调用

### let 和 const 操作符
`let`为块级变量声明操作符，用来代替ES5中的`var`。用法与`var`相同，声明的变量只在当前范围内有效。`const`用来声明常量，一旦声明，不能再改变（再次赋值无效，但不报错）。

```javascript
const foo = "Hello world!";
foo = "Bye world!";
console.log(foo); // "Hello world!"
{
    let bar = 0;
    let bar = 1;  // TypeError: Duplicate declaration bar
}
console.log(bar); // ReferenceError: bar is not defined.
```


### 箭头函数
ES6 中允许使用箭头`=>`来定义函数，并且同时支持语句体和表达式。需要注意的是，函数体内的`this`对象是与上下文中的`this`相绑定，函数体内也不存在`arguments`对象，且使用箭头`=>`定义的函数不可以被当做构造函数。

```javascript
const foo = ['H', 'e', 'l', 'l', 'o'];

var bar = "";

foo.forEach(i => {
    bar += i;
    console.log(bar);
});

console.log(foo.map(i => i.charCodeAt()));
```


### 类结构
ES6 的类结构对于目前基于原型的面向对象的 ES5 而言是一个简单的语法糖。亮点在于其支持构造，继承，静态方法，以及父类型方法调用。

```javascript
class Creature {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    setName(name) {
        this.name = name;
    }
    setAge(age) {
        this.age = age;
    }
}

class Human extends Creature {
    constructor(name, age, gender) {
        super(name, age);
        this.gender = gender;
    }
    setName(name) {
        super.setName(name);
    }
    setAge(age) {
        super.setAge(age);
    }
    setGender(gender) {
        this.gender = gender;
    }
    static newCreature(name, age) {
        return new Creature(name, age);
    }
}
```

### 增强的对象写法
ES6 标准简化了 Object 对象的初始化，直接写入变量和函数，以作为属性以及方法，且同时支持动态属性名称。

```javascript
var name = "foo";

var bar = {
    __proto__: String,
    name,
    setName(name) {
        this.name = name;
    },
    ['is' + (() => "String")()]: true
};
```


### 字符串模板
ES6 中提供的字符串模板同样为对于 ES5 的语法糖，支持类似 Perl 风格的模板插值功能，同时也支持多行的字符串。这个特性极大地提升了字符串构造的安全性。

```javascript
var [firstId, secondId, className] = ["foo", "bar", "baz"];

var template = `<div>
<div id="${firstId}" class="${className}">
    ${firstId}! 
</div>
<div id="${secondId}" class="${className}">
    ${secondId}!
</div>
</div>`;

console.log(template);
```


### 多变量赋值
ES6 允许使用多种方式对变量初始化并赋值，并且接受默认值。

```javascript
var [a, , b] = [1, 2, 3];

var { foo, bar } = { foo: "foo", bar: "bar" };

var [baz = 1] = [];

console.log([a, b, foo, bar, baz]);  // [ 1, 3, 'foo', 'bar', 1 ]
```


### 遍历器和 for...of 操作符
拥有`next`方法，并且返回一个带有`done`和`value`两个属性的对象就是一个遍历器。遍历器可以被`for...of`遍历。（目前最新版本的 Google Chrome 已经支持这个特性）

```javascript
let fib = {
    [Symbol.iterator]() {
        let pre = 0, cur = 1;
        return {
            next() {
                [pre, cur] = [cur, pre + cur];
                return { done: false, value: cur };
            }
        };
    }
};

for (var n of fib) {
    if (n > 100) console.log(n);
}
```


### 默认参数和 rest 运算符
类似 Python 以及 Go 风格的函数初始化，允许初始化未传入参数的默认值，以代替`arguments`对象。rest 运算符则用于获取多余参数，以简化函数定义。

```javascript
function foo(a=2, ...bar) {
    for (var v of bar) {
        a *= v;
    }
    return a;
}

console.log(foo());         // 2
console.log(foo(3, 5, 2));  // 30
```


### 生成器
调用时返回一个内部状态的遍历器，使用`next()`方法获取下一个对象。

```javascript
function* nameGenerator() {
    yield "foo";
    yield "bar";
    yield "baz";
}

var g = nameGenerator();

console.log(g.next().value);  // "foo"
console.log(g.next().value);  // "bar"
console.log(g.next().value);  // "baz"
console.log(g.next().value);  // undefined
```


### Unicode
更好地支持 Unicode 字符。

```javascript
console.log("呵呵".length === 4);
```


### 模块
从语言层面支持模块定义以及模块引用，与目前流行的模块加载器 AMD 等用法相同，并同样采用异步加载。

```javascript
// math.js

export function factorial(n=0) {
    return n <= 1 ? 1 : n * factorial(n - 1);
}
```

```javascript
// foo.js

import factorial from "math";

console.log("5! = " + factorial(5));
```

```javascript
// bar.js

module math from "math";

console.log("5! = " + math.factorial(5));
```


### Map 与 Set
Map 和 Set 是 ES6 中新增的高效数据结构。（目前最新版本的 Google Chrome 已经支持这个特性）

```javascript
var s = new Set();
s.add("foo").add("bar").add("foo").add("bar");
console.log(s.size);        // 2
console.log(s.has("bar"));  // true
```

```javascript
var m = new Map();
m.set("foo", "bar");
console.log(m.get("foo"));  // "bar"
```


### 符号
ES6 支持符号定义，允许在对象内部以符号形式进行访问控制。同时，符号也是一种新的基本数据类型。（目前最新版本的 Google Chrome 已经支持这个特性）

```javascript
var key = Symbol("foo");

class Bar {
    constructor(foo) {
        this[key] = foo;
    }
}

var bar = new Bar("foo");

console.log(bar['foo']);  // undefined
console.log(bar[key]);    // "foo"
```


### Promises
Promises 是一个异步方案库。目前很多 JavaScript 库都是使用 Promises 模型建立的。ES6 将 Promises 移入其标准中，意味着更好地支持更多流行的 JavaScript 库。


### 新 API
ES6 在原来的基础上，对于`Number`对象、`String`对象等增添了许多新的库级方法，将极大地提高开发效率。（目前最新版本的 Google Chrome 已经支持部分特性）

```javascript
console.log(Number.EPSILON);         // 2.220446049250313e-16
console.log(Number.isInteger(1.4));  // false

console.log("foo".repeat(5));        // "foofoofoofoofoo"
console.log("foo".contains("oo"));   // true
```

### 二进制及八进制写法
两个新的进制写法：二进制`0b`和八进制`0o`。

```javascript
console.log(0b10000000000);  // 1024
console.log(0o2000);         // 1024
console.log(0x400);          // 1024
```


### 尾调用
在 ES6 中，尾调用被安全限制，调用递归函数时，保证栈不会溢出。

```javascript
function factorial(n=0) {
    return n <= 1 ? 1 : n * factorial(n - 1);
}

factorial(100000);  // 在 ES5 中将会出现栈溢出
```

*本作品采用知识共享署名-非商业性使用-禁止演绎 2.5 中国大陆许可协议进行许可。*
