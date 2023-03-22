# AJAX（Asynchronous Javascript And Xml）



## 传统请求及缺点

- 传统的请求都有哪些？
  - 直接在浏览器地址栏上输入URL。
  - 点击超链接
  - 提交form表单
  - 使用JS代码发送请求
    - window.open(url)
    - document.location.href = url
    - window.location.href = url
    - ....
- 传统请求存在的问题
  - 页面全部刷新导致了用户的体验较差。
  - 传统的请求导致用户的体验有空白期。（用户的体验是不连贯的）



## AJAX概述

- AJAX不能称为一种技术，它是多种技术的综合产物。
- AJAX可以让浏览器发送一种特殊的请求，这种请求可以是：异步的。
- 什么是异步，什么是同步？
  - 假设有t1和t2线程，t1和t2线程并发，就是异步。
  - 假设有t1和t2线程，t2在执行的时候，必须等待t1线程执行到某个位置之后t2才能执行，那么t2在等t1，显然他们是排队的，排队的就是同步。
  - AJAX是可以发送异步请求的。也就是说，在同一个浏览器页面当中，可以发送多个ajax请求，这些ajax请求之间不需要等待，是并发的。
- AJAX代码属于WEB前端的JS代码。和后端的java没有关系，后端也可以是php语言，也可以是C语言。
- AJAX 应用程序可能使用 XML 来传输数据，但将数据作为纯文本或 JSON 文本传输也同样常见。
- AJAX可以更新网页的部分，而不需要重新加载整个页面。（页面局部刷新）
- AJAX可以做到在同一个网页中同时启动多个请求，类似于在同一个网页中启动“多线程”，一个“线程”一个“请求”。



## XMLHttpRequest对象

- XMLHttpRequest对象是AJAX的核心对象，发送请求以及接收服务器数据的返回，全靠它了。

- XMLHttpRequest对象，现代浏览器都是支持的，都内置了该对象。直接用即可。

- 创建XMLHttpRequest对象

  ```javascript
  var xhr = new XMLHttpRequest();
  ```

- XMLHttpRequest对象常用方法

| 方法                                          | 描述                                                         |
| :-------------------------------------------- | :----------------------------------------------------------- |
| abort()                                       | 取消当前请求                                                 |
| getAllResponseHeaders()                       | 返回响应头信息                                               |
| getResponseHeader()                           | 返回特定的响应头信息                                         |
| open(*method*, *url*, *async*, *user*, *psw*) | 规定请求method：请求类型 GET 或 POSTurl：文件位置async：true（异步）或 false（同步）user：可选的用户名称psw：可选的密码 |
| send()                                        | 将请求发送到服务器，用于 GET 请求                            |
| send(*string*)                                | 将请求发送到服务器，用于 POST 请求                           |
| setRequestHeader()                            | 向要发送的报头添加标签/值对                                  |

- XMLHttpRequest对象常用属性

| 属性               | 描述                                                         |
| :----------------- | :----------------------------------------------------------- |
| onreadystatechange | 定义当 readyState 属性发生变化时被调用的函数                 |
| readyState         | 保存 XMLHttpRequest 的状态。0：请求未初始化     1：服务器连接已建立（open方法调用完毕）     2：请求已收到（send方法调用完毕）    3：正在处理请求（服务端返回了部分结果）    4：请求已完成且响应已就绪（服务端返回所有结果） |
| responseText       | 以字符串返回响应数据                                         |
| responseXML        | 以 XML 数据返回响应数据                                      |
| status             | 返回请求的状态码    200: "OK"     403: "Forbidden"    404: "Not Found" |
| statusText         | 返回状态文本（比如 "OK" 或 "Not Found"）                     |



## AJAX GET请求步骤及基本语法

### GET请求详解

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>发送ajax get请求</title>
</head>
<body>
<script type="text/javascript">
	window.onload = function () {
        document.getElementById("btn").onclick = function () {
        //1. 创建AJAX核心对象
        	var xhr = new XMLHttpRequest();
        //2. 注册回调函数
        // 这是一个回调函数，这个函数在XMLHttpRequest对象的readystate状态值发生改变的时候调用该函数，且该函数会被调用多次,如 0 --> 1 被调用一次， 0 --> 2 被调用一次
            xhr.onreadystatechange = function(){
				//console.log(this.readyState);
                //当响应结束时，一般会有一个HTTP的状态码
                if(this.readyState == 4){
                    // 获取HTTP状态码
                    // console.log("HTTP状态码是：", this.status);
                    if (this.status == 404){
                        console.log('您访问的资源不存在，请检查请求路径');
                    }else if (this.status == 500){
                        console.log('服务器发生了严重的内部错误，请联系管理员');
                    }else if (this.status == 200){
                    	alert('响应成功');
                        // 返回后端响应的字符串
                        console.log(this.responseText);
                    }
                }
            }
        // 3. 开启通道（open只是浏览器和服务器建立连接，并不会发送请求）
        // XMLHttpRequest对象的open方法
        // open(method, url, async, user, psw)
        // method：请求的方式，可以是GET、POST等
        // url：请求的路径
        // async：只能是true或者false，true表示此ajax是一个异步请求，false表示是同                   步请求，在一般情况下都是true，极少数情况会是false
        // user、psw：用户名和密码。该用户名和密码用于进行身份认证，想要访问服务器上的                   资源可能需要某些口令，是否需要用户名和密码要看服务器态度
            xhr.open("GET", "/ajax/ajaxrequest1", true)
        // 4. 发送请求
            xhr.send()
        }
    }
</script>
	<button id="btn">发送ajax get请求</button>
	<span id="myspan"></span>
	<div id="mydiv"></div>
</body>
</html>
```

### GET请求与express结合案例

用express创建服务器

```js
const express = require('express');
const app = express();

app.get('/server', (req, res) => {
    res.setHeader('Access-Control-Allow-Origin', '*');
    res.send('服务端已收到请求');

})
app.listen(80, () =>{
    console.log('80端口正在服务');
})
```

发送ajaxGET请求

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style lang="">
        #res{
            width: 100px;
            height: 100px;
            border: 1px solid orange;
        }
    </style>
</head>
<body>
    <button>点击发送请求</button>
    <div id="res"></div>
    <script>
        var btn = document.querySelector('button');
        var res = document.querySelector('#res');
        btn.addEventListener('click', function(){
            
            // 1、创建XMLHttpRequest实例对象
            const xhr = new XMLHttpRequest();
            // 2、初始化
            xhr.open('GET', 'http://127.0.0.1:80/server');
            // 3、发送
            xhr.send();
            // 4、事件绑定
            xhr.onreadystatechange = function(){
                if (xhr.readyState == 4){
                    if (xhr.status >= 200 && xhr.status < 300){
                        // 行、头、空行、体
                        // 1)处理响应行
                        // 状态码  200
                        console.log(xhr.status);
                        // 状态字符串  ok
                        console.log(xhr.statusText);
                        // 2)处理响应头
                        //content-length: 24content-type: text/html; charset=utf-8
                        console.log(xhr.getAllResponseHeaders());  
                        
                        // 3)处理响应体
                        //服务端已收到请求
                        console.log(xhr.response);  
                        res.innerHTML = xhr.response;
                    }
                }
            }
        })
    </script>
</body>
</html>
```



### GET请求如何提交数据

- get请求提交数据是在“请求行”上提交，格式是：

  url?name=value&name=value&name=value....

- 其实这个get请求提交数据的格式是HTTP协议中规定的，遵循协议即可。



### GET请求的缓存问题

- 对于低版本的IE浏览器来说，AJAX的get请求可能会走缓存。存在缓存问题。对于现代的浏览器来说，大部分浏览器都已经不存在AJAX get缓存问题了。

- 什么是AJAX GET请求缓存问题呢？
  - 在HTTP协议中是这样规定get请求的：get请求会被缓存起来。
  - 发送AJAX GET请求时，在同一个浏览器上，前后发送的AJAX请求路径一样的话，对于低版本的IE来说，第二次的AJAX GET请求会走缓存，不走服务器。

- POST请求在HTTP协议中规定的是：GET请求会被浏览器缓存而POST请求不会被浏览器缓存。

- GET请求缓存的优缺点：
  - 优点：直接从浏览器缓存中获取资源，不需要从服务器上重新加载资源，速度较快，用户体验好。
  - 缺点：无法实时获取最新的服务器资源。因为路径没变会直接走缓存

- 浏览器什么时候会走缓存？
  - 第一：是一个GET请求
  - 第二：请求路径已经被浏览器缓存过了。第二次发送请求的时候，这个路径没有变化，会走浏览器缓存。

- 如果是低版本的IE浏览器，怎么解决AJAX GET请求的缓存问题呢？
  - 可以在请求路径url后面添加一个时间戳，这个时间戳是随时变化的。所以每一次发送的请求路径都是不一样的，这样就不会走浏览器的缓存问题了。

    ​	```"url?t=" + new Date().getTime() + "&name=value&name=value"```

  - 或者可以通过随机数

    ​	```"url?t=" + Math.random()```

  - 也可以随机数 + 时间戳



## AJAX POST请求步骤及基本语法

### POST请求详解

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>发送ajax POST请求</title>
</head>
<body>
<script type="text/javascript">
    window.onload = function () {
        document.getElementById("btn").onclick = function () {
            var xhr = new XMLHttpRequest();
            xhr.onreadystatechange = function(){
                if (this.readyState == 4) {
                    if (this.status == 200) {
                        document.getElementById("mydiv").innerHTML = this.responseText
                    }else{
                        alert(this.status)
                    }
                }
            }
            //3. 开启通道
            xhr.open("POST", "/ajax/ajaxrequest2", true)
            //4. 发送请求
            // 设置请求头内容类型
            // 这段代码非常关键，必须加上这段话才能模拟AJAX提交form表单，且不能放到open前，因为执行该语句必须在open后
            xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
            // GET请求参数是通过URL进行传递的,POST请求的参数包含在请求体当中
            // 因此POST请求参数需要在send中以键值字符串的形式传递
            var username = document.getElementById('username').value;
            var password = document.getElementById('password').value;
            xhr.send("username=" + username + "&password=" + password);
        }
    }
</script>
    用户名<input type="text" id="username"><br>
    密码<input type="password" id="pasword"><br>
    <button id="btn">发送ajax POST请求</button>
    
    <div id="mydiv"></div>
</body>
</html>
```

### POST请求与express结合案例

express创建服务器

```js
const express = require('express');
const app = express();

app.post('/server', (req, res) => {
    res.setHeader('Access-Control-Allow-Origin', '*');
    res.send('服务端已收到请求');
})
app.listen(80, () =>{
    console.log('80端口正在服务');
})
```

发送ajaxPOST请求

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style lang="">
        #res{
            width: 100px;
            height: 100px;
            border: 1px solid orange;
        }
    </style>
</head>
<body>
    <button>点击发送请求</button>
    <div id="res"></div>
    <script>
        var btn = document.querySelector('button');
        var res = document.querySelector('#res');
        btn.addEventListener('click', function(){
            const xhr = new XMLHttpRequest();

            xhr.open('POST', 'http://127.0.0.1:80/server');

            xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
            xhr.send('a=100&b=101&c=102');

            xhr.onreadystatechange = function(){
                if (xhr.readyState == 4){
                    if (xhr.status >= 200 && xhr.status < 300){
                        res.innerHTML = xhr.response;
                    }
                }
            }
        })
    </script>
</body>
</html>
```



## AJAX POST请求与GET请求的区别

- POST请求数据在响应体中，GET请求数据在响应头中

- 处理POST请求需要设置请求头内容类型

```javascript
// 设置请求头的内容类型
xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded") 
// 获取表单中的数据
var username = document.getElementById("username").value;
var password = document.getElementById("password").value;
// send函数中的参数就是发送的数据，这个数据在“请求体”当中发送。
xhr.send("username="+username+"&password="+password)
```

  

## 基于JSON的数据交换

### json

- JSON是一种基于JavaScript语法子集的开放标准数据交换格式
- JSON常用转换方法

| 方法                | 作用                         |
| ------------------- | ---------------------------- |
| JSON.parse(str)     | 将JSON类型字符串转化为JS对象 |
| JSON.stringify(obj) | 将JS对象转化为JSON类型字符串 |

### 数据转换

- 方式一：用 ```JSON.parse() ```和``` JSON.stringify() ```进行转换
- 方式二：用```xhr.responseType = 'json'```设置响应体数据类型

### 案例

express服务器

```js
const express = require('express');
const app = express();

app.post('/server', (req, res) => {
    res.setHeader('Access-Control-Allow-Origin', '*');
    // 想要将对象传递给前端页面
    const obj = {
        name: '张三',
        sex: '男'
    }
    // 这里直接写res.send(obj)是错误的，因为send只能传字符串
    res.send(JSON.stringify(obj));
})

app.listen(80, () =>{
    console.log('80端口正在服务');
})
```

前端接收

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style lang="">
        #res{
            width: 100px;
            height: 100px;
            border: 1px solid orange;
        }
    </style>
</head>
<body>
    <button>点击发送请求</button>
    <div id="res"></div>
    <script>
        var btn = document.querySelector('button');
        var res = document.querySelector('#res');
        btn.addEventListener('click', function(){
            const xhr = new XMLHttpRequest();
            
            // 方式二
            // xhr.responseType = 'json';

            xhr.open('POST', 'http://127.0.0.1:80/server');

            xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
            xhr.send('a=100&b=101&c=102');

            xhr.onreadystatechange = function(){
                if (xhr.readyState == 4){
                    if (xhr.status >= 200 && xhr.status < 300){
                        // 方式一
                        // 这里不能直接写res.innerHTML = xhr.response，因为得到的是JSON格式字符串
                        var data = JSON.parse(xhr.response);
                        
                        // 方式二
                        // res.innerHTML = xhr.response;
                        
                        res.innerHTML = data.name;
                    }
                }
            }
        })
    </script>
</body>
</html>
```



## 发送AJAX请求可能会遇到的问题

### 网络异常和连接超时问题

| 方法            | 作用                                     |
| --------------- | ---------------------------------------- |
| xhr.timeout     | 设置超时时长，到达该时长执行超时回调函数 |
| xhr.ontimeout() | 超时回调函数                             |
| xhr.onerror()   | 处理网络错误回调函数                     |

服务端代码

```js
const express = require('express');
const app = express();

app.get('/server', (req, res) => {
    res.setHeader('Access-Control-Allow-Origin', '*');

    // 服务端过三秒之后再发送已收到请求的响应
    setTimeout(() => {
        res.send('服务端已收到请求');
    }, 3000);
    
})
app.listen(80, () => {
    console.log('正在服务');
})
```

前端代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <button>点我发送请求</button>
    <script>
        let btn = document.querySelector('button');

        btn.addEventListener('click', function(){
            const xhr = new XMLHttpRequest();
            xhr.open('GET', 'http://127.0.0.1:80/server');

            // 设置超时判定时间
            // 客户端向服务端发送请求之后，一直等待服务端的响应，
            // 如果服务端回应超出这里的响应时间，就会执行下面的超时回调函数
            xhr.timeout = 2000;
            // 超时回调函数
            xhr.ontimeout = function(){
                alert('连接超时，请稍后再试');
            }
            
            // 设置错误回调函数
            // 当客户端因为网络等一系列问题连接不上服务器时就会执行该回调函数
            xhr.onerror = function(){
                alert('你的网络似乎出了一点问题');
            }

            
            xhr.send();
            xhr.onreadystatechange = function(){
                if (xhr.readyState == 4){
                    if (xhr.status >= 200 && xhr.status < 300){
                        console.log('成功!');
                    }
                }
            }
        })
    </script>
</body>
</html>
```

### 取消请求

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <button id = 'btn1'>点我发送请求</button>
    <button id = 'btn2'>点我取消请求</button>
    <script>
        let btn1 = document.querySelector('.btn1');
        let btn2 = document.querySelector('.btn2');
		const xhr = null;
        btn1.addEventListener('click', function(){
            xhr = new XMLHttpRequest();
            xhr.open('GET', 'http://127.0.0.1:80/server');
            
            xhr.send();
            xhr.onreadystatechange = function(){
                if (xhr.readyState == 4){
                    if (xhr.status >= 200 && xhr.status < 300){
                        console.log('成功!');
                    }
                }
            }
        })

        btn2.onclick = function(){
			// 调用xhr.abort()取消发送请求
            xhr.abort();
        }
        
    </script>
</body>
</html>
```

### 防止重复发送相同请求

前端代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <button>点我发送请求</button>
    <script>
        let btn = document.querySelector('button');
        var xhr = null;

        // 设置是否发送了请求
        let flag = false;

        btn.addEventListener('click', function(){
            // 如果前面有相同的请求了，把前面的请求取消，重新发送
            if (flag){
                xhr.abort();
                console.log(flag);
            }

            // 开始发送请求，将标志量设置为true
            flag = true;

            xhr = new XMLHttpRequest();
            xhr.open('GET', 'http://127.0.0.1:80/server');
            xhr.send();
            
            xhr.onreadystatechange = function(){
                if (xhr.readyState == 4){
                    // 请求发送完毕，将flag置为false
                    flag = false;

                    if (xhr.status >= 200 && xhr.status < 300){
                    
                        console.log('成功!');
                    }
                }
            }
        })
    </script>
</body>
</html>
```

服务器端代码

```js
const express = require('express');
const app = express();

app.get('/server', (req, res) => {
    res.setHeader('Access-Control-Allow-Origin', '*');
    
    setTimeout(() => {
        res.send('服务端已收到请求');
    }, 3000);
    
})
app.listen(80, () => {
    console.log('正在服务');
})
```

## Axios

基于`promise`用于浏览器和`node.js`的http客户端，因此可以使用Promise API

[Axios中文文档](http://www.axios-js.com/zh-cn/docs/#axios-patch-url-data-config-1)

### Axios安装

- 用`npm i `安装

```api
npm i axios
```

- 用标签内引入

```html
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```

### API请求配置

以下是创建请求时可以用的配置选项。只有 `url` 是必需的。如果没有指定 `method`，请求将默认使用 `get` 方法。

```js
{
   	// `url` 是用于请求的服务器 URL
  	url: '/user',

 	// `method` 是创建请求时使用的方法
  	method: 'get', // default

  	// `baseURL` 将自动加在 `url` 前面，除非 `url` 是一个绝对 URL。
  	// 它可以通过设置一个 `baseURL` 便于为 axios 实例的方法传递相对 URL
 	baseURL: 'https://some-domain.com/api/',

    // `headers` 是即将被发送的自定义请求头
    headers: {'X-Requested-With': 'XMLHttpRequest'},

    // `params` 是即将与请求一起发送的 URL 参数
    // 必须是一个无格式对象(plain object)或 URLSearchParams 对象
    params: {
        ID: 12345
    },

    // `data` 是作为请求体被发送的数据
    // 只适用于这些请求方法 'PUT', 'POST', 和 'PATCH'
    // 在没有设置 `transformRequest` 时，必须是以下类型之一：
    // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
    // - 浏览器专属：FormData, File, Blob
    // - Node 专属： Stream
    data: {
        firstName: 'Fred'
    },

    // `timeout` 指定请求超时的毫秒数(0 表示无超时时间)
    // 如果请求花费超过 `timeout` 的时间，请求将被中断
    timeout: 1000,

    // `withCredentials` 表示跨域请求时是否需要使用凭证
    withCredentials: false, // default

    // `adapter` 允许自定义处理请求，以使测试更轻松
    // 返回一个 promise 并应用一个有效的响应
    adapter: function (config) {
        /* ... */
    },

    // `auth` 表示应该使用 HTTP 基础验证，并提供凭据
    // 这将设置一个 `Authorization` 头，覆写掉现有的任意使用 `headers` 设置的自定义 `Authorization`头
    auth: {
        username: 'janedoe',
        password: 's00pers3cret'
    },

    // `responseType` 表示服务器响应的数据类型，可以是 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'
    responseType: 'json', // default

    // `responseEncoding` 用于解码响应的编码
    // Note: Ignored for `responseType` of 'stream' or client-side requests
    responseEncoding: 'utf8', // default
}
```

### 配置默认值

```js
// 以后再写路径就不用写前面域名，只用写路径即可
axios.defaults.baseURL = 'https://api.example.com';
```

### Axios 发送请求

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
</head>

<body>
    <button>GET</button>
    <button>POST</button>
    <button>AJAX</button>

    <script>
        var btn = document.querySelectorAll('button');
        axios.defaults.baseURL = 'http://127.0.0.1:80';

        // 发生get请求
        btn[0].onclick = function () {
            axios.get('/server', {
                // 配置url参数
                params: {
                    id: '987',
                    vip: 7
                },
                // 请求头信息
                headers: {
                    name: 'zhangsan',
                }
            }).then(value => {
                console.log(value);
            })
        }

        // 发送post请求
        btn[1].onclick = function () {
            axios.post('/server', {
                username: 'admin',
                password: 'admin'
            }, {
                params: {
                    id: '111',
                    vip: 5
                },
                headers: {
                    name: 'lisi',
                }
            }).then(value => {
                console.log(value);
            })
        }

        // 用axios封装好的函数
        btn[2].addEventListener('click', function(){
            axios({
                // 请求方法
                method: 'get',
                // url路径
                url: '/server',
                // url参数
                params: {
                    id: '333',
                    vip: 2
                },
                // 请求头
                headers:{
                    name: 'lisi',
                },
                // 请求体
                data: {
                    username:'admin',
                    password: 'admin'
                },
            }).then(response =>{
                console.log(response);
                // 获取状态码
                console.log(response.status);
                // 获取状态信息
                console.log(response.statusText);
                // 获取响应头
                console.log(response.headers);
                // 获取响应体
                console.log(response.data);
            })
        })
    </script>
</body>

</html>
```



## AJAX跨域问题

### 同源策略

- 同源策略是指一段脚本只能读取来自同一来源的窗口和文档的属性，同源就是协议、域名和端口都相同，是浏览器的一种保护机制。

- 跨域是指从一个域名的网页去请求另一个域名的资源。比如从百度(https://baidu.com)页面去请求京东(https://www.jd.com)的资源。
- 通过超链接或者form表单提交或者window.location.href的方式进行跨域是不存在问题的。但在一个域名的网页中的一段js代码发送ajax请求去访问另一个域名中的资源，由于同源策略的存在导致无法跨域访问，那么ajax就存在这种跨域问题。
- 同源策略有什么用？如果你刚刚在网银输入账号密码，查看了自己还有1万块钱，紧接着访问一些不规矩的网站，这个网站可以访问刚刚的网银站点，并且获取账号密码，那后果可想而知。所以，从安全的角度来讲，同源策略是有利于保护网站信息的。
- 有一些情况下，我们是需要使用ajax进行跨域访问的。比如某公司的A页面(a.bjpowernode.com)有可能需要获取B页面(b.bjpowernode.com)。

### 判断是否同源

- 区分同源和不同源的三要素
  - 协议
  - 域名
  - 端口
- 协议一致，域名一致，端口号一致，三个要素都一致，才是同源

| **URL1**                           | **URL2**                        | **是否同源** | 描述               |
| ---------------------------------- | ------------------------------- | ------------ | ------------------ |
| http://localhost:8080/a/index.html | http://localhost:8080/a/first   | 同源         | 协议 域名 端口一致 |
| http://localhost:8080/a/index.html | http://localhost:8080/b/first   | 同源         | 协议 域名 端口一致 |
| http://www.myweb.com:8080/a.js     | https://www.myweb.com:8080/b.js | 不同源       | 协议不同           |
| http://www.myweb.com:8080/a.js     | http://www.myweb.com:8081/b.js  | 不同源       | 端口不同           |
| http://www.myweb.com/a.js          | http://www.myweb2.com/b.js      | 不同源       | 域名不同           |
| http://www.myweb.com/a.js          | http://crm.myweb.com/b.js       | 不同源       | 子域名不同         |

### 复现AJAX跨域问题

### AJAX跨域解决方案

#### 方案1：设置cors响应头

在服务端设置

```java
// 允许某个
response.setHeader("Access-Control-Allow-Origin", "http://localhost:8080"); 
// 允许所有
response.setHeader("Access-Control-Allow-Origin", "*"); 

// 一般情况也会加上如下响应头
response.setHeader("Access-Control-Allow-Headers", "*"); 
response.setHeader("Access-Control-Allow-Method", "*"); 
```

#### 方案2：jsonp

- jsonp是利用script标签的跨域能力来发送请求的
- jsonp不是一个真正的ajax请求。只不过可以完成ajax的局部刷新效果。可以说jsonp是一种类ajax请求的机制。
- jsonp不是ajax请求，但是可以完成局部刷新的效果，并且可以解决跨域问题。
- jsonp解决跨域的时候，只支持get请求。不支持post请求。

服务端代码

```js
const express = require('express');
const app = express();

app.get('/server/jsonp', (req,res) => {
    let data = {
        msg: '用户名已存在'
    }
    const result = JSON.stringify(data);
    res.end(`check(${result})`);
})

app.listen(80, () => {
    console.log('正在服务');
})
```

前端代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .box{
            width: 100px;
            height: 100px;
            border: 1px solid skyblue;
        }
    </style>
</head>
<body>
    <input type="text">
    <div class="box">

    </div>
    <script>

        let input = document.querySelector('input');
        let div = document.querySelector('.box');

        // 这里编写函数让服务端调用
        function check(data){
            div.innerHTML = data.msg;
        }

        input.addEventListener('blur', () => {
            let username = this.value;
            // 1、创建script标签
            const script = document.createElement('script');
            // 2、将script标签的src属性改为url地址
            script.src = 'http://127.0.0.1:80/server/jsonp';
            // 3、将script标签插入到页面中
            document.body.appendChild(script);
        })
    </script>
</body>
</html>
```

#### 方案3：jQuery封装的jsonp

- 牛人们写的jQuery库，已经对jsonp进行了封装。大家可以直接拿来用。

- 用之前需要引入jQuery库的js文件。（这里的jQuery库咱们就不再封装了，咱们直接用jQuery写好的jsonp方式。）

- jQuery中的jsonp其实就是我们方案2的高度封装，底层原理完全相同。

- 核心代码


```javascript
$.ajax({
    type : "GET",
    url : "跨域的url",
    dataType : "jsonp", // 指定数据类型
    jsonp : "fun", // 指定参数名（不设置的时候，默认是："callback"）
    jsonpCallback : "sayHello" // 指定回调函数的名字
							   // （不设置的时候，jQuery会自动生成一个随机的回调函数，
    						   //并且这个回调函数还会自动调用success的回调函数。）
})
```

#### 方案4：代理机制（httpclient）

![image-20221114173334742](C:\Users\澈\AppData\Roaming\Typora\typora-user-images\image-20221114173334742.png)

- 使用Java程序怎么去发送get/post请求呢？【GET和POST请求就是HTTP请求。】
  - 第一种方案：使用JDK内置的API（java.net.URL.....），这些API是可以发送HTTP请求的。
  - 第二种方案：使用第三方的开源组件，比如：apache的httpclient组件。（httpclient组件是开源免费的，可以直接用）
- 在java程序中，使用httpclient组件可以发送http请求。
  - 对于httpclient组件的代码，大家目前可以不进行深入的研究，可以从网上直接搜。然后粘贴过来，改一改，看看能不能完成发送get和post请求。
  - 使用httpclient组件，需要先将这个组件相关的jar包引入到项目当中。

#### 方案5：nginx反向代理

- nginx代理属于代理服务器的一种
- nginx反向代理实现起来非常简单，只要修改一个nginx的配置即可。（后端）



## 基于XML的数据交换

- 注意：如果服务器端响应XML的话，响应的内容类型需要写成：

  ```java
  response.setContentType("text/xml;charset=UTF-8");
  ```

- xml和JSON都是常用的数据交换格式

  - XML体积大，解析麻烦。较少用。
  - JSON体积小，解析简单，较常用。

- 基于XML的数据交换，前端代码

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>使用XML完成数据交换</title>
  </head>
  <body>
  <script type="text/javascript">
      window.onload = function(){
          document.getElementById("btn").onclick = function(){
              // 1.创建XMLHTTPRequest对象
              var xhr = new XMLHttpRequest();
              // 2.注册回调函数
              xhr.onreadystatechange = function () {
                  if (this.readyState == 4) {
                      if (this.status == 200) {
                          // 服务器端响应了一个XML字符串，这里怎么接收呢？
                          // 使用XMLHTTPRequest对象的responseXML属性，接收返回之后，可以自动封装成document对象（文档对象）
                          var xmlDoc = this.responseXML
                          //console.log(xmlDoc)
                          // 获取所有的<student>元素，返回了多个对象，应该是数组。
                          var students = xmlDoc.getElementsByTagName("student")
                          //console.log(students[0].nodeName)
                          var html = "";
                          for (var i = 0; i < students.length; i++) {
                              var student = students[i]
                              // 获取<student>元素下的所有子元素
                              html += "<tr>"
                              html += "<td>"+(i+1)+"</td>"
                              var nameOrAge = student.childNodes
                              for (var j = 0; j < nameOrAge.length; j++) {
                                  var node = nameOrAge[j]
                                  if (node.nodeName == "name") {
                                      //console.log("name = " + node.textContent)
                                      html += "<td>"+node.textContent+"</td>"
                                  }
                                  if (node.nodeName == "age") {
                                      //console.log("age = " + node.textContent)
                                      html += "<td>"+node.textContent+"</td>"
                                  }
                              }
                              html += "</tr>"
                          }
                          document.getElementById("stutbody").innerHTML = html
                      }else{
                          alert(this.status)
                      }
                  }
              }
              // 3.开启通道
              xhr.open("GET", "/ajax/ajaxrequest6?t=" + new Date().getTime(), true)
              // 4.发送请求
              xhr.send()
          }
      }
  </script>
  <button id="btn">显示学生列表</button>
  <table width="500px" border="1px">
      <thead>
      <tr>
          <th>序号</th>
          <th>姓名</th>
          <th>年龄</th>
      </tr>
      </thead>
      <tbody id="stutbody">
      <!--<tr>
          <td>1</td>
          <td>zhangsan</td>
          <td>20</td>
      </tr>
      <tr>
          <td>2</td>
          <td>lisi</td>
          <td>22</td>
      </tr>-->
      </tbody>
  </table>
  </body>
  </html>
  ```




## AJAX乱码问题

- 测试内容：

  - 发送ajax get请求
    - 发送数据到服务器，服务器获取的数据是否乱码？
    - 服务器响应给前端的中文，会不会乱码？
  - 发送ajax post请求
    - 发送数据到服务器，服务器获取的数据是否乱码？
    - 服务器响应给前端的中文，会不会乱码？

- 包括还要测试tomcat服务器的版本：

  - tomcat10和tomcat9都要进行测试。

- 测试结果：

  - 对于tomcat10来说，关于字符集，我们程序员不需要干涉，不会出现乱码。

  - 对于tomcat9来说呢？

    - 响应中文的时候，会出现乱码，怎么解决？

      ```java
      response.setContentType("text/html;charset=UTF-8");
      ```

    - 发送ajax post请求的时候，发送给服务器的数据，服务器接收之后乱码，怎么解决？

      ```java
      request.setCharacterEncoding("UTF-8");
      ```

      

## AJAX的异步与同步

- 什么是异步？什么是同步？

  - ajax请求1和ajax请求2，同时并发，谁也不用等谁，这就是异步。（a不等b，b也不等a）
  - 如果ajax请求1在发送的时候需要等待ajax请求2结束之后才能发送，那么这就是同步。（a等待b，或者b等待a，只要发生等待，就是同步。）

- 异步和同步在代码上如何实现？

  ```javascript
  // 假设这个是ajax请求1
  // 如果第三个参数是false：这个就表示“ajax请求1”不支持异步，也就是说ajax请求1发送之后，会影响其他ajax请求的发送，只有当我这个请求结束之后，你们其他的ajax请求才能发送。
  // false表示，不支持异步。我这个请求发了之后，你们其他的请求都要靠边站。都等着。你们别动呢，等我结束了你们再说。
  xhr1.open("请求方式", "URL", false)
  xhr1.send()
  
  // 假设这个是ajax请求2
  // 如果第三个参数是true：这个就表示“ajax请求2”支持异步请求，也就是说ajax请求2发送之后，不影响其他ajax请求的发送。
  xhr2.open("请求方式", "URL", true) 
  xhr2.send()
  ```

- 什么情况下用同步？（大部分情况下我们都是使用ajax异步方式，同步很少用。）

  - 举一个例子
    - 用户注册
      - 用户名需要发送ajax请求进行校验
      - 邮箱地址也需要发送ajax请求校验
      - 其他的也可能需要发送ajax请求。。。
      - 并且最终注册按钮的时候，也是发送ajax请求进行注册。
      - 那么显然，注册的Ajax请求和校验的ajax请求不能异步，必须等待所有的校验ajax请求结束之后，注册的ajax请求才能发。

## 案例：使用AJAX POST请求验证用户名是否可以注册

实现步骤如下：

- 在前端，用户输入用户名之后，失去焦点事件blur发生，然后发送AJAX POST请求，提交用户名
- 在后端，接收到用户名，连接数据库，根据用户名去表中搜索
- 如果用户名已存在
  - 后端响应消息：对不起，用户名已存在（在前端页面以红色字体展示）
- 如果用户名不存在
  - 后端响应消息：用户名可以使用（在前端页面以绿色字体展示）
- 实现一个案例：用户点击按钮之后，发送AJAX请求，显示学生列表。

  - 在后端java程序中拼接HTML代码，然后将HTML代码直接响应到浏览器客户端。这种方式不好，不应该在java代码中编写HTML代码，能否在java程序中直接向前端响应数据？可以，可以在后端拼接JSON格式的字符串，或者XML格式的字符串，将这个字符串发送给前端，前端解析即可。

## 案例：AJAX实现省市联动

- 什么是省市联动？

  - 在网页上，选择对应的省份之后，动态的关联出该省份对应的市。选择对应的市之后，动态的关联出该市对应的区。（首先要清楚需求）

- 进行数据库表的设计

```
    t_area （区域表）
    id(PK-自增)	  code		name		pcode
    ---------------------------------------------
    1				001		 河北省		null
    2				002		 河南省		null
    3				003		 石家庄	    001
    4				004		 邯郸			 001
    5				005		 郑州			 002
    6				006		 洛阳			 002
    7				007		 丛台区	    004  
    
    将全国所有的省、市、区、县等信息都存储到一张表当中。
    采用的存储方式实际上是code pcode形势。
```

- 建表t_area，模拟好数据。

- 首先实现第一个功能：

  - 页面加载完毕之后，先把省份全部展现出来。



## 案例：AJAX实现搜索联想 自动补全

- 什么是搜索联想？自动补全？
  - 百度是一个很典型的代表。在百度的搜索框中输入相关信息的时候，会有搜索联想以及自动补全。
  - 搜索联想和自动补全：实际上是为了方便用户的使用。让用户的体验更好。
  - 搜索联想：当用户输入一些单词之后，自动联想出用户要搜索的信息，给一个提示。
  - 自动补全：当联想出一些内容之后，用户点击某个联想的单词，然后将这个单词自动补全到搜索框当中。
  - 搜索联想和自动补全功能，因为是页面局部刷新效果，所以需要使用ajax请求来完成。
- 搜索联想，自动补全功能的核心实现原理？
  - 当键盘事件发生之后，比如：keyup：键弹起事件。
  - 发送ajax请求，请求中提交用户输入的搜索内容，例如：北京（发送ajax请求，携带“北京”两个字）
  - 后端接收到ajax请求，接收到“北京”两个字，执行select语句进行模糊查询。返回查询结果。
  - 将查询结果封装成json格式的字符串，将json格式的字符串响应到前端。
  - 前端接收到json格式的字符串之后，解析这个json字符串，动态展示页面。

## 附录1：计算机网络基础

### 请求结构

<img src="D:\Typora\photos\image-20221112160649410.png" alt="image-20221112160649410" style="zoom:50%;" />

#### 请求行结构

<img src="D:\Typora\photos\image-20221112160718248.png" alt="image-20221112160718248" style="zoom:50%;" />

##### 请求方法

<img src="D:\Typora\photos\image-20221112160950702.png" alt="image-20221112160950702" style="zoom:50%;" />

##### URL

- 协议

<img src="D:\Typora\photos\image-20221112161130808.png" alt="image-20221112161130808" style="zoom:50%;" />

​	`http://`告诉浏览器使用何种协议。对于大部分 Web 资源，通常使用 HTTP 协议或其安全版本，HTTPS 协议。另外，浏览器也知道如何处理其他协议。

​	例如， `mailto:` 协议指示浏览器打开邮件客户端；`ftp:`协议指示浏览器处理文件传输。

- 主机

<img src="D:\Typora\photos\image-20221112161326598.png" alt="image-20221112161326598" style="zoom:50%;" />

​	`www.example.com` 既是一个域名，也代表管理该域名的机构。它指示了需要向网络上的哪一台主机发起请求

- 端口

<img src="D:\Typora\photos\image-20221112161614298.png" alt="image-20221112161614298" style="zoom:50%;" />

​	两个主机之间要发起 TCP 连接需要两个条件，主机 + 端口。它表示用于访问 Web 服务器上资源的入口。如果访问的该 Web 服务器使用HTTP协议的标准端口（HTTP为80，HTTPS为443）授予对其资源的访问权限，则通常省略此部分。

- 路径

<img src="D:\Typora\photos\image-20221112161718785.png" alt="image-20221112161718785" style="zoom:50%;" />

​	`/path/to/myfile.html` 是 Web 服务器上资源的路径。以端口后面的第一个 `/` 开始，到 `?` 号之前结束，中间的 每一个`/` 都代表了层级（上下级）关系。

- 查询参数

<img src="D:\Typora\photos\image-20221112162148923.png" alt="image-20221112162148923" style="zoom:50%;" />

​	`?key1=value1&key2=value2` 是提供给 Web 服务器的额外参数。如果是 GET 请求，一般带有请求 URL 参数，如果是 POST 请求，则不会在路径后面直接加参数。这些参数是用 & 符号分隔的`键/值对`列表。

- 片段

<img src="D:\Typora\photos\image-20221112163059238.png" alt="image-20221112163059238" style="zoom:50%;" />

​	#SomewhereInTheDocument 是资源本身的某一部分的一个锚点。锚点代表资源内的一种“书签”，它给予浏览器显示位于该“加书签”点的内容的指示。 例如，在HTML文档上，浏览器将滚动到定义锚点的那个点上；在视频或音频文档上，浏览器将转到锚点代表的那个时间。值得注意的是 # 号后面的部分，也称为片段标识符，永远不会与请求一起发送到服务器。

#### 请求头

包含服务器相关数据，如主机名、编码格式等信息

#### 请求体

实际传输的数据，它不一定是纯文本，可以是图片、视频等二进制数据。

### GET和POST的区别

- GET方法的数据参数是暴露在起始行的URL中的，而POST方法的数据参数是在报文主体中的。
- GET方法相对来说没有POST安全，因为它的数据参数可以直接从URL中获取，但是GET的效率更高。
- GET方法的数据参数大小有一定的限制（1024）（原因也是因为它的数据参数是放在URL中的），而POST对数据大小是没有限制的。
- 本质区别是GET是从服务器上请求数据，而POST是向服务器发送数据

### 相关基础概念

#### 客户端与服务器

- 客户端

  负责消费资源的电脑叫做客户端

- 服务器

  负责对外提供网络资源的电脑叫做服务器

- 服务器和普通电脑的区别在于，服务器上安装了一系列web服务器软件

- 自己的电脑也可以成为一台服务器，其服务器地址为127.0.0.1

#### IP地址

- IP地址是互联网每台计算机的唯一地址，IP地址具有唯一性

- IP地址的格式：点分十进制（a.b.c.d）的格式，a, b, c, d通常都是0-255的数字

  如192.168.1.1

#### 域名

- IP地址能唯一的标识计算机，但IP地址不直观也不便于记忆，发明了另一套字符型地址方案，即域名。单纯用IP地址是可以使用的，但有域名之后会更方便
- IP地址和域名是一一对应的关系，这份关系存放于域名服务器（DNS）中，因此DNS就是提供IP地址和域名地址转换的服务器
- 127.0.0.1的域名为localhost

#### 端口号

- 在一台电脑中，可以运行成百上千个web服务，每个服务都对应一个唯一的端口号。
- 客户端发来的网络请求通过端口号可以被准确的交给对应的web服务进行处理
- 每个端口号不能被多个web服务占用
- 在实际应用中，url为80的端口可以被省略

## 附录2：HTTP状态信息

### 1xx: 信息

| 消息:                   | 描述:                                                        |
| :---------------------- | :----------------------------------------------------------- |
| 100 Continue            | 服务器仅接收到部分请求，但是一旦服务器并没有拒绝该请求，客户端应该继续发送其余的请求。 |
| 101 Switching Protocols | 服务器转换协议：服务器将遵从客户的请求转换到另外一种协议。   |

### 2xx: 成功

| 消息:                             | 描述:                                                        |
| :-------------------------------- | :----------------------------------------------------------- |
| 200 OK                            | 请求成功（其后是对GET和POST请求的应答文档。）                |
| 201 Created                       | 请求被创建完成，同时新的资源被创建。                         |
| 202 Accepted                      | 供处理的请求已被接受，但是处理未完成。                       |
| 203 Non-authoritative Information | 文档已经正常地返回，但一些应答头可能不正确，因为使用的是文档的拷贝。 |
| 204 No Content                    | 没有新文档。浏览器应该继续显示原来的文档。如果用户定期地刷新页面，而Servlet可以确定用户文档足够新，这个状态代码是很有用的。 |
| 205 Reset Content                 | 没有新文档。但浏览器应该重置它所显示的内容。用来强制浏览器清除表单输入内容。 |
| 206 Partial Content               | 客户发送了一个带有Range头的GET请求，服务器完成了它。         |

### 3xx: 重定向

| 消息:                  | 描述:                                                        |
| :--------------------- | :----------------------------------------------------------- |
| 300 Multiple Choices   | 多重选择。链接列表。用户可以选择某链接到达目的地。最多允许五个地址。 |
| 301 Moved Permanently  | 所请求的页面已经转移至新的url。                              |
| 302 Found              | 所请求的页面已经临时转移至新的url。                          |
| 303 See Other          | 所请求的页面可在别的url下被找到。                            |
| 304 Not Modified       | 未按预期修改文档。客户端有缓冲的文档并发出了一个条件性的请求（一般是提供If-Modified-Since头表示客户只想比指定日期更新的文档）。服务器告诉客户，原来缓冲的文档还可以继续使用。 |
| 305 Use Proxy          | 客户请求的文档应该通过Location头所指明的代理服务器提取。     |
| 306 *Unused*           | 此代码被用于前一版本。目前已不再使用，但是代码依然被保留。   |
| 307 Temporary Redirect | 被请求的页面已经临时移至新的url。                            |

### 4xx: 客户端错误

| 消息:                             | 描述:                                                        |
| :-------------------------------- | :----------------------------------------------------------- |
| 400 Bad Request                   | 服务器未能理解请求。                                         |
| 401 Unauthorized                  | 被请求的页面需要用户名和密码。                               |
| 402 Payment Required              | 此代码尚无法使用。                                           |
| 403 Forbidden                     | 对被请求页面的访问被禁止。                                   |
| 404 Not Found                     | 服务器无法找到被请求的页面。                                 |
| 405 Method Not Allowed            | 请求中指定的方法不被允许。                                   |
| 406 Not Acceptable                | 服务器生成的响应无法被客户端所接受。                         |
| 407 Proxy Authentication Required | 用户必须首先使用代理服务器进行验证，这样请求才会被处理。     |
| 408 Request Timeout               | 请求超出了服务器的等待时间。                                 |
| 409 Conflict                      | 由于冲突，请求无法被完成。                                   |
| 410 Gone                          | 被请求的页面不可用。                                         |
| 411 Length Required               | "Content-Length" 未被定义。如果无此内容，服务器不会接受请求。 |
| 412 Precondition Failed           | 请求中的前提条件被服务器评估为失败。                         |
| 413 Request Entity Too Large      | 由于所请求的实体的太大，服务器不会接受请求。                 |
| 414 Request-url Too Long          | 由于url太长，服务器不会接受请求。当post请求被转换为带有很长的查询信息的get请求时，就会发生这种情况。 |
| 415 Unsupported Media Type        | 由于媒介类型不被支持，服务器不会接受请求。                   |
| 416                               | 服务器不能满足客户在请求中指定的Range头。                    |
| 417 Expectation Failed            |                                                              |

### 5xx: 服务器错误

| 消息:                          | 描述:                                              |
| :----------------------------- | :------------------------------------------------- |
| 500 Internal Server Error      | 请求未完成。服务器遇到不可预知的情况。             |
| 501 Not Implemented            | 请求未完成。服务器不支持所请求的功能。             |
| 502 Bad Gateway                | 请求未完成。服务器从上游服务器收到一个无效的响应。 |
| 503 Service Unavailable        | 请求未完成。服务器临时过载或当机。                 |
| 504 Gateway Timeout            | 网关超时。                                         |
| 505 HTTP Version Not Supported | 服务器不支持请求中指明的HTTP协议版本。             |
