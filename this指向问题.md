## 一：普通函数的this指向

**普通函数中this是动态的，当我们调用函数的时候确定的，调用方式的不同决定了this的指向不同，一般指向我们函数的调用者。（谁调用指向谁）**

### 1、普通函数

```js
//1.普通函数
function fn() {
	console.log('普通函数的this' + this);
}
fn();//window   实际上，这里是window.fn();谁调用，this就指向谁
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
  console.log('绑定事件的this' + this); //指向btn 也是指向函数的调用者
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
<script>
    function fn() {
        return () => {
            console.log(this);
        }
	}
    let test = fn()
    test()//window
</script>
```

​		箭头函数定义在fn函数作用域内，则箭头函数的this与所在作用域的this指向一致，而普通函数的this指向全局window对象，则该箭头函数也指向window

再次验证：

```js
<script>
    function fn() {
        return () => {
            console.log(this);
        }
	}
    let obj = {
        name: '箭头函数的指向'
    }
	let test = fn.call(obj)
	test()//打印出obj对象
</script>
```

​		当执行fn函数的时候，通过call方法修改fn的this指向，则箭头函数的this也跟着改变，意思就是 箭头函数定义在fn函数作用域内，则箭头函数的this与所在作用域的this指向一致

### 2、箭头函数定义在对象中

```js
<script>
    let obj = {
        name: 'cirrod',
        play: () => {
            console.log(this);
        }
    }
	console.log(obj.play()); //window全局对象
</script>
```

​		play箭头函数定义在对象中，而在js中只有全局作用域，函数作用域，块级作用域，没有对象作用域，所以相当于箭头函数定义在全局作用域中，箭头函数的this与所在作用域的this指向一致,所以this指向全局对象window

### 3、箭头函数定义在对象的方法中

```js
<script>
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
console.log(obj1.play());//obj这个对象
</script>
```

​		箭头函数定义在fn函数的返回对象中，而对象没有作用域，则向上一层查找fn函数，相当于箭头函数定义在fn函数的作用域内，fn函数的this指向自己所属的对象obj，箭头函数的this与所在作用域的this指向一致，所以箭头函数的this指向为obj

### 4、箭头函数定义在定时器中

- 定时器在全局作用域中

```js
<script>
    setTimeout(() => {
        console.log(this);//window
    }, 100)
</script>
```

箭头函数定义在定时器的参数上，定时器的参数没有作用域，向外一级是全局作用域，相当于箭头函数定义在全局作用域，箭头函数的this与所在作用域的this指向一致，所以箭头函数的this指向为window

- 定时器在函数作用域

```js
<script>
    function Person(name) {
        this.name = name
        setTimeout(() => {
            console.log(this);//cirrod这个实例对象
        }, 100)
    }
    const cirrod = new Person('黄某某')
</script>
```

​		箭头函数定义在定时器的参数上，定时器的参数没有作用域，向外一级是构造函数的作用域，相当于箭头函数定义在构造函数的作用域，而构造函数的this指向实例对象，箭头函数的this与所在作用域的this指向一致，所以箭头函数的this指向为构造函数的实例对象cirrod

## 三、总结：

1. 普通函数中this是动态的，当我们调用函数的时候确定的，调用方式的不同决定了this的指向不同，一般指向我们函数的调用者。（谁调用指向谁）

2. 箭头函数的this指向是静态的，当箭头函数定义的时候，箭头函数的this指向就已经确定了，箭头函数的this指向跟箭头函数所在作用域的this指向一致

## 四、应用场景：

1. 箭头函数（静态）适合与this无关的回调，定时器，数组的方法的回调等
2. 普通函数（动态）适合与this有关的回调，事件回调，对象方法等