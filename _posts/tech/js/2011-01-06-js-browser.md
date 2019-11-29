---
layout: post
title: js 浏览器
category: 技术
tags: [js, 廖雪峰,浏览器]
keywords: [javaScript , 廖雪峰,浏览器]
---
### 浏览器对象
- **window**
innerWidth和innerHeight属性，可以获取浏览器窗口的内部宽度和高度。内部宽高是指除去菜单栏、工具栏、边框等占位元素后，用于显示网页的净宽高

outerWidth和outerHeight属性，可以获取浏览器窗口的整个宽高
- **navigator**
  
navigator对象表示浏览器的信息，最常用的属性包括：

1. navigator.appName：浏览器名称；
2. navigator.appVersion：浏览器版本；
3. navigator.language：浏览器设置的语言；
4. navigator.platform：操作系统类型；
5. navigator.userAgent：浏览器设定的User-Agent字符串

```javascript
//浏览器类型判断
var width;
if (getIEVersion(navigator.userAgent) < 9) {
    width = document.body.clientWidth;
} else {
    width = window.innerWidth;
}
//navigator的信息可以很容易地被用户修改
navigator.userAgent = 0; 
//上述userAgent认为更改后,导致判断不准确.所以正确的方法是充分利用JavaScript对不存在属性返回undefined的特性，直接用短路运算符||计算：
var width = window.innerWidth || document.body.clientWidth;
```

- **screen**
. screen.width：屏幕宽度，以像素为单位；
. screen.height：屏幕高度，以像素为单位；
. screen.colorDepth：返回颜色位数，如8、16、24。

- **location**

location对象表示当前页面的URL信息
```javascript
http://www.example.com:8080/path/index.html?a=1&b=2#TOP

location.protocol; // 'http'
location.host; // 'www.example.com'
location.port; // '8080'
location.pathname; // '/path/index.html'
location.search; // '?a=1&b=2'
location.hash; // 'TOP'

//载一个新页面，可以调用location.assign() 和 location.href= "http://www.baidu.com" 类似
location.assign('http://www.baidu.com'); // 设置一个新的URL地址
```

- **document**
document对象表示当前页面。由于HTML在浏览器中以DOM形式表示为树形结构，document对象就是整个DOM树的根节点。

document的title属性是从HTML文档中的<title>xxx</title>读取的，但是可以动态改变

```javascript
document.title = '努力学习JavaScript!'
//读取浏览器cookie
document.cookie;
//服务器在设置Cookie时可以使用httpOnly，设定了httpOnly的Cookie将不能被JavaScript读取;
``` 

- **history**
  
history对象保存了浏览器的历史记录
`history.back()` 返回

`history.forward()`前进

`history.go(数字)` 前进或倒退 数字 步骤

### 操作DOM

因为目前很多封装好的js框架技术能更好实现对dom结构的增,删,改,查操作;而且更方便使用和代码查看维护,所以开发过程中可以不用全部使用原生dom对象来操作编写;

常用:
```javascript
// 返回ID为'test'的节点：
var test = document.getElementById('test');

// 先定位ID为'test-table'的节点，再返回其内部所有tr节点：
var trs = document.getElementById('test-table').getElementsByTagName('tr');

// 先定位ID为'test-div'的节点，再返回其内部所有class包含red的节点：
var reds = document.getElementById('test-div').getElementsByClassName('red');

// 获取节点test下的所有直属子节点:
var cs = test.children;

// 获取节点test下第一个、最后一个子节点：
var first = test.firstElementChild;
var last = test.lastElementChild;
//更新
var p = document.getElementById('p-id');
// 设置CSS:
p.style.color = '#ff0000';
p.style.fontSize = '20px';
p.style.paddingTop = '2em';
//插入
var
js = document.getElementById('js'),
list = document.getElementById('list');
list.appendChild(js);
//删除
var parent = document.getElementById('parent');
parent.removeChild(parent.children[0]);
parent.removeChild(parent.children[1]); // <-- 浏览器报错
```
### 操作表单
```javascript
//onsubmit 事件处理表单提交时候控制
//return true 继续提交 false 阻止提交
<form id="test-form" onsubmit="return checkForm()">
    <input type="text" name="test">
    <button type="submit">Submit</button>
</form>

<script>
function checkForm() {
    var form = document.getElementById('test-form');
    // 可以在此修改form的input...
    // 继续下一步:
    return true;
}
</script>
```
很多登录表单希望用户输入用户名和口令，但是，安全考虑，提交表单时不传输明文口令，而是口令的MD5。普通JavaScript开发人员会直接修改

// 把用户输入的明文变为MD5:

`pwd.value = toMD5(pwd.value);`

但用户输入了口令提交时，口令框的显示会突然从几个*变成32个*（因为MD5有32个字符）

要想不改变用户的输入，可以利用<input type="hidden">实现,提交的时候提交隐藏域的值而不是真实的密码框值,这样密码框的书写就不会变化


### 操作文件
在HTML表单中，可以上传文件的唯一控件就是<input type="file">。

注意：当一个表单包含<input type="file">时，表单的enctype必须指定为multipart/form-data，method必须指定为post，浏览器才能正确编码并以multipart/form-data格式发送表单的数据。

**FileAPI**

html5增加对文件的读取操作,File和FileReader两个主要对象，可以获得文件信息并读取文件
```javascript
var
    fileInput = document.getElementById('test-image-file'),
    info = document.getElementById('test-file-info'),
    preview = document.getElementById('test-image-preview');
// 监听change事件:
fileInput.addEventListener('change', function () {
    // 清除背景图片:
    preview.style.backgroundImage = '';
    // 检查文件是否选择:
    if (!fileInput.value) {
        info.innerHTML = '没有选择文件';
        return;
    }
    // 获取File引用:
    var file = fileInput.files[0];
    // 获取File信息:
    info.innerHTML = '文件: ' + file.name + '<br>' +
                     '大小: ' + file.size + '<br>' +
                     '修改: ' + file.lastModifiedDate;
    if (file.type !== 'image/jpeg' && file.type !== 'image/png' && file.type !== 'image/gif') {
        alert('不是有效的图片文件!');
        return;
    }
    // 读取文件:
    var reader = new FileReader();
    //
    reader.onload = function(e) {
        var
            data = e.target.result; // 'data:image/jpeg;base64,/9j/4AAQSk...(base64编码)...'            
        preview.style.backgroundImage = 'url(' + data + ')';
    };
    // 以DataURL的形式读取文件: 因为这里是一个异步操作,所以不知道什么时候读取完成, 因此 reader.onload 作为一个异步处理的回调标示处理完成后执行的操作
    reader.readAsDataURL(file);
});
```
### AJAX

原生ajax的js用法代码量相对jquery , vue.axios等等已经封装过的插件多且不便维护;因此通常都是借助第三方组件来使用; 

```javascript
$.get(url,paraData,function(){});
$.post(url,paraData,function(){});
```

**ajax跨域的处理**

因为浏览器的同源策略导致的。默认情况下，JavaScript在发送AJAX请求时，URL的域名必须和当前页面完全一致; 

完全一致的意思是，域名要相同（www.example.com和example.com不同），协议要相同（http和https不同），端口号要相同（默认是:80端口，它和:8080就不同）。有的浏览器口子松一点，允许端口不同，大多数浏览器都会严格遵守这个限制。

处理跨域主要有以下方法:

1. 一是通过Flash插件发送HTTP请求，这种方式可以绕过浏览器的安全限制，但必须安装Flash，并且跟Flash交互。不过Flash用起来麻烦，而且现在用得也越来越少了
2. 是通过在同源域名下架设一个代理服务器来转发，JavaScript负责把请求发送到代理服务器
   
`'/proxy?url=http://www.sina.com.cn'`

代理服务器再把结果返回，这样就遵守了浏览器的同源策略。这种方式麻烦之处在于需要服务器端额外做开发。

3. JSONP，它有个限制，只能用GET请求，并且要求返回JavaScript。这种方式跨域实际上是利用了浏览器允许跨域引用JavaScript资源; `<script src="http://example.com/abc.js"></script>`;SONP通常以函数调用的形式返回，例如，返回JavaScript内容如:`foo('data');`
```javascript
//http://api.money.126.net/data/feed/0000001,1399001?callback=refreshPrice
//如上接口地址返回结果为:
refreshPrice({"0000001":{"code": "0000001", "percent": -0.008849, "askvol1": 0, "askvol3": 0, "askvol2": 0, "askvol5": 0, "askvol4": 0, "price": 2864.12, "open": 2885.97, "bid5": 0, "bid4": 0, "bid3": 0, "bid2": 0, "bid1": 0, "high": 2892.39, "low": 2858.58, "updown": -25.57, "type": "SH", "bidvol1": 0, "status": 0, "bidvol3": 0, "bidvol2": 0, "symbol": "000001", "update": "2019/11/29 14:17:13", "bidvol5": 0, "bidvol4": 0, "volume": 9874745000, "ask5": 0, "ask4": 0, "ask1": 0, "name": "\u4e0a\u8bc1\u6307\u6570", "ask3": 0, "ask2": 0, "arrow": "\u2193", "time": "2019/11/29 14:17:12", "yestclose": 2889.69, "turnover": 114502711454},"1399001":{"code": "1399001", "percent": -0.008717, "high": 9633.138, "askvol3": 0, "askvol2": 0, "askvol5": 0, "askvol4": 0, "price": 9538.271, "open": 9614.712, "bid5": 0.0, "bid4": 0.0, "bid3": 0.0, "bid2": 0.0, "bid1": 0.0, "low": 9490.847, "updown": -83.873, "type": "SZ", "bidvol1": 0, "status": 0, "bidvol3": 0, "bidvol2": 0, "symbol": "399001", "update": "2019/11/29 14:17:08", "bidvol5": 0, "bidvol4": 0, "volume": 16833685089, "askvol1": 0, "ask5": 0.0, "ask4": 0.0, "ask1": 0.0, "name": "\u6df1\u8bc1\u6210\u6307", "ask3": 0.0, "ask2": 0.0, "arrow": "\u2193", "time": "2019/11/29 14:17:09", "yestclose": 9622.144, "turnover": 165185025425.249} });

//refreshPrice 为要求事先准备好的处理函数
function refreshPrice(data) {
    var p = document.getElementById('test-jsonp');
    p.innerHTML = '当前价格：' +
        data['0000001'].name +': ' + 
        data['0000001'].price + '；' +
        data['1399001'].name + ': ' +
        data['1399001'].price;
}

//改函数实现一个动态JS插入的功能,动态载入*.js后调用refreshPrice(data)方法,载入的过程执行了一次URL请求
//执行流程调用 getPrice()->refreshPrice(data)->跨域请求效果
function getPrice() {
    var
        js = document.createElement('script'),
        head = document.getElementsByTagName('head')[0];
    js.src = 'http://api.money.126.net/data/feed/0000001,1399001?callback=refreshPrice';
    head.appendChild(js);
}
```
**jquery跨域写法**

这个博客解释的比较明了

<https://www.cnblogs.com/chiangchou/p/jsonp.html>

示例代码如下:

前台
```javascript
$.ajax({
    url:url,
    type:'GET', //必须是GET
    dataType: "jsonp"， //指定服务器返回的数据类型
	jsonp: "functionName", //指定参数名称
    //jsonp 用于后台获取回调函数的名称
    //后台 request.getParameter("functionName") == "refreshPrice"
	jsonpCallback: "refreshPrice" //指定回调函数,如果没有这个属性jquery会在请求地址后自动添加回调函数如:
    //url?callback=jQuery183847402939(自动生成)
    //设置后
    //url?callback=refreshPrice
    data:{},
    success:function(),
    error:function(),
    finally:function()
})
```
后台
```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    response.setCharacterEncoding("UTF-8");
    response.setContentType("text/html;charset=UTF-8");
    //数据
    List<Student> studentList = getStudentList(); 
    JSONArray jsonArray = JSONArray.fromObject(studentList);
    String result = jsonArray.toString();

    //前端传过来的回调函数名称
    String callback = request.getParameter("theFunction");
    //用回调函数名称包裹返回数据，这样，返回数据就作为回调函数的参数传回去了
    result = callback + "(" + result + ")";
    //结果如: "refreshPrice([{key:data},{key:data}])" 字符串
    response.getWriter().write(result);
}
```
**CORS**

CORS全称Cross-Origin Resource Sharing，是HTML5规范定义的如何跨域访问资源; 简单理解必须在支持h5的浏览器中才能使用;其次请求服务端必须设置: Access-Control-Allow-Origin 为 * 或者当前请求源所在域
```java
// * 表示允许任何域名跨域访问
response.setHeader("Access-Control-Allow-Origin", "*");
// 指定特定域名可以访问
response.setHeader("Access-Control-Allow-Origin", "http:localhost:8080/");
//指定可以响应的请求类型
response.setHeader("Access-Control-Allow-Methods":"POST, GET, PUT, OPTIONS");
//指定响应最多时长
response.setHeader("Access-Control-Max-Age", 86400)
```
### Promise

在JavaScript的世界中，所有代码都是单线程执行的。

由于这个“缺陷”，导致JavaScript的所有网络操作，浏览器事件，都必须是异步执行,因此本节记录的promise全部是针对异步请求来优化处理的,同步操作

前面的ajax异步执行的回调函数都必须在 success , error , failly ,done, fail 等里面处理代码不好看，而且不利于代码复用; 
```javascript
$.post(url,data,
    function(){
        //成功处理
    },function(){
        //失败处理
    })
```
Promise是在es6新增到浏览器支持中好处在于:***先统一执行AJAX逻辑，不关心如何处理结果，然后，根据结果是成功还是失败，在将来的某个时候调用success函数或fail函数***

**串行执行**

```javascript
var p = new Promise(function(resolve,reject){
    $.get(url,{},function(re){
        resolve(re);
    },function(err){
        reject(err);
    }
})
p.then(function(re){
    console.info(re);
}).catch(function(err){
    console.error(err);
});
```

**步骤执行**

promise按步骤执行,有若干个异步任务，需要先做任务1，如果成功后再做任务2，任何任务失败则不再继续并执行错误处理函数
```javascript
function multiply(input) {
    return new Promise(function (resolve, reject) {
        log('calculating ' + input + ' x ' + input + '...');
        setTimeout(resolve, 500, input * input);
    });
}

// 0.5秒后返回input+input的计算结果:
function add(input) {
    return new Promise(function (resolve, reject) {
        log('calculating ' + input + ' + ' + input + '...');
        setTimeout(resolve, 500, input + input);
    });
}

var p = new Promise(function (resolve, reject) {
    log('start new Promise...');
    resolve(1);
    //reject(500); 直接执行 p.catch() 错误获取
});

p.then(multiply) //先*
 .then(add)      //再+
 .then(multiply) //再*
 .then(add)      //再+
 .then(function (result) {
    console.log('Got value: ' + result);
})
.catch(function(err){
    console.error(err);
});
```
**理解提示**

 resovle(xx) 会将xx值作为参数传递给下一个函数

function(xx){
    return new Promise(resovle,reject){}
}

**并行执行**
```javascript
var p1 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 500, 'P1');
});
var p2 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 600, 'P2');
});
// 同时执行p1和p2，并在它们都完成后执行then:
Promise.all([p1, p2]).then(function (results) {
    console.log(results); // 获得一个Array: ['P1', 'P2']
})
```
有些时候，多个异步任务是为了容错。比如，同时向两个URL读取用户的个人信息，只需要获得先返回的结果即可。这种情况下，用Promise.race()实现：
```javascript
var p1 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 500, 'P1');
});
var p2 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 600, 'P2');
});
Promise.race([p1, p2]).then(function (result) {
    console.log(result); // 'P1'
});
```
由于p1执行较快，Promise的then()将获得结果'P1'。p2仍在继续执行，但执行结果将被丢弃

### Canvas

<https://www.liaoxuefeng.com/wiki/1022910821149312/1023022423592576>

Canvas是HTML5新增的组件，它就像一块幕布，可以用JavaScript在上面绘制各种图表、动画;