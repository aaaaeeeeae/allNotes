# vue3

## ref 全家桶

### ref 、Ref、isRef

- 作用: 定义一个响应式的数据
- 语法: ```const xxx:Ref<T> = ref(initValue)``` 
  - 创建一个包含响应式数据的<strong style="color:#DD5145">引用对象（reference对象，简称ref对象）</strong>。
  - JS中操作数据： ```xxx.value```
  - 模板中读取数据: 不需要.value，直接：```<div>{{xxx}}</div>```

```vue
<template>
  <div>{{ man }}</div>
  <div>{{ man2 }}</div>
  <button @click="fn">点击修改</button>
</template>
<script setup lang="ts">
    import { ref,isRef } from 'vue'
    import type { Ref } from 'vue'
    type Man = {
      name: string
    }
    const man = ref({ name: 'zhangsan' })
    // 类型约束需使用Ref
    const man2: Ref<Man> = ref({ name: 'lisi' })

    // 判断是否是ref
    console.log(isRef(man)); // true

    const fn = ()=>{
      man.value.name = '张三'
    }
</script>
```

- 备注：
  - 接收的数据可以是：基本类型、也可以是对象类型。
  - 基本类型的数据：响应式依然是靠``Object.defineProperty()``的```get```与```set```完成的。
  - 对象类型的数据：内部求助了Vue3.0中的一个新函数—— ```reactive```函数。

### shallowRef

- shallowRef是做浅层次响应式的，只能监视到 value 层
- 但 <strong style="color:#DD5145">ref 和 shallowRef 不能混着写</strong>，否则会影响 shallowRef，造成视图的更新

```vue
<template>
  <div>{{ man }}</div>
  <div>{{ man3 }}</div>
  <button @click="fn2">shallowRef修改</button>
  <hr>
  <button @click="fn3">同时修改</button>
</template>
<script setup lang="ts">
    import { ref,shallowRef } from 'vue'
    const man = ref({ name: 'zhangsan' })
    // shallowRef是做浅层次响应式的
    const man3 = shallowRef({ name: 'wangwu' })
    const fn2 = () => {
      // 不是响应式
      // man3.value.name = '王五' 

      // 这样才是响应式
      man3.value = { name: '王五' }
    }
    const fn3 = () => {
      man.value.name = '张三'
      man3.value.name = '王五'
    }
</script>
```

### triggerRef

- triggerRef 会强制更新视图
- 如果和 shallowRef 一起使用就会更新视图了

```vue
<template>
  <div>{{ man }}</div>
  <div>{{ man3 }}</div>
  <button @click="fn4">强制更新</button>
</template>
<script setup lang="ts">
    import { ref, triggerRef } from 'vue'

    const man = ref({ name: 'zhangsan' })

    const man3 = shallowRef({ name: 'wangwu' })

    const fn4 = () => {
      triggerRef(man3)
      man3.value.name = '王五'  
    }
</script>
```

### customerRef(待补充)

## reactive 全家桶

### reactive

- 作用: 定义一个<strong style="color:#DD5145">对象类型</strong>的响应式数据（基本类型不要用它，用```ref```函数）

- 语法：```const 代理对象= reactive(源对象)```

  接收一个对象（或数组），返回一个<strong style="color:#DD5145">代理对象（Proxy的实例对象，简称proxy对象）</strong>

- reactive定义的响应式数据是 "深层次的"。

- 内部基于 ES6 的 Proxy 实现，通过代理对象操作源对象内部数据进行操作

```vue
<template>
    <div>{{ man }}</div>
</template>

<script setup lang="ts">
    import { reactive } from "vue"
    type Man = {
        name: string,
        sex: string
    }
    const man = reactive<Man>({
        name: '张三',
        sex: '男'
    })
</script>
```

- 需要注意的是，在vue3中用reactive定义的数组对其直接赋值会丢失响应式（vue2不会）

```vue
<template>
    <ul>
        <li v-for="num in list">{{ num }}</li>
    </ul>
    <hr>
    <ul>
        <li v-for="num in list2.arr">{{ num }}</li>
    </ul>
    <button @click="fn">点击重新赋值</button>
</template>

<script setup lang="ts">
    import { reactive } from "vue"

    let list = reactive<number[]>([])
    let arr = reactive<number[]>([1,2,3])
    let list2 = reactive<{arr:number[]}>({arr:[]})

    const fn = () => {
        // 会发现值其实变了，但视图没更新
        // list = arr
        console.log(list);

        // 解决方法：调用数组方法
        list.push(...arr)
        // 或者修改reactive结构
        list2.arr = arr
    }
</script>
```

- 与 ref 的区别
  - 从定义数据角度对比：
    -  ref一般用来定义：<strong style="color:#DD5145">基本类型数据</strong>
    -  reactive用来定义：<strong style="color:#DD5145">引用数据类型</strong>（包括set、map、weakSet、weakMap）
    -  备注：ref 也可以用来定义<strong style="color:#DD5145">引用数据</strong>, 它内部会自动通过```reactive```转为<strong style="color:#DD5145">代理对象</strong>
  - 从原理角度对比：
    -  ref通过``Object.defineProperty()``的```get```与```set```来实现响应式（数据劫持）。
    -  reactive通过使用<strong style="color:#DD5145">Proxy</strong>来实现响应式（数据劫持）, 并通过<strong style="color:#DD5145">Reflect</strong>操作<strong style="color:orange">源对象</strong>内部的数据。
  - 从使用角度对比：
    -  ref 定义的数据：操作数据<strong style="color:#DD5145">需要</strong>```.value```，读取数据时模板中直接读取<strong style="color:#DD5145">不需要</strong>```.value```。
    -  reactive 定义的数据：操作数据与读取数据：<strong style="color:#DD5145">均不需要</strong>```.value```。

### shallowReactive

- 只能读得到第一个点后的，类似于shallowRef 不赘述了

## readonly 与 shallowReadonly

- readonly: 让一个响应式数据变为只读的（深只读）。

```vue
<script setup lang="ts">
    import { reactive,readonly } from "vue"
    type Man = {
        name: string,
        sex: string
    }
    const man = reactive<Man>({
        name: '张三',
        sex: '男'
    })
    const ro = readonly(man)
    console.log(ro);
    // ro.name = '123'  // warning
    
    // 当然如果给原对象修改是可以的
	man.name = '123'
</script>
```

- shallowReadonly：让一个响应式数据变为只读的（浅只读）。
- 应用场景: 不希望数据被修改时。

## to 系列全家桶

### toRef

- 作用：创建一个 ref 对象
- 语法：```const xxx = toRef(obj,key)```
- 应用:   要将响应式对象中的某个属性单独提供给外部使用时
- 需要注意的是，即便使用 toRef 将对象中的某个属性转换成了 ref ，如果<strong style="color:#DD5145">原对象不是响应式的话，用 toRef 生成的新属性也不是响应式的</strong>，是无法更新视图的

```vue
<template>
    <div>
        {{ man.name }}+{{ man.age }}
    </div>
    <div>{{ name }}</div>
    <div>{{ age }}</div>
    <button @click="fn">修改</button><br>
    <button @click="fn2">toRef修改</button>
</template>

<script setup lang="ts">
    import { toRef,reactive } from "vue"

    const man = { name: 'zhangsan', age: 18 }
    // 直接修改man中的属性是不会更新视图的，因为man不是响应式对象
    const fn = () => {
        man.name = "张三"
        console.log(man);
    }

    let name = toRef(man, 'name')

    const manReactive  = reactive(man)
    let age = toRef(manReactive, 'age')

    const fn2 = () => {
        // toref也只能修改响应式对象的值，非响应式的是不会更新视图的
        // 依然无变化
        name.value = "张三"
        console.log(name);

        // 原对象是响应式的才更新了视图
        age.value = 20
    }
</script>
```

### toRefs

- ```toRefs``` 与```toRef```功能一致，但可以批量创建多个 ref 对象，语法：```toRefs(person)```

- toRefs可以做一些解构的操作（不使用toRefs的解构会丢失响应式，视图不更新）
- 和toref一样，需保证原对象是响应式的到页面上才会更新

```js
// 手写torefs的源码
const myToRefs = <T extends object>(obj: T) => {
    const map: any = {}

    for (let key in obj) {
        map[key] = toRef(obj, key)
    }

    return map
}
```

```vue
<template>
    <div>{{ jiegou_name }}</div>
    <div>{{ jiegou_age }}</div>
    <button @click="fn3">加了toRefs的修改</button>
    <hr>
    <div>{{ jiegou_name2 }}</div>
    <div>{{ jiegou_age2 }}</div>
    <button @click="fn4">没加toRefs的修改</button>

</template>

<script setup lang="ts">
    import { reactive,toRefs } from "vue"

    const manReactive = reactive({ name: 'zhangsan', age: 18 })

    const { name:jiegou_name, age:jiegou_age } = toRefs(manReactive)

    const fn3 = () => {
        jiegou_name.value = '张三'
        console.log(jiegou_name);
    }

    // 如果不加 toRefs 就会发现解构出来的东西丢失了响应式
    let { name:jiegou_name2, age:jiegou_age2 } = man
    const fn4 = () => {
        jiegou_age2 = 20
        console.log(jiegou_age2); // 20，页面上是 18
    }
</script>
```

### toRaw

- 作用：将一个由 ```reactive``` 生成的 <strong style="color:orange">响应式对象</strong> 转为 <strong style="color:orange">普通对象</strong>
- 使用场景：用于读取响应式对象对应的普通对象，对这个普通对象的所有操作都不会引起页面更新。

## 计算属性和属性监听

### computed

- 和vue2差不多，但写法上略微有点差别

```js
// 计算属性——简写
let fullName = computed(()=>{
    return person.firstName + '-' + person.lastName
})

// 计算属性——完整
let fullName = computed({
    get(){
        return person.firstName + '-' + person.lastName
    },
    set(value){
        const nameArr = value.split('-')
        person.firstName = nameArr[0]
        person.lastName = nameArr[1]
    }
})
```

### watch

- 使用 watch 监听 ref 和 reactive 定义的对象

```vue
<template>
    <div>
        <input type="text" v-model="message"><br>
        <input type="text" v-model="message2">
    </div>
</template>

<script setup lang="ts">
    import { ref, watch } from "vue"

    let message = ref('message')
    let message2 = ref('message2')

    // 监听单个
    watch(message, (newVal, oldVal) => {
        console.log(newVal, oldVal);
    })

    // 多个
    watch([message, message2], (newVal, oldVal) => {
        console.log(newVal, oldVal);
    })
</script>
```

- 但监听 ref 和 reactive 时有所不同
  - 监听ref定义的对象时<strong style="color:#DD5145">需要开启深度监听</strong>，且打印出的新旧值是一样的
  - 监听reactive定义的对象<strong style="color:#DD5145">自动开启了深度监听</strong>，且打印出的新旧值也是一样的

```vue
<template>
    <div>
        <input type="text" v-model="obj.name"><br>
        <input type="text" v-model="obj.son.name">
    </div>
</template>

<script setup lang="ts">
    import { ref, watch,reactive } from "vue"

    // 监听ref定义的对象
    interface Person {
        name: string,
        age: number,
        son?: Person
    }
    let obj = reactive<Person>({
        name: '张三',
        age: 25,
        son: {
            name: '张四',
            age: 3,
        }
    })
    watch(obj, (newVal, oldVal) => {
        // 会发现没有监听到，需要开启深度监听
        // 会发现新值和旧值是一样的
        console.log(newVal, oldVal);
    },{
        deep:true
    })
    // 监听reactive定义的对象会自动开启深度监听

</script>
```

- 想要监听对象中的某个属性时<strong style="color:#DD5145">需要写成函数返回值的形式</strong>

```js
watch(()=>obj.son?.age,()=>{
    
})
```

- 一些配置选项
  - deep
  - immediate
  - flush（一般在watchEffect里用得多）
    - pre 组件更新之前
    - sync 同步
    - post 组件更新后

### watchEffect

- 不用指明监视哪个属性，监视的回调中用到哪个属性，那就监视哪个属性
- 可以取消监听

```vue
<template>
    <div>
        <input type="text" v-model="message"><br>
        <input type="text" v-model="message2">
        <button @click="stopWatch">停止监听</button>
    </div>
</template>

<script setup lang="ts">
import { ref, watch,reactive,watchEffect } from "vue"

let message = ref('message')
let message2 = ref('message2')

const stop = watchEffect((oninvalidate)=>{
    const m = message.value
    const m2 = message2.value
    console.log(m,'message的值改了')
    console.log(m2,'message2的值改了')
    oninvalidate(()=>{
        console.log('before')
    })
}, {
    // 调试工具
    onTrigger(e) {
        debugger
    }
})

// 停止监听
const stopWatch = ()=>stop()

</script>
```

## 生命周期

- 在setup语法糖模式是没有beforeCreate created这两个生命周期的，用setup去代替

- vue3也提供了生命周期钩子，与Vue2中钩子对应关系如下：

  - `beforeCreate`===>`setup()`
  - `created`=======>`setup()`
  - `beforeMount` ===>`onBeforeMount`
  - `mounted`=======>`onMounted`
  - `beforeUpdate`===>`onBeforeUpdate`
  - `updated` =======>`onUpdated`
  - `beforeDestroyed` ==>`onBeforeUnmount`
  - `destroyed` =====>`onUnmounted`

- 这俩是调试用，只在开发模式下生效

  - onRenderTracked

    在组件实例的任何属性或状态发生更改时被调用。它接受两个参数：target 和 key

  - onRenderTriggered

    在组件实例的渲染函数被调用时被调用。它接受一个参数：target

## 组件通信

### defineProps

- 相当于vue2中的props，适用于<strong style="color:#DD5145">父给子传递参数</strong>
- 有两种写法

```vue
// 父组件
<template>
	<son :title="message" :arr="[1, 2, 3, 4]"></son>
</template>
<script setup lang="ts">
	const message = { name: '张三' }
</script>


// 子组件
<template>
    <div>son</div>
    <div>父组件传递过来1：{{ title }}</div>
    <div>父组件传递过来2：{{ arr }}</div>
</template>

<script setup lang="ts">
import { } from "vue"

// 纯组合式写法
// 在type中定义类型
const props = defineProps({
    title: {
        type: Object,
        // 父组件必须传递
        require: true,
        defalt: ''
    },
    arr: {
        type: Array
    }
})

// 在获取使用时必须通过变量.的方式
console.log(props.title);
console.log(props.arr);
</script>
```

```vue
<template>
    <div>son</div>
    <div>父组件传递过来1：{{ title }}</div>
    <div>父组件传递过来2：{{ arr }}</div>
</template>

<script setup lang="ts">
import { } from "vue"

// ts写法
// 在泛型中定义类型
const props2 = defineProps<{
    title: { name: string }
    arr: number[],
}>()
    
// 在获取使用时必须通过变量.的方式
console.log(props2.title);
console.log(props2.arr);
</script>
```

#### withDefaults

- 在上述ts写法中，如果需要赋默认值就需要用到 `withDefaults`

- 支持传入两个参数，第一个参数就是上面定义的defineProps，第二个参数是一个对象，里面分别是需要赋的默认值。需要注意的是<strong style="color:#DD5145">默认值如果是引用类型需要写成函数返回的形式</strong>

```js
const props2 = withDefaults(defineProps<{
    title: { name: string }
    arr: number[],
}>(),{
    // 必须写成函数形式
    title:()=>{return {name:'temp'}},
    arr:()=>[]
})
```

### defineEmits

- 和vue2中的emit差不多，适用于<strong style="color:#DD5145">父给子传递函数</strong>（在子中去触发父的函数）

```vue
// ------------父组件------------
<template>
    <div>father</div>
    <div>子给父传递的值：{{ sonSend }}</div>
    <hr>
    <son @getVal="getProps"></son>
    
</template>

<script setup lang="ts">
import { ref } from "vue"
import son from './components/son.vue'

const sonSend = ref()
const getProps = (val: any) => {
    sonSend.value = val
}
</script>


// ------------子组件------------
<template>
    <div>son</div>
    <hr>
    <button @click="sendToFather">给父组件传值</button>
</template>

<script setup lang="ts">
import { } from "vue"

// 跟vue2中用$emit触发的思路是一样的
const emit = defineEmits(['getVal','get2','get2'])
const sendToFather = () => {
    // 触发的哪一个，传递的参数
    emit('getVal',{name:'zhangsan'})
}
</script>
```

- ts写法

```vue
const emit = defineEmits<{
    (e: "getVal", val:Object): void
    (e: "get2"): void
    (e: "get3"): void
}>()
```

### defineExpose

- 适用于<strong style="color:#DD5145">子组件给父组件传递参数（可传递函数）</strong>
- 类比于props，但思路却像 export 和 ref 的结合，利用 export 向外暴露，通过 ref 获取实例来获取组件进而获取参数

```vue
// 父组件
<template>
    <button @click="fn">传递</button>
    <son ref="son_obj" ></son>
</template>

<script setup lang="ts">
import { ref } from "vue"
import son from './components/son.vue'

const son_obj = ref();
const fn = () => {
    console.log(son_obj.value?.obj)
    son_obj.value?.fn()
}
</script>


// 子组件
<template>
    <div>son</div>
</template>

<script setup lang="ts">
    const fn = ()=>{console.log('传递');}
    defineExpose({
        obj: { sex: '男' },
        fn
    })
</script>
```

### 组件注入-provide/inject

- 适用于父往子或更深子层次传递数据
- 只需要在顶层用 `provide` 去传递，子层就可以通过 `inject` 获取

```vue
// 顶层
<template>
    <div>爷爷</div>
    <label>
        <input type="radio" v-model="colorVal" value="red">
        红色
    </label>
    <label>
        <input type="radio" v-model="colorVal" value="blue">
        蓝色
    </label>
    <label>
        <input type="radio" v-model="colorVal" value="orange">
        橙色
    </label>
    <div class="box"></div>
    <parent></parent>
</template>

<script setup lang="ts">
import { ref, provide,readonly } from "vue"
import parent from './components/parent.vue'
const colorVal = ref<string>('red')

provide('color', colorVal)

</script>

<style scoped>
.box {
    width: 100px;
    height: 100px;
    background-color: v-bind(colorVal);
    border: 1px solid silver;
}
</style>


// 子类一层
<template>
    <div>父亲</div>
    <div class="box"></div>
    <min />
</template>

<script setup lang="ts">
import { inject, Ref } from "vue"
import min from './min.vue'
const color = inject<Ref<string>>('color')

</script>

<style scoped>
.box {
    width: 100px;
    height: 100px;
    border: 1px solid silver;
    /* 在vue3中连css都可以绑定了 */
    background-color: v-bind(color);
}
</style>


// 子类二层
<template>
    <div>儿子</div>
    <div class="box"></div>
    <button @click="changeYellow">修改颜色为yellow</button>
</template>
  
<script setup lang="ts">
import { inject, Ref, ref } from "vue"
    
const color = inject<Ref<string>>('color')
</script>
  
<style scoped>
.box {
    width: 100px;
    height: 100px;
    background-color: v-bind(color);
    border: 1px solid silver;
}
</style>
```

- 子层可以修改 `inject` 获得的值

```js
import { inject, Ref, ref } from "vue"

// 由于inject接受到的值可能不存在，就会返回undefined，所以下面用color.value会报错
// 有两种解决方案，一是加！表示该值一定存在，二是为color添加一个默认值
const color = inject<Ref<string>>('color')!
// const color = inject<Ref<string>>('color', ref('red'))
      
const changeYellow = () => {
    color.value = 'yellow'
}
```

- 如果不想子类去修改获得的值，只需要在顶层添加 `readOnly`

```vue
provide('color', readonly(colorVal))
```

- 兄弟组件通信

  - 可以通过父组件作为桥梁

  - 第三方库Mitt

    



## 组件相关

### 递归组件

```vue
// App.vue
<template>
    <div></div>
    <Tree :data="data"></Tree>
</template>

<script setup lang="ts">
import { reactive } from "vue"
import Tree from './components/Tree.vue'
export interface TreeType {
    name: string,
    checked: boolean,
    childen?: TreeType[]
}
const data = reactive<TreeType[]>([
    {
        name: '1',
        checked: false,
        childen: [{
            name: '1-1',
            checked: false
        }]
    }, {
        name: '2',
        checked: false
    }, {
        name: "3",
        checked: false,
        childen: [{
            name: '3-1',
            checked: false,
            childen: [{
                name: '3-1-1',
                checked: false
            },{
                name: '3-1-2',
                checked: false
            }]
        }]
    }

])
</script>


// Tree.vue
<template>
    <div v-for="item in data">
        <!-- 需要注意的是写递归组件用事件委托时会因为事件冒泡多次触发，需要用.stop去阻止 -->
        <input type="checkbox" v-model="item.checked"><span>{{ item.name }}</span>
        <!-- 这里直接写要递归组件的名字就行 -->
        <Tree v-if="item?.childen?.length" :data="item?.childen"></Tree>
    </div>
</template>

<script setup lang="ts">
import { } from "vue"
import { TreeType } from '../main'
const props = defineProps<{ data: TreeType[] }>()
console.log(props.data);
</script>

<style scoped></style>
```

### 动态组件-component

- 通过`<component :is="xxx"></component>` 来动态展示组件

```vue
// 写法一
<template>
  <div>
    <div v-for="(item,index) in allComponents" :key="index">
      <button @click="fn(index)">{{ item.tips }}</button>
    </div>
    <div style="border: 1px solid silver; width: 100%; height: 300px; overflow: hidden; margin-top: 100px;" >
      <component :is="componentId"></component>
    </div>
  </div>
</template>

<script setup lang="ts">
import {ref,reactive} from "vue"
import son from './components/son.vue'
import tree from './components/Tree.vue'
import hello from './components/HelloWorld.vue'

const componentId = ref<any>(son)

const allComponents = reactive([
  {
    tips:'点击展示son',
    com:son
  },
  {
    tips:'点击展示Tree',
    com:tree
  },
  {
    tips:'点击展示hello',
    com:hello
  },
])

const fn = index =>{
  componentId.value = allComponents[index].com
}
</script>
```

```vue
// 写法2
<template>
  <div>
    <div v-for="(item, index) in allComponents" :key="index">
      <button @click="fn(index)">{{ item.tips }}</button>
    </div>
    <div style="border: 1px solid silver; width: 100%; height: 300px; overflow: hidden; margin-top: 100px;">
      <component :is="componentId"></component>
    </div>
  </div>
</template>

<script setup lang="ts">
import { reactive, shallowRef } from "vue"
    
const componentId = shallowRef<any>(son)

const allComponents = reactive([
  {
    tips: '点击展示son',
    com: 'son'
  },
  {
    tips: '点击展示Tree',
    com: 'tree'
  },
  {
    tips: '点击展示hello',
    com: 'hello'
  },
])

const fn = (index: any) => {
  componentId.value = allComponents[index].com
}
</script>
<script lang="ts">
import son from './components/son.vue'
import tree from './components/Tree.vue'
import hello from './components/HelloWorld.vue'
export default {
  components: {
    son,
    tree,
    hello
  }
}
</script>
<style scoped></style>
```



- 但是这种方法会带来性能问题，因为它会将组件中的所有属性都进行数据代理，非常消耗性能且会有警告信息

#### markRaw

- 可以通过markRaw()来让组件跳过数据代理
- 用 shallowRef 实现浅层代理

```vue
// 优化后

<template>
  <div>
    <div v-for="(item,index) in allComponents" :key="index">
      <button @click="fn(index)">{{ item.tips }}</button>
    </div>
    <div style="border: 1px solid silver; width: 100%; height: 300px; overflow: hidden; margin-top: 100px;" >
      <component :is="componentId"></component>
    </div>
  </div>
</template>

<script setup lang="ts">
import {reactive,markRaw,shallowRef} from "vue"
import son from './components/son.vue'
import tree from './components/Tree.vue'
import hello from './components/HelloWorld.vue'

// 用 shallowRef 实现浅层代理
const componentId = shallowRef<any>(son)

const allComponents = reactive([
  {
    tips:'点击展示son',
      // 用markRaw()跳过代理
    com:markRaw(son)
  },
  {
    tips:'点击展示Tree',
    com:markRaw(tree)
  },
  {
    tips:'点击展示hello',
    com:markRaw(hello)
  },
])

const fn = (index:any) =>{
  componentId.value = allComponents[index].com
}
</script>
```

### 异步组件-defineAsyncComponent

- 可以使用 `defineAsyncComponent()` 来定义一个异步组件

```js
// 写法一（简易模式）
const SyncVue = defineAsyncComponent(()=>import("./components/HelloWorld.vue"))

// 写法二（完整写法，用得比较少）
const SyncVue = defineAsyncComponent({
    loader: () => import('./components/HelloWorld.vue'),
    // 展示加载组件前的延迟时间，默认200
    delay: 200,
    // 如果提供了一个 timeout 时间限制，并超时了
    // 也会显示这里配置的报错组件，默认值是：Infinity
    timeout: 3000,
    // 加载失败后展示的组件
    errorComponent: {
        template: '<div>Failed to load component</div>'
    },  
})
```

- 异步组件的打包分割
  - 在使用打包工具打包时，没有使用 `defineAsyncComponent` 且没做分包处理时会被打包成一个 js 文件，但使用 `defineAsyncComponent` 后，会生成一个新的文件，能实现按需加载的功能

#### Suspense

- Suspense包裹的异步组件会首先显示fallback内容（通常是loading提示），直到异步组件加载完成为止，然后才会显示异步组件的内容。可以优化体验

- 支持传入两个插槽

  ```vue
  <Suspense>
      // 默认展示这个插槽里的
      <template #default>
          xxx组件
      </template>
  
      // 当默认的在加载时展示这个
      <template #fallback>
          <loading></loading>
      </template>
  </Suspense>
  ```

- 案例

```vue
<template>
    <div>
        展示异步组件
    </div>
    <!-- 支持传入两个插槽 -->
    <Suspense>
        <!-- 默认展示这个插槽里的 -->
        <template #default>
            <SyncVue></SyncVue>
        </template>
        <!-- 当出现延迟时加载这个 -->
        <template #fallback>
            <loading></loading>
        </template>
    </Suspense>
</template>

<script setup lang="ts">
import { defineAsyncComponent } from "vue"
import loading from './components/loading.vue'
const SyncVue = defineAsyncComponent(()=>import("./components/HelloWorld.vue"))

</script>
```

### 传送组件-Teleport

- 将包裹的组件传送到指定位置

  ```vue
  // 将组件传送到 body 中，用 disabled 控制是否传送，true表示失效不传送
  <Teleport :disabled="false" to="body">
      xxx组件
  </Teleport>
  ```

- 案例

```vue
<template>
  <div class="content">
    <!-- 用传送组件将其传送到body中 -->
    <teleport to='body' :disabled="true">
        <!-- 这个组件主要是将一个色块相对于视口居中 -->
        <middle></middle>
    </teleport>
  </div>
    
</template>

<script setup lang="ts">
import {} from "vue"
import middle from './components/middle.vue'
</script>

<style scoped>
.content{
    height: 50vh;
    /* 一旦开启定位，那么子组件middle就不能保持整体居中了，而是会相对于父组件居中 */
    position: relative;
    background-color: orange;
}
</style>
```

### 缓存组件-keep-alive

- 这个在vue2中是一样的，更新一下理解
- 缓存组件可以运用到表单数据中，它可以记录填写过的数据，提升用户体验
- 属性
  - include 支持传入数组或者字符串，表示让传入的组件名缓存
  - exclude 让传入的组件名不缓存
  - max 指定缓存组件的数量（会进行LRU算法保存活跃的）
- 使用keep-alive会新增两个钩子：onActivated、onDeactivated
  - 需要注意的是当开启 `keep-active` 之后用 `v-if` 切换销毁组件时是不会触发 `onUnmounted` 钩子的，而是会去触发 `onDeactivated` 钩子的回调函数
  - 在v-if切换进入组件时只会触发一次 `unMounted` 钩子里的回调函数，想要在每次切换都触发应该使用 `onActivated`（源码是第一次创建，不走缓存，后续走缓存，所以只调用一次）
  - keep-alive中只能缓存一个子节点，否则会报错


### 动画组件-transition

- 跟vue2是一样的，就当复习了
- 会生成六种状态

```css
.xxx-enter-from{
    
}
.xxx-enter-active{
    
}
.xxx-enter-to{
    
}

.xxx-leave-from{
    
}
.xxx-leave-active{
    
}
.xxx-leave-to{
    
}
```

```vue
<template>
  <div>
    <button @click="show = !show">切换</button>
    <transition name="ttran">
        <div class="temp" v-show="show"></div>
    </transition>
  </div>
</template>

<script setup lang="ts">
    import {ref} from "vue"
    const show = ref(false)
</script>

<style scoped>
.temp{
    width:200px;
    height: 200px;
    background-color: #bfa;
}
.ttran-enter-from{
    width: 0px;
    height: 0px;

}
.ttran-enter-active{
    transition: all 1.5s;
}
.ttran-enter-to{
    width:200px;
    height: 200px;
}
.ttran-leave-from{
    width:200px;
    height: 200px;
}
.ttran-leave-active{
    transition: all 2s;
}
.ttran-leave-to{
    width: 0px;
    height: 0px;
}
</style>
```

- 可以用不固定类名的写法

  这种写法的好处在于可以添加一些第三方库，例如Animator

  - enter-from-class
  - enter-active-class
  - leave-to-class
  - ...

```vue
<template>
  <div>
    <button @click="show = !show">切换</button>
    <transition name="ttran" enter-from-class="temp-from" leave-to-class="temp-to">
        <div class="temp" v-show="show"></div>
    </transition>
    
  </div>
</template>

<script setup lang="ts">
    import {ref} from "vue"
    const show = ref(false)
</script>

<style scoped>
.temp{
    width:200px;
    height: 200px;
    background-color: #bfa;
}
.temp-from{
    width: 0px;
    height: 0px;

}
.temp-to{
    width: 0px;
    height: 0px;
}
</style>

```

#### 八个生命周期钩子

- before-enter
- ...

```vue
<template>
    <div>
        <button @click="show = !show">切换</button>
        <transition 
            @before-enter="enterFrom" 
            @enter="enterActive" 
            @after-enter="enterTo" 
            @before-leave="leaveFrom"
            @leave="leaveActive"
            @after-leave="leaveTo"
            @enter-cancelled="enterCancel"
            @leave-canceled="leaveCancel">
            <div class="temp" v-show="show"></div>
        </transition>
    </div>
</template>

<script setup lang="ts">
    import { ref } from "vue"
    const show = ref(false)
    const enterFrom = (e: Element) => {
        console.log("进入前");
    }
    const enterActive = (e: Element, done: Function) => {
        console.log("进入时");
        // 2s后会调用enterTo
        setTimeout(() => {
            done()
        }, 2000)
    }

    const enterTo = (e: Element) => {
        // 完成进入需要用done来指定运行
        console.log("完成进入后");

    }
    const enterCancel = (e: Element) => {
        console.log("执行取消");
    }

    const leaveFrom = (e: Element)=>{
        console.log("离开前");
    }
    const leaveActive = (e: Element,done:Function)=>{
        console.log("离开时");
        setTimeout(() => {
            done()
        }, 2000)
    }
    const leaveTo = (e: Element)=>{
        console.log("完成离开后");
    }
    const leaveCancel = (e: Element) => {
        console.log("执行取消");
    }
</script>

<style scoped>
.temp {
    width: 200px;
    height: 200px;
    background-color: #bfa;
}
```

#### appear

- 初始进入动画，三个状态 from active to
- 可结合第三方库

```vue
<template>
    <div>
        <button @click="show = !show">切换</button>
        <transition name="ttran" appear appear-from-class="from" appear-active-class="active" appear-to-class="to">
            <div class="temp" v-show="show"></div>
        </transition>
    </div>
</template>

<script setup lang="ts">
import { ref } from "vue"
const show = ref(true)
</script>

<style scoped>
.from{
    width: 0px;
    height: 0px;
}
.active{
    transition: all 2s;
}
.to{
    width: 200px;
    height: 200px;
}
</style>
```







## 其他新特性

### ??

- 在 `?? `左边的如果是 <strong style="color:#DD5145">undefined 或者null </strong>，就返回 `??` 右边的

```js
var a = {}
a.childen.length // 报错
a.childen?.length ?? [] // []
```

- 需要与 `||` 区分开，它只处理 `undefined` 和 `null`，例如如果 `??` 左边是 `0` 或者 `false`，依然返回左边的值

