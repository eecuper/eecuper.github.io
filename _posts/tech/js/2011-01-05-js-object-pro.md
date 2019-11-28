---
layout: post
title: js 面向对象编程
category: 技术
tags: [js, 廖雪峰]
keywords: [javaScript , 廖雪峰]
---
类：类是对象的类型模板，例如，定义Student类来表示学生，类本身是一种类型，Student表示学生类型，但不表示任何具体的某个学生；

实例：实例是根据类创建的对象，例如，根据Student类可以创建出xiaoming、xiaohong、xiaojun等多个实例，每个实例表示一个具体的学生，他们全都属于Student类型。

JavaScript不区分类和实例的概念，而是通过原型（prototype）来实现面向对象编程

### 创建对象
```javascript
var Student = {
    name: 'Robot',
    height: 1.2,
    run: function () {
        console.log(this.name + ' is running...');
    }
};

var xiaoming = {
    name: '小明'
};
//xiaoming指向对象 Studeng ,就拥有了  Student 的属性和方法
xiaoming.__proto__ = Student;
xiaoming.run();  //小明 is running ...

var Bird = {
    fly: function () {
        console.log(this.name + ' is flying...');
    }
};
//xiaoming指向对象 Bird ,拥有了Bird fly方法 , 不保留拥有Student run方法
xiaoming.__proto__ = Bird;
xiaoming.fly(); // 小明 is flying ...
xiaoming.run(); // 无法执行
```
__proto__会改变所指向对象不建议在编写的时候使用,通常使用 Object.create()来创建对象
```javascript
// 原型对象:
var Student = {
    name: 'Robot',
    height: 1.2,
    run: function () {
        console.log(this.name + ' is running...');
    }
};

function createStudent(name) {
    // 基于Student原型创建一个新对象:
    var s = Object.create(Student);
    // 初始化新对象:
    s.name = name;
    return s;
}

var xiaoming = createStudent('小明');
xiaoming.run(); // 小明 is running...
xiaoming.__proto__ === Student; // true
```
### 原型继承

JavaScript对每个创建的对象都会设置一个原型，指向它的原型对象

当我们用obj.xxx访问一个对象的属性时，JavaScript引擎先在当前对象上查找该属性，如果没有找到，就到其原型对象上找，如果还没有找到，就一直上溯到Object.prototype对象，最后，如果还没有找到，就只能返回undefined

```javascript
var arr = [1, 2, 3]
//原型链接
arr ----> Array.prototype ----> Object.prototype ----> null
```

**构造函数**
```javascript
function Student(name) {
    this.name = name;
    this.hello = function () {
        alert('Hello, ' + this.name + '!');
    }
}
//注意，如果不写new，这就是一个普通函数，它返回undefined。但是，如果写了new，它就变成了一个构造函数，它绑定的this指向新创建的对象，并默认返回this，也就是说，不需要在最后写return this;

//新创建的xiaoming的原型链是：
xiaoming ----> Student.prototype ----> Object.prototype ----> null
//也就是说，xiaoming的原型指向函数Student的原型。如果你又创建了xiaohong、xiaojun，那么这些对象的原型与xiaoming是一样的：
xiaoming ↘
xiaohong -→ Student.prototype ----> Object.prototype ----> null
xiaojun  ↗
```

**NEW的重要性**

如果一个函数被定义为用于创建对象的构造函数，但是调用时忘记了写new怎么办？

在strict模式下，this.name = name将报错，因为this绑定为undefined，在非strict模式下，this.name = name不报错，因为this绑定为window，于是无意间创建了全局变量name，并且返回undefined，这个结果更糟糕。

所以，调用构造函数千万不要忘记写new。为了区分普通函数和构造函数，按照约定，构造函数首字母应当大写，而普通函数首字母应当小写，这样，一些语法检查工具如jslint将可以帮你检测到漏写的new。

最后，我们还可以编写一个createStudent()函数，在内部封装所有的new操作。一个常用的编程模式像这样：

```javascript  
function Student(props) {
    this.name = props.name || '匿名'; // 默认值为'匿名'
    this.grade = props.grade || 1; // 默认值为1
}

Student.prototype.hello = function () {
    alert('Hello, ' + this.name + '!');
};

function createStudent(props) {
    return new Student(props || {})
}
//这个createStudent()函数有几个巨大的优点：一是不需要new来调用，二是参数非常灵活，可以不传，也可以这么传

//这里既创建了对象 , 同时又避免了忘记new的情况, 直接使用普通函数来获取新的对象

var xiaoming = createStudent({
    name: '小明'
});

xiaoming.grade; // 1
```

### class继承