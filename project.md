nprogress进度条使用

当发送请求之后页面顶端会出现进度条

npm i nprogress

由于nprogress在发送请求的时候会被使用，因此要在request.js文件引入。除此之外还要把css样式引入

```js
import nprogress from 'nprogress'
import nprogress from "nprogress/nprogress.css";
```

nprogress中有两个函数会经常被使用，一个是start:进度条开始，另一个是done:进度条结束

```js
// 进行二次封装axios
import axios from 'axios'
import nprogress from 'nprogress';
import "nprogress/nprogress.css"

// 利用axios对象的方法create去创建一个axios实例
const requests = axios.create({
    // 配置对象
    baseURL:'/api',
    timeout:5000,

})

// 请求拦截器：在发请求之前，请求拦截器可以监测到，可以在请求发出去之前做一些事情
requests.interceptors.request.use(config => {
    // config:配置对象，里面的headers属性很重要
    // 设置进度条开始
    nprogress.start();
    return config;
})


// 响应拦截器
requests.interceptors.response.use(res => {
    // 进度条结束
    nprogress.done();
    // 成功的回调
    return res.data;
}, err => {
    // 失败的回调
    return Promise.reject(new Error('failed!'));
})

export default requests;
```



---------------------



节流：在规定的间隔时间范围内不会重复触发回调，只有大于这个时间间隔才会触发回调，把频繁触发变为少量触发（技能CD）

防抖：前面的所有触发都被取消，最后一次执行在规定的时间之后才会触发，也就是如果连续快速的触发，只会执行一次（回城）

loadsh插件：封装了与防抖与节流相关的函数

防抖：_.debounce(function(){}, 1000)

节流：_.throttle(function(){}, 1000)

js实现防抖

```js
function debounce(fn, wait){
	var timer = null;
    return function (){
        const context = this;
        var arg = arguments;
        if (timer){
            clearTimeout(timer);
         	timer = null;
        }
        timer = setTimeout(() => {
            fn.apply(context, arg);
        },wait)
    }
}
```

js实现节流

```js
function throttle(fn, threshold, scope){
    let timer;
    return function(){
        let context = scope || this;
        let arg = arguments;
        if (!timer){
            timer = setTimeout(function(){
                fn.apply(context, args);
                timer = null;
            }, threshold)
        }
    }
}
```



路由的跳转可以分为

声明式导航：router-link

编程式导航

当三级联动（需要从API里调用来动态显示）使用声明式导航时会出现卡顿现象，是因为router-link是一个组件，当服务器的数据返回之后会循环生成很多的router-link，因此适合编程式导航+事件委派（事件的冒泡原理，将事件绑定到父元素身上）



关于发送请求的优化

如果发送请求获取数据时在各个组件中发送再传入store中，则会使每次创建组件时都会发生请求，效率不高，最好将发送请求调用



---------------



由于大量数据不会从接口中调用，得自己创造假数据，安装mockjs实现构造数据

```
npm i mockjs
```

使用mock步骤：

- 在项目中src文件夹下创建mock文件夹
- 准备JSON数据
- 把mock数据所需要的图片放置到public文件夹中
- 创建mockServe.js文件模拟数据
  - 第一个参数是请求地址，第二个参数是数据

​				Mock.mock(path, data)

```
Mock.mock('/mock/banner', {code:200, data:banner})
```



引入swiper可以实现轮播图

npm i swiper@5

https://www.swiper.com.cn/

需要注意的是script代码不能在mounted钩子中实现。script代码只有在页面结构存在时才能被执行。mounted是在页面被挂载的时候实现，同时也是在此时发送异步请求将数据存入state中。但数据是在computed中才从state中取出，由于请求是异步的，若将script代码放入mounted钩子中会导致页面结构还没被解析完全就调用script代码，导致执行错误。

可以使用setTimeout延缓执行，但不推荐，最正确的做法应该是使用watch监听(只能保证当前有数据，但不一定执行完了)+nextTick从state中取出的数据项。

但如果数据是从props对象传给组件的，可直接在mounted中写入轮播图的script代码



-------------------

serch组件

- 关于vuex中的数据

项目中的getters主要作用是简化仓库中的数据，例如该项目中的结构是比较复杂的，可以在getters中进行处理，就不用mapSate映射了

<img src="D:\Typora\photos\image-20230227110131061.png" alt="image-20230227110131061" style="zoom:25%;" />

需要注意getters中的state是当前仓库的state，不是大仓库的state

```js
const getters = {
	goodList(state){
		console.log(state) // 当前仓库的state
        // 这里写一个空列表是为了出现一下情况：假如网络不给力，state.serchList.goodsList返回一个undefined，解析到页面中v-for遍历undefined就会出错。
		return state.serchList.goodsList||[]
	}
}

computed: {
    ...mapGetters(["goodsList", "attrsList", "trademarkList"]),
}
beforeMount() {
    // ES6新增的assign合并参数
    Object.assign(this.serchParams, this.$route.query, this.$route.params);
  },
```

- 关于serch中的路由跳转问题

由于serch组件有很多点击某个链接重新发送请求更新页面的业务，不只是在页面挂载时发送请求更新页面，因此，把发送请求的代码封装为一个函数，当需要发送请求时就调用这个函数来更新页面

- 关于面包屑

  根据具体的业务生成对应的逻辑，这里就不再赘述了

  - 点击typeNav生成面包屑
  - 品牌
  - 各个分层

- 排序

  根据具体的业务生成逻辑

  a标签的 :class{active}

  可以看一下再次点击让排序由降序改为升序，升序改为降序的逻辑

- 分页器（重难点）

  想要实现分页器，需要知道以下的四个条件

  - pageNo：当前是第几页
  - pageSize：每一页展示多少条数据
  - total：一共有多少条数据
  - continues：分页器连续页码个数

  计算一共有几页：totalPage = Math.ceil(total/pageSize)

  计算中间开始页码和结束页码：

  ```js
  let start = 0, end = 0;
  if (totalPage < continues){
      start = 1
      end = totalPage
  }
  else{
      start = pageNo - Math.floor(continues/2)
      end = pageNo + Math.floor(continues/2)
      if (start < 1){
          start = 1
          end = continues
      }
      if (end > totalPage){
          end = totalPage
          start = totalPage - continues + 1
      }
  }
  ```

  用v-for遍历和v-show限制来动态展示页数，并为1、... 、最后一页、... 添加v-show的限制，出现1的条件是start > 1，左点的条件是start > 2，右点的条件是end < totalPage - 1，totalPage的条件是end < totalPage

  运用props向分页器动态传输数据，利用组件自定义事件向父组件传输点击的页数以及分页器active的动态绑定

---------

商品详情页

- 选择配置的排他思想

- 大小窗的绑定

- 购买数量问题

  @change()可以在input框失去焦点后触发事件

  存在input标签时，一定不能相信用户的输入。

  若用户输入的是非数值：用e.target得到用户的输入，*1，如果是非数字类型会返回NaN，以此来判断用户的输入

  若用户输入的是负数，直接<0判断

  若用户输入的是小数，直接取整

- 购买商品问题

  - 在商品详情页点击并商品之后，会首先向服务器发送数据。依然在store中发送的请求（只进入actions不进入mutations和state）。但想在detail组件中获取请求的结果（是否添加成功）是无法通过组件通信的方式得到的。可以在store发送请求完毕通过异步async/await，return一个成功或者失败的promise，在detail组件中获得这个异步成功/失败的结果来实现功能

  - 其次才会跳转到已加入购物车页面（这个根据具体业务来）

------------

购物车

- 游客登录模式

  给购物车页面配置好路由、封装好API、在Store中存储好数据、已成功加入购物车页面跳转后，会发现store中依然没有任何数据，这是因为服务器不知道你是谁，不知道该返回谁的数据给你展示。因此需要用到uuid(token)

  - uuid具体使用参考官网。需要注意的是一台机器只会产生一次uuid，不能每次进入页面都生成新的uuid，否则会丢失以前购买的数据。因此，初始化时用uuid库生成一个id，存储到localStorage中，判断localStorage中是否存在uuid，存在就直接取用，不存在就生成。
  - 这种工具包推荐放入pages/utils包中，将生成uuid的逻辑代码封装到utils/getUuid.js文件中并暴露出去，在store/detail中引入该文件即可

- 删除选中的全部商品

  由于接口只能一次性删除一个商品，可以在组件中通过遍历来实现删除所有的，但在此为了应用其他知识点，将删除选中的商品封装为一个API，在store中进行遍历删除。在store的Actions中可以通过context.dispatch来获取。在遍历的时候将结果存入一个数组中，用promise.all()判断是否全部成功

--------------------

注册业务

- 正则验证

登录业务

- 持久化存储token

  - 登录成功的时候，后台为了区分这个用户是谁，服务器会下发token，以后会带着token请求头去找服务器要数据。
  
  - token需要放到sessionStorage中，因为vuex中的数据不是一直存在的，页面一刷新就没了，且每次发请求都应该在sessionStorage中找token才能保证每次发请求都带有token请求头。
  
  - 但当进入非home主页且再次刷新时依然会丢失userList，因为userList是在home组件发送带token请求头获取到的，进入其他页面时并没有发送对应请求且vuex中的数据因为刷新而被删除

- 需要解决的问题有
  - 未登录情况下用户是不能进入我的购物车页面的
  - 在非home主页再次刷新时会丢失登录
  - 已经登录的用户不能回到登录页

- 上述问题可以通过路由守卫来解决







-----------

webTerminal

天气组件的递归？？？后面补

天气组件的bug:

- 第一次输入天气时加载会为空，第二次输入命令才会呈现出数据，且修改地区之后上一次的结果不会响应的改变
  - 原因：数据没有被代理，由于调用接口得到的数据是对象，我在pinia的store中将发送请求得到天气数据直接赋值给state中的weatherData（state.weatherData = data），导致数据丢失代理，所以发生错误。
  - 处理方式：将state中的weatherData重新用接口规范定义，然后将请求得到的数据利用Object.assign()方法浅拷贝给weatherData，成功解决

```js
// 输出列表(展示)

const outputList = reactive<TerminalStandard.OutputType[]>([]);

// 命令列表(存储命令历史)

const commandList = reactive<TerminalStandard.CommandOutputType[]>([]);
```

这里最好不要写成reactive，虽然两者都是对象/数组类型，但是如果直接写outputList = [] 就会丢失代理，但如果将其定义为ref形式，写outputList.value = [] 就不会丢代理
