# This指向相关

## 一：普通函数的this指向

**普通函数中this是动态的，当我们调用函数的时候确定的，调用方式的不同决定了this的指向不同，一般指向我们函数的调用者。（谁调用指向谁）**

### 1、普通函数

```js
//1.普通函数
function fn() {
	console.log('普通函数的this' + this);
}
fn();// window   实际上，这里是window.fn();谁调用，this就指向谁
```

​		这里的this指向全局对象window

### 2、对象的方法

```js
//2.对象的方法
var o = {
  sayHi: function () {
      console.log('对象的方法' + this); //object  this指向的是对象 o
  }
}
o.sayHi()
```

​		对象中的方法的this指向该方法所属的对象

### 3、构造函数

```js
//3.构造函数  this指向实例对象ldh  
function Star() {
    Star.prototype.sing = function () { }//原型对象中的this也是指向实例对象
}
var ldh = newStar();
```

​		构造函数和原型对象中的方法，它的调用者是实例对象（这里是ldh），因此在构造函数和原型对象中的方法，this都指向实例对象

### 4、绑定事件函数

```js
//4.绑定事件函数
var btn = document.querySelector('button');
btn.onclick = function () {
  console.log('绑定事件的this' + this); // 指向btn 也是指向函数的调用者
}
```

​		绑定事件的函数，同样遵循谁调用，this指向谁的原则，在这里是btn绑定了点击事件，因此这里的this指向函数的调用者btn

### 5、定时器函数

```js
//5.定时器函数  this指向window 实际上为window. setTimeout(),也是指向函数的调用者
setTimeout(function () {
    console.log('定时器的this' + this);
}, 1000);
```

​	  定时器函数的调用，实际上是window. setTimeout()，因此定时器函数的this指向window 

### 6、立即执行函数

```js
//6.立即执行函数  this指向window  相当于普通函数，只是不需要手动调用
(function () {
    console.log('立即执行函数的this' + this);
})();
```

​		立即执行函数相当于第一条普通函数，只是它不需要手动调用，因此它的this也指向window。

 **普通函数总结：**

| 调用方式     | this指向       |
| ------------ | -------------- |
| 普通函数调用 | window         |
| 构造函数调用 | 实例对象       |
| 对象方法调用 | 该方法所属对象 |

| 事件绑定方法 | 绑定事件的对象 |
| ------------ | -------------- |
| 定时器函数   | window         |
| 立即执行函数 | window         |



## 二：箭头函数中的this指向 

​     **箭头函数的this指向是静态的，当箭头函数定义的时候，箭头函数的this指向就已经确定了，箭头函数的this指向跟箭头函数所在作用域的this指向一致**

### 1、箭头函数定义在函数中

```js
function fn() {
    return () => {
        console.log(this);
    }
}
let test = fn()
test()//window
```

​		箭头函数定义在fn函数作用域内，则箭头函数的this与所在作用域的this指向一致，而普通函数的this指向全局window对象，则该箭头函数也指向window

再次验证：

```js
function fn() {
    return () => {
        console.log(this);
    }
}
let obj = {
    name: '箭头函数的指向'
}
let test = fn.call(obj)
test() // 打印出obj对象
```

​		当执行fn函数的时候，通过call方法修改fn的this指向，则箭头函数的this也跟着改变，意思就是箭头函数定义在fn函数作用域内，则箭头函数的this与所在作用域的this指向一致

### 2、箭头函数定义在对象中

```js
let obj = {
    name: 'cirrod',
    play: () => {
        console.log(this);
    }
}
console.log(obj.play()); //window全局对象
```

​		play箭头函数定义在对象中，而在js中只有全局作用域，函数作用域，块级作用域，没有对象作用域，所以相当于箭头函数定义在全局作用域中，箭头函数的this与所在作用域的this指向一致,所以this指向全局对象window

### 3、箭头函数定义在对象的方法中

```js
let obj = {
    name: 'cirrod',
    fn: function() {
        return {
            play: () => {
                console.log(this);
            }
        }
	}
}
let obj1 = obj.fn();
console.log(obj1.play()); // obj这个对象
```

​		箭头函数定义在fn函数的返回对象中，而对象没有作用域，则向上一层查找fn函数，相当于箭头函数定义在fn函数的作用域内，fn函数的this指向自己所属的对象obj，箭头函数的this与所在作用域的this指向一致，所以箭头函数的this指向为obj

### 4、箭头函数定义在定时器中

- 定时器在全局作用域中

```js
setTimeout(() => {
    console.log(this); // window
}, 100)
```

箭头函数定义在定时器的参数上，定时器的参数没有作用域，向外一级是全局作用域，相当于箭头函数定义在全局作用域，箭头函数的this与所在作用域的this指向一致，所以箭头函数的this指向为window

- 定时器在函数作用域

```js
function Person(name) {
    this.name = name
    setTimeout(() => {
        console.log(this); // cirrod这个实例对象
    }, 100)
}
const cirrod = new Person('黄某某')
```

​		箭头函数定义在定时器的参数上，定时器的参数没有作用域，向外一级是构造函数的作用域，相当于箭头函数定义在构造函数的作用域，而构造函数的this指向实例对象，箭头函数的this与所在作用域的this指向一致，所以箭头函数的 this指向为构造函数的实例对象cirrod

## 三、闭包中的this指向问题

- 返回普通函数的闭包

  在闭包中，`this` 的值取决于函数的调用方式。如果是使用函数名调用闭包函数，那么 `this` 的值将会是全局对象 `window`（在浏览器环境下）。这是因为函数名调用方式会将函数的 `this` 绑定到全局对象上。

```js
function foo() {
  console.log(this); // window
  const x = 10;

  function bar() {
    console.log(this); // window
    console.log(x);
  }

  return bar;
}

const baz = foo();
baz();
```

- apply修改

  如果使用 `call`、`apply` 或者 `bind` 方法来调用闭包函数，那么 `this` 的值将会被绑定到传入的对象上。

```js
const obj = { x: 10 };

function foo() {
  console.log(this); // obj

  return function() {
    console.log(this); // obj
    console.log(this.x); // 10
  };
}

const bar = foo.call(obj);
bar();
```

- 箭头函数的闭包见上

## 三、总结：

1. 普通函数中this是动态的，当我们调用函数的时候确定的，调用方式的不同决定了this的指向不同，一般指向我们函数的调用者。（谁调用指向谁）

2. 箭头函数的this指向是静态的，当箭头函数定义的时候，箭头函数的this指向就已经确定了，箭头函数的this指向跟箭头函数所在作用域的this指向一致

## 四、应用场景：

1. 箭头函数（静态）适合与this无关的回调，定时器，数组的方法的回调等
2. 普通函数（动态）适合与this有关的回调，事件回调，对象方法等





# Promise异步执行顺序

## 1、同步代码块

```js
console.log('1')

const p = new Promise((resolve, reject) => {
	console.log('2')
})

console.log('3')
```

- 同步代码块是从上往下执行的。当我们 new 一个 Promise 时，里面的起始函数会立即调用，而且里面的代码块是同步代码

​	所以这段代码依次输出结果为 1 2 3

## 2、出现异步代码

```js
console.log('start')

conse p = new Promise((resolve, reject) => {
	console.log('1')
	resolve('2')
})

p.then(res => {
	console.log(res)
})

console.log('end')
```

- 在这部分代码中，出现了一段异步代码：也就是 .then() 中的回调函数

- 要记住一个原则：同步先行，异步靠后，Javascript 引擎总是先执行同步代码，然后在执行异步代码。此时，我们只需要区分同步代码和异步代码即可。

所以这段代码输出结果为： start 1 end 2

## 3、有 resolve 的地方

```js
console.log('strt')

const p = new Promise((resolve, reject) => {
	console.log(1)
	resolve(2)
	console.log(3)
})

p.then(res => {
	console.log(res)
})

console.log('end')
```

- 这段代码与前面的代码基本一致，唯一区别是在 resolve() 之后有一个 console.log()要记住：resolve 方法不会中断函数的执行，它背后的代码仍然会继续执行。

所以输出的结果是： start 1 3 end 2

## 4、不被调用 resolve 的地方

```js
console.log('start')

const p = new Promise((resolve, reject) => {
	console.log(1)
})

p.then(res => {
	console.log(2)
})

console.log('end')
```

- 在这段代码中，resolve 方法从未被调用过，因此 p始终处于挂起状态 。 所以 p.then(…) 从未被执行过，故不会在控制台中打印。

所以输出的结果是： start 1 end

## 5、带有函数的

```js
console.log('start')

const fn = () => {
	return new Promise((resolve, reject) => {
		console.log(1)
		resolve(2)
	})
}

console.log(3)

fn().then(res => {
	console.log(res)
})

console.log('end')
```

- 这段代码部分人开起来可能会有些迷惑，因为加了一层 fn。但我们要要记住，无论有多少层函数调用，我们的基本原则都是一样的：

  - 同步先行，异步靠后

  - 同步代码按照调用顺序执行

输出结果为 start 3 1 end 2

详细解析：

- 首先，第一步输出 start ，相信大家都没什么疑问。
- fn 是一个函数，里面返回的是一个 new Promise 对象，new Promise 对象中的回调函数会立即执行，但由于此时 fn 函数还未调用，故先不执行。
- 接下来输出 3
- 下一行代码 fn() 函数调用，所以立即 打印 1
- 调用 resolve() 方法，这是一个异步代码，作为异步，需要等到同步代码执行完成后才会执行，故打印 end
- 同步代码全部执行完成后，执行异步代码， 打印 2

- 所以输出结果为 start 3 1 end 2

## 6、Fulfilling Promise

```js
console.log('start')

Promise.resolve(1).then((res) => {
	console.log(res)
})

Promise.resolve(2).then(res => {
	console.log(res)
})

console.log('end')
```

输出结果为： start end 1 2

详细解析：

- 首先，我们要明白 Promise.resolve(1) 这行代码，Promise.resolve方法返回一个promise的实例，状态为已完成。这行代码等价于：new Promise((resolve) => { resolve(1) })
- 看到这里，是不是就明白了？Promise.resolve(1) 是同步代码，但后面的 then() 是异步，所以输出结果为： start end 1 2

## 7、宏任务 微任务

```js
console.log('start')

setTimeout(() => {
	console.log('setTimeout')
}, 0)

Promise.resolve().then(() => {
	console.log('resolve')
})

console.log('end')
```

详细解析：

- 这段代码相比较上一个来说，多了一个定时器，我们知道，定时器也是一个异步操作。那么这时候问题就来了，同步代码是按照调用顺序执行的，那么异步回调函数时按照什么顺序执行的呢？

- 在上面的代码中，setTimeout的定时器是0秒，Promise.resolve() 也会在执行后立即返回一个已完成的 Promise 对象。两个异步任务都是立即完成的，那么谁的回调函数会先执行呢？

- 有人会说 setTimeout 在前面，所以先打印 setTimeout，然后打印 resolve，实际上，当我们在控制台运行下代码时会发现，实际情况并不是我们想的那样。它们是有优先级的。

- 微任务 > 宏任务

  - 具有较高优先级的任务叫做微任务。包括：Promise、ObjectObserver、MutationObserver、Process.nextTick、async/await。

  - 优先级较低的任务称为宏任务。包括：setTimeout、setInterval、XHR

  虽然 setTimeout 和 Promise.resolve() 是同时完成的，甚至 setTimeout 的代码写在前面，但是由于它的优先级较低，所以它的回调函数是在后面执行的。

所以输出结果是：start end resolve success

## 8、微任务 + 宏任务 升级

```js
const p = new Promise((resolve, reject) => {
	console.log(1)
	setTimeout(() => {
		console.log('timerStart')
		resolve('resolve')
		console.log('timerEnd')
	}, 0)
	console.log(2)
})

p.then((res) => {
	console.log(res)
})

console.log('end')
```

详细解析：

- 首先执行同步代码： 1 2 end

- 然后找到 微任务 和 宏任务。从上一个代码解析中我们知道，定时器是宏任务，Promise 是微任务。但这里有一个陷阱： 当前的promise 还处于 pending 状态，所以promise.then() 这部分代码（微任务）暂时不会被执行。所以会先执行定时器中的回调函数，然后在执行微任务。

结果为： 1 2 end timerStart timerEnd resolve

## 9、微任务 宏任务 交替执行排序

```js
const timer1 = setTimeout(() => {
	console.log(1)
    const promise1 = Promise.resolve().then(() => {
        console.log('promise1')
    })
}, 0)

const timer2 = setTimeout(() => {
	console.log('timer2')
}, 0)
```

输出结果为： 1 promise1 tiemr2

详细解析：

- 关于 宏任务 和 微任务，正确的理解是：
  - 首先执行所有的微任务
  - 执行宏任务
  - 再次执行所有（新添加的）微任务
  - 执行下一个宏任务

- 在上面代码中，没有和宏任务同步的微任务，所以开始执行 timer1 这个宏任务；

- 在 timer1 的宏任务中，新增了一个微任务，且该promise 对象的状态已经变为 fufilled（已成功），所以Promise.then() 的回调函数会在第二个宏任务 setTimeout 的回调函数之前执行。

所以执行结果为： 1 promise1 tiemr2

## 10、最终升级版

```js
console.log('start')

const promise1 = Promise.resolve().then(() => {
	console.log('promise1')
	const timer2 = setTimeout(() => {
		console.log('timer2')
	}, 0)
})

const timer1 = setTimeout(() => {
	console.log('timer1')
	const promise2 = Promise.resolve().then(() => {
		console.log('promise2')
	})
}, 0)

console.log('end')
```

详细解析:

- 按照以下思路进行思考：

  - 同步代码

  - 所有微任务

  - 第一个宏任务

  - 所有新添加的微任务

  - 下一个宏任务
    …

- 执行所有同步代码：

  打印 start end

- 执行所有微任务

  <img src="D:\Typora\photos\image-20230305162443663.png" alt="image-20230305162443663" style="zoom:50%;" />

  在这里打印 promise1

- 执行第一个宏任务

  <img src="D:\Typora\photos\image-20230305162518706.png" alt="image-20230305162518706" style="zoom:50%;" />

  在这里打印 timer1

​		注意：在这一步中，宏任务将一个新的为任务添加到任务队列中

- 执行所有新添加的微任务

  <img src="D:\Typora\photos\image-20230305162543747.png" alt="image-20230305162543747" style="zoom:50%;" />

  在这里打印 promise2

- 执行下一个宏任务

  <img src="D:\Typora\photos\image-20230305162603432.png" alt="image-20230305162603432" style="zoom:50%;" />

所以，最后输出结果为： start end promise1 timer1 promise2 timer2




在面试中，如果有类似的面试题，只需要记住一下三个核心要点，问题即可迎刃而解。

- JavaScript 引擎总是先执行同步代码，然后在执行异步代码（同步先行，异步靠后）
- 微任务的优先级高于宏任务
- 微任务可以在 Event Loop 中插队。



# JS继承方式总结

## 原型链继承

- 核心：子构造函数.prototype == 父类实例
  - 优点：复用了父类构造函数的方法
- 缺点：
  - 创建子类实例时，不能传父类的参数
  - 子类实例共享了父类构造函数的引用属性
  - 无法实现多继承
- 演示：

```js
// 将属性封装到构造函数上（强调私有）
function Parent(name) {
    this.name = name || '父亲'
    this.arr = [1]
}
// 将需要复用、共享的方法定义在父类原型上
Parent.prototype.say = function () { 
    console.log('hello')
}

// 子类构造函数
function Child(like) {
    this.like = like
}


// 核心，但此时 Child.prototype.constructor == Parent
Child.prototype = new Parent() 
// 修正constructor指向
Child.prototype.constructor = Child


let boy1 = new Child()
let boy2 = new Child()
// 优点：共享了⽗类构造函数的say⽅法
console.log(boy1.say(), boy2.say(), boy1.say === boy2.say) // hello，hello，true
// 缺点1：不能向⽗类构造函数传参
console.log(boy1.name, boy2.name, boy1.name === boy2.name) // 父亲，父亲，true
// 缺点2：子类实例共享了父类构造函数的引用属性
// 修改了boy1的arr属性，boy2的arr属性，也会变化，因为两个实例的原型上(Child.prototype)有了⽗类构造函数的实例属性arr；所以只要修改了boy1.arr，那么boy2.arr的属性也会变化。
boy1.arr.push(2)
console.log(boy2.arr) // [1,2]
```

> 注意1： 修改boy1的name属性， 是不会影响到boy2.name。 因为设置boy1.name相当于在子类实例新增了name属性。
> 注意2：
> console.log(boy1.constructor); // Parent 你会发现实例的构造函数居然是Parent。而实际上，我们希望子类实例的构造函数是Child，所以要记得修复构造函数指向。
> 修复如下： Child.prototype.constructor = Child;



## 构造函数继承

- 核心：子构造函数内部用 apply 或者 call 方法调用父类构造函数
- 优点：
  - 可以向父类构造函数传参数
  - 子类实例之间相互独立，不共享父类构造函数的引用属性
  - 可以实现多继承（通过多个 call 或者 apply 继承多个父类）

- 缺点

  - 父类的方法不能复用

    由于方法在父构造函数中定义，导致方法不能复用，每次创建子类实例都要创建一遍方法

  - 子类实例继承不了父类原型上的属性和方法（因为没有用到原型）

- 演示：

```js
function Parent(name) {
    this.name = name; // 实例基本属性 (该属性，强调私有，不共享)
    this.arr = [1]; // (该属性，强调私有)
    this.say = function () { // 实例引用属性 (该属性，强调复用，需要共享)
        console.log('hello')
    }
}


function Child(name, like) {
    Parent.call(this, name) // 核心 拷贝了父类的实例属性和方法
    this.like = like;
}


let boy1 = new Child('⼩红', 'apple')
let boy2 = new Child('⼩明', 'orange')
// 优点1：可向父类构造函数传参
console.log(boy1.name, boy2.name) // ⼩红，⼩明
// 优点2：不共享父类构造函数的引用属性
boy1.arr.push(2)
console.log(boy1.arr, boy2.arr) // [1,2] [1]
// 缺点1：方法不能复用
console.log(boy1.say === boy2.say) // false （说明，boy1 和 boy2 的 say 方法是独立的，不是共享的）
// 缺点2：不能继承父类原型上的方法
// 在⽗类的原型对象上定义一个 walk ⽅法。
Parent.prototype.walk = function () { 
	console.log('我会走路')
}
boy1.walk; // undefined （说明，实例不能获得父类原型上的方法）
```

## 组合继承

- 核心：结合原型链继承和构造函数继承
- 优点：
  - 保留了构造函数继承的优点：创建子类实例，可以向父类构造函数传参数
  - 保留原型链的优点：父类的方法定义在父类的原型对象上，可以实现方法的复用
  - 不共享父类的引用属性

- 缺点
  - 调用了两次父类的构造函数，会存在一份多余的父类实例属性（一次是在创建子类型原型的时候，另一次是在子类型构造函数内部）

- 演示：

```js
// 实例基本属性 （强调私有，不共享）
function Parent(name) {
    this.name = name
    this.arr = [1]
}
Parent.prototype.say = function () { // --- 将需要复用、共享的方法定义在⽗类原型上
    console.log('hello')
}

// 核心
function Child(name, like) {
    Parent.call(this, name, like) // 第二次调用
    this.like = like;
}
Child.prototype = new Parent() // 第一次调用
Child.prototype.constructor = Child // 修正 constructor 指向


let boy1 = new Child('⼩红', 'apple')
let boy2 = new Child('⼩明', 'orange')
// 优点1：可以向父类构造函数传参数
console.log(boy1.name, boy1.like); // ⼩红，apple
// 优点2：可复用父类原型上的方法
console.log(boy1.say === boy2.say) // true
// 优点3：不共享父类的引用属性，如arr属性
boy1.arr.push(2)
console.log(boy1.arr,boy2.arr); // [1,2] [1] 可以看出没有共享 arr 属性
// 缺点：由于调用了2次父类的构造方法，会存在一份多余的父类实例属性

```

## 组合继承优化

- 核心：让子类构造函数的 `prototype` 指向父类构造函数的 `prototype`
- 优点
  - 只调用一次父类的构造函数
  - 可以向父类构造函数传参
  - 将父类的实例方法定义在原型身上，可以实现函数复用
- 缺点
- 演示：

```js
// 实例基本属性 (强调私有，不共享)
function Parent(name) {
    this.name = name;
    this.arr = [1];
}
// 需要复用、共享的方法定义在父类原型上
Parent.prototype.say = function() { 
    console.log('hello')
}


function Child(name,like) {
    Parent.call(this,name,like) // 核心
    this.like = like;
}
Child.prototype = Parent.prototype // 核心 子类原型和父类原型，实质上是同一个
// 修复构造函数指向
Child.prototype.constructor = Child


// 优点1：可以向⽗类构造函数传参数
let boy1 = new Child('⼩红','apple')
let boy2 = new Child('⼩明','orange')
let p1 = new Parent('爸爸')
console.log(boy1.name,boy1.like); // ⼩红，apple
// 优点2：可复用父类原型上的方法
console.log(boy1.say === boy2.say) // true
// 缺点：当修复子类构造函数的指向后，父类实例的构造函数指向也会跟着变了。
// 没修复之前：console.log(boy1.constructor); // Parent
// 修复代码：Child.prototype.constructor = Child
// 修复之后：
console.log(boy1.constructor); // Child
console.log(p1.constructor);// Child 这里就是存在的问题（我们希望是Parent）
```

具体原因：因为这种方式是通过原型来实现继承的，Child.prototype的上面是没有constructor 属性的，构造函数上找不到就会往原型链上找，这样就找到了Parent.prototype上面的 constructor 属性；因此当修改了子类实例的 construtor属性，所有的 constructor 的指向都会发生变化。

## 寄生组合继承

- 核心：利用 `Object.create(object，[propertiesObject]) `
  - `Object.create() ` 方法创建⼀个新对象，使用第一个参数来提供新创建对象的`__proto__`（以第一个参数作为新对象的构造函数的原型对象）； 方法还有第二个可选参数，是添加到新创建对象的属性
- 最完美的继承方式
- 演示：

```js
// 实例基本属性（强调私有，不共享）
function Parent(name) {
    this.name = name;
    this.arr = [1];
}
// --- 将需要复用、共享的方法定义在父类原型上
Parent.prototype.say = function() { 
    console.log('hello')
}


function Child(name,like) {
    Parent.call(this, name, like)  // 核心
    this.like = like;
}
// 核心 通过创建中间对象，子类原型和父类原型就会隔离开。有效避免了⽅式4的缺点。
Child.prototype = Object.create(Parent.prototype)
// 修复构造函数指向
Child.prototype.constructor = Child


let boy1 = new Child('⼩红','apple')
let boy2 = new Child('⼩明','orange')
let p1 = new Parent('⼩爸爸')
```





# JS常见设计模式

## 单例模式

- 基本了解
  - 单例模式是一种只允许创建一个实例的对象。
  - 通过封装一个私有的构造函数和一个用于创建单例对象的静态方法来实现。这种模式的主要目的是确保系统中某个类只有一个实例，并提供一个全局的访问点。
  - 单例模式通常用于管理全局状态或资源，例如：全局配置对象、日志记录器、缓存、数据库连接等。通过使用单例模式，可以避免多个实例对系统造成的资源浪费和状态不一致的问题。

- 实现代码：

```js
var Singleton = (function(){
    // 将单例对象储存在这个变量中，确保单例对象的唯一性
    var instance;
    // 首次创建
    function createInstance(){
        // 放置单例对象的初始化代码
        return{
            // 放置单例对象的属性和方法
        }
    }
    return{
        getInstance:function(){
            // 如果对象没被创建
            if (!instance){
                instance = createInstance()
            }
            // 如果已经存在，返回原创建结果
            return instance
        }
    }
})()

// 使用
var s1 = new Singleton.getInstance()
var s2 = new Singleton.getInstance()
console.log(s1 === s2) // true
```

- 在源码中的使用

  - Vuex：实现了一个全局的 store 用来存储应用的所有状态。这个 store 的实现就是单例模式的典型应用。

  ```js
  let Vue // instance 实例
  
  export function install (_Vue) {
    // 判断传入的Vue实例对象是否已经被install过（是否有了唯一的state）
    if (Vue && _Vue === Vue) {
      if (process.env.NODE_ENV !== 'production') {
        console.error(
          '[vuex] already installed. Vue.use(Vuex) should be called only once.'
        )
      }
      return
    }
    // 若没有，则为这个Vue实例对象install一个唯一的Vuex
    Vue = _Vue
    // 将Vuex的初始化逻辑写进Vue的钩子函数里
    applyMixin(Vue)
  }
  ```

  - 通过这种方式，可以保证一个 Vue 实例只会被 install 一次 Vuex 插件，所以每个 Vue 实例只会拥有一个全局的 Store

## 工厂模式

- 基本了解

  - 思想类似于 java 对象和接口，使用者只需要知道产品名字就可以拿到实例，不关心创建过程。所以我们可以把复杂的过程封装在一起，更便于使用。

- 实现举例

  ```js
  function createPerson(name,age){
      var person = {};
      person.name = name;
      person.age = age;
      person.say = function(){
          console.log("my name is " + person.name+",my age is " + person.age);
      }
      return person;
  }
  
  var Jim = createPerson("jim", 20);
  var Tom = createPerson("Tom", 18);
  Jim.say();
  Tom.say();
  ```

  

- 在源码中的使用

  - 在`vue-router` 中使用了工厂模式的思想来获得响应路由控制类的实例，`this.history` 用来保存路由实例。

  ```js
  export default class VueRouter{
      constructotr(options){
          const mode = options.mode || "hash";
          switch (mode) {
              case 'history':
                  this.history = new HTML5History(this, options.base)
                  break
              case 'hash':
                  this.history = new HashHistory(this, options.base, this.fallback)
                  break
              case 'abstract':
                  this.history = new AbstractHistory(this, options.base)
                  break
              default:
                  if (process.env.NODE_ENV !== 'production') {
                    assert(false, `invalid mode: ${mode}`)
                  }
          }
      }
  }
  ```

## 构造函数模式

- 顾名思义，利用函数的构造函数来创建对象
- 常用于创建多个相似对象，通过构造函数来定义对象的属性和方法，从而实现代码的复用
