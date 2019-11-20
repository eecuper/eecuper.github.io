---
layout: post
title: js 笔记
category: 技术
tags: [js]
keywords: javaScript , 廖雪峰
---

### 入门知识

- 第一种是==比较，它会自动转换数据类型再比较，很多时候，会得到非常诡异的结果；
- 第二种是===比较，它不会自动转换数据类型，如果数据类型不一致，返回false，如果一致，再比较。
- 由于JavaScript这个设计缺陷，不要使用==比较，始终坚持使用===比较。

#### strict模式

> 'use strict';

在strict模式下运行的JavaScript代码，强制通过var申明变量，未使用var申明变量就使用的，将导致运行错误

#### ES6 变量转换

```javascript
var name = '小明';
var age = 20;
var message = '你好, ' + name + ', 你今年' + age + '岁了!';
alert(message);
如果有很多变量需要连接，用+号就比较麻烦。ES6新增了一种模板字符串，表示方法和上面的多行字符串一样，但是它会自动替换字符串中的变量：

var name = '小明';
var age = 20;
var message = `你好, ${name}, 你今年${age}岁了!`;
alert(message);
```

### 函数

#### 定义和调用
由于JavaScript函数允许接收任意个参数，于是我们就不得不用arguments来获取所有参数：

```javascript
function foo(a, b) {
    var i, rest = [];
    if (arguments.length > 2) {
        for (i = 2; i<arguments.length; i++) {
            rest.push(arguments[i]);
        }
    }
    console.log("a = " + a);
    console.log("b = " + b);
    console.log(rest);
}
```

ES6标准引入了rest参数，上面的函数可以改写为

```javascript
function foo(a, b, ...rest) {
    console.log('a = ' + a);
    console.log('b = ' + b);
    console.log(rest);
}

foo(1, 2, 3, 4, 5);
// 结果:
// a = 1
// b = 2
// Array [ 3, 4, 5 ]

foo(1);
// 结果:
// a = 1
// b = undefined
// Array []
```

**JavaScript引擎有一个在行末自动添加分号的机制，这可能让你栽到return语句的一个大坑：**

```javascript
function foo() {
    return { name: 'foo' };
}

foo(); // { name: 'foo' }

function foo() {
    return
        { name: 'foo' };
}

foo(); // undefined
```

#### 变量作用域与解构赋值

```javascript
function foo() {
    var x = 1;
    function bar() {
        var x = 'A';
        console.log('x in bar() = ' + x); // 'A'
    }
    console.log('x in foo() = ' + x); // 1
    bar();
}

foo();
```

JavaScript的函数在 **查找变量** 时从自身函数定义开始，**从“内”向“外”查找**。如果内部函数定义了与外部函数重名的变量，则内部函数的变量将“屏蔽”外部函数的变量。

**变量提升**

```javascript
function foo() {
    var y; // 提升变量y的申明，此时y为undefined
    var x = 'Hello, ' + y;
    console.log(x);
    y = 'Bob';
}
```

虽然是strict模式，但语句var x = 'Hello, ' + y;并不报错，原因是变量y在稍后申明了。但是console.log显示Hello, undefined，说明变量y的值为undefined。这正是因为JavaScript引擎自动提升了变量y的声明，但不会提升变量y的赋值。 所以 **请严格遵守“在函数内部首先申明所有变量”**

#### 局部作用域

由于JavaScript的变量作用域实际上是函数内部，我们在for循环等语句块中是无法定义具有局部作用域的变量的

```javascript
function foo() {
    for (var i=0; i<100; i++) {
        //
    }
    i += 100; // 仍然可以引用变量i
}

//了解决块级作用域，ES6引入了新的关键字let，用let替代var可以申明一个块级作用域的变量：

function foo() {
    var sum = 0;
    for (let i=0; i<100; i++) {
        sum += i;
    }
    // SyntaxError:
    i += 1;
}
```

#### 常量

ES6标准引入了新的关键字const来定义常量，const与let都具有块级作用域：

```javascript
const PI = 3.14;
PI = 3; // 某些浏览器不报错，但是无效果！
PI; // 3.14
```

#### 解构赋值

```javaScript
//es5方式
var array = ['hello', 'JavaScript', 'ES6'];
var x = array[0];
var y = array[1];
var z = array[2];

//es6方式
// 如果浏览器支持解构赋值就不会报错:
var [x, y, z] = ['hello', 'JavaScript', 'ES6'];
// x, y, z分别被赋值为数组对应元素:
console.log('x = ' + x + ', y = ' + y + ', z = ' + z);
```

对一个对象进行解构赋值时，同样可以直接对嵌套的对象属性进行赋值，只要保证对应的层次是一致的

```javascript
var person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678',
    school: 'No.4 middle school',
    address: {
        city: 'Beijing',
        street: 'No.1 Road',
        zipcode: '100001'
    }
};
var {name, address: {city, zip}} = person;
name; // '小明'
city; // 'Beijing'
zip; // undefined, 因为属性名是zipcode而不是zip
// 注意: address不是变量，而是为了让city和zip获得嵌套的address对象的属性:
address; // Uncaught ReferenceError: address is not defined
```

使用解构赋值对对象属性进行赋值时，如果对应的属性不存在，变量将被赋值为undefined，这和引用一个不存在的属性获得undefined是一致的。如果要使用的变量名和属性名不一致，可以用下面的语法获取：

```javaScript
var person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678',
    school: 'No.4 middle school'
};

// 把passport属性赋值给变量id:
let {name, passport:id} = person;
name; // '小明'
id; // 'G-12345678'
// 注意: passport不是变量，而是为了让变量id获得passport属性:
passport; // Uncaught ReferenceError: passport is not defined
```

__有些时候，如果变量已经被声明了，再次赋值的时候，正确的写法也会报语法错误：__

```javaScript
// 声明变量:
var x, y;
// 解构赋值:
{x, y} = { name: '小明', x: 100, y: 200};
// 语法错误: Uncaught SyntaxError: Unexpected token =

//这是因为JavaScript引擎把{开头的语句当作了块处理，于是=不再合法。解决方法是用小括号括起来：

({x, y} = { name: '小明', x: 100, y: 200});

```

### 方法

对象中的函数则叫做方法;

方法中的 this 

```javaScript
'use strict';

var xiaoming = {
    name: '小明',
    birth: 1990,
    age: function () { //函数内部又封装了一个自函数,
        var that = this ;  //这里的 this 指向 xiaoming 这个对象
        function getAgeFromBirth() {
            var y = new Date().getFullYear();
            return y - this.birth;  //这里的this 只是指向 undefined ,如果没有使用 strict 模式,则指向window 对象 , 而都不是指向 xiaoming 
        }
        return getAgeFromBirth();
    }
};

xiaoming.age(); // Uncaught TypeError: Cannot read property 'birth' of undefined
```

一个独立的函数调用中，根据是否是strict模式，this指向undefined或window，不过，我们还是可以控制this的指向的！使用函数的 apply() 方法

```javaScript
function getAge() {
    var y = new Date().getFullYear();
    return y - this.birth;
}

var xiaoming = {
    name: '小明',
    birth: 1990,
    age: getAge
};

xiaoming.age(); // 25
getAge.apply(xiaoming, []); // 25, this指向xiaoming, 参数为空

//普通函数通常把 this 绑定 null
Math.max.apply(null, [3, 5, 4]); // 5
Math.max.call(null, 3, 5, 4); // 5
```

#### 装饰器

利用apply()，我们还可以动态改变函数的行为。

JavaScript的所有对象都是动态的，即使内置的函数，我们也可以重新指向新的函数。

现在假定我们想统计一下代码一共调用了多少次parseInt()，可以把所有的调用都找出来，然后手动加上count += 1，不过这样做太傻了。最佳方案是用我们自己的函数替换掉默认的parseInt()：

```javaScript
'use strict';

var count = 0;
var oldParseInt = parseInt; // 保存原函数

window.parseInt = function () {
    count += 1;
    return oldParseInt.apply(null, arguments); // 调用原函数
};

// 测试:
parseInt('10');
parseInt('20');
parseInt('30');
console.log('count = ' + count); // 3.14
```


### 高阶函数

JavaScript的函数其实都指向某个变量。既然变量可以指向函数，函数的参数能接收变量，那么一个函数就可以接收另一个函数作为参数，这种函数就称之为高阶函数

```javascript
function add(x, y, f) {
    return f(x) + f(y);
}
```

#### map/reduce方法

**map方法**

```javaScript
'use strict';

function pow(x) {
    return x * x;
}

var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
var results = arr.map(pow); // [1, 4, 9, 16, 25, 36, 49, 64, 81]; //数组每个值分别作为 pow函数参数执行,然后生成新的数组
console.log(results);

```

**reduce方法**

reduce的用法。Array的reduce()把一个函数作用在这个Array的[x1, x2, x3...]上，这个函数必须接收两个参数，reduce()把结果继续和序列的下一个元素做累积计算

```javascript
[x1, x2, x3, x4].reduce(f) = f(f(f(x1, x2), x3), x4)

var arr = [1, 3, 5, 7, 9];
arr.reduce(function (x, y) {
    return x + y;
}); // 25

//要把[1, 3, 5, 7, 9]变换成整数13579，reduce()也能派上用场：

var arr = [1, 3, 5, 7, 9];
arr.reduce(function (x, y) {
    return x * 10 + y;
}); // 13579

```

#### filter方法

和map()类似，Array的filter()也接收一个函数。和map()不同的是，filter()把传入的函数依次作用于每个元素，然后根据返回值是true还是false决定保留还是丢弃该元素

```javaScript
var arr = [1, 2, 4, 5, 6, 9, 10, 15];
var r = arr.filter(function (x) {
    return x % 2 !== 0;
});
r; // [1, 5, 9, 15]
```

#### sort方法

字符串根据ASCII码进行排序，而小写字母a的ASCII码在大写字母之后, 
// 无法理解的结果:

```javascript
// 无法理解的结果:
[10, 20, 1, 2].sort(); // [1, 10, 2, 20]

//Array的sort()方法默认把所有元素先转换为String再排序，结果'10'排在了'2'的前面，因为字符'1'比字符'2'的ASCII码小。
```


#### Array

### 闭包

### 箭头函数

### generator
