# ES6



## let声明

### 基本使用

- let声明的变量只在所处的块级有效

- 块级作用域：一对 {} 之间产生的作用域

```js
if (true){
	let b = 10;
	console.log(b); //10
	if(true){
		let c = 20;
		console.log(b); // 10
	}
	console.log(c);  //报错
	}
console.log(b); //报错
```

- let的一个关键性作用是：防止变成全局变量

```js
for(var i = 0; i < 5; i++){

}
console.log(i); //5
for(let i = 0; I < 5; i++){

}
console.log(i); //报错
```

- 在之前函数进阶用闭包来实现的功能就可以用let关键字来实现且更加简便

```js
var arr = [];

for (var i = 0; i < 2; i++){
	arr[i] = function(){
		console.log(i);
	}
}
arr[0]();  //2
arr[1]();  //2

var arr = [];
for (let I = 0; I < 2; i++){
	arr[i] = function(){
		console.log(i);
	}
}
arr[0]();  //0
arr[1]();  //1

// 能产生这样情况的关键点在于每循环一次就能产生新的块级作用域，也就是说里面用let声明的i在每次循环都是虽然同名但值不同的变量。原因依然是let关键字只在所处的块级有效的特性
```

- let不存在变量提升，只能先声明再使用

  变量提升是指在执行过程中会把所有带var的语句和函数的声明提升到当前作用域的最前面（只声明不赋值）

```js
console.log(a);  //undefined
var a = 10;
console.log(b);  //报错
let b = 10;
```

- let声明的变量不会称为window的一个属性

```js
var a = 10;

window.a;  //10

let b = 20;

window.b;  //undefined
```

### 暂时性死区

当程序的控制流程在新的作用域进行实例化时，在此作用域中用let/const声明的变量会先在作用域中产生块级作用域，但因此时还未进行词法绑定，所以是不能被访问的，如果访问就会抛出错误。因此，在这运行流程进入作用域创建变量，到变量可以被访问之间的这一段时间，就称之为暂时死区。

```js
var a = 10;
if (true){
	console.log(a); //10，a此时是全局变量
}
if (true){
	a = 10;
	console.log(a); 
	//10，此时虽然不报错但是不建议这样使用(前面说的全局变量是在函数内部且没有用var声明才算)
}
if (true){
    console.log(a); //报错，未声明就使用
    a = 10;
}
var a = 10;
if (true){
	console.log(a);  //报错，暂时性死区
	let a = 10;
}

// 造成该错误的主要原因是：ES6新增的let、const关键字声明的变量会产生块级作用域，如果变量在当前作用域中被创建之前被创建出来，由于此时还未完成语法绑定，如果我们访问或使用该变量，就会产生暂时性死区
```

## const常量声明

用const关键字声明的就是常量

### 声明常量的注意事项

- 在声明的时候一定要赋初始值

```js
const a; // 报错
```

- 一般而言常量使用大写（规范）
- 常量的值不能被修改也不能重复
- 用const声明的常量也拥有块级作用域

```js
{
	const A = 19;
}
console.log(A); //报错
```

- 对于数组和对象的元素进行修改，不算做对常量的修改

```js
const TEAM = ['ache', 'tom', 'jone'];
TEAM.push('zhangsan');
```



## 对象的解构

ES6允许按照一定模式从数组、对象中提取值，对变量进行赋值，这被称为解构赋值

- 数组的解构

```js
const STAR = ['凯文', '爱莉希雅', '伊甸', '阿波尼亚', '苏'];
let [kavin, elysia, , ,su,mei] = STAR;
console.log(kavin);
console.log(elysia);
console.log(su);
console.log(mei); //undefined
```

- 对象的解构

​		属性解构很少使用

- 同名情况

```js
let person = {
	name: 'zhangsan',
	age: 18,
	sing: function(){
		console.log('song');
    }
}

let {name, age, sing, sex = true} = person;

console.log(name);
console.log(age);
console.log(sing); //函数
console.log(sex);
sing();

function connect({host, username, password}){

}

connect({
	host: 'localhost',
	username: 'root',
	password: 'root'
})
```

- 不同名情况

```js
let person = {
	name: 'zhangsan',
	age: 18
}

let {name: myname, sing: mysing , age: myage} = person;

console.log(myname);
console.log(myage);
console.log(mysong);
mysing();
```



## 箭头函数

​	箭头函数是用来简化函数定义语法的

- 箭头函数的定义

```js
() => {}

var fn = () => {}

fn()
```

### 箭头函数的语法规范

- 函数体中只有一句代码，且代码执行结果就是返回值，可以省略大括号

```js
const sum = (num1, num2) => num1 + num2;

sum(10, 20);
```

- 如果形参只有一个，可以省略小括号

```js
const fn = v => {
	console.log(v * v);
}

fn(2);
```

### 箭头函数this的问题

- 箭头函数不绑定this关键字，箭头函数中的this指向的是函数定义位置的this

- 箭头函数不能使用argument来获取参数

- 如果箭头函数的父级是对象中的方法，那么this指向的就是方法之上的对象

- 如果箭头函数的父级是对象（把方法写为箭头函数），this指向的是window，因为对象不能产生独立作用域

```js
const obj = {name: 'zhangsan'}
function fn(){
	console.log(this);
	return () => {
		console,log(this);
	}
}
const refn = fn.call(obj);
refn(); //obj


var name = 'ache';
var obj = {
	name: 'zhangsan',
	fn: ()=>{
		console.log(this.name);
	}
}
obj.fn() //ache


let num = 11;
const obj1 = {
	num: 22,
	fn1: function() {
		let num = 33;
		const obj2 = {
			num: 44,
			fn2: () => {
				console.log(this.num);
			}
		}
		obj2.fn2();
	}
}
obj1.fn1();  // 22


let num = 11;  //如果把let改为var，则this.num的值为11
const obj1 = {
	num: 22,
	fn1: () => {
		let num = 33;
		const obj2 = {
			num: 44,
			fn2: () => {
				console.log(this.num);
			}
		}
	obj2.fn2();
	}
}
obj1.fn1();  //undefined
```

### 箭头函数的一些应用

- 让div 2s后变颜色

```js
let div = document.querySelector('div');
div.addEventListener('click', function(){
	setTimeout(function(){
		this.style.background = 'red';
	}, 2000)
})
```

​	这样写是不能让其变色的，还会报错，因为setTimeout里的this指向的是window

```js
let div = document.querySelector('div');
div.addEventListener('click', function(){
    setTimeout(() =>{
        this.style.background = 'red';
    }, 2000)
})
```

- 使用箭头函数可以大大简化代码

```js
let arr = [1, 2, 3, 4, 5]

arr.map(val => var * 2);

const res = arr.filter(item => item % 2 === 0);
```

### 箭头函数适合的场合

​		与this无关的回调、定时器、方法的回调



## 函数

### 函数默认值

- ES6允许在函数中给参数赋初始值，当没传入参数时该值为初始值，传入参数后该值为传入值，且写初始值时一般写在最后

```js
function fn (a, b, c = 1){
	console.log(c);
}

fn(1, 2);
```

### 剩余参数

- 剩余参数语法允许我们将一个不定数量的参数表示为一个数组

```js
function sum(first, …args) {
	console.log(first);  // 10
	console.log(args);  // [20, 30]
}

sum(10, 20, 30);
```

- 剩余参数语法也可以与解构配合使用

```js
let people = ['zhangsan', 'lisi', 'wangwu'];
let [p1, …p2] = people;

console.log(p1);  //zhangsan
console.log(p2);  //['lisi', 'wangwu']
```

### 获取函数实参

ES5之前获取实参的方法是arguments，且通过arguments获取的是一个对象

```js
function date(arguments){
	console.log(arguments);
}

date('一月', '二月', '三月'); 
```

在ES6中除箭头函数外都可用rest参数获取，用rest参数获取的结果是一个数组

- rest参数(形式为'...变量名')，用于获取函数的多余参数

- rest参数之后不能再有其他参数，即只能是最后一个参数，若在它只后还有参数会报错

- rest参数是真正意义上的数组，可以使用数组的任何方法

```js
function fn(…args){
	console.log(args);  //['一月', '二月', '三月']
}

fn('一月', '二月', '三月');

function fn(a, …args){
	console.log(args);  //['二月', '三月']
}

fn('一月', '二月', '三月');
```

### 扩展运算符

- 扩展运算符 ``` … ``` 可以将数组或者对象转换为用逗号分隔的参数序列

```js
const hero = ['zhangsan', 'lisi', 'wangwu'];

function heros() {
	console.log(arguments);
}

heros(…hero);  //'zhangsan', 'lisi', 'wangwu'
```

#### 扩展运算符的使用

- 数组合并

```js
let people = ['zhangsan', 'lisi', 'wangwu'];

let month = ['一月'， '二月'];

const add1 = people.concat(month);

const add2 = […people, …month];
```

- 数组的克隆（浅拷贝）

```js
const people = ['zhangsan', 'lisi', 'wangwu'];

const clone = […people];
```

- 将伪数组转换为真正的数组
```js
<div></div>
<div></div>
<div></div>

const divs = document.querySelectorAll('div');
const divarr = […divs];
```
- 转换arguments

```js
function fn (arguments){
	let argument = […arguments];
}

fn(1, 2, 3);
```



## 迭代器（未懂）

迭代器是一种接口，为各种不同的数据结构提供同一访问机制

ES6创建了一种新的遍历命令for of循环，iterator接口主要供for of使用

- for of与for in的区别

for...in 循环出的是 key，for...of 循环出的是 value

const arr = ['张三', '李四', '王五'];

for(let v of arr){

console.log(v);  //张三 李四 王五

}

for(let v in arr){

console.log(v);  //0 1 2

}

因此建议在循环对象属性的时候使用 for...in，在遍历数组的时候的时候使用 for...of

 

## 生成器（以后再看）

生成器其实是一个特殊的函数，是针对异步编程的一种解决方案

- 生成器的使用方法

//yield是函数中代码的分隔符，像下面的代码把函数分成了四个部分，每个部分都由一个next迭代

function *fn(){

yield 111;

var two = yield 222;

console.log(two);  //BBB

yield 333;

}

let iterator = fn();

iterator.next(); //调用，返回迭代器对象 {value: 111, done: false}

//next中可以传入参数，当成是yield语句的返回值

iterator.next('BBB');

 

## Array扩展方法

### Array.from()

- 将类数组或可遍历的对象转换成真正的数组

​	```Array.from(object, mapFunction, thisValue)```

​	object：必需，要转换为数组的对象。

​	mapFunction：可选，数组中每个元素要调用的函数。

​	thisValue：可选，映射函数(mapFunction)中的 this 对象。

```js
var setObj = newSet(["a", "b", "c"]);
var objArr = Array.from(setObj);

objArr[1] == "b";  //true

var arr = Array.from([1, 2, 3], x => x * 2);
```

### find()

- 找出第一个符合条件的数组成员的值（不是下标），若没有找到返回undefined

​	```array.find(function(currentValue, index, arr){},thisValue)```

​	currentValue：必须。数组中正在处理的当前元素。

​	index：可选。当前元素的索引值。

​	arr：可选。当前元素所在的数组对象。

 ```js
 let arr1 = [1, 2, 3, 4, 5];
 let num = arr1.find(item=>item > 1);
 console.log(num)  // 2
 
 
 var arr = [{
     id: 1,
     name: '张一',
     age: 25,
     class: '一班'
 }, {
     id: 1,
     name: '李四',
     age: 25,
     class: '二班'
 }, {
     id: 2,
     name: '王五',
     age: 25,
     class: '三班'
 }]
 
 let obj = arr.find(item => item.id == 1)
 
 console.log(obj);
 ```

### findindex()

- 查找数组中第一个符合条件的数组成员的位置，如果没有找到返回-1

​	```array.find(function(currentValue, index, arr){},thisValue)```

​	currentValue：必须。数组中正在处理的当前元素。

​	index：可选。当前元素的索引值。

​	arr：可选。当前元素所在的数组对象。

```js
let arr = [1, 5, 10, 15];

let index = arr.findIndex(value => value > 9);

console.log(index);  //2
```

### includes()

- 表示某个数组是否包含给定的值，返回布尔值

```js
[1, 2, 3].includs(3); //true
```

### reduce()

```js
reduce((pre, curr) =>{ }, 初始值)
```

- 下一次pre的值是上一次调用的返回值

- 用其计算数组长度（一般不用这个，直接用length）

```js
arr.reduce((pre, cur) =>{
	return pre++;
}, 0)
```

- 用其求和

```js
arr.reduce((pre, cur) =>{
	return pre + cur;
}, 0)
```



## 模板字符串

- 模板字符串用 `` 表示，需要使用的时候用 ${变量名}获取使用

```js
let string = `张三`;

console.log('我的名字是${string}');
```

- 模板字符串可以换行
```js
let res = {
	name: 'zhangsan',
	age: 18
}

let html = `
<div>
	<span>${res.name}</span>
	<span>${res.age}</span>
</div>
`;
```

- 模板字符串中可以调用函数

```js
const sing = function(){
	return '执子之手与子偕老';
}

let greet = '${sing()}';

console.log(greet);
```



## String扩展方法

### startsWith()

- 表示参数字符串是否在原字符串的头部，返回布尔值

```js
let str = 'Hello world';

str startsWith('Hello');  //true
```

### endsWith()

- 表示参数字符串是否在原字符串的尾部，返回布尔值

```js
let str = 'Hello world';

str startsWith('world');  //true
```

### repeat()

- repeat()方法表示将原字符串重复n次，返回一个新字符串

```js
console.log('x'.repeat(3));
```



## Set数据结构

ES6提供了新的数据结构Set，类似于数组，但成员值都是唯一的，利用这点可以做数组去重

### Set的创建

```js
const s1 = new Set();

const s2 = new Set([1,2,3,4,4,4]);

console.log(s2);  //[1,2,3,4]
```

### Set的一些方法和属性

#### size

- 获取集合的大小，类似于数组中的length

```js
const s = new Set([1,2,3,4,4,4]);

console.log(s.size);  //4
```

#### add(value)

#### delete(value)

#### has(value) 

- 显示该值是否为Set成员，返回一个布尔值

#### clear()  清除所有成员

```js
const s = new Set();

s.add(a).add(b).add(c);

s.delete(b);

s.has(a);  //true

s.clear();
```

### Set遍历

- forEach()可对其遍历

```js
const s = new Set([1,2,3]);

s.forEach(value => {
	console.log(value);
})
```



## 模块化编程

模块功能主要由两个命令构成：export、import

- export命令用于规定模块的对外接口

- import命令用于导入其他模块

### 暴露语法

#### 分别暴露

在所有语句前都加上export即可

​	假设在文件demo1.js中有这样的代码：

```js
export let school = "清华";

export function fn(){
	console.log('111');
}
```

#### 统一暴露

在最后写export{}即可

​	假设在文件demo2.js中有这样的代码

```js
let school = "清华";

function fn(){
	console.log('111');
}

export{school, fn};
```

#### 默认暴露

```js
export default {
	以对象的形式写居多
}
```

假设在文件demo2.js中有这样的代码

```js
export default{
	school: '清华',
	fn: function(){
		console.log('111');
	}
}
```

但使用这种方法，在调用时必须要加上default

```js
demo3.default.fn();

demo3.default.school;
```

### 引入模块语法

#### 通用引入

在文件main.html下有以下代码
```html
<body>
	<script type="module">
		import * as demo1 from "路径";
	</script>
</body>
```
#### 解构赋值形式
```html
<body>
    <script type="module">
        import {school, fn} from "路径";
        //若重名可以取别名
        import {school as d1school, fn as d1fn} from "路径";
        
        //但在默认暴露下这种方式写会有些不同
        //得到的d3是一个对象
        import {default as d3} from "路径";
    </script>
</body>
```

#### 简便形式

只能针对默认暴露，其他暴露方式都不支持这样使用
```html
<body>
	<script type="module">
        //同样返回对象
        import demo3 from "路径名";
    </script>
</body>
```



以上形式可能会因为导入包过多导致代码体积庞大，还可以单独设立一个app.js文件，在这个文件中用上面说的引入模块方法引入，然后在主文件中通过script标签引入app.js文件

```html
<body>
	<script src= "路径/app.js" type="module"></script>
</body>
```



## Promise

Promise 是异步编程的一种解决方案，可以简化代码、支持链式调用，解决回调地狱

### 异步任务有哪些

- 定时器
- 数据库操作
- Ajax请求
- fs文件操作

### Promise基本使用

```js
const but=document.getElementById("but")
but.addEventListener("click",function (){
    let randomNumber=Math.ceil(Math.random()*100)
    // 创建 promise 实例
       const p = new Promise(function (resolve, reject){
           setTimeout(()=>{
               if(randomNumber<30){
               //成功的状态
               resolve(n);
           }else{
               //失败的的状态
               reject(n);
           }
           },1000)
       })
       // 这里用then函数执行成功或者失败的回调
       // 参数需要两个回调，第一个为成功的回调，第二个为失败的回调
        p.then(value => {
            alert('恭喜，您中奖了' + value);
        },reason => {
            alert('很遗憾，您没有中奖' + reason);
        })
})

```

### Promisify()

```promisify()```是```util```封装好的工具，可将传入参数变为``promise``风格

```js
// 将fs模块promise化流程

// 1、引入所需要的模块
const util = require('util');
const fs = require('fs');

// 2、将fs.readFile变成promise风格
const promiseFS = util.promisify(fs.readFile);

// 3、正常使用
promiseFS('text').then(value => {
    console.log(value);
}, () => {
	console.log('error');  
})
```

### Promise的流程

#### promiseState

- 状态值

  - pending  未决定的

  - resolve / fullfilled  成功

  - rejected  失败

- Promise 状态改变

  - pending 变为 resolved
  - pending 变为 rejected

#### promiseResult

- 保存异步任务成功 / 失败的结果
- 只能由resolve、reject来修改

#### 流程

![image-20221108164221889](D:\Typora\photos\image-20221108164221889.png)

### API

#### 构造函数

函数在Promise内部立即同步调用执行

```js
const p = new Promise((resolve, reject) => {
	console.log(111);
})
console.log(222);
// 111
// 222
```

#### then方法

```js
p.then(resolve(){
       
}, reject(){
    
})
```

#### catch方法

- 指定失败的回调

```js
p.catch(reson => {
    console.log(reason);
})
```

#### resolve方法

- 将一个元素转换成一个成功 / 失败的promise对象，成功 / 失败取决于传入参数

```js
let p1 = Promise.resolve(521);
console.log(p1) // promise对象

let p2 = Promise.resolve(
    new Promise((resolve, reject) => {
        // resolve('OK');
        reject("Error");
    })
);
// 如果不对出错进行处理（指定错误回调），控制台就会报错
p2.then(value => {
	console.log(value);
},reason => {
    // Error
	console.log(reason);
});
console.log(p2)
```

#### reject方法

- 将传入的参数转换成失败的promise对象（promiseState为rejected）

```js
let p1 = Promise.reject(521);
let p2 = Promise.reject('haha');
// 即便传入成功的，得到的promise对象状态也是失败的
let p3 = Promise.reject(
	new Promise((resolve, reject) => {
		resolve("OK");
	})
);

p3.then((value) => {
	console.log(value);
},(reason) => {
	console.log(reason);
});

console.log(p1);
console.log(p2);
console.log(p3);
```

#### all方法

- 传入一个promise的数组，返回一个新的promise
- 其promiseState为传入数组中的promise对象状态逻辑与结果（全为成功才是成功，一个失败全失败）
- 其promiseResult结果为成功，则promiseResult的结果为传入数组各promiseResult的结果组成的数组，若失败，promiseResult的结果为第一个失败的对象中promiseResult值

```js
let p1 = new Promise((resolve, reject) => {
	resolve("OK");
});
let p2 = Promise.resolve('Success');
let p3 = Promise.resolve("Oh Yeah");
var result = Promise.all([p1, p2, p3]);

console.log(result);
/*
Promise
[[Prototype]]: Promise
[[PromiseState]]: "fulfilled"
[[PromiseResult]]: Array(3)
0: "OK"
1: "Success"
2: "Oh Yeah"
length: 3
[[Prototype]]: Array(0) */


let p4 = Promise.reject("Error");
var result = Promise.all([p1, p2, p4]);

console.log(result);
/*
[[Prototype]]: Promise
[[PromiseState]]: "rejected"
[[PromiseResult]]: "Error */
```

#### race方法

- 第一个完成的promise的状态和结果值就是最终生成的promise对象的状态和结果值

```js
let p1 = new Promise((resolve, reject) => {
  	setTimeout(() => {
    	reject("OK");
  	}, 1000);
});
let p2 = Promise.resolve("Error");
let p3 = Promise.resolve("Oh Yeah");

const result = Promise.race([p1, p2, p3]);
console.log(result);  // p2
```

### 几个关键问题

#### 如何改变 Promise 的状态

```js
let p = new Promise((resolve, reject) => {
	// 1. resolve 函数
	resolve("ok"); // pending   => fulfilled (resolved)
	// 2. reject 函数
	reject("error"); // pending  =>  rejected
	// 3. 抛出错误
	throw "出问题了";
});
```

#### 指定多个回调函数，是否都会被调用

```js
let p = new Promise((resolve, reject) => {
    //如果注释掉resolve()，那么p的状态就还是pending，即状态未发生改变，不会调用then
	resolve("OK"); 
});

// 绑定的这两个回调，只要状态发生了改变就都会被同时调用
p.then((value) => {
	console.log(value);
});

p.then((value) => {
	console.log(value);
});
```

#### 改变状态和then函数执行先后

- 谁先谁后都是有可能的，在一般情况下（回调函数内是一个同步任务，直接调用resolve或者reject时）先改变状态再执行then函数，若回调函数内是一个异步任务，则会先执行then函数再改变状态
- 什么时候才能得到数据？
  - 如果先指定的回调, 那当状态发生改变时, 回调函数就会调用, 得到数据
  - 如果先改变的状态, 那当指定回调时, 回调函数就会调用, 得到数据

#### Promise .then()返回的新 Promise 的结果状态由什么决定

- then() 会返回一个新的promise

- 其状态值和结果值由then()指定的回调函数执行结果决定
  - 若抛出异常，新promise则为失败，reason为结果值
  - 若返回的是非promise的任意值，新promise值会变成成功，value为返回值
  - 若返回的是另一个新的promise，该promise的结果是新的promise的结果（是成功新的也为成功，是失败新的也会是失败）
  - 若什么回调函数都不返回，其新promise状态值为fullfilled，结果值为undefined

```js
let p = new Promise((resolve, reject) => {
	resolve("ok");
	// reject("no");
});
//执行 then 方法
let result = p.then((value) => {
	// console.log(value);
	//1. 抛出错误 rejected
	// throw "出了问题";
   	//2. 返回结果是非 Promise 类型的对象 fulfilled
   	// return 521;
   	//3. 返回结果是 Promise 对象 fulfilled/rejected
   	return new Promise((resolve, reject) => {
     	// resolve("success");
     	reject("error");
   	});
},(reason) => {
   	console.warn(reason);
})
console.log(result);
```

#### Promise 如何串连多个操作任务?

- 由于Promise的 then()返回一个新的 Promise,因此可以通过 then 的链式调用串连多个同步/异步任务

```js
let p = new Promise((resolve, reject) => {
   	setTimeout(() => {
       	resolve('OK');
   	}, 1000);
});	
p.then(value => {
   	return new Promise((resolve, reject) => {
       	resolve("success");
   	});
}).then(value => {
   	console.log(value);  // success
}).then(value => {
    // 这里是打印的是结果值，then函数还是会返回一个promise类型
   	console.log(value);  // undefined
})
```

#### Promise 异常传透

- 当使用 Promise 的 then 链式调用时，可以在最后指定失败的回调
- 中间任何操作抛出了异常，都会传到最后失败的回调中处理，且出现异常之后的回调都不会再执行

```js
let p = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve('OK');
        // reject('Err');
    }, 1000);
});
p.then(value => {
    // console.log(111);
    throw '失败啦!';
}).then(value => {
    console.log(222);
}).then(value => {
    console.log(333);
}).catch(reason => { //用then也可以捕获异常，不过then要传两个参数
    console.warn(reason);
});
console.log(p)
```

#### 如何中断 Promise 链

- 处于pending状态时，状态并未发生改变，then()的回调函数不会被执行，可以在回调函数中返回一个 pendding 状态的 Promise 对象中中断promise链

```js
let p = new Promise((resolve, reject) => {
	setTimeout(() => {
		resolve('OK');
    }, 1000);
});
let a = p.then(value => {
    console.log(111);
    // 中断promise链
    return new Promise(() => {});
}).then(value => {
    console.log(222);
}).then(value => {
    console.log(333);
}).catch(reason => {
    console.warn(reason);
});
```

### async函数

- 返回值为promise对象
- promise对象的结果由async函数执行的返回值决定（和 then() 规则一样）
  - 返回值是一个非promise对象，其状态值为fullfilled，其结果值为return的值
  - 返回值如果是一个promise对象，函数返回值则为该对象
  - 返回值抛出异常，函数返回状态值为reject，结果值为异常

```js
async function main() {
//1. 如果返回值是一个非Promise类型的数据 则状态为成功，返回一个成功的promise对象
 	return 521;
}
async function main1() {
//2. 如果返回的是一个Promise对象 则状态取决于返回结果
 	return new Promise((resolve, reject) => {
   	// resolve('OK'); //成功
   	reject("Error"); //失败
 	});
}
async function main2() {
// 3. 抛出异常 返回一个失败的promise对象
 	throw "Oh NO";
}
let result = main();
let result1 = main1();
let result3 = main2();
console.log(result);
console.log(result1);
console.log(result3);
```

### await关键字

- 获取状态为成功时的结果值
  - 如果是promise对象且状态为成功，返回其成功时的结果值，
  - 如果是promise对象状态为失败，抛出错误，且需要通过 try…catch 捕获处理
  - 如果是非promise对象，返回关键字后面的值

- await 必须写在 async 函数中, 但 async 函数中可以没有 await

```js
async function main() {
	let p = new Promise((resolve, reject) => {
		resolve("OK");
		// reject("Error");
  	});
	//1. 右侧为promise的情况
	let res = await p;
    console.log(res); // ok
	//2. 右侧为其他类型的数据
	let res2 = await 20;
    console.log(res); // 20
	//3. 如果promise是失败的状态
  	try {
		let res3 = await p;
		console.log(res3)
  	} catch (e) {
		//catch捕获失败状态
		console.log(e);  // Error
	}
}
```

### async函数和await关键字的综合应用

```js
const fs = require('fs');
const util = require('util');
const readFile = util.promisify(fs.readFile);

async function test(){
    try{
        let data1 = await readFile('./data1.txt');
        let data2 = await readFile('./data2.txt');
        let data3 = await readFile('./data3.txt');
        
        console.log(data1 + data2 + data3);
    }catch(e){
        console.log(e);
    }
}
```
