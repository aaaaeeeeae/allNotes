# Node.js 基础



[官网传送门](https://nodejs.org/en/)



## 1、初识 Nodejs

> Node.js® is a JavaScript runtime built on Chrome's V8 JavaScript engine
>
> Node.js® 是一个基于 Chrome V8 引擎 的 JavaScript 运行时环境

- 基于 [Express 框架](http://www.expressjs.com.cn/)，可以快速构建 Web 应用
- 基于 [Electron 框架](https://electronjs.org/)，可以构建跨平台的桌面应用
- 基于 [restify 框架](http://restify.com/)，可以快速构建 API 接口项目
- 读写和操作数据库、创建实用的命令行工具辅助前端开发、etc…



## 2、相对路径与绝对路径(补充)

### 相对路径

- 相对路径就是相对于本文件找所需文件的路径，

- 具体操作
  - 用  `../` 表示上层目录
  - 用  `./`  表示本层目录，一般而言 ./ 可以不写
  - 用  `/`  回到根目录（盘目录）

### 绝对路径

- 绝对路径就是指从盘目录开始，到目标文件目录为止的路径

### 案例

读取data中的temp文件

<img src="D:\Typora\photos\image-20221026170215523.png" alt="image-20221026170215523"  />

在temp.js用相对路径读取  ```../data/temp```

用绝对路径读取  ``` D:\web\NodeLearning\test\data\temp```



## 3、Buffer 缓冲区

> [Buffer 缓冲区文档](http://nodejs.cn/api/buffer.html)

- Buffer 的结构与数组类似，操作方法也与数组类似
- 数组不能存储二进制文件，Buffer 是专门存储二进制数据的
- Buffer 存储的是二进制数据，显示时以 16 进制的形式显示
- Buffer 每一个元素范围是 00~ff，即 0~255、00000000~11111111
- 每一个元素占用一个字节内存
- Buffer 是对底层内存的直接操作，因此大小一旦确定就不能修改

Buffer 常用方法：

- `Buffer.from(str[, encoding])`：将一个字符串转换为 Buffer
- `Buffer.alloc(size)`：创建指定大小的 Buffer
- `Buffer.alloUnsafe(size)`：创建指定大小的 Buffer，可能包含敏感数据（分配内存时不会清除内存残留的数据）
- `buf.toString()`：将 Buffer 数据转为字符串

```js
var str = 'Hello前端'

var buf = Buffer.from(str)

// 占用内存的大小，一个汉字3字节 13
console.log(buf.length)
// 字符串的长度 7
console.log(str.length)
// 8进制输出第一个元素 145
console.log(buf[1].toString(8))

//创建一个10个字节的buffer
var buf2 = Buffer.alloc(10)
//通过索引，来操作buf中的元素
buf2[0] = 88
buf2[1] = 255
buf2[2] = 0xaa
buf2[3] = 255

var buf3 = Buffer.allocUnsafe(10)
console.log(buf3)
```



## 4、fs 文件系统模块

### 什么是fs文件系统模块

- fs模块是Node.js官方提供用来操作文件额度模块，他提供了一系列的方法和属性1，用来满足用户对文件的操作需求

### 关于fs文件系统模块（了解）

- fs 模块中所有的操作都有两种形式可供选择:同步和异步

- 同步文件系统会阻塞程序的执行，也就是除非操作完毕，否则不会向下执行代码
- 异步文件系统不会阻塞程序的执行，而是在操作完成时，通过回调函数将结果返回
- 实际开发很少用同步方式，因此只介绍异步方式

打开模式：
|模式|说明|
|-|-|
|r|读取文件，文件不存在抛异常|
|r+|读写文件，文件不存在抛异常|
|rs|同步模式下打开文件用于读取|
|rs+|同步模式下打开文件用于读写|
|w|写文件，不存在则创建，存在则覆盖原有内容|
|wx|写文件，文件存在打开失败|
|w+|读写文件，不存在创建，存在截断|
|wx+|读写，存在打开失败|
|a|追加，不存在创建|
|ax|追加，存在失败|
|a+|追加和读取，不存在创建|
|ax+|追加和读取，存在失败|

### 如何进行文件操作

#### 导入fs模块

```js
const fs = require('fs');
```

#### 读取文件

##### 简单文件读取

语法格式：

```js
fs.readFile(path[, options], callback)
```

- `path`：文件路径（必选参数）
- `options`：配置选项，若是字符串则指定编码格式（可选参数）
  - `encoding`：编码格式，默认以utf-8模式读取
  - `flag`：打开方式
- `callback`：回调函数（必选参数）
  - `err`：错误信息
  - `data`：读取的数据，如果未指定编码格式则返回一个 Buffer

- 读取结果

  - 如果读取成功，error的值为null

  - 如果读取成功，error的结果是一个对象，data的值为undefined

```js
const fs = require('fs')

fs.readFile('./files/1.txt', 'utf-8', function(error, data) => {
    // console.log(error); null
	if(err) {
		return console.log('failed!' + err.message)
	}
  	console.log('content:' + data)
})


// 复制文件内容
fs.readFile("C:/Users/笔记.mp3", function(err, data) {
	if(!err) {
		console.log(data);
		// 将data写入到文件中
		fs.writeFile("C:/Users/hello.jpg", data, function(err){
			if(!err){
				console.log("文件写入成功");
			}
		} );
	}
});
```

##### 流式文件读取

- 简单文件读取的方式会一次性读取文件内容到内存中，若文件较大，会占用过多内存影响系统性能，且读取速度慢
- 大文件适合用流式文件读取，它会分多次将文件读取到内存中

```js
var fs = require('fs')

// 创建一个可读流
var rs = fs.createReadStream('C:/Users/笔记.mp3')
// 创建一个可写流
var ws = fs.createWriteStream('a.mp3')

// 监听流的开启和关闭
// 这几个监听不是必须的
rs.once('open', function () {
	console.log('可读流打开了~~')
})

rs.once('close', function () {
	console.log('可读流关闭了~~')
	//数据读取完毕，关闭可写流
	ws.end()
})

ws.once('open', function () {
	console.log('可写流打开了~~')
})

ws.once('close', function () {
	console.log('可写流关闭了~~')
})

//要读取一个可读流中的数据，要为可读流绑定一个data事件，data事件绑定完毕自动开始读取数据
rs.on('data', function (data) {
	console.log(data)
	//将读取到的数据写入到可写流中
	ws.write(data)
})
```

简便方式：

```js
var fs = require('fs')

var rs = fs.createReadStream('C:/Users/lilichao/Desktop/笔记.mp3')
var ws = fs.createWriteStream('b.mp3')

// pipe()可以将可读流中的内容，直接输出到可写流中
rs.pipe(ws)
```

#### 写入文件

##### 普通文件写入

- 重复调用 ``fs.writeFile() ``写入同一个文件，新写入的内容会覆盖之前的旧内容

- 语法格式：

```js
fs.writeFile(file, data[, options], callback)
```

- `file`：文件路径（）
- `data`：写入内容
- `options`：配置选项，包含 `encoding, mode, flag`；若是字符串则指定编码格式
- `callback`：回调函数

```js
const fs = require('fs')
fs.writeFile('./files/2.txt', 'Hello Nodejs', function (err) {
	if (err) {
		return console.log('failed!' + err.message)
	}
	console.log('success!')
})

fs.writeFile('C:/Users/hello.txt', '通过 writeFile 写入的内容', { flag: 'w' }, function (err) {
	if (!err) {
		console.log('写入成功！')
  	} else {
    	console.log(err)
  	}
})
```

##### 流式文件写入

```js
// 同步、异步、简单文件的写入都不适合大文件的写入，性能较差，容易导致内存溢出
var fs = require('fs')

// 创建一个可写流
var ws = fs.createWriteStream('hello3.txt')

ws.once('open', function () {
  	console.log('流打开了~~')
})

ws.once('close', function () {
  	console.log('流关闭了~~')
})

// 通过ws向文件中输出内容
ws.write('通过可写流写入文件的内容')
ws.write('1')
ws.write('2')
ws.write('3')
ws.write('4')

// 关闭流
ws.end()
```



### 文件操作案例

把文件内容

<img src="D:\Typora\photos\image-20221026110924628.png" alt="image-20221026110924628" style="zoom:50%;" />

修改为

<img src="D:\Typora\photos\image-20221026110942470.png" alt="image-20221026110942470" style="zoom:50%;" />

```js
const fs = require('fs');

fs.readFile('test.txt','utf-8',function(err, data){
    if (err){
        return console.log('Failed'+err.message);

    }
    // console.log(data);
    var strarr = data.split(' ');
    var arr = [];
    strarr.forEach(value => {
        arr.push(value.replace('=','：'));
    })
    const str = arr.join('\r\n');
    // console.log(str);
    fs.writeFile('score.txt', str, function(err){
        if (err){
            return console.log('Faild' + err.message);
        }
    })
})
```



### 路径动态拼接问题 `__dirname`

- 在使用 fs 模块操作文件时，如果提供的操作路径是以 `./` 或 `../` 开头的相对路径时，容易出现路径动态拼接错误的问题

- 原因：代码在运行的时候，会以执行 node 命令时所处的目录，与readFile中指定的目录动态拼接出被操作文件的完整路径，若此时命令窗口不在当前目录下，就会出现找不到文件的错误

- 解决方案：

  在使用 fs 模块操作文件时，直接提供完整的路径，从而防止路径动态拼接的问题

  - 直接使用绝对路径，但这种方式移植性很差，不利于维护
  - __dirname 获取当前文件所处目录


```js
fs.readFile(__dirname + '/files/1.txt', 'utf8', function(err, data) {
  ...
})
```



### 其它操作（补充）

验证路径是否存在：

- `fs.exists(path, callback)`
- `fs.existsSync(path)`

获取文件信息：

- `fs.stat(path, callback)`
- `fs.stat(path)`

删除文件：

- `fs.unlink(path, callback)`
- `fs.unlinkSync(path)`

列出文件：

- `fs.readdir(path[,options], callback)`
- `fs.readdirSync(path[, options])`

截断文件：

- `fs.truncate(path, len, callback)`
- `fs.truncateSync(path, len)`

建立目录：

- `fs.mkdir(path[, mode], callback)`
- `fs.mkdirSync(path[, mode])`

删除目录：

- `fs.rmdir(path, callback)`
- `fs.rmdirSync(path)`

重命名文件和目录：

- `fs.rename(oldPath, newPath, callback)`
- `fs.renameSync(oldPath, newPath)`

监视文件更改：

- `fs.watchFile(filename[, options], listener)`





## 5、path 路径模块

path 模块是 Node.js 官方提供的、用来处理路径的模块。它提供了一系列的方法和属性，用来满足用户对路径的处理需求。

### 导入路径模块

```js
const path = require('path')
```

### 路径拼接 `path.join()`

- 注意，在路径拼接过程中
  -  ../ 会抵消前面一个路径
  -  ./ 会被忽略

```js
const path = require('path')
const fs = require('fs')

//  由于../的存在，b和c都被抵消
//  ./不会被读取因此d存在
const pathStr = path.join('/a', '/b/c', '../../', './d', 'e')
console.log(pathStr) // \a\d\e
```

- 运用path.jion()可以获取文件路径

  以后凡是涉及到路径拼接的操作，都要使用path.join()，这样可以避免因为字符串拼接导致错误。

```js
const path = require('path')
const fs = require('fs')

// 不建议这么写
// fs.readFile(__dirname + '/file/1.txt')....

fs.readFile(path.join(__dirname, '/files/1.txt'), 'utf8', function (err, dataStr) {
  if (err) {
    return console.log(err.message)
  }
  console.log(dataStr)
})
```

### 获取路径文件名 `path.basename()`

- 使用 `path.basename()` 方法，可以获取路径中的最后一部分，常通过该方法获取路径中的文件名

  - path: 文件路径
  - ext: 文件扩展名

  ```js
  path.basename(path[, ext]);
  ```

- 若写了文件拓展名参数，那么最后得到的结果只是文件名

```js
const path = require('path')

// 定义文件的存放路径
const fpath = '/a/b/c/index.html'

const fullName = path.basename(fpath)
console.log(fullName) // index.html

const nameWithoutExt = path.basename(fpath, '.html')
console.log(nameWithoutExt) // index
```

### 获取路径文件扩展名 `path.extname()`

```js
const path = require('path')

const fpath = '/a/b/c/index.html'

const fext = path.extname(fpath)
console.log(fext) // .html
```



## 6、http 模块

http 模块是 Node.js 官方提供的、用来创建 web 服务器的模块。

### 创建基本 Web 服务器

#### 导入模块

```js
const http = require('http');
const server = http.createServer();
```

#### 创建服务器基本步骤

```js
// 1、导入模块
const http = require('http');

// 2、创建 web 服务器实例
const server = http.createServer();

// 3、为服务器实例绑定 request 事件，监听客户端的请求
server.on('request', function (req, res) {
	console.log('Someone visit our web');
})

// 4、启动服务器
server.listen(8080, function () {
	console.log('server running at http://127.0.0.1:8080');
})
```

##### req请求对象

req请求对象可以访问与客户端相关的数据或属性

| 属性       | 作用                                    |
| ---------- | --------------------------------------- |
| req.url    | 客户端请求的url地址，如端口号后面的内容 |
| req.method | 客户端method请求类型，如get、post等     |

```js
const http = require('http');
const server = http.createServer();
server.on('request', (req) => {
    console.log('你正在使用服务器');
    const url = req.url;
    const method = req.method;
    // url = /   method = GET
    const str = `Your server url is ${url}，your server method is ${method}`
    console.log(str);
})
server.listen(80, ()=>{
    console.log("你的网页运行在http://127.0.0.1");
})
```

##### res响应对象

| 属性            | 作用                               |
| --------------- | ---------------------------------- |
| res.end()       | 向客户端发送指定内容并结束此次请求 |
| res.setHeader() | 解决发送中文字符的乱码问题         |

- 需要注意的是调用```res.end()```方法，向客户端发送中文内容的时候，会出现乱码问题，因此要自己设置一下编码值

```js
const http = require('http');
const server = http.createServer();
server.on('request', (req,res) => {
    const str = `你的请求的url是${req.url}，你的请求方式是${req.method}`
    console.log(str);
    // 设置中文乱码
    res.setHeader('Content-Type', 'text/html; charset=utf-8');
    res.end(str);
})
server.listen(80, ()=>{
    console.log("你的网页运行在http://127.0.0.1");
})

```

##### 创建服务器实例

```js
const http = require('http');
const server = http.createServer();
server.on('request', (req,res) => {
    const str = `你的请求的url是${req.url}，你的请求方式是${req.method}`
    console.log(str);
    res.setHeader('Content-Type', 'text/html; charset=utf-8');
    res.end(str);
})
server.listen(80, ()=>{
    console.log("你的网页运行在 http://127.0.0.1");
})
```



### 实现简陋路由效果

```js
const http = require('http');
const server = http.createServer();

server.on('request', (req, res) => {
  const url = req.url;
  // 设置默认的响应内容为 404 Not found
  let content = '<h1>404 Not found!</h1>';
  // 判断用户请求的是否为 / 或 /index.html 首页
  // 判断用户请求的是否为 /about.html 关于页面
  if (url === '/' || url === '/index.html') {
    content = '<h1>首页</h1>';
  } else if (url === '/about.html') {
    content = '<h1>关于页面</h1>';
  }

  res.setHeader('Content-Type', 'text/html; charset=utf-8');
  res.end(content);
})

server.listen(80, () => {
  console.log('server running at http://127.0.0.1');
})
```





## 7、模块化

### 模块化概念

- 模块化是指解决一个复杂问题时，自顶向下逐层把系统划分为若干模块的过程，模块是可组合、分解和更换的单元。
- 模块化可提高代码的复用性和可维护性，实现按需加载。
- 模块化规范是对代码进行模块化拆分和组合时需要遵守的规则，如使用何种语法格式引用模块和向外暴露成员。

### Node.js 中模块的分类

- 内置模块
  - Node.js官方提供的，例如fs、path、http等

- 自定义模块
  - 用户创建的每个.js文件

- 第三方模块
  - 由第三方开发出来的模块


### Node.js 中的模块作用域

- 和函数作用域类似，在自定义模块中定义的变量、方法等成员，只能在当前模块内被访问，这种模块级别的访问限制，叫做模块作用域
- 模块作用域的好处：防止全局变量污染

### 向外共享模块作用域

#### module对象

- 自定义模块中都有一个 `module` 对象，存储了和当前模块有关的信息

```js
console.log(module)
```

<img src="D:\Typora\photos\image-20221030142436012.png" alt="image-20221030142436012" style="zoom:50%;" />

##### module.exports 对象

- 在自定义模块中，可以使用```module.exports```对象将模块中的成员共享出去，供外界使用
- 外界用 ```require() ```方法导入自定义模块时，得到的就是```module.exports```指向的对象

```js
// 文件1中
// 往里面增加一些属性和方法
module.export.name = '张三';
nodule.export.sayHello = function() {
	console.log('hello world');
}

// 文件2中
const f1 = require('./文件1');
console.log(f1) //{name: '张三', sayHello: [function]};
```

#### exports对象

- 由于module.exports单词写起来比较复杂，为了简化，Node.js提供了exports对象

- `exports` 和 `module.exports`在一般情况下是指向的同一个对象。但最终共享的结果，以 `module.exports` 指向的对象为准。

#### exports对象和module.exports对象使用误区

- 时刻谨记，使用```require() ```调用模块时，得到的永远都是```module.exports()```指向的对象
- 为了防止混乱，不建议在同一个模块中同时使用```exports()```和```module.exports()```

![image-20221030171820729](D:\Typora\photos\image-20221030171820729.png)

![image-20221030171718999](D:\Typora\photos\image-20221030171718999.png)

![image-20221030171951669](D:\Typora\photos\image-20221030171951669.png)



![image-20221030172209632](D:\Typora\photos\image-20221030172209632.png)

### CommonJS 模块化规范

- 每个模块内部，`module` 变量代表当前模块
- `module` 变量是一个对象，`module.exports` 是对外的接口
- 加载某个模块即加载该模块的 `module.exports` 属性

### 模块加载机制

模块第一次加载后会被缓存，即多次调用 `require()` 不会导致模块的代码被执行多次，提高模块加载效率。

#### 内置模块加载

由于内置模块是```Node.js```官方提供的模块，因此内置模块加载优先级最高。

#### 自定义模块加载

- 加载自定义模块时，路径要以 `./` 或 `../` 开头，否则会作为内置模块或第三方模块加载。

- 导入自定义模块时，若省略文件扩展名，则 Node.js 会按顺序尝试加载文件：

  - 按确切的文件名加载

  - 补全 `.js` 扩展名加载

  - 补全 `.json` 扩展名加载

  - 补全 `.node` 扩展名加载

  - 报错


#### 第三方模块加载

- 若导入第三方模块， Node.js 会从**当前模块的父目录**开始，尝试从 `/node_modules` 文件夹中加载第三方模块。如果没有找到对应的第三方模块，则移动到再**上一层父目录**中，进行加载，直到**文件系统的根目录**。

例如，假设在 `C:\Users\bruce\project\foo.js` 文件里调用了 `require('tools')`，则 Node.js 会按以下顺序查找：

- `C:\Users\bruce\project\node_modules\tools`
- `C:\Users\bruce\node_modules\tools`
- `C:\Users\node_modules\tools`
- `C:\node_modules\tools`

#### 目录作为模块加载

当把目录作为模块标识符进行加载的时候，有三种加载方式：

- 在被加载的目录下查找 `package.json` 的文件，并寻找 `main` 属性，作为 `require()` 加载的入口
- 如果没有 `package.json` 文件，或者 `main` 入口不存在或无法解析，则 Node.js 将会试图加载目录下的 `index.js` 文件。
- 若失败则报错



## 8、npm和包

[下载包官网](https://www.npmjs.com/)

- Node.js中的第三方模块又叫包
- 由于Node.js的内置模块仅提供了一些底层的API，导致在基于内置模块进行项目开发的时候效率不高，导入第三方模块可以提高开发效率

### 安装指定包

- 安装包使用如下命令
- 可同时安装多个包，每个包名字之间用空格分隔开

```apl
npm install 包完整名字
```

- 可以简写成

```apl
npm i 包名
```

- 安装指定版本的包

  - 默认情况下，用```npm install```命令安装包的时候，会自动安装最新版本的包
  - 想安装指定版本的包，需要通过``` @  ```符来指定具体的版本

  ```apl
  npm i dayjs@1.10.1
  ```

#### node_modules和package_lock.json

- 初次装包完成后，会在项目文件夹下多一个叫做```node_modules```的文件夹以及```package_lock.json```的文件，程序员不要手动修改这两个文件，npm包管理工具会自动维护他们

- ```node_modules```文件夹用来存放所有已安装到项目中的包，```require()```时会在这个文件夹下查找包
- ```package_lock.json```文件用来记录```node_modules```目录下每个包的下载信息

### 一次性下载项目所需包

```apl
npm install (npm i)
```

- 执行npm install 命令时，npm包管理工具会读取package.json中的 dependencies节点，读取到记录的所有依赖包名称和版本号之后，npm包管理工具会把它们下载下来

### 卸载包

```apl
npm uninstall 包名
```

- 该命令执行成功后，会把卸载的包自动从dependencies节点中删除

### 包管理配置文件（package.json）

- npm规定，在项目根目录中，必须提供一个叫做 ```package.json``` 的包管理配置文件，用来记录与项目有关的一些信息，如
  - 项目名称、版本号、描述等
  - 项目中都用到了哪些包
  - 哪些包只在开发期间用到
  - 哪些包在开发和部署时需要用到
- 由于第三方包的体积非常庞大，在做项目时一般只需要上传源代码文件，第三方包让使用者自行安装，在package.json文件说明需要用到哪些包

#### 创建包管理配置文件

- 该命令只能在英文目录下成功运行，所以项目文件夹名称一定要用英文，不能使用中文和空格
- 运行npm install 命令安装包时，npm包管理工具会自动把包的名称和版本号记录在该文件中，因此不需要程序员手动修改

```apl
npm init -y
```

#### dependencies节点

- 如果某些包在开发以及项目上线之后会用到，建议在该节点记录所需包

- 该结点在初始化 ```package.json```是不存在的，只有安装包之后才会有这个节点
- 这个结点专门记录用```npm install```命令安装了哪些包

#### DevDependencies节点

- 如果某些包只在项目开发阶段会用到，项目上线之后不会使用到，建议在该节点记录包

```apl
npm i 包名 --save-dev
```

- 简写为

```apl
npm i 包名 -D
```

### 解决国外服务器下载包缓慢问题

#### 镜像源下载

- 镜像是一种文件存储形式，一个磁盘上的数据在另一个磁盘上存在完全相同的副本即为镜像

##### 切换npm下包镜像源

- 下包镜像源指的就是下包服务器地址

```apl
// 查看当前下包镜像源
npm config get registry

// 将下包的镜像源切换为淘宝镜像源
npm config set registry=https://registry.npm.taobao.org/

```

#### nrm

- 可以通过nrm这个小工具，利用nrm提供的终端命令	快速查看和切换下包镜像源

```apl
// 通过 npm 包管理器，将nrm 安装为全局可用的工具
npm i nrm -g
// 查看所有可用的镜像源
nrm ls
// 将下包的镜像源切换为 taobao 镜像
nrm use taobao
```

### 包的基本概念

#### 包的语义化版本规范

- 包版本号是以点分十进制定义的，总共有三位数字，如 1.10.1
  - 第一位数字：大版本
  - 第二位数字：功能版本
  - 第三位数字：Bug修复版本

#### 包分类

##### 项目包

- 被安装到项目中```node_modules```目录中的包

  - 开发依赖包

    被记录到```devDependencies```节点中的包

  - 核心依赖包

    被记录到```dependencies```节点中的包

##### 全局包

- 在执行 ```npm install``` 命令时，如果提供了```-g```参数，则会把包安装为全局包
- 只有工具性质的包才有全局安装的必要，是否需要全局看参考文档即可

```apl
npm i 包名 -g
npm  uninstall 包名 -g
```

#### 规范的包结构

- 包必须以单独的目录存在
- 包的顶级目录下必须包含package.json 这个包管理配置文件
- package.json中必须包含name，version，main这三个属性，分别代表包的名字、版本号、包的入口

#### 其他常用包

##### i5ting_toc(了解，typora自带)

- 调用i5ting_toc，可实现md转html格式

```apl
// 将i5ting_toc安装为全局包
npm install -g i5ting_toc

// 将md文件转换为html
i5ting_toc -f 路径名 -o
```

#### nodemon

- 直接使用```node 路径```执行文件时，若文件修改需要重复执行该命令，```nodemon```可以监听项目文件的变动，当代码发生改变的时候会帮助我们自动重启项目

```
nodemon 路径
```



### 开发包流程

- 新建文件夹作为包的根目录
- 在文件夹中新建三个文件
  - package.json      包管理配置文件
  - index.js                包入口文件
  - README.md       包的说明文档

- 初始化 package.json 文件

<img src="D:\Typora\photos\image-20221101093400883.png" alt="image-20221101093400883" style="zoom:50%;" />

- 编写index.js脚本

```apl
....
// 对外共享
module.exports = {
	....
}
```

- 编写包的说明文档
  - 具体编写内容并没有强制性的要求，只要能把包的作用、用法表述清楚即可
  - 一般而言会包含以下几个内容
    - 安装方法
    - 导入方法
    - 功能介绍
    - 开源协议
- 在npm上发布包

#### 案例

自行编写日期格式化案例

formatfunction.js

```js
function timeFormat(dataStr){
    const dt = new Date(dataStr);

    const year = dt.getFullYear();
    const month = padZero(dt.getMonth() + 1);
    const date = padZero(dt.getDate());

    const hour = padZero(dt.getHours());
    const minutes = padZero(dt.getMinutes());
    const seconds = padZero(dt.getSeconds());
    
    return `${year}-${month}-${date} ${hour}:${minutes}:${seconds}`;
}
function padZero(n){
    return n < 9 ? "0" + n : n;
}
// 这里假设要暴露上述两个函数
module.exports{
	timeFormat,
	padZero 
}
```

index.js

```js
const timeFormat = require('./timeFormat/formatfunction');
module.exports = {
    // 用ES6中的扩展运算符，将对象转换成用逗号分隔的参数序列
    ...timeFormat
}
```

使用该包

```js
// 这里的路径可以不写到index.js文件，是因为在package.json中main节点已经记录了
const format = require('./timeFormat');
const form = format.timeFormat(new Date())
console.log(form);
```



## 9、MySQL在项目中的使用

### 安装mysql模块

```apl
npm i mysql
```

### 配置mysql模块

```js
// 1、导入mysql模块
const mysql = require('mysql')

// 2、建立与mysql数据库的连接
const db = mysql.createPool({
    // 数据库的IP地址
    host: '127.0.0.1',
    // 数据库账号
    user: 'root',
    // 数据库密码
    password: 'ache',
    //需要操作的库名
    database: 'contest'
})

// 测试模块是否能正常工作
db.query('select 1', (err, results) => {
    if (err) return console.log(err.message);
    // 打印出结果：[ RowDataPacket { '1': 1 } ] 表示连接成功
    console.log(results);
})
```

### mysql模块操作数据库

#### 查询数据

- 如果执行的是select查询语句，则执行的结果是数组

```js
db.query('select * from user', (err, results) => {
    if (err) return console.log(err.message);
    console.log(results);
})
/*
[
  RowDataPacket { name: 'lisi', id: 1234 },
  RowDataPacket { name: 'wangwu', id: 777 },
  RowDataPacket { name: 'xuliu', id: 134 }
]
*/
```

#### 插入数据

- 插入数据执行结果是一个对象，通过results.affectedRows 属性能获取到执行的sql语句影响的行数

```js
// 1、要插入到users表中的数据对象
const user = {name: 'hhh', id: 111};

// 2、待执行的SQL语句，其中英文 ? 表示占位符
const sqlStr = 'insert into user (name, id) values (?, ?)';

// 3、插入数据
db.query(sqlStr, [user.name, user.id], (err, results) => {
    if (err) return console.log(err.message);
    // results.affectedRows 执行的sql语句影响的行数
    if (results.affectedRows === 1){
        console.log('插入数据成功');
    }
})
```

- 便捷方式

  向表中新增数据时，如果数据对象的每个属性和数据表的字段一一对应，则可以通过如下简便方法插入数据

```js
// 1、要插入到user表中的数据对象
const user1 = {name:'abc', id: 222};

// 2、待执行的SQL语句，其中英文 ? 表示占位符
const sqlStr = 'insert into user set ?';

// 插入数据
db.query(sqlStr, user1, (err, results) => {
    if(err) return console.log(err.message);
    if (results.affectedRows === 1){
        console.log('插入数据成功');
    }
})
```

#### 更新数据

- 更新数据执行结果是一个对象

```js
// 1、要插入到user表中的数据对象
const user2 = {name:'hhh2', id:222};

// 2、待执行的SQL语句，其中英文 ? 表示占位符
const sqlStr = 'update user set name = ? where id = ?';

// 插入数据
db.query(sqlStr, [user2.name, user2.id], (err, results) => {
    if(err) return console.log(err.message);
    if (results.affectedRows === 1){
        console.log('插入数据成功');
    }
})
```

- 便捷方式

  向表中新增数据时，如果数据对象的每个属性和数据表的字段一一对应，则可以通过如下简便方法插入数据

```js
// 1、要插入到user表中的数据对象
const user2 = {name:'hhh3', id: 222};

// 2、待执行的SQL语句，其中英文 ? 表示占位符
const sqlStr = 'update user set ? where id = ?';

// 插入数据
db.query(sqlStr, [user2, user2.id], (err, results) => {
    if(err) return console.log(err.message);
    if (results.affectedRows === 1){
        console.log('插入数据成功');
    }
})
```



# Express

[官网传送门](https://www.expressjs.com.cn/)

> 基于 Node.js 平台，快速、开放、极简的 Web 开发框架

Express npm上的第三方包，基于内置的http模块进一步封装，用于快速创建web服务器。

## 1、Express 初体验

### 基本使用

- 安装 Express：

```bash
npm install express
```

#### 创建服务器的步骤

```js
// 1、导入express模块
const express = require('express');
// 2、创建 web 服务器实例
const app = express();

// 3、启动服务器
app.listen(80, () => {
  	console.log('express server running at http://127.0.0.1');
})
```

#### 处理GET、POST请求

##### req对象常用属性

| 属性         | 作用                            |
| ------------ | ------------------------------- |
| req.method   | 获取请求类型  GET POST          |
| req.body     | 获取请求体                      |
| req.query    | 获取查询参数  ?id=123&name=lisi |
| req.params   | 获取动态匹配的 URL 参数         |
| req.path     | 获取路径  /article/details      |
| req.hostname | 获取主机地址  baidu.com         |

##### res对象常用方法

| 方法       | 作用                       |
| ---------- | -------------------------- |
| res.send() | 发送数据                   |
| res.end()  | 发送指定内容并结束此次请求 |

```js
const express = require('express');
const app = express();

// 监听客户端的 GET 和 POST 请求，并向客户端响应具体的内容
app.get('/user', (req, res) => {
	res.send({ name: 'zs', age: 20, gender: '男' });
})
app.post('/user', (req, res) => {
    // 用req.body属性接受客户端发送过来的请求体数据
    // 默认情况下，如果不配置解析表单数据的中间件，req.body默认为undefined
    // post请求数据在请求体内，因此用req.body获取
    console.log(req.body);
	res.send('请求成功');
})

app.get('/', (req, res) => {
	// 通过 req.query 可以获取到客户端发送过来的查询参数
    // 客户端使用 ?name=za&age=20 这种查询字符串的形式发送到服务器，可用该属性获取
    // 默认req.query是一个空对象
    // get请求数据都在请求头上，因此用req.query获取
  	console.log(req.query);
    
    
  	res.send(req.query);
})

app.get('/user/:ids/:username', (req, res) => {
  	// req.params 是动态匹配到的 URL 参数，默认是一个空对象
    // : 后面的内容是参数名称，可以自己任意取
  	console.log(req.params);
  	res.send(req.params);
})

app.listen(80, () => {
  	console.log('express server running at http://127.0.0.1');
})
```

### 托管静态资源

- 通过 `express.static()` 方法可创建静态资源服务器，向外开放访问静态资源。

  ```js
  const app = require('express');
  app.use(express.static('文件路径'));
  ```

- 托管静态资源时，若需要托管多个静态资源，多次调用express.static即可

- 访问静态资源时，express会根据托管顺序查找文件

- Express 在指定的静态目录中查找文件，并对外提供资源的访问路径，存放静态文件的目录名不会出现在 URL 中

  即若在 public 文件夹下有 photos、css、js 这三个文件，在访问时，可直接通过以下方式访问，路径不需要带 public

  ```
  http://localhost:80/photos/ph1.jpg
  
  http://localhost:80/css/css1.css
  
  http://localhost:80/js/js1.js
  ```

- 可为静态资源访问路径添加前缀，这时路径就必须加所给定的前缀

```js
app.use(express.static('public'))
app.use(express.static('files'))
app.use('/bruce', express.static('bruce'))

通过带有 /bruce 前缀的地址访问 bruce 目录下的文件
http://localhost:80/bruce/photos/ph1.jpg

http://localhost:80/bruce/css/css1.css

http://localhost:80/bruce/js/js1.js

```



## 2、Express 路由

- 在express中，路由指的是客户端请求和服务器处理函数之间的映射关系
- 前文的get、post请求可以看成是路由，但express不建议用这种方式创建路由，应该写成模块化的形式
- express中的路由有3部分组成，分别是请求的类型、URL地址、处理函数

### 创建路由模块

```js
const express = require('express');
// 创建路由对象
const router = express.Router();

// 挂载具体路由
router.get('/user/list', (req, res) => {
  	res.send('Get user list.');
})
router.post('/user/add', (req, res) => {
  	res.send('Add new user.');
})

// 向外共享路由对象
module.exports = router;
```

### 注册路由模块

```js
const express = require('express');
const router = require('./router');

const app = express();

// app.use(router);
// 注册路由模块，添加访问前缀
app.use('/api', router);

app.listen(80, () => {
  	console.log('http://127.0.0.1');
})
```

## 3、Express 中间件

- 网页向服务器发送请求，服务器收到请求后，先调用中间件进行预处理，服务器再响应该请求的处理环节

<img src="D:\Typora\photos\image-20221103160819105.png" alt="image-20221103160819105" style="zoom:50%;" />

### 定义中间件函数

- 中间件是一个函数，包含 `req, res, next` 三个参数，中间件函数的形参列表必须包含next，而路由处理函数中只包括req和res
- `next()` 函数是实现多个中间件连续调用的关键，它表示把流转关系交给下一个中间件或路由
- 中间件不能写在get、post监听之后，因为代码是从上到下执行的，写在后面都开始监听也就没中间件的事了

```js
const mw = function(req, res,next){
    console.log('这是最简单的中间件函数');
    // 在当前中间件业务处理完毕之后，必须调用next()函数，表示把流转关系转交给下一个中间件或路由
    next();
}
```

#### 全局中间件

- 服务端发起的任何请求，到达服务器之后都会触发的中间件，叫做全局生效的中间件

- 通过 `app.use()` 定义的中间件为全局中间件

```js
const express = require('express')
const app = express()

// 定义第一个全局中间件
const mw = function(req, res, next) => {
  console.log('调用了第1个全局中间件')
  next();
}
app.use(mw);

// 定义第二个全局中间件（中间件的简化形式）
app.use((req, res, next) => {
  console.log('调用了第2个全局中间件')
  next()
})

app.get('/user', (req, res) => {
  res.send('User page.')
})

app.listen(80, () => {
  console.log('http://127.0.0.1')
})
```

#### 局部中间件

- 不使用app.use()定义的中间件叫做局部生效的中间件

##### 绑定一个中间件

```js
const express = require('express');
const app = express();

// 定义局部中间件
const mw = function(req,res,next){
    console.log('创建了局部中间件');
    next();
}
// 为路由绑定局部中间件
app.get('/', mw, (req, res) => {
    res.send('请求成功');
})

app.listen(80, function(){
    console.log('Express server running at http://127.0.0.1');
})
```

##### 绑定多个中间件

```js
const express = require('express');
const app = express();

// 定义中间件函数
const mw1 = (req, res, next) => {
  console.log('调用了第一个局部生效的中间件');
  next();
}

const mw2 = (req, res, next) => {
  console.log('调用了第二个局部生效的中间件');
  next();
}

// 两种定义局部中间件的方式
app.get('/hello', mw1, mw2, (req, res) => res.send('hello page.'));
app.get('/about', [mw1, mw2], (req, res) => res.send('about page.'));

app.get('/user', (req, res) => res.send('User page.'));

app.listen(80, function () {
  console.log('Express server running at http://127.0.0.1');
})
```

#### 中间件定义时的注意事项

- 必须在路由之前注册中间件，否则不会执行中间件
- 中间件可连续调用多个
- 执行完中间件之一定要调用 `next()` 函数
- 调用`next()` 函数后别在后面继续写代码
- 连续调用多个中间件时会共享 `req`、 `res`对象，因此可以在中间件为req挂载一些属性，这样后面能直接通过req.属性名得到

### 中间件分类

#### 应用级别的中间件

- 通过 `app.use()` 或 `app.get()` 或 `app.post()` ，绑定到```express```实例对象上的中间件（全局中间件绑定或者局部中间件绑定）

```js
// 全局中间件
app.use((req, res, next) => {
    next();
})

// 局部中间件
app.get('/', mw, (req, res) => {
    res.end('success');
})
```

#### 路由级别的中间件

- 绑定到 `express.Router()` 路由模块实例上的中间件，叫做路由级别的中间件。用法和应用级别中间件没有区别。

```js
const app = express()
const router = express.Router()

router.use(function (req, res, next) {
  console.log(1)
  next()
})

app.use('/', router)
```

#### 错误级别的中间件

- 用来捕获整个项目中发生的异常错误，从而防止项目异常崩溃的问题
- 错误级别中间件的处理函数中，必须有 4 个形参，形参顺序从前到后分别是 `(err, req, res, next)` 。
- 错误级别的中间件必须注册在所有路由之后

```js
const express = require('express')
const app = express()

app.get('/', (req, res) => {
    // 抛出一个自定义的错误
  	throw new Error('服务器内部发生了错误！');
    // 由于上面抛出了一个错误，这行代码不会执行，会执行下面错误级别中间件
  	res.send('Home page.')
})

// 定义错误级别的中间件，捕获整个项目的异常错误，从而防止程序的崩溃
app.use((err, req, res, next) => {
  	console.log('发生了错误！' + err.message)
  	res.send('Error：' + err.message)
})

app.listen(80, function () {
  	console.log('Express server running at http://127.0.0.1')
})
```

#### Express 内置中间件

自 Express 4.16.0 版本开始，Express 内置了 3 个常用的中间件，极大的提高了 Express 项目的开发效率和体验：

- `express.static` 快速托管静态资源的内置中间件，例如： HTML 文件、图片、CSS 样式等（无兼容性）
- `express.json` 解析 JSON 格式的请求体数据（有兼容性，仅在 4.16.0+ 版本中可用）
- `express.urlencoded` 解析 URL-encoded 格式的请求体数据（有兼容性，仅在 4.16.0+ 版本中可用）

```js
app.use(express.json())
app.use(express.urlencoded({ extended: false }))
app.post('user/', (req, res) => {
    // 这里必须用 express.json() 或 express.urlencoded() 将数据解析，否则req.body为undefined
    console.log(req.body);
    res.send('ok');
})
```

#### 第三方中间件



## 4、CORS 跨域资源共享

### CORS 中间件解决跨域

- 安装中间件：`npm install cors`
- 导入中间件：`const cors = require('cors')`
- 配置中间件：`app.use(cors())`

### CORS

- CORS（Cross-Origin Resource Sharing，跨域资源共享）解决跨域，是通过 HTTP 响应头决定浏览器是否阻止前端 JS 代码跨域获取资源
- 浏览器的同源安全策略默认会阻止网页“跨域”获取资源。但如果接口服务器配置了 CORS 相关的 HTTP 响应头，就可解除浏览器端的跨域访问限制
- CORS 主要在服务器端进行配置。客户端浏览器无须做任何额外的配置，即可请求开启了 CORS 的接口。
- CORS 在浏览器中有兼容性。只有支持 XMLHttpRequest Level2 的浏览器，才能正常访问开启了 CORS 的服务端接口（例如：IE10+、Chrome4+、FireFox3.5+）。

### CORS 常见响应头

- `Access-Control-Allow-Origin`：制定了允许访问资源的外域 URL

```js
res.setHeader('Access-Control-Allow-Origin', 'http://bruceblog.io')
res.setHeader('Access-Control-Allow-Origin', '*')
```

- `Access-Control-Allow-Headers`
- 默认情况下，CORS 仅支持客户端向服务器发送如下的 9 个请求头：`Accept、Accept-Language、Content-Language、DPR、Downlink、Save-Data、Viewport-Width、Width 、Content-Type （值仅限于 text/plain、multipart/form-data、application/x-www-form-urlencoded 三者之一）`
- 如果客户端向服务器发送了额外的请求头信息，则需要在服务器端，通过 A`ccess-Control-Allow-Headers` 对额外的请求头进行声明，否则这次请求会失败！

```js
res.setHeader('Access-Control-Allow-Headers', 'Content-Type, X-Custom-Header')
```

- `Access-Control-Allow-Methods`
- 默认情况下，CORS 仅支持客户端发起 GET、POST、HEAD 请求。如果客户端希望通过 PUT、DELETE 等方式请求服务器的资源，则需要在服务器端，通过 `Access-Control-Alow-Methods` 来指明实际请求所允许使用的 HTTP 方法

```js
res.setHeader('Access-Control-Allow-Methods', 'POST, GET, DELETE, HEAD')
res.setHEader('Access-Control-Allow-Methods', '*')
```

### CORS 请求分类

#### 简单请求

- 请求方式：GET、POST、HEAD 三者之一
- HTTP 头部信息不超过以下几种字段：无自定义头部字段、Accept、Accept-Language、Content-Language、DPR、Downlink、Save-Data、Viewport-Width、Width 、Content-Type（只有三个值 application/x-www-formurlencoded、multipart/form-data、text/plain）

#### 预检请求

- 请求方式为 GET、POST、HEAD 之外的请求 Method 类型
- 请求头中包含自定义头部字段
- 向服务器发送了 application/json 格式的数据

在浏览器与服务器正式通信之前，浏览器会先发送 OPTION 请求进行预检，以获知服务器是否允许该实际请求，所以这一次的 OPTION 请求称为“预检请求”。服务器成功响应预检请求后，才会发送真正的请求，并且携带真实数据
