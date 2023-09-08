```js
const x = fn => fn();
const x2 = fn => {fn()};
const x3 = fn => ({fn:fn()});

const fn = () => 1;

console.log(x(fn)); // 1
console.log(x2(fn)); // undefined
console.log(x3(fn)); // { fn: 1 }
```

解释：ES6 箭头函数不加 `{}` 时默认有个return，而加 `{}` 的话就必须写return，因此 x 和 x2 相当于下面的代码

```js
const x = fn => return fn()
const x = fn => return 1

const x2 = fn => { 1 }
console.log(x2) // undefined
```

至于x3，用法很稀奇古怪，看看就行，`()` 相当于加了个 `return` ，即等同于下面的代码

```js
const x3 = fn => return { fn: fn() }
```



## Tree-shaking

在 webpack 项目中，有一个入口文件，相当于一棵树的主干，入口文件有很多依赖的模块，相当于树枝。实际情况中，虽然依赖了某个模块，但其实只使用其中的某些功能。通过 Tree-Shaking，将没有使用的模块摇掉，这样来达到删除无用代码的目的。

ES模块是ES6标准中引入的模块化系统，它在静态分析阶段就能确定模块的依赖关系，因此可以在打包过程中进行Tree Shaking操作，只将被使用的模块打包进最终的输出文件中。

CommonJS模块是Node.js中使用的模块化系统，它是动态加载模块的，模块的依赖关系只能在运行时确定，无法在静态分析阶段进行Tree Shaking操作。



## Vue2 双向绑定+vue3 Proxy代理

说一下input框怎么实现双向绑定的

vue2双向绑定有哪些缺点

Proxy怎么实现代理的，API会使用



## 正则表达式有哪些API？

- test

- match

  用于在字符串中查找匹配的正则表达式，返回一个数组或null。如果找到匹配项，则返回一个数组，其中包含所有匹配的字符串。

```js
const regex = /\d+/;
console.log("2022-01-01".match(regex)); // ["2022", "01", "01"]
console.log("hello".match(regex)); // null
```

- exec

  该方法用于检索字符串中的正则表达式的匹配。该函数的参数是一个字符串，该函数返回一个数组，其中存放匹配的结果。如果未找到匹配，则返回值为 null。第一个元素是匹配的字符串，后续元素是捕获组的匹配项。

```js
const regex = /(\d+)-(\d+)/;
console.log(regex.exec("2022-01-01")); // ["2022-01-01", "2022", "01"]
console.log(regex.exec("2022-02-02")); // ["2022-02-02", "2022", "02"]
console.log(regex.exec("hello")); // null
```

- replace

  用于在字符串中替换匹配的正则表达式部分，返回一个新的字符串。

```js
const regex = /hello/;
console.log("hello world".replace(regex, "hi")); // "hi world"
console.log("hi there".replace(regex, "hi")); // "hi there"
```

- split

  用于将字符串分割成数组，根据正则表达式匹配的部分进行分割。

```js
const regex = /[, ]+/;
console.log("apple, banana, orange".split(regex)); // ["apple", "banana", "orange"]
console.log("apple banana orange".split(regex)); // ["apple", "banana", "orange"]
```





## 前端优化相关

问知道哪些优化：懒加载、长列表、取消请求

图片怎么优化（懒加载），怎么判断在可视区，除了懒加载还有哪些优化

可视区：scrollTop + clientHeight > offsetTop

其他优化：图片压缩、预加载-preload、设置合理的缓存策略，让图片从缓存中获取，设置`Cache-Control`和`Expires`等HTTP头来控制缓存策略、CDN加速

缓存优化，缓存有哪些字段：cache- control、max-age、if-motified-since/last-motified、expires



## Webpack、vite

为什么vite会比webpack打包更快，是什么机制让它更快的

webpack打包使用过哪些plugin和loader

- 样式资源

  - 处理css资源需要两个loader
    - `css-loader`：负责将 Css 文件编译成 Webpack 能识别的模块
    - `style-loader`：会动态创建一个 Style 标签，里面放置 Webpack 中 css 模块内容

  - 处理less
    - `less-loader`：负责将 Less 文件编译成 CSS 文件
  - 处理sass也需要两个loader
    - `sass-loader`：负责将 Sass 文件编译成 css 文件
    - `sass`：`sass-loader` 依赖 `sass` 进行编译

- 图片资源

  - 图片资源在webpack5中已内置，只需要进行配置即可

- js资源

  - eslint：eslint-webpack-plugin

  - babel：将 ES6 语法编写的代码转换为向后兼容代码

    - `babel-loader`:
    - `@babel/preset-env`: 一个智能预设，允许您使用最新的 JavaScript。
    - `@babel/preset-react`：一个用来编译 React jsx 语法的预设
    - `@babel/preset-typescript`：一个用来编译 TypeScript 语法的预设

    

箭头函数和普通函数的区别，箭头函数内部this问题



CommonJS和ES6Moudule的区别



跨域



怎么去理解响应式布局？

在传统的固定布局中，元素的大小和位置是固定的，无法根据不同设备的屏幕尺寸和方向进行调整。而响应式布局则可以根据屏幕的宽度、高度、方向等因素动态地调整元素的大小、位置和排列方式，以适应不同的设备。

- 自适应布局：响应式布局能够根据屏幕的尺寸和方向自动适应元素的布局。例如，当屏幕变窄时，元素可以自动调整大小、换行或重新排列，以适应较小的屏幕空间。

- 弹性布局：Flex布局和Grid布局都是弹性布局，可以根据可用空间自动调整元素的大小和位置。例如，Flex布局中的元素可以通过设置flex-grow、flex-shrink和flex-basis等属性来自动调整它们的大小和位置。

- 媒体查询：响应式布局通常会结合媒体查询来实现不同屏幕尺寸下的样式调整。通过媒体查询，可以根据屏幕的宽度、高度、方向等条件来应用不同的样式规则，从而实现布局的响应式调整。

那百分比布局同样可以实现自适应，为什么非要用响应式布局呢？

- 响应式布局可以根据不同设备和屏幕尺寸自动调整布局，而百分比布局只能根据父元素的尺寸进行调整。
- 响应式布局可以使用媒体查询和CSS属性来精确地调整元素的样式和布局。相比之下，百分比布局只能通过设置百分比来调整元素的大小，无法对元素的位置和排列进行精确控制
- 响应式布局可以使用CSS的层叠特性来管理不同屏幕尺寸下的样式规则，使代码更易于维护和管理。相比之下，百分比布局需要在HTML中使用大量的行内样式或者额外的CSS类来实现布局的调整，使代码变得冗余和难以维护
- 响应式布局可以根据不同设备和屏幕尺寸提供更好的用户体验。通过自适应和弹性布局，响应式布局可以确保网页在不同设备上都能良好地显示和操作





