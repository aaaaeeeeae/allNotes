# 1、Vue基础



[官网传送门](https://cn.vuejs.org/)



## 1.1 模板语法和指令语法

### 模板语法

#### 插值语法

- 插值语法常用于指定标签体内容（标签里面写的内容）

- 用 {{}} 表示值的语法

```html
<body>
    <div id="root">
        <h1>插值语法</h1>
        <h3>你好，{{name}}</h3>
    </div>
    <script>
        //阻止vue在启动时产生生产提示
        Vue.config.productionTip = false
        //创建Vue实例
        new Vue({
            el:'#root', 
            data:{ 
                name:'hhh'
            }
        })
    </script>
</body>
```

- 括号内还可以写一些表达式

```html
<h3>你好，{{name.subtring(0, 3)}}</h3>
```

- 插值语法还能插入methods里面的函数，函数名后加括号表示调用函数，不加括号表示返回该函数

```html
<h3>{{hello()}}</h3>
<script>
//阻止vue在启动时产生生产提示
Vue.config.productionTip = false
  //创建Vue实例
new Vue({
	el:'#root', 
    data:{ 
    	name:'hhh'
	},
	methods:{
        hello(){
        	retrun '你好！'
        }
	}
})
</script>
```

### 指令语法

- 指令语法可以用来解析标签（解析包括：标签属性、标签体内容、绑定事件等）

- 指令语法常用于指定标签的属性

- 里面的值同样要写成表达式形式

#### 单向数据绑定

- 单向绑定是指仅能从实例的Vue对象中的data中读取值

- ```v-bind:  ```可以为标签中的任何属性（包括自定义属性）动态绑定一个值，里面的值会直接读取到data中的内容。
- ``` v-bind:  ```   还可以简写成  ``` : ```

``` html
<body>
	<div id="root">
		<h1>插值语法</h1>
		<h3>你好，{{name}}</h3>
		<hr>
		<h1>指令语法</h1>
		<h3>点击转到百度1：<a v-bind:href="url">百度</a></h3>
		<h3>点击转到百度2：<a :href="url">百度</a></h3>
	</div>
<script>
	Vue.config.productionTip = false
//创建Vue实例
	new Vue({
        el:'#root', 
        data:{ 
            name:'hhh',
            url: ' https://www.baidu.com/['](https://www.baidu.com/),
        }
	})
</script>
</body>
```

#### 双向数据绑定

- 双向绑定是指不仅能从实例的Vue对象的data中获取值；用户还可以在页面输入值，从而对Vue对象的data进行修改

- ```v-model:  ```是双向绑定，可以简写为```v-model```

- v-model只能应用在表单元素（有value值的，如input、select等）

```html
<body>
    <div id="root">
        <h1>单向绑定</h1>
        <input type="text" v-bind:value="one">
        <hr>
        <h1>双向绑定</h1>
        <input type="text" v-model:value="one">

        <!-- 简写形式 -->
        <input type="text" :value="one">
        <input type="text" v-model="one">

    </div>
    <script>
        Vue.config.productionTip = false
        //创建Vue实例
        new Vue({
            el:'#root', 
            data:{ 
                name:'hhh',
                one: 'value'
            }
        })
    </script>

</body>
```

#### 简写形式的总结

- 以后使用``` v-bind: ```都只写``` : ```
- 使用``` v-model : value='xxx' ```都写成``` v-model = 'xxx'```

### el和data的两种写法

#### el的写法

- new Vue的时候配置el属性

``` js
el: '#root'
```

- 先创建Vue实例，随后通过下列语句指定

```js
v.$mount('#root')
```

- 一般情况下，这两个都可以使用，但第二种适应的地方会更广一点，如

```js
const v = new Vue({
	data:{}
})

setTimeout(() => {
	v.$mount('#root')
},1000);
```

#### data的写法

- 对象式

```vue
data:{

	xxx:xxx

}
```

- 函数式
- 不能写箭头函数，必须有返回值，返回值要以对象的形式

```js
data: function(){
	return{
		xxx:xxx
	}
}
```

- 可以省略 :function，写成

```js
data(){
	return{
		xxx:xxx
	}
}
```

- 写组件的时候必须用第二种方法

## 1.2 MVVM模型

M：模型（model），data中的数据

V：视图（view），模板代码

VM：视图模型（ViewModel），vue实例

![image-20221027204706826](D:\Typora\photos\image-20221027204706826.png)

## 1.3 数据代理和数据监测

### 数据代理

- 一个对象能操纵另一个对象中的属性和方法叫做数据代理

- 需要知道object.defineProperty() 的原理和应用，见JS高级-对象进阶

#### 原理描述

原本所有数据存储在Vue实例中data属性里，但通过数据代理将Vue中的data属性里的值在Vue实例中进行映射，在Vue对象中直接创建了值对应的属性。该属性调用getter和setter方法读取和修改data属性中的值。

#### 原理过程

通过object.defineProperty() 把对象中所有属性都添加到vm（vue的实例对象）上，为每一个添加到vm上的属性都指定getter和setter，在getter和setter内部去操作data中对应的属性

#### 好处

可以直接通过 vm.属性名读取，而不用进行vm._data.属性名读取，更加方便

### 监测数据

- Vue如何监测对象中的数据

  通过setter监测，且要在new Vue时就传入要监测的数据

  （1）在对象中追加的属性，Vue默认不做响应式处理（实际修改了数据但是页面不会更改数值）

  （2）如需给后添加的属性做响应式，需要使用

​				Vue.set(tartget, key, val)

​				vm.$set(target, key, val)

- Vue如何监测数组中的数据

​		通过包裹数组更新元素的方法实现

​		（1）调用原生对应的方法对数组进行更新

​		（2）重新解析模板，进而更新页面

​		（3）在Vue中修改数组中某个元素一定要用这些方法

​				使用API：push()、pop()、shift()、unshift()、splice()、sort()、reverse()

​				使用set：Vue.set() 或vm.$set()

- Vue.set()和vm.$set()不能为vm，也不能为vm的根数据(vm._data)，只能为vm._data中的对象添加



## 1.4 事件处理

### 绑定事件
- 用```  v-on: 事件名 = "函数名"        ```在标签内绑定元素
```html
<div v-on:click = "fn"></div>
```

- 可以简写成@事件名 = "函数名"

```html
<div @click = "fn"></div>
```

- 除了写函数名以外，引号里还能写一些简单的表达式

```html
<div @click = "this.x++"></div>
```

- 可以在函数名后直接传入参数，相当于在引号内直接调用函数

```html
<div @click = "fn1(1,2,3)"></div>
```

- 由于传入参数会把event事件覆盖，因此用$event占位符进行占位

```html
<div @click = "fn2(1,$event)"></div>
```

​		写在methods里面的函数是没有进行数据代理的，若有需求做数据代理可放到data里，但是不建议这样使用，因为这样使用没意义，函数一般用这种情况做数据代理


- ```@click = "fn"```和```@click = "fn($event)"```是一样的，即不传参数时加括号与不加都可以

```html
<div id = "root">
	<h2>欢迎！{{name}}</h2>
	<button v-on:click="fn1">点我！</button>
	<button @click="fn2(1,$event)">点我！</button>   
</div>

<script>
    Vue.config.productionTip = false
    //创建Vue实例
    const v = new Vue({
        data(){
            return{ 
                name: '阿澈'
            }
        },
        methods:{
            fn1(event){
                // 这里传入的对象是事件
                console.log(event);
                // 这里的this指向vm（Vue实例）
                // 如果fn是箭头函数this指向window，因此不建议写成箭头函数
                console.log(this);
            },
            fn2(a,event){
                console.log(event);
                console.log(a);
            }
        }
	})
	v.$mount('#root');
</script>
```

### 事件修饰符

以下修饰符在事务需要的时候是可以连着写的，例如

```html
<a href = 'www.baidu.com'  @click.prevent.stop = 'fn'></a>
```

#### .prevent 

阻止事件默认发生

在以前，可用e.preventDefault()阻止事件默认发生，在vue中，可用.prevent代替这个语句

```html
<a href = 'www.baidu.com' @click.prevent = 'fn'>点击进入百度</a>
```

#### .stop 

阻止事件冒泡 （常用）

```html
<div @click = 'fn'>
	<button @click = 'fn'>点我</button>
</div>
```

在这个案例中点击按钮，由于事件冒泡会弹出两次警示框，以前使用e.stopPropagation()阻止事件冒泡，现在，可用.stop代替
```html
<div @click = 'fn'>
	<button @click.stop = 'fn'>点我</button>
</div>
```

#### .once  

事件只触发一次 （常用）

```html
<div @click = 'fn'>
	<button @click.stop = 'fn'>点我</button>
</div>
```

按钮能重复点击，加上.once之后，按钮点击一次就失效
```html
<div @click = 'fn'>
	<button @click.once = 'fn'>点我</button>
</div>
```

#### .capture 

使用事件的捕获模式

```html
<div @click = 'msg(1)'>div1
	<div @click.once = 'msg(2)'>div2</div>
</div>
```

按照事件捕获以及冒泡规律，当点击div2时，会先打印2再打印1

当给div1添加.capure后，在div1捕获到点击信息的时候就会立即执行，即先会打印1再打印2
```html
<div @click.capture = 'msg(1)'>div1
	<div @click.once = 'msg(2)'>div2</div>
</div>
```

#### .self 

只有e.target是当前操作的元素才触发事件

```html
<div @click = 'pro'>
	<button @click = 'pro'>点我</button>
</div>
```

当点击button并用e.target获取事件源时，由于事件冒泡会打印出两个button

当为div添加.self后，表示e.target等于div时才会触发div上的点击事件pro，因此只会出现一个button

.self也是作为阻止事件冒泡的一种手段
```html
<div @click.self = 'pro'>
	<button @click = 'pro'>点我</button>
</div>
```

#### .passive 

事件的默认行为立即执行，无需等待事件回调执行完毕

- 什么是事件回调：事件响应的过程中，会先执行函数内的代码，再让浏览器响应该有的行为

- 在鼠标滚轮移动事件，函数中for循环的体量过大，会一直持续执行for循环体的内容，页面并没有因为滚轮滑动而下移，为滚轮事件添加.passive后，页面下移立刻执行，不需要让调用函数执行完毕
```js
Vue.config.productionTip = false
//创建Vue实例
const v = new Vue({
    data(){
        return{ 

        }
    },
    methods:{
        fn(e){

        },
        msg(context){
        	console.log(context);
        },
        pro(e){
       		console.log(e.target)
    	}
	}
})
v.$mount('#root');
```

### 键盘事件别名

#### 常用别名

当有业务需要按下指定两个键时，也可以向上面那样连这写，如

```html
<input @keyup.ctrl.y = 'fn'>
```

以下常用别名可以写键盘上首字母大写的名称，如enter->Enter，delete->Delete

- .enter 回车

  业务：在输入框中输入内容，按下enter键后把输入内容打印出来

  以往的做法：

```js
fn(e) {
	if(e.keyCode == 13){
		console.log(e.target.value);
	}
}
```

​		现在只需要加入.enter

```html
<input type="text" placeholder="按下enter输入" @keyup.enter = "fn">
```

- .delete 删除 （包括backspace和delete）
- .esc 退出
- .space 空格
- .tab  换行（特殊按键，绑定事件必须是keydown，因为按下tab会立刻转移焦点）
- .up 上
- .down 下
- .left  左
- .right  右
- 在以上没有提供的按键且由多个单词组成，应该全小写并由 - 进行分隔，如CapsLock->caps-lock

系统修饰键（ctrl、alt、shift、meta）使用比较特殊

- 绑定事件keyup时，按下对应按键的同时，还需按下并松开其他任意按键，事件才能触发

- 绑定事件keydown时，正常触发

#### 自定义别名按键

Vue.config.keyCodes.自定义键名 = 键码



## 1.5 计算属性和属性监听

### 计算属性

- 计算属性是指用已有的属性通过一系列计算获得新属性

- computed必须有get方法，当有人读取计算后的新属性时会调用get方法，其返回值就是该属性的值。读取一次之后vue就会缓存该属性，即不再多次调用get方法了；但当data中的属性改变时，又会重新调用get方法
- data中的属性都可以在vue的实例对象._data中获得，而计算属性不能以这种方式获得，即没有数据代理
- 在computed中的任何计算属性中的this都是指向的Vue的实例对象，即data中的属性都用this.属性名调用，this是不能省略的
- set不是必须写的，若业务要求需要更改属性就可以写set

#### 完整写法

```js
<div>{{name}}</div>
new Vue({
    el:'#root',
    data:{
        firstName:  '张',
        lasetName:  '三'
    },
    computed:{
        name:{
            get(){
            	return this.firstName + '-' + this.lastName;
            },
            set(value){
                var arr = value.split('-');
                this.firstName = arr[0];
                this.lastName = arr[1];
            }
        }
    }
})
```

#### 简写形式

不写set只写get的时候才能使用简写形式

```js
new Vue({
    el:'#root',
    data:{
        firstName:  '张',
        lasetName:  '三'
    },
    computed:{
        name(){
            return this.firstName + '-' + this.lastName;
        }
    }
})
```



### 属性监听

- 属性监听是当属性发生更改的时候，就会调用对应属性的handler()函数，该函数有两个默认的值：newValue和oldValue，分别储存修改后的值和修改前的值
- immediate配置项是为了在初始化时（没被修改时）让handler调用一次
- 如果写了一个并不存在的属性监听，并不会报错但也不会起作用

#### 两种监听方式

```js
<div @click = 'change'>今天的天气是{{isHot}}</div>
new Vue({
    el:'#root',
    data:{
        isHot: true
    },
    methods:{
        change(){
            this.isHot = !this.isHot;
        }
    },
    watch:{
        isHot:{
            immediate: true,
            handler(newValue, oldValue){
                console.log(oldValue + '->' + newValue);
            }
        }
    }
})
const vm = new Vue({
    el:'#root',
    data:{
        isHot: true
    },
    methods: {
        change(){
            this.isHot = !this.isHot;
        }
    }
})
vm.$watch('isHot',{
    immediate: true,
    handler(newValue, oldValue){
        console.log(oldValue + '->' + newValue);
    }		
})
```

#### 深度监听

- Vue是可以监测到多层级结构中的属性的，但是watch默认只能监测一级，所以使用watch时要根据数据的具体结构来决定是否需要深度监视

```js
<div>a的值是：{{numbers.a}}</div>
<button @click = 'numbers.a++'>点我让a + 1</button>
new Vue({
    el:'#root',
    data:{
        numbers{
            a: 1,
            b: 1
        }
	},
    watch:{
        a:{
        	handler(){
    			console.log('a被改变了')
			}
		}
	}
})
// 上述例子中点击button可以实现a++的功能，但是a被修改时并没有输出提示信息

// 想要监听多级结构中的一个属性
new Vue({
    el:'#root',
    data:{
        numbers{
            a: 1,
            b: 1
        }
    },
    watch:{
        'numbers.a':{
            handler(){
                console.log('a被改变了')
            }
		}
	}
})

// 但是这种并不适用于同时监测numbers中所有属性，需要用到deep配置项
new Vue({
    el:'#root',
    data:{
        numbers{
            a: 1,
            b: 1
        }
	},
    watch:{
        numbers:{
        	deep:true;
        	handler(){
    			console.log('a被改变了')
			}
		}
	}
})
```

#### 监视简写

- 不需要用到immediate和deep配置项时就可简写

```js
new Vue({
    el:'#root',
    data:{
        numbers{
            a: 1,
            b: 1
        }
	},
    watch:{
        numbers(newValue, oldValue){
    		console.log('a被改变了')
		}
	}
})

vm.$watch('isHot',function(newValue, oldValue){
    console.log(oldValue + '->' + newValue);
})
```

### 监听属性和计算属性的区别

- 一般情况下用计算属性写的代码也可以用监听属性来写，但计算属性写的代码更简单，也不用命名新属性，更推荐使用计算属性
- 计算属性不能开启异步任务，因为计算属性高度依赖return语句

```js
<div id="root">
    <input type="text" v-model = 'firstName'><br>
    <input type="text" v-model = 'lastName'><br>
    <input type="text" :value = 'name'>
</div>

new Vue({
    el:'#root',
    data:{
        firstName: '张',
        lastName: '三',
    },
    computed:{
        name(){
            return this.firstName + '-' + this.lastName
        }
    }
})

new Vue({
    el:'#root',
    data:{
        firstName: '张',
        lastName: '三',
        fullName: '张-三'
    },
    watch:{
        firstName(newValue){
            this.fullName = newValue + '-' + this.lastName;
        },
        lastName(newValue){
            this.fullName = this.firstName + '-' + newValue;
        }
    }
})

/* 若要求修改firstName和lastName之后延迟一秒fullName才更改，只能用watch来实现，且必须使用箭头函数！！！因为setTimeout由Vue代理，但里面的函数不由Vue代理而是由js引擎管理，因此写普通函数时定时器中this指向的是window而不是vm */
new Vue({
    el:'#root',
    data:{
        firstName: '张',
        lastName: '三',
        fullName: '张-三'
    },
    watch:{
        firstName(newValue){
            setTimeout(() => {
                this.fullName = newValue + '-' + this.lastName;
            }, 1000)
        },
        lastName(newValue){
            setTimeout(() => {
                this.fullName = this.firstName + '-' + newValue;
            }, 1000)
        }
    }
})
```

重要原则：

- 所有被Vue管理的函数，最好写成普通函数，这样this的指向才是vm或者组件实例对象
- 所有不被Vue管理的函数（如定时器回调函数、ajax的回调函数等），最好写成箭头函数，这样this的指向才是vm或者组件实例对象



## 1.6 绑定样式

### 绑定class样式

想要修改一个元素的class，原先的方式是用querySelector拿到元素，然后用className = 'xxx'进行修改，现在可以使用 :class

#### 绑定class-字符串写法

适用于样式类名不确定，需要动态指定，多选一的问题

```vue
<div class = "basic" :class = "cc" @click = "change"></div>
.basic{
	border: 1px solid red;
}
.happy{
	background-color : orange;
}
.normal{
	font-size: 20px;
}
.sad{
	background-color : blue;
}

new Vue({
    el:'#root',
    data:{
    	cc: '',
    },
    methods:{
        change(){
            const arr = ['happy', 'sad', 'normal'];
            let index = floor(Math.random() * 3);
            this.cc = arr[index]
        }
    }
})
```

#### 绑定class-数组写法

适用于要绑定的样式个数不确定，名字也不确定，任意添加的问题

```html
<div class = "basic" :class = "classarr" @click = "change"></div>
const vm = new Vue({
    el:'#root',
    data:{
    	classarr: ['happy', 'sad', 'normal']
    },
})
//以后需要添加或者删除
vm.classarr.shift();
vm.classarr.push('happy')
```

#### 绑定class-对象写法

适用于要绑定样式的个数确定，名字也确定，从中选一个或多个的问题

```html
<div class = "basic" :class = "obj" @click = "change"></div>
const vm = new Vue({
    el:'#root',
    data:{
        obj:{
            happy: false,
            sad: false,
            normal: false
        }
    },
})
//需要修改时
vm.obj.happy = true;
```



### 绑定style样式（很少使用）

用 :style 绑定style样式

- 绑定对象写法

```html
<div class = "basic" :style = "styleobj" @click = "change"></div>
const vm = new Vue({
    el:'#root',
    data:{
        styleobj:{
            fontSize: '20px',
            color: 'red',
            backgroundColor: 'pink'
        }
    },
})
```

- 绑定数组写法

```html
<div class = "basic" :style = "[styleobj1, styleobj2]" @click = "change"></div>
const vm = new Vue({
    el:'#root',
    data:{
        styleobj1:{
            fontSize: '20px',
            color: 'red',
        },
        styleobj2{
            backgroundColor: 'pink'
        }
    },
})
```



## 1.7 条件渲染

### v-show 隐藏元素

引号中可以写表达式，只要这个表达式能转换为布尔值即可，其底层逻辑实现是控制display

```html
<div v-show = "false"></div>
```

### v-if 、v-else-if、v-else

- 逻辑跟普通的if、else if、else语句是一样的，但这三个语句如果要使用就必须要连在一起写，若中间出现新标签则打乱了条件分割的语义，打断后的逻辑将不会执行且会报错 

- 引号中同样可以写能转换为布尔值的表达式

- 使用v-if时需要注意的是标签使用了v-if可能会无法获取（因为标签被删除），而使用v-show是一定能获取到标签的

- 实现隐藏元素的功能：

```html
<div v-if = "false"></div>
```

两者区别：v-if会直接在浏览器中删除标签改变结构

​					如果需要频繁隐藏建议使用v-show

### template使用

```html
<template v-if = "true">
    <div></div>
    <div></div>
</template>
```

- template标签在页面渲染之后会消失，不会产生该标签，但template只能配合v-if使用，不能配合v-show

## 1.8 列表渲染

### v-for 循环生成标签

- 语法类似于正常JS中的for 循环的语法，用于遍历数组、对象、数组对象、字符串（很少用）、遍历固定次数（很少用）均可

- 以下用for in或者for of都可

```vue
<ul>
    //在里面生成3个li
    <li v-for = "p in persons" :key = "p.id">
        {{p.name}} - {{p.class}}
    </li>
</ul>
const vm = new Vue({
    el:'#root',
    data:{
        persons[
            {id: 951, name: '张三', class: 02},
            {id: 952, name: '李四', class: 02},
            {id: 953, name: '王五', class: 01},
        ]
    },
})
```

### 关于key的机制

- 在虚拟DOM中key的作用

​	key是虚拟DOM对象的标识，当状态中的数据发生变化的时候，Vue会根据新数据产生新的虚拟DOM，随后Vue进行新虚拟DOM与旧虚拟DOM的差异比较。

- 比较规则

（1）旧虚拟DOM中找到了与新虚拟DOM相同的key

​		若虚拟DOM中内容没变，直接使用之前生成的真实DOM

​		若虚拟DOM中内容变了，则生成新的真实DOM，随后替换掉页面中之前的真实DOM

（2）旧虚拟DOM中未找到与新虚拟DOM相同的key

​		创建新的真实DOM，然后渲染到页面

- 如何选择key

​	最好选择使用每条数据的唯一标识作为key，比如id、身份证号等

​	如果不存在对数据逆序添加、逆序删除等破坏顺序的操作，仅用于渲染列表用以展示，使用index作为key可以的

![image-20221126163321549](D:\Typora\photos\image-20221126163321549.png)

### 案例

```vue
<!-- 案例：写入关键字进行搜索 -->
<div id="root">
    <ul>
        <input type="text" v-model="keyword">
        <li v-for="p in fillpersons">
            {{p.name}} -- {{p.age}} -- {{p.sex}}
        </li>
    </ul>
</div>
<script>
    Vue.config.productionTip = false
    //用监视属性来写
    /*new Vue({
		el:'#root',
		data:{ 
			keyword:"",
			persons:[
				{name: '周冬雨', age: 25, sex: '女'},
				{name: '马冬梅', age: 28, sex: '女'},
				{name: '周杰伦', age: 34, sex: '男'},
				{name: '温兆伦', age: 36, sex: '男'}
			],
			fillpersons:[]
		},
		watch:{
			keyword:{
				immediate:true,
				handler(val){
					this.fillpersons = this.persons.filter((curval) =>{
					//这里写-1是因为没找到返回-1，但如果匹配空字符串结果会为0
					return curval.name.indexOf(val) != -1;
					})
				}
			}
		}
	})*/
    //用计算属性来写
    new Vue({
        el:'#root',
        data:{ 
            keyword:"",
            persons:[
                {name: '周冬雨', age: 25, sex: '女'},
                {name: '马冬梅', age: 28, sex: '女'},
                {name: '周杰伦', age: 34, sex: '男'},
                {name: '温兆伦', age: 36, sex: '男'}
            ],
        },
        computed:{
            fillpersons(){
                return arr = this.persons.filter((curval)=>{
                    return curval.name.indexOf(this.keyword) != -1;
                })
            }
        }
    })
</script>
```

```vue
<!-- 案例：列表排序 -->
<div id="root">
    <ul>
        <input type="text" v-model="keyword">
        <button @click="type = 1">升序排列</button>
        <button @click="type = 2">降序排列</button>
        <button @click="type = 0">原排列</button>
        <li v-for="p in fillpersons">
            {{p.name}} -- {{p.age}} -- {{p.sex}}
        </li>
    </ul>
</div>
<script>
    new Vue({
        type: 0,
        el:'#root',
        data:{ 
            type: 0,
            keyword:"",
            persons:[
                {name: '周冬雨', age: 25, sex: '女'},
                {name: '马冬梅', age: 28, sex: '女'},
                {name: '周杰伦', age: 34, sex: '男'},
                {name: '温兆伦', age: 36, sex: '男'}
            ],
        },
        computed:{
            fillpersons(){
                const arr = this.persons.filter((curval)=>{
                    return curval.name.indexOf(this.keyword) != -1;
                })
                if (this.type){
                    arr.sort((a,b)=>{
                        return this.type == 1 ? a.age - b.age : b.age - a.age;
                    })
                }
                return arr;
            }
        }
    })
<script>
```



## 1.9 收集表单数据

### v-model收集表单数据

- 普通input框（type="text"）---- v-model收集的是value值，用户输入的也是value值

- 单选框（type="radio"）---- v-model收集value值，必须给标签配置value值才能收集

- 多选框（type="checkbox"）

  - 没有配置value属性 ---- 那么收集的就是checked（是否勾选，true/false）

  - 配置了value属性后 ---- 若v-model为非数组，一样收集checked

  											  								 ---- 若为数组，收集的就是value组成的数组

### v-model修饰符

- v-model.number 

  限制输入是数值型

```vue
<input type="number" v-model.number="age">
```
​		修改`<input type="number">`的区别

​			input修改后在Vue是以字符串形式进行代理，而直接加修饰符会是数值型

​			一般情况下两者会同时使用

- v-model.lazy

  常用在textarea中实现防抖功能

```vue
<textarea v-model.lazy="other"></textarea>	
```
​		不会实时收集用户输入信息，而是等待用户输入完毕后进行收集

- v-model.trim

​		去掉输入时前后的空格

### 案例

```vue
<!-- 收集表单数据案例 -->
<div id="root">
	<form @submit.prevent="demo">
		账号：<input type="text" v-model="account"> <br><br>
		密码：<input type="text" v-model="password"> <br><br>
		年龄：<input type="number" v-model.number="age"><br><br>
		性别：
		<!-- 这里一定要写value，才能实现绑定 -->
		男<input type="radio" name="sex" v-model="sex" value="男"> 
		女<input type="radio" name="sex" v-model="sex" value="女"><br><br>
		爱好: 
		<!-- 多选框如果不写value值，Vue会提取其checked值（true/false），且注意要把
		多选框绑定值改为数组，不能是字符串 -->
		下棋<input type="checkbox" v-model="hobby" value="chess"> 
		听音乐<input type="checkbox" v-model="hobby" value="music"> 
		玩游戏<input type="checkbox" v-model="hobby" value="game"> <br><br>
		所属校区：
		<!-- 下拉菜单绑定在select上 -->
		<select v-model="city">
			<option value="">请选择校区</option>
			<option value="北京">北京</option>
			<option value="上海">上海</option>
			<option value="重庆">重庆</option>
		</select><br><br>
		其他信息：
		<textarea cols="20" rows="2" v-model.lazy="other"></textarea><br><br>
		<input type="radio" v-model="agree">阅读并接受<a href="www.baidu.com">《用户协议》</a>
		<br><br><button>提交</button>
	</form>
</div>
<script>
    Vue.config.productionTip = false
    const vm = new Vue({
        el: '#root',
        data:{ 
            account:"",
            password:"",
            age:undefined,
            sex:"",
            // 这里一定要是数组
            hobby:[],
            city:"",
            other:"",
            agree:""
        },
        methods: {
            demo(){
            }
        }
    })
</script>
```



## 1.10 过滤器

### 局部过滤器

对传入的数据进行加工，但该属性在Vue3中已经被移除了

```vue
new Vue({
    el: '#root',
    data:{ 
    },
    filters:{
        filter1(val,items){
            return xxx;
        },
        filter2(val){
            return xxx;
        }
    },
})
```

- 多个过滤器可以叠加使用

  例如：

  在处理时间时通常会得到时间戳，通常我们需要将时间戳转换为可读性更好的时间格式，可以通过外部引入新的js包结合计算属性来实现，也可以通过Vue的过滤器来实现

```vue
<div id="root">
    <div>{{cmtime}}</div>
    <!-- 过滤器不写参数time也会默认传递给过滤器函数
即便是传一个模板值，在函数内仍然有一个val来表示传入time的值 -->
    <div>{{time | timeFormater("YYYY年MM月DD日")}}</div>
    <!-- 过滤器可以叠加使用，上一个处理完后将结果传递给下一个 -->
    <div>{{time | timeFormater("YYYY年MM月DD日") | myslice}}</div>
</div>
<script>
    Vue.config.productionTip = false
    new Vue({
        el: '#root',
        data:{ 
            time:Date.now(),
        },
        computed:{
            cmtime(){
                return dayjs(this.time).format('YYYY-MM-DD HH:mm:ss');
            }
        },
        filters:{
            timeFormater(val,str='YYYY-MM-DD HH:mm:ss'){
                return dayjs(val).format(str);
            },
            myslice(val){
                return val.slice(0,4);
            }
        },
    })
</script>
```

### 全局过滤器

以上写的都是局部过滤器，只能在vm实例内部进行使用，但若添加组件后，两个组件相互独立，A组件中不能调用B组件中过滤器中的函数，B组件同理，因此需要全局过滤器。

- 全局过滤器的定义：

```vue
Vue.filter('xxx', function(){
	xxxx;
})
```

全局过滤器只能一个一个添加，不能一次性写多个，且必须写在new Vue前

过滤器可以使用在插值语法以及v-bind（很少用）中，但是不能使用到v-model中，会报错

```html
<div :x="msg | mySlice"><div>
```



## 1.11 指令

### 其他常用内置指令

之前学的v-on、v-bind、v-for、v-if 等都是Vue编写好的内置指令，除此之外还有其他内置指令

#### v-text

- 与innerText功能一样(不能解析标签，即在里面写标签无法识别)

- 与插值语法类似，但是实用性没有插值语法高，插值语法可以解析标签
```vue
<div v-text="msg"></div>
<div>{{msg}}</div>

new Vue({
    el: '#root',
    data:{ 
        msg:"hello world!"
    },
})
```

#### v-html

- 与innerHTML功能一样，能解析标签

```vue
<div v-html="http"></div>

new Vue({
    el: '#root',
    data:{ 
        http:"<div></div>"
    },
})
```
- 关于v-html的安全问题

账号敏感信息存储于cookie之中，当其他人使用你的cookie就能登录你的账号（XSS攻击）

在网站上动态渲染任意html是非常危险的，会受到XSS攻击，一定要在可信内容上使用v-html，永远不要相信用户输入内容

#### v-cloak

- 加载完毕之后这个配置项就会被删除

- 可以与css属性一起使用，通常是为了防止因网络等问题将未经解析的模板直接呈现在页面上

例如：引入一个js文件，并把链接写到body中时，body中的标签需要用Vue进行代理解析，但由于阻塞，Vue并不能及时解析标签内容，就会造成把Vue的代码呈现给用户，交互性并不好，可以用v-cloak和css中的display一起使用，当页面加载完毕Vue监管到代码后，v-cloak配置项会消失，而让内容一起呈现
```vue
[v-cloak]{
    display:none;
}

<div id = "root">
    <div v-cloak>{{name}}</div>
</div>
//假设这段代码要加载5s
<script src="xxxx"></script>

new Vue({
    el: '#root',
    data:{ 
        name:'zhangsan'
    },
})
```
#### v-once

- v-once所在的节点在初次动态渲染之后就视为静态内容了，以后的数据改变都不会引起v-once所在结构的改变

```vue
<div v-once>{{n}}</div> //这里的n永远为1
<button @click="n++">点我n+1</button>
<div>{{n}}</div>

new Vue({
    el: '#root',
    data:{ 
        n:1;
    },
})
```
#### v-pre

- v-pre可以将该节点跳过编译

- 可以利用它跳过：没有使用指令语法、插值语法的结点，能加快编译速度

```vue
<div v-pre v-once>{{n}}</div>  //会直接出现{{n}}
    <button @click="n++">点我n+1</button>
<div>{{n}}</div>

new Vue({
    el: '#root',
    data:{ 
        n:1;
    },
})
```

### 自定义指令

#### 函数式

- 写法

```vue
<div v-xxx=""></div>

new Vue({
	el: '#root',
	directives:{
		xxx:function(element, binding){

		}
	}
})
```
​		第一个参数element：真实DOM元素，类似于通过querySelector获得到的元素

​		第二个参数binding：对象，只需要使用其value属性

可简写为
```vue
new Vue({
	el: '#root',
	directives:{
		xxx(){

		}
	} 
})
```
- 该函数何时会被调用？

​	1、指令与元素成功绑定时（初始化时）

​	2、指令所在的模板被重新解析时（data中任何内容发生改变时）

```vue
<!-- 定义一个v-big指令，和v-text功能类似，但把绑定的数值放大10倍 -->

<div id="root">
	<h2>当前的n值是 <span v-text="n"></span></h2>
	<h2>放大10倍后的n值是 <span v-big="n"></span></h2>
	<button @click="n++">点我n++</button>
</div>

new Vue({
	el: '#root',
	data:{ 
		n:1,
	},
	directives:{
		big(element, binding){
			element.innerText = binding.value * 10;
		}
	} 
})
```
#### 对象式

- 写法

```vue
new Vue({
	el: '#root',
	data:{ 
		n:1,
	},
	directives:{
		xxx:{
			bind(element, binding){},
			inserted(element, binding){},
			update(element, binding){}
		}
	} 
})
```
​		bind()：当指令与元素成功绑定的时候调用

​		inserted()：指令所在元素被插入页面时调用

​		update()：指令所在模板被重新解析时调用

- 其实函数式就是对象式没写inserted()函数的情况


```vue
<!-- 定义一个input框，让其value值等于n，并获取焦点 -->

<input type="text" v-fbind:value="n">

Vue.config.productionTip = false
new Vue({
    el: '#root',
    data:{ 
        n:1,
    },
    directives:{
        big(element, binding){
            element.innerText = binding.value * 10;
        },
        fbind:{
            bind(element, binding){
                element.value = binding.value;
            },
            inerted(element, binding){
                element.focus();
            },
            update(element, binding){
                element.value = binding.value;
            }
        }
    } 
})
```


- 当命名有多个单词时
```vue
<div v-bind-number=""></div>

new Vue({
    el: '#root',
    directives:{
        'bind-number'(element, binding){

        }
    }
})
```
- this的指向问题

​		不论是用函数式来写还是对象式来写，自定义指令中所有this都是指向window

#### 全局自定义指令

- 与filters一样，写在Vue中的自定义指令都是局部的，另一个Vue不能共享其中的自定义指令，想要共用需要写成全局，语法格式都与filters一样，全局与局部命名发生冲突时以局部为主

```vue
Vue.directive('fbind', {
    bind(element, binding){},
    inserted(element, binding){},
    update(element, binding){}
})
```


## 1.12 生命周期

### 生命周期图

![生命周期](D:\Typora\photos\生命周期.png)

### nextTick(新钩子)

在下一次DOM更新结束后执行指定的回调

- 使用方法

```
this.$nextTick(回调函数)
```

- 使用场景

​	当改变数据后要基于更新后的新DOM进行某些操作时（相当于生命周期中的mouted），要在nextTick所指定的回调函数中执行

​	例如，要实现input框的聚焦效果。写在函数内的会一次性执行完毕后再挂载到页面，而聚焦功能只有在挂载到页面后才能实现，因此失效。

# 2、组件化编程

## 组件

- 实现应用中局部功能代码和资源的集合

![image-20221128120543186](D:\Typora\photos\image-20221128120543186.png)

### 单文件组件

一个文件中只包含有一个组件

### 非单文件组件

一个文件中包含有很多个组件

- 局部组件的创建和使用
  - 定义组件


```vue
const 组件暂时名 = Vue.extend({
    template:`
        <div>
        </div>
    `,
    data(){
        return{
        }
	}
})
```

​			  注册组件

```vue
new Vue({
    el: '#root',
    data:{
    },
    components:{
        组件名: 组件暂时名
    }
})
```

- 全局组件的创建和使用

  - 定义全局组件

    与前面一样

  - 注册全局组件


​				必须写在new Vue前

​				Vue.component('组件名', 组件暂时名)

​             使用组件

​				与前面一样

- 一些细节问题
  - 定义局部组件时，不需要写el，因为组件可以随时随地复用，不能把它绑死了
  - template中一定要加一个div，因为template不能作为根元素
  - 写组件的时候必须要写成函数返回对象的形式，这样创建组件的时候因为堆栈原因，各个组件才不会互相影响
  - 定义局部组件时，真实名字和组件前面定义的名字二者同名时可以简写为一个，但全局组件必须写全两个


- 组件命名

  - 一个单词组成

    - 首字母小写：school
    - 首字母大写：School

  - 多个单词组成


​				短横线法（在Vue中需要外包引号）：

​						Vue实例中：my-school

​						使用：`<my-school></my-school>`

​				驼峰命名法（必须在Vue脚本中才能使用）：

​						Vue实例中：MySchool

​						使用：`<MySchool></MySchool>`

​				组件名尽可能回避HTML中已有的元素名称

- 可以通过name配置项指定组件在开发中工具中呈现的名字（只会更改在开发者工具中的名字）

```vue
const 组件暂时名 = Vue.extend({
    name: '开发工具名',
    template:`
        <div>
        </div>
    `,
    data(){
        return{
        }
    }
})
```

- 组件标签的使用
  - 普通使用

  ```vue
  <school></school>
  ```

  - 自闭和标签

    在普通Vue中支持这种使用方法

  ```vue
  <school/>
  ```

  ​		但只能使用一次，后续的标签会因此无法渲染。在脚手架中就可以重复使用了

- 定义组件时的简写形式

```vue
const 组件暂时名 = {
    template:`
        <div>
        </div>
    `,
    data(){
        return{
        }
    }
}
```

- 组件的嵌套
  - 在父组件定义的时候用components继续注册子组件套娃，但子组件定义必须写在父组件前
  - 一般在开发中，Vue只需要管理一个app组件，其他组件都按逻辑层级注册到app身上即可


## VueComponent

- 组件的本质是一个名为VueComponent的构造函数，这个构造函数不是程序员自己定义的，而是Vue.extend生成的

- 当我们使用组件时，Vue解析时会帮助创建对应组件的实例对象，即执行 new      VueComponent(options)

- 每次调用Vue.extend，返回的都是一个全新的VueComponent

- this指向问题

  - 在Vue实例对象中，data函数、methods函数、watch函数、computed中的函数，this均是Vue实例对象vm
  - 在组件配置中，data函数、methods函数、watch函数、computed中的函数，this均是VueComponent实例对象

  - VueComponent实例对象以后简称为组件实例对象（vc），一个vm管理多个vc

- 重要的内置对象关系

​		VueComponent.prototype.__proto__ === Vue.prototype

​		这样做可以让组件的实例对象vc访问到Vue原型上的属性和方法

![image-20221128163701514](D:\Typora\photos\image-20221128163701514.png)



# 3、Vue脚手架

## 脚手架的初始化

- 脚手架文件结构

<img src="D:\Typora\photos\image-20221128164037952.png" alt="image-20221128164037952" style="zoom: 50%;" />

- vue.js与vue.runtime. xxx .js的区别

​			vue.js是完整版的Vue，包含核心功能和模板解析器

​			vue.runtime. xxx .js是运行版的Vue，只包含核心功能，没有模板解析器

- 由于vue.runtime.     xxx .js没有模板解析器，所以不能使用template配置项，需要使用rander函数接收到的createElement函数去指定具体内容

- vue.config.js配置文件
  - 使用vue inspect > output.js 命令后会生成一个output.js文件，可以查看到Vue脚手架的一些默认配置（不能更改只能查看）
  - 使用vue.config.js可以对脚手架继续个性化定制，具体如何写参考官方文档

- 组件化编码流程
  - 拆分静态组件，组件要按照功能点拆分，命名不能与html元素冲突

  - 实现动态组件，考虑好数据存放位置
    - 一个组件在用：放组件自身即可
    - 一些组件在用：放共同的父组件上

  - 实现交互：从绑定事件开始


## scoped

- 在组件文件中写的style样式最终在Vue解析的时候会合并成一个css文件，因此若出现两个组件文件中css样式重名的情况则会被覆盖，为了解决这一问题，需要分别在组件文件style标签上添加scoped，代表作用域为该组件文件。

```vue
<style scoped>
<style>
```


- 需要注意的是如果在app组件的style标签上添加scoped，则style标签中的样式并不能作用给其他引入的组件标签，因此app组件一般不会添加scoped
- style标签还有一个lang配置项，这个配置项默认不写为css，若lang =     'less'，则可使用less语法

## ref

- 以前在原生dom环境中获取元素可以通过 `document.getElementById()` 或者`document.querySelector()`，在vue环境中可以在标签内用 `ref =""` 定义元素，再通过`this.$refs.名字` （这里是refs别写成ref了）获得元素以达到相同效果

- 与原生js获取元素的方法不同的是，用ref配置项可以获取组件元素

```vue
<template>
    <div>
        <h3 v-text="msg" ref="hello"></h3>
        <fhead ref = "fhead"></fhead>
        <fbody></fbody>
        <button @click="sayHello"></button>
    </div>
</template>
<script>
    import fhead from './components/FHead.vue';
    import fbody from './components/FBody.vue';
    export default {
        name:'app',
        components:{
            fhead,
            fbody
        },
        data(){
            return{
                msg:'你好啊'
            }
        },
        methods:{
            sayHello(){
                console.log(this.$refs.hello);
                console.log(this.$refs.fhead);
            }
        }
    }
</script>
```

## props

前面已经写过框架了，想更新框架中的数据需要props配置项

通常props有三种写法:

- 简单写法

​		类似于数组

```vue
props:['v1', 'v2', 'v3']
```

- 接收的时候对数据进行限制

```vue
props:{
    v1: String,
    v2: Number
}
```

- 对接收内容进行类型限制、默认值指定、必要性限制

​		里面有三种配置项，一般来说default和required不会同时出现

```vue
props:{
    v1:{
        type: String,
        required: true
    },
    v2:{
        type: Number,
        default: 999
    }
}
```

一些细节问题：

- 假如props传入的数据与data中的数据重名，控制台会报错，且以props传入的数据作为呈现（props上的数据会被优先放入vc身上）
- props传入的数据一般情况下是不允许被修改的，修改对象里的值（对象的地址没变，Vue只能浅监视，对象内的数据被改变暂时无法监测）也不会报错（仅限于对象中某个数据改变，其他类型改变会引起地址的改变）
- 如果非要修改传入的数据，可在data中创建一个属性用this赋值的方式让其与修改数据相等，然后再修改data中的属性即可
- 名字不能与关键字如key、ref等重名
- v-model绑定的值不能是props传过来的值，因为props中的数据是不能被修改的

```vue
<!-- app文件： -->
<student name='张三' id="003" :age="18"></student>
<hr>
<student name='李四' id="033" ></student>
<hr>
<student name='王五' id="057" :age="22"></student>

<!-- student组件： -->
<template>
	<div>
        <h3>{{msg}}</h3>
        <h2>学生姓名：{{name}}</h2>
        <h2>学生id:{{id}}</h2>
        <h2>学生年龄:{{newAge + 1}}</h2>
        <button @click="this.newAge++">点我age+1</button>
    </div>
</template>
<script>
    export default {
        data(){
            return {
                msg:'我是一名学生',
                newAge: this.age
            }
        },

        // 写法一: 简单接收（常用）
        props:['name', 'id', 'age']
        // 写法二：接收的同时对数据进行类型限制
        /* props:{
				name:String,
		        age:Number,
		        id:String
		    } */
        //写法三：可对接收内容进行类型限制、默认值指定、必要性限制
        /* props:{
				name:{
					type: String,
		            required:true,
		        },
		        age:{
		            type:Number,
		            default:18
		        },
		        id:{
		            type: String,
		            required:true,
		        }
		  } */
    }
</script>
```

## mixins

可以将一些通用的东西放在外部js文件中，然后通过mixins来引入js文件，以减少代码的重复使用

外部js文件可以配置组件中的所有内容，包括data、methods等，若data中的名字与引入js文件的名字重名，则会以原本data中的为主

- 局部混入

```js
// 在min.js 文件中
export const mix1 = {
    methods: {
        show(){
            alert(this.name);
        }
    },
}
export const mix2 = {
    data(){
        return {
            x:100,
            y:100
        }
    }
}

// 在任意组件中
import { mix1, mix2 } from "../min";
export default {
    name:'stu',
    data() {
        return {
            name: "张三",
            sex: "男",
        };
    },
    mixins: [mix1, mix2]
};
```

- 全局混入

```js
// 在main.js中

import Vue from 'vue'
import App from './app.vue'
import {mix1, mix2} from './min'

Vue.mixin(mix1);
Vue.mixin(mix2);

new Vue({
	render: h => h(App),
}).$mount('#app')
```

## 插件

- 插件的功能是增强Vue

- 其本质是包含install方法的一个对象，install的第一个参数是Vue，后续参数可以自己设定

- 定义插件

  - 定义插件

    （需要暴露的其他语法暂时不写，只写一个默认暴露）

  ```js
  export default{
      install = function(Vue, other){
          Vue.filter(…)
          Vue.directive(…)
          Vue.mixin(…)
          Vue.prototype.$methords = function(){…}
          Vue.prototype.$property = …
      }
  }
  ```

## 本地储存

### localStorage

其中储存的东西即使浏览器关闭也不会消失，除非手动清除缓存或者使用清空

- 存储

  ```js
  （window.）localStorage.setItem('key', 'value')
  ```

  - window可以省略

  - key和value必须是字符串，就算不是字符串，也会调用toString方法将其转换为字符串

  - 写对象的时候若直接写localStorage.setItem('key', obj)会调用对象的toString方法，转换为[obj, obj]这种格式，因此需要调用JSON.stringify(obj)将对象及其属性转换为字符串

- 读取

  ```js
  （window.）localStorage.getItem('key')
  ```

  - 当读取不存在的key值时，结果为null，JSON.parse(null)的结果仍然是null

- 删除

  ```js
  （window.）localStorage.removeItem('key')
  ```

- 清空

  ```js
  （window.）localStorage.clear();
  ```

### sessionStorage

- 其中存储的东西浏览器一关闭便不会再保存

- 用法和localStorage一模一样，把上述localStorage换成sessionStorage即可

 ```vue
 <button onclick="saveData()">点我添加数据</button>
 <button onclick="readData()">点我读取数据</button>
 <button onclick="deleteData()">点我删除数据</button>
 <button onclick="removeAll()">点我清空全部数据</button>
 
 <script>
 	function saveData(){
 		let obj = {name:'张三', age:18};
         
 		localStorage.setItem('msg1', 'hello');
         
 		// 不是字符串的会被转换成字符串
 		localStorage.setItem('msg2', 666);
         
 		// 这个结果是[object Object]
 		// localStorage.setItem('msg3', obj);
         
 		//结果是{"name":"张三","age":18}
 		localStorage.setItem('msg3', JSON.stringify(obj));
 
 	}
 
 	function readData(){
 		console.log(localStorage.getItem('msg1'));
 		console.log(localStorage.getItem('msg2'));
 
 		//这个结果是 {"name":"张三","age":18} （字符串型）
 		// console.log(localStorage.getItem('msg3'));
 
 		//这个结果是 {name: '张三', age: 18} （对象型）
 		const res = localStorage.getItem('msg3');
 		console.log(JSON.parse(res));
 	}
 
 	function deleteData(){
 		localStorage.removeItem('msg1');
 	}
 
 	function removeAll(){
 		localStorage.clear();
 	}
 </script>
 ```



## 组件间通信

### 组件自定义事件

一种组件间通信的方式，适用于子组件传递信息给父组件

- 如何实现自定义事件？

  - 方式一：props传递函数

    通过props将父组件中的函数传递给子组件，在子组件中执行父组件的函数

  ```vue
  // 父组件
  
  <div id="app">
      <div>利用props传递函数实现子组件向父组件通信</div>
      <div>son1中的内容是：{{sontext1}}</div>
      <sonf :getson="getsonf"></sonf>
  </div>
  <script>
  export default {
    name: "App",
    data(){
      return{
        sontext1:'',
      }
    },
    components:{
      sonf,
    },
    methods:{
      getsonf(value){
        this.sontext1 = value;
        console.log("子组件已触发父组件getson函数！");
      },
    },
  };
  </script>
  ```

  ```vue
  // 子组件
  
  <template>
    <div>
      <button @click="getmessage">点我传递</button>
    </div>
  </template>
  
  <script>
  export default {
    name: "sonf",
    data(){
      return{
        text:'这是子组件1传递给父组件的信息！'
      }
    },
    methods:{
      getmessage(){
        this.getson(this.text);    
      }
    },
    props:['getson']
  };
  </script>
  ```

  - 方式二：利用$emit触发事件

    在子组件中用$emit触发父组件中的自定义事件

  ```vue
  // 父组件
  
  <template>
    <div id="app">
      <div>利用$emit传递函数实现子组件向父组件通信</div>
      <div>son2中的内容是：{{sontext2}}</div>
      <song @get="getsong"></song>
    </div>
  </template>
  
  <script>
  export default {
    name: "App",
    data(){
      return{
        sontext2:'',
      }
    },
    components:{
      song,
    },
    methods:{
      getsong(value){
        this.sontext2 = value;
        console.log("子组件已触发父组件getson函数！");
      }
    },
  };
  </script>
  ```

  ```vue
  // 子组件
  
  <template>
    <div>
      <button @click="getmessage">点我传递</button>
    </div>
  </template>
  
  <script>
  export default {
    name: "song",
    data(){
      return{
        text:'这是子组件2传递给父组件的信息！',
      }
    },
    methods:{
      getmessage(){
          this.$emit('get', this.text)
      },
    }
  };
  </script>
  ```

  - 方式三：利用ref和$on绑定事件

    方式三和方式二其实是一个道理，只不过把绑定的实现写在mounted钩子中，通过ref获得子组件对象，并用$on给其绑定了一个自定义事件

  ```vue
  // 父组件
  
  <template>
    <div id="app">
      <div>利用ref传递函数实现子组件向父组件通信</div>
      <div>son3中的内容是：{{sontext3}}</div>
      <sonh ref="sonhd"></sonh>
    </div>
  </template>
  
  <script>
  export default {
    name: "App",
    data(){
      return{
        sontext3:'',
      }
    },
    components:{
      sonh,
    },
    methods:{
      getsonh(value){
        this.sontext3 = value;
        console.log("子组件已触发父组件getson函数！");
      }
    },
    mounted(){
      this.$refs.sonhd.$on('getsong', this.getsonh)
    }
  };
  </script>
  ```

  ```vue
  // 子组件
  
  <template>
    <div>
      <button @click="getmessage">点我传递</button>
    </div>
  </template>
  
  <script>
  export default {
    name: "song",
    data(){
      return{
        text:'这是子组件3传递给父组件的信息！'
      }
    },
    methods:{
      getmessage(){
        this.$emit('getsong', this.text)
      }
    }
  };
  </script>
  ```

- 触发事件

​		在对应组件中使用 this.$emit(事件名, 传入参数) 触发该事件

- 解绑事件

  - 解绑一个事件

    在对应组件中使用 this.$off(事件名) 解绑事件

  - 解绑多个事件

​				当需要解绑多个事件时 this.$off([事件名1, 事件名2, 事件名3 ….])

​				或者用this.$off() 解绑该组件上的所有自定义事件

- 组件上绑定原生DOM事件如click等会被识别成自定义组件，在后面添加修饰符native即可

```vue
<student @click.native="hello"/>
```



### 全局事件总线

一种组件间通信的方式，适用于任意组件间通信。类似于中介，各个组件将事件传递给该中介，然后又从该中介中取得信息

- 注册全局事件总线

```vue
new Vue({
    …
    beforeCreate(){
    	Vue.prototype.$bus = this //这里的this是vm
    }
    …
})
```

- 使用全局事件总线

​	在A组件中绑定自定义事件（事件回调函数在A组件身上）

```vue
methods(){
	demo(){}
}

mounted(){
	this.$bus.$on('xxx', this.demo)
}
```

- 在B组件中触发自定义事件

```js
this.$bus.$emit('xxx', 传入参数)
```

- 最好在beforeDestroy钩子中用$off解绑事件（在app组件里解绑）
- 案例

```vue
// 父组件

<template>
  <div id="app">
    <div>利用事件总线实现组件通信</div>
    <div>son4中的内容是：{{sontext4}}</div>
    <soni></soni>
  </div>
</template>

<script>
export default {
  name: "App",
  data(){
      sontext4:''
    }
  },
  methods:{
    getsoni(value){
      this.sontext4 = value
      console.log("子组件已触发父组件getson函数！");
    }
  },
  mounted(){
    this.$bus.$on('getsoni',this.getsoni)
  }
};
</script>
```

```vue
// 子组件

<template>
  <div>
    <button @click="getmessage">点我传递</button>
  </div>
</template>

<script>
export default {
  name: "sonf",
  data() {
    return {
      text: "这是子组件4传递给父组件的信息！",
    };
  },
  methods: {
    getmessage() {
      this.$bus.$emit('getsoni', this.text)
    },
  },
};
</script>
```



### 消息订阅与发布

一种依赖第三方库的方式实现任意组件间通信

- 安装pubsub

```
npm i pubsub-js
```

- 使用

在需要进行组件通信的组件中引入库（不管订阅还是发布）

```
import pubsub from 'pubsub-js'
```

在A组件接收数据，则需要坐在A组件中订阅消息

```
methods(){
	demo(msgName, data){}
}
mounted(){
	this.pdi = pubsub.subscribe('things', this.demo);
}
```

在B组件提供数据

```
pubsub.publish('item', data)
```

- 使用时的注意点

- - 最好在beforeDestroy钩子中用PubSub.unsubscribe(pid)去取消订阅
  - 在subscribe中的函数要么写箭头函数，要么在methods中写方法，因为该库是第三方库，this指向为undefined
  - 函数参数有两个，一个为messageName:      订阅消息名称，另一个才是将要传入的数据，使用时如果不需要可以使用 _ 去占位

### 组件间通信总结

- props: 父给子

- 自定义事件：子给父

- 全局事件总线：万能

- pubsub: 万能

- 插槽：父给子

- vuex: 万能



## 动画与过渡

### 动画

与CSS3动画结合实现动画功能

- 实现过程

- - 将要实现动画的结构放在transition标签内，这个标签在模板解析的时候不会出现
    - 这里加上 :appear="true" 即可实现页面刷新直接执行，可直接简写为appear
    - 用不简写的方式时不加：会报错，建议直接写appear

- ```vue
  <transition>
  					
  </transition>
  ```

- - 实现CSS的动画

  ```css
  @keyframes 动画名{
      from{
  
      }
      to{
  
      }
  }
  ```

  - 添加到Vue

    在style标签中

  ```css
  .v-enter-active{
  
      animation: 动画名 …效果
  
  }
  
  .v-leave-active{
  
      animation: 动画名 …效果
  
  }
  ```

- 当需要添加多个动画的时候，可以考虑在     transition 标签上添加 name 属性

```vue
<transition name="animation1"></transition>

<transition name="animation2"></transition>
```

​		相应的类名也需要修改

```vue
.animation1-enter-active{}

.animation1-leave-active{}

.animation2-enter-active{}

.animation2-leave-active{}
```

- 案例

```vue
<template>
<div>
    <button @click='isShow = !isShow'>点我隐藏信息</button>
    <transition name='hello' appear>
        <h3 v-show="isShow">{{msg}}</h3>
    </transition>
    </div>
</template>
<script>
    export default {
        data(){
            return {
                msg:'你好',
                isShow:true
            }
        },

    }
</script>
<style scoped>
    h3{
        background-color: orange;
    }
    .hello-enter-active{
        animation: animateName 0.75s linear;
    }
    .hello-leave-active{
        animation: animateName 0.75s linear reverse;
    }
    @keyframes animateName{
        from{
            transform: translateX(-100%);
        }
        to{
            transform: translateX(0);
        }
    }
</style>
```

### 过渡

必须要有 `v-if` 或者 `v-show` 标签才能实现过渡的效果

- 实现过程

```vue
<transition name="animation">

</transition>

.animation-enter-active, .animation-leave-active{
	过渡效果
}

.animation-enter{
	进入时最初的地方
}

.animation-enter-to{
	进入时到达的地方
}

.animation-leave{
	离开时最初的地方
}

.animation-leave-to{
	离开时到达的地方
}
```

![image-20221129174040389](D:\Typora\photos\image-20221129174040389.png)

- 上述位置相同的地方是可以连写的

```vue
.animation-enter, .animation-leave-to{
	进入时出现的地方 / 离开时到达的地方
}

.animation-enter-to, .animation-leave{
	进入时到达的地方 / 离开时最初的地方
}
```

- 案例

```vue
<template>
<div>
    <button @click='isShow = !isShow'>点我隐藏信息</button>
    <transition name='hello' appear>
        <h3 v-show="isShow">{{msg}}</h3>
    </transition>


    </div>
</template>
<script>
    export default {
        data(){
            return {
                msg:'你好',
                isShow:true
            }
        },

    }
</script>
<style scoped>
    h3{

        background-color: orange;
    }
    .hello-enter-active, .hello-leave-active{
        transition: 0.75s linear;
    }
    .hello-enter, .hello-leave-to{
        transform: translateX(-100%);
    }
    .hello-enter-to,.hello-leave{
        transform: translateX(0);
    }
</style>		<template>
<div>
    <button @click='isShow = !isShow'>点我隐藏信息</button>
    <transition name='hello' appear>
        <h3 v-show="isShow">{{msg}}</h3>
    </transition>


    </div>
</template>
<script>
    export default {
        data(){
            return {
                msg:'你好',
                isShow:true
            }
        },

    }
</script>
<style scoped>
    h3{

        background-color: orange;
    }
    .hello-enter-active, .hello-leave-active{
        transition: 0.75s linear;
    }
    .hello-enter, .hello-leave-to{
        transform: translateX(-100%);
    }
    .hello-enter-to,.hello-leave{
        transform: translateX(0);
    }
</style>
```



## 为多个元素添加效果

不管用哪种方式添加动画效果甚至是不同的动画效果，transition都只能为一个元素添加动画，想要为多个元素添加需要用到transition-group标签，且其子元素都必须加上唯一的key标签

```vue
<transition-group>
    <h2 key="2"></h2>
    <h2 key="1"></h2>
</transition-group>
```



## 第三方动画库

[官网](https://animate.style/)

在npm官网上输入animate.css

- 安装

```apI
npm install animate.css --save
```

- 引入库

```js
import 'animate.css'
```

- 在transition标签中加入  `class="animate__animated animate__bounce"`

```js
<transistion class="animate__animated animate__bounce">

</transistion>
```

- 用 `enter-active-class 和 leave-active-class` 配置自己想要的样式

```vue
<transistion 
	class="animate__animated animate__bounce"
  	enter-active-class="animate__bounceInRight"
  	leave-active-class="animate__bounceOutLeft"
  	\>
</transistion>
```



## Vue跨域处理

- 可通过设置vue.config.js开启代理服务器

vue.config.js配置文件

```js
module.exports = {
    pages:{
        index:{
            entry:'src/main.js',
        },
    },
    // 关闭语法检查
    lintOnSave:false,

    // 开启代理服务器（方式一）
    // 这种方式有局限性，只能绑定一台服务器；不能灵活控制请求是否走代理
    /* devServer:{
        // 连接服务器的地址
        proxy:'http://localhost:5000'
    } */
    
    // 开启代理服务器（方式二）
    // 这种方式就可以绑定多台服务器，也可以通过是否添加前缀来决定是否走代理
    /* devServer:{
        proxy:{
            '/test':{
                // 连接服务器的url地址
                target:'http://127.0.0.1:5000',
                
                // 由于服务器代理时，多出的url地址（api）会直接映射到服务器，
                // 导致地址部分错误，出现404错误
                pathRewrite:{'^/test':''},

                // 用于支持 websocket (以后再说)
                ws: true,
                // 隐藏客户端主机名，用于保密，默认为true，隐藏
                changeOrigin: true
            }
        }
    } */
}
```

App文件

```vue
<template>
    <div>
        <button @click='sendmail'>点我发送请求</button>
    </div>
</template>

<script>
    import axios from 'axios';

    export default {
        name:'app',
        components:{}, 
        methods: {
            sendmail(){
                // 这里的url地址应该写vue代理服务器的地址
                // 把前面设置的 /test 前缀加到端口号中间即可按方式二走代理
                // 若不走代理服务器，可直接写服务器的urL地址 http://127.0.0.1:5000/server，也可以实现跨域（用CORS方法）
                axios.get('http://127.0.0.1:8080/test/server').then(response =>{
                    console.log(response.data);
                }, err => {
                    console.log(err.message);
                })
            }
        },
    }
</script>

```

服务器端

```js
const express = require('express');
const app = express();

app.get('/server', (req, res) => {
    const data = {
        mstr: 'success!'
    }
    
    // 一定要加允许跨域请求头，否则会出现错误
    res.setHeader('Access-Control-Allow-Origin', '*');

    res.send(data.mstr);
    
})

app.listen(5000, () => {
    console.log('服务端正在工作');
})
```

## 插槽

让父组件可以向子组件指定位置插入html结构，也属于组件间通信的方式之一

### 插槽的分类

#### 默认插槽

```vue
// 父组件
<template>
  <div class="container">
    <!-- 这里title前面不需要加 : 是因为加了表示data里的数据传入页面，
        而这需要传递的是字符串而不是数据，不需要解析字符串 -->
    <!-- 默认插槽不能使用简写的自结束标签<category/> -->
    <category title="游戏分类">
      <ul>
        <li v-for="(list, index) in games" :key="index">{{list}}</li>
      </ul>
    </category>
  </div>
</template>

<script>
import category from "./components/category.vue";

export default {
  name: "app",
  components: { category },
  data() {
    return {
      games: ["原神", "明日方舟"],
    };
  },
};
</script>


// 子组件
<template>
  	<div class="category">
        <h3>{{title}}</h3>
        <!-- slot是一个位置标符，表示图片该放在这个位置 -->
        <!-- <slot>这是一个默认值，当使用者没有传递具体结构时会出现</slot> -->
        <slot></slot>
  	</div>
</template>

<script>
export default {
    props:['title']
}
</script>
```

#### 具名插槽

```vue
// 父组件
<template>
  <div class="container">
    <!-- 可以为插槽加名字，将特定的元素插入特定的位置 -->
    <!-- 当插槽外部结构为template时，可以使用v-slot:xxx(这里不需要加引号) -->
    <category title="美食分类">
      <img src="./assets/logo.png" />
        
      <!-- v-slot方式(2.6版本之后使用这种方式) -->
      <template v-slot:middle>
        <img src="./assets/logo.png" />
      	<img src="./assets/logo.png" />
        <img src="./assets/logo.png" />
      <template>
      
      <!-- slot方式 -->
      <ul slot="foot">
        <li v-for="(list, index) in movies" :key="index">{{list}}</li>
      </ul>
    </category>
  </div>
</template>

<script>
import axios from "axios";
import category from "./components/category.vue";

export default {
  name: "app",
  components: { category },
  data() {
    return {
      foods: ["火锅", "烧烤", "牛排"],
    };
  },
};
</script>


// 子组件
<template>
  <div class="category">
    <h3>{{title}}</h3>
    <slot name="middle"></slot>
    <slot name="foot"></slot>
    <slot></slot>
  </div>
</template>

<script>
export default {
    props:['title']
}
</script>
```

#### 作用域插槽

数据在组件自身，但是要求根据数据在组件使用者身上生成不一样的结构

```vue
// 父组件
<template>
  <div class="container">
      
    <!-- 简写形式 -->
    <!-- 这里简写形式必须写在组件上！ -->
    <category v-slot="movie">
      <!-- 这里的movie是一个对象 -->
      <!-- {{movie}} -->
      <ul>
        <li v-for="(m,index) in movie.movie" :key="index">{{m}}</li>
      </ul>
    </category>
      
    <!-- 非简写形式(不推荐) -->
    <category> 
      <template v-slot:default="movie">
        <ol>
          <li v-for="(m,index) in movie.movie" :key="index">{{m}}</li>
        </ol>
	  </template>
    </category>
  </div>
</template>

<script>
import category from "./components/category.vue";

export default {
  name: "app",
  components: { category },
};
</script>


// 子组件
<template>
  <div class="category">
    <slot :movie="movies"></slot>
  </div>
</template>

<script>
export default {
  data() {
    return {
      foods: ["火锅", "烧烤", "牛排"],
      movies: ["《教父》", "《绿皮车》", "《海边的曼彻斯特》", "冰雪奇缘"],
      games: ["原神", "明日方舟"],
    };
  },
  props:['title']
}
</script>
```



# 4、Vuex

专门在Vue中实现集中式状态（数据）管理的一个Vue插件，对vue应用中多个组件的共享状态进行集中式的管理（读/写），也是一种组件间通信方式，且适用于任何组件间通信

- 什么时候使用Vuex
  - 多个组件依赖于同一状态
  - 来自不同组件的行为需要变更同一状态



## Vuex的安装与环境搭建

### vuex安装

​	vue2中只能用vuex的3版本，vue3只能用vuex的4版本，在安装时一定要指定版本，不然默认安装最新版本vuex4，在vue2中无法使用

```apl
npm i vuex@3
```

### vuex环境搭建

创建文件 src/store/index.js

```js
// 该文件用于创建Vuex中最为核心的store

// 引入核心库
import Vue from 'vue'
// 引入Vuex
import Vuex from 'vuex'

Vue.use(Vuex)

//准备actions--用于响应组件中的动作
const actions = {}
// 准备mutations--用于操作数据
const mutations = {}
// 准备state--用于存储数据
const state = {}
// 准备getters--用于修改state中的数据
const getters = {}

// 创建并暴露store
export default new Vuex.Store({
    actions,
    mutations,
    state,
    getters
});
```

在main.js文件中

```js
import Vue from 'vue'
import App from './app.vue'

/* 本来应该在这个文件下用下列语句导入和引用vuex，但由于预解析，所有import
语句会被优先执行，这就导致了Vue并没有先Vue.use(Vuex)，出现错误，因此把
下列语句都放入到store.js文件下即可解决
import Vuex from 'vuex'
Vue.use(Vuex)
*/

// 引入vuex
import store from './store/index'

Vue.config.productionTip = false
new Vue({
  render: h => h(App),
  // store:store ES6简写
  store
}).$mount('#app')
```



## Vuex的使用

- 使用时时刻需要注意的是vuex在vue上，且vuex的三个部分实际上是由store管理的，因此可以通过 ` this.$store ` 来获取所需要的函数和数据
- 数据都是存储在State部分，因此想获取放入到vuex中的数据用 `this.$store.state.xxx` 

- 在完整的业务逻辑中，只需要调用`this.$store.dispatch()`进入Actions部分，但当逻辑简单，没有网络请求或其他业务逻辑的时候，可以直接通过`this.$store.commit()`直接进入Mutations部分

- getters用于把state中的数据进行处理加工

<img src="D:\Typora\photos\vuex.png" alt="vuex" style="zoom:50%;" />

index.js


```js
// 该文件用于创建Vuex中最为核心的store

// 引入Vuex
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)
//准备actions--用于响应组件中的动作
const actions = {
    // context是一个mini版的store，只保留了部分store中的内容，便于后续mutations处理
    /*
    add(context,value){
        // 调用commit函数，交由mutations处理数据
        // 这里第一个参数建议大写
        context.commit('ADD',value);
    },
    sub(context, value){
        context.commit('SUB', value);
    },
    */
    addOdd(context, value){
        if (!(context.state.sum % 2))
        context.commit('ADDODD', value);
    },
    addWait(context, value){
        setTimeout(() => {
            context.commit('ADD', value);
        },1000)
    },
}
// 准备mutations--用于操作数据
const mutations = {
    // 第一个参数state是对State中所有数据进行数据劫持后的对象
    ADD(state, value){
        state.sum += value;
    },
    SUB(state, value){
        state.sum -= value;
    },
    ADDODD(state, value){
        state.sum += value;
    }
}
// 准备state--用于存储数据
const state = {
    // 把数据储存到这里
    sum:1,
}

// 准备getters--用于把state中的数据进行处理加工
// 语法类似计算属性
const getters = {
    handle(state){
        return state.sum * 10 / 2 - 4;
    }
}

// 创建并暴露store
export default new Vuex.Store({
    actions,
    mutations,
    state,
    getters
});

```

组件

```vue
<template>
  <div>
    <h3>和为{{$store.state.sum}}</h3>
    <h3>处理后的值为{{$store.getters.handle}}</h3>
    <select v-model="num">
      <option :value="1">1</option>
      <option :value="2">2</option>
      <option :value="3">3</option>
    </select>
    <button @click="increment">+</button>
    <button @click="decrement">-</button>
    <button @click="incrementodd">偶数加</button>
    <button @click="incrementwait">等会加</button>
  </div>
</template>

<script>
export default {
  data(){
    return {
      num:1
    }
  },
  methods:{
    increment(){
      // 调用this.$store.dispatch进入Vuex的actions部分
      // 第一个参数表示要进入actions的哪个函数，第二个参数是传入的值
      // 由于逻辑结构非常简单，可跳过Actions直接调用commit进入Mutations部分
      // this.$store.dispatch('add', this.num)
      this.$store.commit('ADD',this.num);
    },
    decrement(){
      // this.$store.dispatch('sub', this.num);
      this.$store.commit('SUB',this.num);
    },
    incrementodd(){
      this.$store.dispatch('addOdd', this.num);
    },
    incrementwait(){
      this.$store.dispatch('addWait', this.num)
    },
  }
};
</script>

<style scoped>
button{
  margin-left: 5px;
}
</style>
```

## 4个map映射的使用

由于一直写this.$store.state十分繁琐，可以用计算属性去代替，但在计算属性中依然有很多代码冗余，因此vuex官方提出了mapState和mapGetters简化代码

```js
<h3>和为{{sum}}</h3>
<h3>处理后的值为{{handle}}</h3>
<h3>为了{{purpose}},学习{{thing}}</h3>
// 计算属性
sum(){
    return this.$store.state.sum;
},
purpose(){
    return this.$store.state.purpose;
},
thing(){
    return this.$store.state.thing;
},
handle(){
    return this.$store.getters.thing;
}
```

1、mapState：帮助我们映射state中的数据为计算属性

```js
import {mapState} from 'vuex'
computed:{
    // 借助mapState生成计算属性，从state中读取数据（对象写法）
    // 这种方法可以取别的名字
    ...mapState({sum:'sum',purpose:'purpose',thing:'thing'})
    
    // 借助mapState生成计算属性，从state中读取数据（数组写法）
    // 这种写法要求state存储的名字和计算属性名一致
    ...mapState(['sum','purpose','thing']),
}
```

2、mapGetters：帮助我们映射getters中的数据为计算属性

```js
computed:{
    // 借助mapGetters生成计算属性（对象写法）
    ...mapGetters({handle:'handle'})
    
    // 借助mapGetters生成计算属性（数组写法）
    ...mapGetters(['handle'])
}
```



---------



同理，在method中我们也会重复去调用`this.$store.dispatch`和`this.$store.commit`这两个方法，用以下两个方法分别代替

```js
// 原写法
<button @click="increment">+</button>
<button @click="decrement">-</button>
<button @click="incrementodd">偶数加</button>
<button @click="incrementwait">等会加</button> 

increment(){
    // 由于逻辑结构非常简单，可跳过Actions直接调用commit进入Mutations部分
    // this.$store.dispatch('add', this.num)
    this.$store.commit('ADD',this.num);
},
decrement(){
    // this.$store.dispatch('sub', this.num);
    this.$store.commit('SUB',this.num);
},
incrementodd(){
    this.$store.dispatch('addOdd', this.num);
},
incrementwait(){
    this.$store.dispatch('addWait', this.num)
},
```

3、mapActions：帮助我们生成与actions对话的方法，即取代`this.$store.dispatch(xxx)`

```js
// 在调用绑定事件的时候一定要记得传参！！！！！
<button @click="increment(n)">+</button>
<button @click="decrement(n)">-</button>
<button @click="incrementodd(n)">偶数加</button>
<button @click="incrementwait(n)">等会加</button> 

// mapActions对象写法（推荐）
// ...mapActions({increment:'add', decrement:'sub'}),
...mapActions({incrementodd:'addOdd',incrementwait:'addWait'}),

// mapActions数组写法
// 这种写法需要actions中的函数与这里调用的函数名相同，这里就不改了
// ...mapActions(['add','sub']),
```

4、mapMutations：帮助我们生成与Mutations对话的方法，即取代`this.$store.commit(xxx)`

```js
// mapMutations对象写法（推荐）
...mapMutations({increment:'ADD', decrement:'SUB'}),
    
// mapActions数组写法
// ...mapMutations(['ADD','SUB'])
```



## Vuex模块化

- 在store目录下创建index.js文件，在store目录下创建各模块的index.js文件

![image-20221208161122448](D:\Typora\photos\image-20221208161122448.png)

- 在模块文件中配置

```js
const state = {
    
}

const actions = {

}

const mutations = {

}

const getters = {

}

export default {
    state,
    mutations,
    actions,
    getters
}
```

- 在入口文件配置

```js
import vue from 'vue'
import vuex from 'vuex'

import home from './home/index'

vue.use(vuex)

export default new vuex.Store({
    modules:{
        home,
        
    }
})
```



# 5、Vue-router

vue-router是vue的一个插件库，专门用来实现SPA应用

## 相关理解

SPA是什么

- 单页web应用(SPA)
- 整个应用只有一个完整页面
- 点击页面中的导航链接不会刷新页面
- 数据需要通过ajax请求获取

什么是路由

- 一个路由是一组映射关系（key-value）
- 多个路由，由路由器进行统一管理
- key为路径，value可能是function或component

路由分类

- 后端路由
  - value为function，用于处理客户端提交的请求
  - 工作过程：服务器收到一个请求时，根据请求路径找到匹配的函数来处理请求，返回响应数据
- 前端路由
  - value是component，用于展示页面内容
  - 工作过程：当浏览器路径改变时，对应组件就会显现



## vue-router安装与环境搭建

### vue-router安装

​	vue2中只能用vue-router的3版本，vue3只能用vue-router的4版本，在安装时一定要指定版本，不然默认安装最新版本vue-router4，在vue2环境中无法使用

```apl
npm i vue-router@3
```

### vue-router环境搭建

创建文件 src/router/index.js

```js
// 该文件专门用于创建整个应用的路由器

import VueRouter from 'vue-router'

// 引入所需要的组件
import About from '../components/About'
import Home from '../components/Home'

// 创建并暴露路由器
export default new VueRouter({
    // routers是一个对象数组，对象里面以键值对形式存储着路径对应的组件
    routes:[
        {
            path:'/about',
            component:About
        },
        {
            path:'/home',
            component:Home
        }
    ]
})
```

在main.js文件中

```js
import Vue from 'vue'
import App from './app.vue'

// 引入vue-router
import VueRouter from 'vue-router'
// 应用插件
Vue.use(VueRouter);
// 引入插件
import router from './router/index'

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
  router
}).$mount('#app')
```



## vue-router的使用

- 在router/index.js中配置好路径对应的组件之后，用  `<router-link to="/路径"></router-link> `  取代原本的a标签，用 `<router-view></router-view>`确定组件呈现的位置 
- 在项目中，路由组件通常放在pages文件夹下，普通组件放在components下
- 未被展示的组件默认是被销毁的，需要的时候再去创建挂载
- 每个组件都有自己的$route属性，储存着自己的路由信息
- 整个应用只有一个router，通过组件的$router属性获取

### 单级路由

index.js

```js
// 该文件专门用于创建整个应用的路由器
import VueRouter from 'vue-router'

// 引入所需要的组件
import About from '../pages/About'
import Home from '../pages/Home'

// 创建并暴露路由器
export default new VueRouter({
    // routers是一个对象数组，对象里面以键值对形式存储着路径对应的组件
    routes:[
        {
            path:'/about',
            component:About
        },
        {
            path:'/home',
            component:Home
        }
    ]
})
```

app组件：

```vue
<template>
  <div>
    <banner/>
    <div class="row">
      <div class="col-xs-2 col-xs-offset-2">
        <div class="list-group">
          <!-- 用vue-router自带的标签处理 -->
          <!-- 用router-link取代a标签，实际上在解析后router-link也会最终转换成a标签，to是点击后要跳转的路径，其路径名前必须加 / 
          active-class是该元素被激活时的样子 -->
          <router-link class="list-group-item" active-class="active" to="/about">About</router-link>
          <router-link class="list-group-item" active-class="active" to="/home">Home</router-link>
        </div>
      </div>
      <div class="col-xs-6">
        <div class="panel">
          <div class="panel-body">
            <!-- 指定组件呈现的位置 -->
            <router-view></router-view>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import banner from './components/banner.vue'
export default {
  name: "app",
  components:{banner}
}
</script>
```

### 多级路由

- 多级路由由单级路由嵌套形成
- 在index.js文件下由children配置项编写子路由，子路由路径不能加 / 
- 多级路径在用router-link to 的时候要加上前面的路径，不能光写子路径

index.js

```js
import VueRouter from 'vue-router'

import About from '../pages/About.vue'
import Home from '../pages/Home.vue'
import HomeNews from '../pages/HomeNews.vue'
import HomeMessage from '../pages/HomeMessage'

export default new VueRouter({
    routes: [
        {
			path: '/about',
            component: About,
        },
        {
            path: '/home',
            component: Home,
            children: [{
                    // 子组件不能加 / ！！！！！
                    path: 'news',
                    component: HomeNews
                },
                {
                    path: 'message',
                    component: HomeMessage
                }
            ]
        },
        // 这种写法是错误的！！！
        /* {
            path:'/home/news',
            component:HomeNews
        },
        {
            path:'/home/message',
            component:HomeMessage
        } */
    ]
})
```

home组件

```vue
<template>
  <div>
    <h2>Home组件内容</h2>
    <div>
      <ul class="nav nav-tabs">
        <li>
          <!-- <a class="list-group-item" href="./home-news.html">News</a> -->
          <router-link class="list-group-item" active-class="active" to="/home/news">News</router-link>
        </li>
        <li>
          <router-link class="list-group-item" active-class="active" to="/home/message">Message</router-link>
          <!-- <a class="list-group-item active" href="./home-message.html">Message</a> -->
        </li>
      </ul>
      <router-view></router-view>
    </div>
  </div>
</template>

<script>

export default {
  name: "Home",
};
</script>

```



## 路由配置项

### 传递参数

#### query

- 将参数以查询字符串的形式传递: localhost?id=xxx&title=xxx
- 只需要在`:to`后添加query参数，将需要传递的数据以键值对形式写入
- 只需用 `$route.query.参数名` 获取

传递参数

```vue
<template>
  <div>
    <ul>
      <li v-for="m in messageList" :key="m.id">
        <!-- 跳转路由并携带query参数，to的字符串写法(不推荐) -->
        <!-- 这里一定要加 : ，否则在解析的时候会被看作字符串，加上：才是JS表达式，然后用ES6模板字符串将参数以键值对形式传入，储存在route的query中 -->
        <!-- <router-link :to="`/home/message/detail?id=${m.id}&title=${m.title}`" >
            {{m.title}}
        </router-link>&nbsp;&nbsp; -->

        <!-- 跳转路由并携带query参数，to的对象写法 -->
        <router-link :to="{
          path:'/home/message/detail',
          query:{
            id:m.id,
            title:m.title
          }
        }">{{m.title}}</router-link>
      </li>
    </ul>
    <hr>
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: "HomeMessages",
  data(){
    return{
      messageList:[
        {id:'001',title:'message001'},
        {id:'002',title:'message002'},
        {id:'003',title:'message003'}
      ]
    }
  }
};
</script>
```

接收参数

```js
<div>
  <ul>
    <!-- 查询字符串参数储存在$route.query中 -->
    <!-- 这里是route，千万不要写成router了！！！route和router不是同一个东西 -->
    <li>消息id是：{{$route.query.id}}</li>
	<li>消息title是：{{$route.query.title}}</li>
  </ul>
</div>
```

#### params

- 将参数以params形式传递: localhost/id=xxx/title=xxx

- 需修改index.js路由配置文件，在path中添加占位符 `/:参数名1/:参数名2`
- 在`:to`中添加params参数，将需要传递的数据以键值对形式写入
- 只需用 `$route.params.参数名` 获取

传递参数

```js
routes: [
	{
		path: '/home',
        component: Home,
        children: [
        {
			path: 'news',
			component: HomeNews
		},
		{
			path: 'message',
			component: HomeMessage,
			children:[
			{
				name:'xinxi',
				// 以params风格传参需要占位
				path:'detail/:id/:title',
				component: Detail
			}]
		}]
    }
]
```

```vue
<router-link :to="{
	// params风格不能写path，只能用name
    // path:'/home/message/detail',
	name:'xinxi',
	params:{
		id:m.id,
		title:m.title
	}
}">{{m.title}}</router-link>
```

接收参数

```vue
<ul>
    <!-- 以params风格传参 -->
    <li>消息id是：{{$route.params.id}}</li>
	<li>消息title是：{{$route.params.title}}</li>
</ul>
```

- 路由传递参数（对象写法）path不能结合params使用
- 如果用params方式传递参数，且配置路由时占位了，但并未传递params参数就会导致路径错误，必须在占位符处加一个`?`代表参数可有可无`path:'/serch/:id?'`
- 如果params传递空字符串也会导致路径错误，可以用undefined代替

#### 两者区别

- params 不能用 path 写路径，会导致路径解析不正确；query 用 path 和 name 都可

- 两者中 query 在刷新页面的时候参数不会消失 但[params](https://so.csdn.net/so/search?q=params&spm=1001.2101.3001.7020)在刷新页面的时候参数会消失 可以考虑本地存储解决此问题

- query 传过来的参数会显示到地址栏中，而 params 传过来的参数不会显示到地址栏中。直白的来说 query 相当于 get 请求，而 params 相当于 post 请求

### 命名路由-name

用于简化路由的跳转

```js
export default new VueRouter({
    routes: [
        {
            path: '/about',
            component: About,
        },
        {
            path: '/home',
            component: Home,
            children: [{
                	// 命名路由
                	name:'xinwen'
                    path: 'news',
                    component: HomeNews
                },
                {
                    path: 'message',
                    component: HomeMessage,
                    children:[
                        {
                            path:'detail',
                            component: Detail
                        }
                    ]
                }
            ]
        },
    ]
})
```

```js
<!-- 简化前，需要写完整的路径 -->
<router-link to="/home/news"><router-link>
    
<!-- 简化后，直接通过名字跳转 -->
<router-link :to="{name:'xinwen'}"><router-link>
<!-- 简化写法配合传递参数 -->
<router-link 
	:to="{
		name:'xinwen',
		query:{
            id:6666,
            title:'你好'
        }
	}">跳转<router-link>
```

### props

为了简化使用时一直写$route.query或者$route.params，可以用props配置项来简化

- 一定要记得在组件中用props接收再使用

- props的第一种写法：值为对象

  该对象中所有键值都会以props形式传递给组件（不推荐）

- props的第二种写法：值为布尔值
  若布尔值为真，把该路由组件收到的params参数，以props形式传递给组件
- props的第三种写法：值为函数
  函数参数为route，可对其解构赋值直接拿到query或params，需返回一个对象

```js
export default new VueRouter({
  routes: [{
	path: '/about',
	component: About,
  },
  {
	path: '/home',
	component: Home,
	children: [{
      path: 'news',
	  component: HomeNews
	},
	{
	  path: 'message',
	  component: HomeMessage,
	  children: [{
		// 命名路由
		name: 'xinxi',
		// 以query风格传参
		path:'detail',

		// 以params风格传参
		// path: 'detail/:id/:title',
		component: Detail,

			// props的第一种写法：值为对象
			// 该对象中所有键值都会以props形式传递给组件（不推荐）
			// props:{id:1, title:'hello'}

			// props的第二种写法：值为布尔值
		// 若布尔值为真，把该路由组件收到的params参数，以props形式传递给组件
			// props: true,

			// props的第三种写法：值为函数
	// 函数参数为route，可对其解构赋值直接拿到query或params，需返回一个对象
			/* props($route){
				return {id: $route.query.id, title: $route.query.title}
			} */
          	// ES6 对象解构
 			props({query}){
				return {id: query.id, title: query.title}
			}
		}]
	  }
	]
	},
  ]
})
```

detail组件

```vue
<template>
  <div>
    <ul>
        <!-- 用props简化之后 -->
        <li>消息id是：{{id}}</li>
        <li>消息title是：{{title}}</li>
    </ul>
  </div>
</template>

<script>
export default {
    name:'Detail',
    // 接受props配置项传递来的数据
    props:['id','title']
}
</script>

<style>

</style>
```

### replace

- `<router-link>`的replace属性用于禁止路由跳转时操作浏览器历史记录的模式
- 浏览器的历史记录有两种写入方式，分别为push和replace，push是追加历史记录（栈），replace是替换当前记录（每次入栈前都把栈顶元素出栈），默认为push

```vue
<router-link replace ...></router-link>
```

### redirect

- 路由重定向
- 默认情况我们希望进入网站就渲染首页的内容，但在一般的实现中默认没有显示首页组件，必须由用户跳转才能进入首页，要想让路径默认跳转到首页就需要重定向

```js
{
	path:'/',
    redirect: 'home',
}
```



## 编程式路由导航

- 路由跳转有两种方式，一种是声明式路由导航router-link，另一种是编程式路由导航push | replace

- 不借助`<router-link></router-link>`也能实现路由跳转，让路由跳转更加灵活（router-link只能被转换成a标签，利用编程式路由导航即可在任意标签元素完成路由跳转）

- 使用 `$router` 中的API来实现（这里是 router 不是 route ）
- push和replace的区别在于，push是有浏览记录的，能被回退键返回，replace无历史记录，不能被回退
- $router.push()

```vue
<!--下面两种代码是等价的 -->
<router-link :to="{
	path:'/home/messages',
    query:{
		id: m.id,
        title: m.title
	}
}"></router-link>

<a @click="getmessage(m)"></a>
methods:{
	getmessage(){
		this.$router.push({
			path:'/home/messages',
    		query:{
				id: m.id,
        		title: m.title
			}
		})
	}
}
```

- $router.replace()

```vue
<!--下面两种代码是等价的 -->
<router-link replace :to="{
	path:'/home/messages',
    query:{
		id: m.id,
        title: m.title
	}
}"></router-link>

<a @click="getmessage(m)"></a>
methods:{
	getmessage(){
		this.$router.replace({
			path:'/home/messages',
    		query:{
				id: m.id,
        		title: m.title
			}
		})
	}
}
```

```vue
<!-- 父组件 -->
<template>
  <div>
    <ul>
      <li v-for="m in messageList" :key="m.id">
        <router-link
          :to="{
            //path:'/home/message/detail',
            name: 'xinxi',
            query: {
              id: m.id,
              title: m.title,
            },
          }">{{ m.title }}</router-link>

        <!-- 使用编程式路由导航 -->
        <button @click="getDetailPush(m)">push查看</button>
        <button @click="getDetailReplace(m)">push查看</button>
      </li>
    </ul>
    <hr />
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: "HomeMessages",
  data() {
    return {
      messageList: [
        { id: "001", title: "message001" },
        { id: "002", title: "message002" },
        { id: "003", title: "message003" },
      ],
    };
  },
  methods: {
    getDetailPush(m) {
      this.$router.push({
        name: "xinxi",
        query: {
          id: m.id,
          title: m.title,
        },
      });
    },
    getDetailReplace(m) {
      this.$router.replace({
        name: "xinxi",
        query: {
          id: m.id,
          title: m.title,
        },
      });
    },
  },
};
</script>


<!-- 子组件 -->
<template>
  <div>
    <ul>
        <!-- 用props简化之后 -->
        <li>消息id是：{{id}}</li>
        <li>消息title是：{{title}}</li>
    </ul>
  </div>
</template>

<script>
export default {
    name:'Detail',
    // 接受props配置项传递来的数据
    props:['id','title']
}
</script>
```

- $router.back、forward、go()
  - back() 实现路由后退功能，类似浏览器的后退键
  - forward() 实现路由前进功能，类似浏览器的前进键
  - go(n)  n为正数时表示前进n步，n为负数时表示后退n步 

```vue
<button @click="back">后退</button>
<button @click="forward">前进</button>
<button @click="go">前进/后退2步</button>

<script>
export default {
  name: "Home",
  methods: {
    back() {
      this.$router.back();
    },
    forward() {
      this.$router.forward();
    },
    go(){
      this.$router.go(2);
    }
  },
};
</script>
```

## 缓存路由组件

- 前面已经提到了，未被展示的组件默认是被销毁的，需要的时候再去创建挂载。若想让不展示的路由组件保持挂载，不被销毁需要用到`<keep-alive></keep-alive>`标签
- 可以添加include配置项指定缓存特定的组件，不添加则缓存 `<router-view></router-view>` 中会展示的所有组件
- include后加的是组件名，即在组件中用 `name:'xxx'` 配置的名字

```vue
<keep-alive include="News">
    <router-view></router-view>
</keep-alive>

<!-- 如果要缓存多个组件，写成数组的形式 -->
<keep-alive :include="['News', 'Message']">
    <router-view></router-view>
</keep-alive>
```



## 新生命周期钩子

- 这两个声明周期钩子是Vue路由中特有的

- activated  路由组件被激活时触发，deactivated  路由组件未激活时触发

- 激活和失活都是指被创建后触发/未触发，因此必须结合`keep-alive`使用，否则无效

```vue
<template>
  <div>
    <h3 :style="{opacity}">欢迎学习路由！！！</h3>
    <ul>
      <li>new001 <input type="text"></li>
      <li>new002 <input type="text"></li>
      <li>new003 <input type="text"></li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "HomeNews",
  data(){
    return {
      opacity:1,
    }
  },
  activated(){
    this.timer = setInterval(() => {
      console.log('组件被激活了');
      this.opacity -= 0.01
      if (this.opacity < 0){
        this.opacity = 1;
      }
    },20)
  },
  deactivated(){
    console.log('组件失活了');
    clearInterval(this.timer);
  }
};
</script>
```

## 路由守卫

- 路由守卫可以在切换路由的时候给予判断，对路由进行权限控制

### 全局路由守卫

index.js文件

```js
import VueRouter from 'vue-router'

import About from '../pages/About.vue'
import Home from '../pages/Home.vue'
import HomeNews from '../pages/HomeNews.vue'
import HomeMessage from '../pages/HomeMessage'
import Detail from '../pages/Detail'

const router =  new VueRouter({
    routes: [{
		name:'xiangguan',
        path: '/about',
        component: About,
        meta:{title:'相关'}
    },
	{
		name:'zhuye',
        path: '/home',
        component: Home,
        meta:{title:'主页'},
        children: [{
            name:'xinwen',
            path: 'news',
            component: HomeNews,
            // 设置是否需要鉴权
            meta:{isAuth:true, title:'新闻'}
		},
		{
			name:'xiaoxi',
            path: 'message',
            component: HomeMessage,
            // 设置是否需要鉴权
            meta:{isAuth:true, title:'信息'},
            children: [{
                name: 'xinxi',
                path:'detail',
                component: Detail,
                props({query}){
                    return {id: query.id, title: query.title}
                },
                meta:{title:'详情'}
			}]
		}]
	},]
})

// 全局前置路由守卫————初始化的时候被调用、每次路由切换之前被调用
router.beforeEach((to, from, next) => {
    // from 和 to 我们一般只会用到其中的 path 和 name
    // 参数顺序是不能被改变的，必须先to再from，注意参数位置！！
    /* if (to.path == '/home/news' || to.path == '/home/message'){
        if (localStorage.getItem('school') === 'cy'){
            next();
        }else{
            alert('无权限！！');
        }
    }else{
        next();
    } */

    // 还可以用meta配置是否鉴权以简化判断
    if (to.meta.isAuth){
        if (localStorage.getItem('school') === 'cy'){
            // document.title = to.meta.title || '首页';
            next();
        }else{
            alert('无权限！！');
        }
    }else{
        // document.title = to.meta.title || '首页';
        next();
    }
})

// 全局后置路由守卫————初始化的时候被调用、每次路由切换之后被调用
// 没有next，很少使用
// 在后置路由守卫中实现切换路由修改标题
router.afterEach((to,from) => {
    document.title = to.meta.title;
})

export default router
```

### 独享路由守卫

- 独享路由守卫通过配置项beforeEnter写在每个路由内部
- 独享路由守卫只有一个，没有afterEnter

index.js文件

```js
import VueRouter from 'vue-router'

import About from '../pages/About.vue'
import Home from '../pages/Home.vue'
import HomeNews from '../pages/HomeNews.vue'
import HomeMessage from '../pages/HomeMessage'
import Detail from '../pages/Detail'

const router =  new VueRouter({
    routes: [{
		name:'xiangguan',
        path: '/about',
        component: About,
        meta:{title:'相关'}
    },
	{
		name:'zhuye',
        path: '/home',
        component: Home,
        meta:{title:'主页'},
        children: [{
            name:'xinwen',
            path: 'news',
            component: HomeNews,
            meta:{isAuth:true, title:'新闻'},
            
            // 设置独享路由守卫
            // 独享路由守卫只有前置守卫
            // 独享路由守卫可以配合全局后置守卫一起使用，两者并不冲突
            beforeEnter(to, from, next){
                if (to.meta.isAuth){
                    if (localStorage.getItem('school') == 'bb'){
                        next();
                    }else{
                        alert("无权限！！！")
                    }
                }
            }
		},
		{
			name:'xiaoxi',
            path: 'message',
            component: HomeMessage,
            meta:{isAuth:true, title:'信息'},
            children: [{
                name: 'xinxi',
                path:'detail',
                component: Detail,
                props({query}){
                    return {id: query.id, title: query.title}
                },
                meta:{title:'详情'}
			}]
		}]
	},]
})


// 全局后置路由守卫
router.afterEach((to,from) => {
    document.title = to.meta.title;
})


export default router
```

### 组件内路由

about组件

```vue
<template>
  <h2>我是About的内容</h2>
</template>

<script>
export default {
  name:'About',

  // 组件内进入守卫
  // 通过路由规则，进入该组件时被触发
  beforeRouteEnter (to, from, next) {
    if (to.meta.isAuth){
      if (localStorage.getItem('id') == 'cy'){
        next();
      }else{
        alert('无权限！！')
      }
    }
  },

  // 组件内离开守卫
  // 通过路由规则，离开该组件时被触发
  // 区别全局后置守卫，一个是next执行完之后的动作，一个是启用next的动作
  beforeRouteLeave (to, from, next) {
    if (to.meta.isAuth){
      if (localStorage.getItem('id') == 'cy'){
        next();
      }else{
        alert('无权限！！')
      }
    }
  }
}
</script>
```

## 路由器两种工作模式

### hash模式

- 对于一个url来说，#及后面的内容就是hash值
- vue-route默认的模式是hash模式
- hash值不会包含在HTTP请求中，即hash值不会带给服务器
- hash模式的优缺点
  - 地址值永远带着#，不美观
  - 若以后将地址通过第三方手机app分享，若app校验严格，则地址会被标记为不合法
  - 兼容性较好

### history模式

- #即后面的内容都不存在，地址干净美观

- 通过路由转换改变地址会传递给服务器，由于并没有发送http请求，会在刷新之后404报错
- 应用部署上线时需要后端人员支持，解决刷新页面服务端404的问题（npm搜索connect-history-api-fallback）



7.1 移动端常用 UI 组件库 

1. Vant https://youzan.github.io/vant 

	2. Cube UI https://didi.github.io/cube-ui 

	3. Mint UI http://mint-ui.github.io 



7.2 PC 端常用 UI 组件库 

1. Element UI https://element.eleme.cn 
2. IView UI https://www.iviewui.co
