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

    console.log('start')
    
    const p = new Promise((resolve, reject) => {
    	console.log(1)
    })
    
    p.then(res => {
    	console.log(2)
    })
    
    console.log('end')

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

## 7、宏任务 微任

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