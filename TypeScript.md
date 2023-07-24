# TypeScript

## 1 普通类型

number、string、null、undefined、void

```typescript
// -----------number-----------
let num: number = 123
// 二进制、十六进制等也可
let binary: number = 0b1010
let hex: number = 0xf00d
// 可以是NaN
let nan: number = NaN
// Infinity
let infinity: number = Infinity

// -----------string-----------
// 可以是模板字符串
let str: string = `${num}`

// -----------boolean-----------
let bool:boolean = true
// 可以通过函数返回布尔值
let bool2: boolean = Boolean(1) 
// 不过使用构造函数 Boolean 创造的对象不是布尔值
// let bool3: boolean = new Boolean(1) // 报错，new Boolean() 返回的是一个 Boolean 对象

// -----------void-----------
// 返回为空的类型
function fn():void{
    console.log(123)
}
// void也可以定义undefined 和 null类型
let u: void = undefined
let n: void = null

// -----------null-----------
let n: null = null

// -----------undefined-----------
let un: undefined = undefined

// undefined 和 null 是所有类型的子类型,可以任意赋值？？？？

// 严格模式下不允许void为null
// 关闭严格模式：在tsconfig文件将strict改为false
// let v1:void = null // 报错
let v2: void = undefined
console.log(v2);
```

## 2 any、unknow、never

1、顶级类型top type：any unknow

2、Object

3、Number String Boolean

4、number string boolean

5、字面值 123  '123'  false

6、never

上面的类型可以包含下面的类型

```typescript
// -----------any-----------
// any类型可以随便赋值也可以随便赋值给别人
let suiyi: any = []
let suiyi2: any = suiyi
```

```typescript
// -----------unknow-----------
// unknow类型可随便赋值，但是不能赋值给别人(只能是unknown或any定义的)
let uk: unknown = 1
let ukCopy1: unknown = uk
let ukCopy2: any = uk
// let ukCopy2:number = uk // 报错

// unknow类型无法读对象的属性和方法
let person: unknown = { height: 180 }
// console.log(person.height); // 报错

// unknow类型比any类型更加安全
```

```typescript
// -----------never-----------
// 一个不存在的类型
type A = string & number
// 在一个死循环或抛出一个错误中使用
function foo(): never {
    // 永远不会返回结果 
    // 死循环 
    while(true) {}
}

function bar(): never {
    throw new Error()
}

// 在联合类型使用never会直接被忽视
type A = void | number | never

// 一个例子
type A = '唱' | '跳' | 'rap'
// 如果后续在A上增加一个“篮球”选项，那么会走到defalt，
// 由于不能把string类型赋值给never类型因此会报错，便于发现错误
function kun(value:A){
    switch(value){
        case '唱':
            break
        case '跳':
            break
        case 'rap':
            break
        defalt:
            // 兜底逻辑
            const error:never = value
    }
}
```

```typescript
// -----------Object、object、{} 之间的区别-----------
// Object可以为任意类型，因为在原型链中是最顶级的
let t1: Object = 1
let t2: Object = '123'
let t3: Object = true
let t4: Object = {}
let t5: Object = []
let t6: Object = () => { }

// object 支持引用数据类型
let t7: object = {}
let t8: object = []
let t9: object = () => { }
// let t10:object = 1 //报错
// let t11:object = '123' //报错

// {}和第一个一样，相当于new Object，但是无法修改
let t12: {} = 123
let t13: {} = { name: 'xiaohong' }
// t13.name = 'xiaoming' // 报错
```

## 3 接口-interface

interface来定义一种约束，让数据的结构满足约束的格式

```typescript
// 注意规范，第一个字母大写
interface Person {
    name: string
}
let p1: Person = {
    name: 'zhangsan',
    // 不能多也不能少
    // age:18 // 报错
    sex: '女'
}


// 遇到重名的会整合到一起
interface Person {
    sex: string
}


// 索引签名，只要这样写之后，后面的属性不会再被限制，可以随便写
// 一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集
interface Animal {
    name: string,
    [propName: string]: any,
    readonly sleep: () => void
}
let cat: Animal = {
    name: '猫',
    a: 1,
    b: '123',
    c: true,
    sleep: () => { }
}


// ? readonly
// readonly常用于函数和id


// extend，继续融合即可，支持多继承
interface A {
    a: string
}
interface B {
    b: string
}
interface C extends A, B {
    C: string
}
```

## 4 数组的定义

```typescript
// -----------数组普通类型-----------
// 方式一
let nums: number[] = [1, 2, 3, 4]
let bools: boolean[] = [true, false, false]
// 方式二
let arr1: Array<number> = [1, 2, 3, 4]
let arr2: Array<string> = ['1', '2']

// 支持联合类型
let arr7: Array<number | string> = [1,'2',3,'4']

// 大杂烩数组
let arr6: any[] = [1, '2', true, {}]
```

```typescript
// -----------对象数组类型-----------
interface Arr {
    name: string
}
let arr3: Arr[] = [{ name: '1' }, { name: '2' }, { name: '3' }]
```

```typescript
// -----------二维数组-----------
let arr4: number[][] = [[1, 2, 3, 4], [2, 3, 4, 5]]
let arr5: Array<Array<number>> = [[1, 2, 3, 4], [2, 3, 4, 5]]
```

```typescript
// -----------数组在函数中的定义和应用-----------
// 如果是arguments，应当定义为IArguments
function a(...args: any[]) {
    console.log(args);
    let b: IArguments = arguments
    console.log(b);
}
a([1, 2, '3', false])
```

## 5 函数的定义

```typescript
// -----------函数定义-----------
// 普通函数
interface User{
    name: string;
    age: number;
}
function getUserInfo(user: User): User {
  return user
}

// 函数表达式
interface Fn {
    // 括号内是参数，外部是返回值
    (num: string): number[]
}
const fn: Fn = function (num: string) {
    return [Number(num)]
}

// 箭头函数
interface Add {
    (num:  number, num2: number): number
}
 
const fn: Add = (num: number, num2: number): number => {
    return num + num2
}
fn(5, 5)
```

```typescript
// 默认值和可选参数不能同时使用
// 严格模式下可选参数会报错
function add(a: number = 1, b: number = 1): number {
    return a + b
}
const add2 = (a?: number, b?: number): number => {
    return a + b
}
```

```typescript
// -----------ts中的this增强（在js中无法使用）-----------
// 在第一个参数（必须是第一个）中定义this的类型
interface Obj {
    user: number[]
    add: (this: Obj, a: number) => void
}
let obj: Obj = {
    user: [1, 2, 3],
    add(this: Obj, a: number) {
        // 这里写this的时候就会有提示了
        this.user.push(a)
    }
}
```

```typescript
// -----------函数重载-----------
interface Message {
    id: number
    content: string
}
let messages: Message[] = [{ id: 1, content: 'chatGPT' }, 
                           { id: 2, content: 'Vue' }, 
                           { id: 3, content: 'TypeScript' }]

function getMessage(type: number): Message
function getMessage(type: string, readNum: number): Message[]
function getMessage(type: any, readNum: number = 2): Message | Message[] | undefined {
    if (typeof type === "number") {
        return messages.find(m => m.id === type)
    } else {
        return messages.filter(m => m.content === type).splice(0, readNum)
    }
}
console.log(getMessage('chatGPT',3));
```

## 6 联合类型，类型断言，交叉类型

```typescript
// -----------联合类型 | ----------- 
let num: number | string = '123'
// !! 将一个其他类型转换成boolean类型，类似于Boolean()
function transform(type: number | boolean): boolean {
    return !!type
}
console.log(transform(-Infinity));
```

```typescript
// -----------交叉类型 & -----------
interface People {
    name: string
    age: number
}
interface Man {
    sex: number
}
let zhangsan: People & Man = { name: 'zhangsan', age: 18, sex: 1 }
```

```typescript
// -----------类型断言-----------
// （值 as 类型）
// 相当于在编译阶段将其看成这个类型（欺骗编译器不报错，运行时依然会报错）
function suiyi(num:number | string):void{
    // 如果是number类型是没有length的，但在编译阶段就会报错
    // console.log(num.length); // 报错
    console.log((num as string).length)
}
console.log(suiyi('123'))
console.log(suiyi(123)) // undefined

let fn = (p:People | Man):void =>{
    console.log((p as Man).sex)
}

// 应用场景
// 如果直接在window身上定义一个值的话会报错
// window.abc = 123 // 报错
(window as any).abc = 123
```

## 7 浏览器内置对象

当你在使用一些常用的变量时，TypeScript 实际上已经帮你做了很多类型判断的工作了

```typescript
// -----------ecma-----------
// Boolean、Number、string、RegExp、Date、Error等

// 定义一个引用数据类型或new出来的直接cv
let num:Number = new Number(1)
let str:String = new String('123')
let r: RegExp = /^1/
let date:Date = new Date()
let e: Error = new Error("error!")
let xhr:XMLHttpRequest = new XMLHttpRequest()
```

```typescript
// -----------dom-----------
// Document、HTMLElement、Event、NodeList 等

// HTML(元素名称)Element
let div:HTMLDivElement | null = document.querySelector('div')
let span:HTMLSpanElement | null = document.querySelector('span')

// 有些会被归纳到HTMLElement中
let header:HTMLElement | null = document.querySelector('header')

// 结点集合就定义为NodeList
let divs:NodeList = document.querySelectorAll('div')

// Event
document.addEventListener('click', function (e: MouseEvent) {
    
});

// 想要获取不止一种类型
let divsAndFooters:NodeListOf<HTMLDivElement | HTMLElement> = document.querySelectorAll('div footer')
```

```typescript
// -----------浏览器类型-----------
let local:Storage = localStorage
let loc:Location = location
```

```typescript
// -----------Promise-----------
// 必须写一个泛型去约束返回的数值
let promise:Promise<number> = new Promise((resolve,reject) => {
    resolve(1)
})
let cookie:string = document.cookie
```

## 8 类

```typescript
// -----------定义和实现类-----------
// 在TypeScript是不允许直接在constructor定义变量的 需要在constructor上面先声明
class Person{
    name:string
    sex:number
    constructor(name:string,sex:number){
        this.name = name
        this.sex = sex
    }
}

// 用implements去实现接口
interface Options{
    el:string | HTMLElement
}
interface VueCls{
    options:Options,
    init():void
}
class Vue implements VueCls{
    options: Options
    constructor(op:Options){
        this.options = op
    }
    init():void{

    }
}

// -----------继承-----------
// 用extend去继承其他类
// 当接口和继承同时写的时候继承在前
interface Fa{
    name:string
    func():void
}
class Father implements Fa{
    name:string
    constructor(name:string){
        this.name = name
    }
    func(){
        console.log(123);
    }
}
class Son extends Father{
    constructor(name:string){
        super(name)
        this.name = name
    }
    func(){}
    chifna(){}
}
// super的原理
// 相当于父类的prototype.constructor.call，因此可以向父类传参
```

```typescript
// -----------修饰符-----------
// private 只能在类的内部使用，子类不能调用，实例也不能被调用
// protected 在类内部和子类使用，实例不能使用
// public 默认
// static 静态，可添加到属性和方法上，不需要通过创建实例，直接用类名.的方式即可调用
// 但在static内部无法用this去调用除static声明之外的(static只能调用static声明的)
class TempF{
    name:string
    constructor(name:string){
        this.name = name
    }
}
class Temp extends TempF{
    constructor(){
        super('123')
    }
    getName(){
        return this.name
    }
    static cslName(){
        // this.getName() // 报错
        console.log(this.name);
    }
    changeName(name:string){
        this.name = name
        // this.cslName()// 报错
    }
}
```

```typescript
// -----------get set 跟java类似-----------
// 类似于陷阱函数，当读取或者设置值时就会触发对应的函数
class Zuobiao{
    x:number
    y:number
    constructor(x:number,y:number){
        this.x = x
        this.y = y
    }
    get X(){
        return this.x
    }
    get Y(){
        return this.y
    }
}
let point = new Zuobiao(1,3)
console.log(point.X);
console.log(point.Y);
```

## 9 抽象类

```typescript
// -----------定义-----------
// 用abstract所定义
// abstract定义抽象类是不能实例化的，abstract定义抽象方法只能描述不能实现
abstract class Person{
    name:string
    constructor(name:string){
        this.name = name
    }
    // abstract init(){} //报错
    abstract init():void
    // 非抽象方法是可以直接使用的
    getName():void{
        console.log(this.name);
    }
}

// -----------继承抽象类-----------
// 用abstract定义的抽象方法继承后必须去实现
class Man extends Person{
    constructor(name:string){
        super(name)
    }
    // 必须去实现抽象方法
    init(): void {
        this.getName()
    }
    setName(name:string):void{
        this.name = name
    }
}
```

## 10 元组和枚举-enum

```typescript
// -----------元组类型-----------
let arr:[number,boolean] = [1,true]
// 在定义的时候只能按照顺序定义且不能多也不能少
// let arr:[number,boolean] = [1,true,false] // 报错
// 但是可以通过数组方法来添加增改(只能添加相应的类型)
arr.push(2)
arr[0] = 2

// 如果不想更改，也不允许使用数组方法，添加readonly修饰符
let arr2:readonly[number,boolean] = [2,true]

// 这种就可以实现可有可无的参数
let arr3:[x:number,y?:boolean] = [1,true]

// 可以用type获取元组属性类型和长度
// 必须按规则写，写let或者不写typeof都是不行的
type first = typeof arr[0] // number
type len = typeof arr['length'] //2
```

```typescript
// -----------枚举类型-----------

// 首字母必须大写不然会报错

// 枚举数字
// 默认从0开始
enum Color{
    red,
    green,
    blue
}
console.log(Color.red); // 0
// 改为从1开始
enum Colors{
    red = 1,
    green,
    blue
}
console.log(Colors.green); // 2
// 随便定义
enum Colorss{
    red = 1,
    green = 9,
    blue = 0
}

// 枚举字符串
enum ColorString{
    red = 'red',
    green = 'green',
    blue = 'blue'
}

// 混用枚举
// 字符串和数字混着写
// 但需要注意的是这种是不可以的
// enum Mix{
//     x = 1,
//     y = 'y',
//     z, // 报错，因为推断不出来z的值
//     c = 2,
// }

// 接口枚举
interface A{
    red:Color.red
}
let obj:A = {
    red:Color.red // red:0 也是可以的
}

// 枚举可用const去声明
const enum Types{
    success,
    faild
}
let code:number = 0
if (code === Types.success){

}
// 用const和不用const的区别
// 用const声明的会变成一个常数，不用const声明的，最后生成的js文件会将其转换为对象

// 反向映射
// 可以通过key -> value，也可以value -> key
enum fun{
    success = 12333
}
let value = fun.success
console.log(value);
let key = fun[value] // success
// 但是反向映射不支持字符串
// enum funs{
//     success = 'sda'
// }
// let value2 = funs.success
// console.log(value); // this is success value
// let key2 = fun[value] // 报错

```

## 11 类型别名-type

```typescript
// 用type去指定一个别名即可
type s = string | number
let str: s = '想要offer'

// 可以定义很多类型，包括函数、对象、数组
type fn = (name: string) => void
let fn = (name: string) => {
    console.log(name);
}
fn('求offer')
type obj = { say: string }
let want: obj = { say: '求offer' }

// 甚至能直接赋值，但这种就表示只能为这两个值
type A = '123' | 1

// 和interface的区别
// 1 interface可以支持用extends实现多继承，type只能单继承
// 2 interface无法使用联合类型，type支持联合类型
// 3 interface如果重名会合并参数，type会报错

// 在type中使用extends
// extends左边的值会作为右边值的子类型
type num = 1 extends number ? 1 : 0 // 1
// number可以换成其他类型，只要在类型顺序在字面量 1 的上层即可返回1
```

## 12 Symbol

```typescript
// symbol可以用于定义一个唯一的值，相当于唯一的内存地址
// 可以传入 number,string,undefined
let a1:symbol = Symbol(1)
let a2:symbol = Symbol(1)
console.log(a1 == a2);  // false
console.log(a1 === a2);  // false

// Symbol.for
// for会去全局找有没有注册过这个key，如果有就直接拿来用，没有就再去创建
// 利用for可以得到一个true
console.log(Symbol.for('1') === Symbol.for('1')); // true

// 项目中的使用
// 解决在对象中定义属性，不小心重名导致属性值被覆盖的问题
let obj = {
    name:1,
    // 这里a1和a2通过索引的方式获取到唯一的symbol
    [a1]:111,
    [a2]:222
}


// for in 无法读取symbol
for(let key in obj){
    console.log(key); // name
}
// Object.keys(obj)一样获取不到
console.log(Object.keys(obj)); // ['name']
// Object.getOwnPropertyNames 一样获取不到
console.log(Object.getOwnPropertyNames(obj)); // ['name']
// Object.getOwnPropertySymbols 只能获取symbol
console.log(Object.getOwnPropertySymbols(obj)); // [ Symbol(1), Symbol(1) ]
// Reflect.ownKeys完美解决
console.log(Reflect.ownKeys(obj)); // [ 'name', Symbol(1), Symbol(1) ]
```

## 13 set、map、weakSet、weakMap

```typescript
// -----------set-----------
// 天然去重(引用类型除外)
let set: Set<number> = new Set([1, 1, 2, 2, 3, 3])
console.log(set); // Set(3) { 1, 2, 3 }
let set2: Set<string> = new Set(['1', '1', '2', '2'])
console.log(set2); // Set(2) { '1', '2' }

// 一些方法
set.add(4)
console.log(set.has(4)) // true
set.delete(4)
console.log(set.size); // 3
// set.clear()
// 迭代方法
set.forEach(item => console.log(item))
console.log(set.keys()); // [Set Iterator] { 1, 2, 3 }
// entries() 返回一个迭代器
const iterator = set.entries()
for (let num of iterator) {
    // 为了与Map的API形式保持一致，故返回一个 [value, value] 的数组
    // [1, 1]
    // [2, 2]
    // [3, 3]
    console.log(num);
}
console.log(set.values());


// -----------map-----------
// key可以是引用类型
let map: Map<object, any> = new Map()
let arr: number[] = [1, 2, 3]
map.set(arr, '111')
console.log(map.get(arr)); // 111
// 方法和set相同就不赘述了
```

```typescript
// -----------weakMap和weakSet-----------
// 两者都是弱引用，如果一个键不再被引用，它会被垃圾回收掉

// 一个对象被强引用着，即使它已经不再被使用，它也不会被回收
// 当一个对象只被弱引用着时，垃圾回收器可以将其自动回收

// weakmap map的区别 weakmap的key只能是引用类型
// 
let obj: any = { name: 'zhangsan' } // 计数器：1
let weakmap: WeakMap<object, any> = new WeakMap()
let obj2: any = obj // 2
weakmap.set(obj, 'temp')
obj = null // 计数器-1:1
console.log(weakmap.get(obj)) // undefined
console.log(weakmap.get(obj2)) // temp
obj2 = null // 0  {name:'zhangsan'}这个对象被垃圾回收机制回收
console.log(weakmap.get(obj2)) // undefined

// weakMap是弱引用不可遍历，所以也没有foreach、entries、keys等方法
```



## 14 迭代器和生成器

```typescript
// -----------生成器-----------
// function后加*，yield后写返回值，支持异步
function* gen() {
    yield 1
    yield Promise.resolve('1')
    yield '1'
}
// 使用
const man = gen()
// 通过next()依次返回
console.log(man.next()); // { value: 1, done: false }
console.log(man.next()); // { value: Promise { '1' }, done: false }
console.log(man.next()); // { value: '1', done: false }
// 当没有东西可以去迭代了，后面的done就会变成true
console.log(man.next()); // { value: undefined, done: true }
```

```typescript
// -----------迭代器-----------
// 所有数组、类数组、set、map、甚至是字符串都可以使用迭代器去遍历
let arr = [1, 2, 3]
console.log(arr[Symbol.iterator]().next()); // { value: 1, done: false }
console.log(arr[Symbol.iterator]().next()); // { value: 1, done: false }

// 定义一个迭代方法(for of 底层)
const each = (value: any) => {
    // 迭代器
    // item[Symbol.iterator] f，因此用（）去调用
    // 返回的其实是一个生成器对象，用next()去获得生成器对象，
    let iterator: any = value[Symbol.iterator]()
    // 这个变量暂存iterator的next
    let next: any = { done: false }

    while (!next.done) {
        next = iterator.next()
        if (!next.done) {
            console.log(next.value);
        }
    }
}
each(arr)

// 迭代器语法糖
// for of
for (let value of arr) {
    console.log(value);
}
// for of 对象不可用，因为对象身上没有iterator
// for(let o of {name:123}){ // 报错

// }

// 数组的解构赋值底层原理都是去调用iterator
```

```typescript
// -----------实现让对象支持for of-----------
let obj = {
    max: 5,
    current: 0,
    // 为对象手动添加一个[Symbol.iterator]函数
    [Symbol.iterator]() {
        // 返回一个对象
        return {
            max: this.max,
            current: this.current,
            // 有next方法
            next() {
                if (this.current == this.max) {
                    // 这里相当于返回的是生成器对象
                    return {
                        value: undefined,
                        done: true
                    }
                } else {
                    return {
                        value: this.current++,
                        done: false
                    }
                }
            }
        }
    }

}
for (let o of obj) {
    console.log(o);
}



// -----------实现对象的数组解构-----------
let jiegou = [...obj]
console.log(jiegou); // [ 0, 1, 2, 3, 4 ]
// 正常的对象解构（对象解构不是调iterator）
let jiegouObj = { ...obj }
console.log(jiegouObj);
// {
//     max: 5,
//     current: 0,
//     [Symbol(Symbol.iterator)]: [Function: [Symbol.iterator]]
// }
```

在js中，类数组虽然不能使用 pop 等数组方法，但是仍然可以使用 forEach 去遍历，那为什么还需要迭代器 for of 呢？

- 虽然类数组可以使用 forEach 方法进行遍历，但是 forEach 方法无法中途停止或跳出循环，也无法直接访问当前迭代项的索引值。
- 而使用 for of 迭代器可以在任意时刻停止或跳出循环，并且可以直接访问当前迭代项的索引值。此外，使用 for of 迭代器可以遍历任何可迭代对象，包括数组、类数组、字符串、Set、Map等，而 forEach 方法只能遍历数组。因此，在某些情况下，使用 for of 迭代器比使用forEach方法更加灵活和方便。



## 15 泛型

泛型定义相关

```typescript
// -----------函数泛型-----------
// 泛型相当于动态类型，在定义的时候不去指定什么类型，在调用的时候去决定是什么类型
// 这里的 T 相当于占位符，会自动的根据传进去的参数做类型推断
function temp<T>(a:T,b:T):Array<T>{
    return [a,b]
}
temp(1,2)
temp(true,false)
temp('1','2')


// -----------type的泛型-----------
type A<T> = string | number | T
let a1:A<boolean> = true
let a2:A<boolean> = 1
let a3:A<boolean> = '1'
// let a4:A<boolean> = {name:'1'} // 报错


// -----------interface的泛型-----------
interface Data_demo<T>{
    msg:T
}
let data:Data_demo<number> = {
    msg:1
}
```

泛型使用相关

```typescript
// -----------支持多个泛型-----------
function temp2<T,K>(a:T,b:K):Array<T | K>{
    return [a,b]
}

// -----------可以设置默认值-----------
function temp3<T = number,K = number>(a:T,b:K):Array<T | K>{
    return [a,b]
}

// -----------泛型约束-----------
// 在类型后面跟一个 extends 类型
function add<T extends number>(a:T, b:T){
    return a+b
}
console.log(add(1,2));
// 这里约束之后只能传number类型或number类型的子类型（浮点数、bigInt、八进制、十六进制等），其他的会报错
console.log(add(1.22,3));
// console.log(add('1','2'));

interface AType{
    length:number
}
function fn<T extends AType>(a:T){
    return a.length
}
console.log(fn([1,2,3,4]));
console.log(fn('123'));
```

```typescript
// -----------keyof-----------
// 通过keyof可以将对象的key推断为联合类型
let obj = {
    name:'zhangsan',
    sex:'man'
}
type Key_temp = typeof obj // 整个obj对象
type Key = keyof typeof obj // 对象的key的联合类型 Key = "name" | "sex"

function ob<T extends object,K extends keyof T>(obj:T,key:K){
    return obj[key]
}
ob(obj,'name')
// ob(obj,'age') // 传入不存在的就会报错


// keyof 高级用法
// 实现功能：将interface中的参数变为可选
interface Data {
    name:string,
    age:number,
    sex:string
}
type Options<T extends object> = {
    // 这里的T就相当于Data
    [key in keyof T]?: T[key]
}
type B = Options<Data>
```



## 16 配置文件解读

| compilerOptions     |                                                              |
| ------------------- | ------------------------------------------------------------ |
| incremental         | TS编译器在第一次编译之后会生成一个存储缓存文件，第二次编译会在缓存文件的基础上增添 |
| tsBuildInfoFile     | 缓存文件存储位置                                             |
| target              | 目标语言的版本（es5、es6）                                   |
| module              | 生成的代码模板标准（commonjs、amd、es6）                     |
| lib                 | TS需要引用的库，es5默认引用dom，es5                          |
| outDir              | 指定输出目录                                                 |
| rootDir             | 指定输出文件目录，用于控制输出目录结构                       |
| declaration         | 生成声明文件，开启后会自动生成声明文件                       |
| declarationDir      | 声明文件存放目录                                             |
| emitDeclarationOnly | 只生成声明文件，而不会生成JS文件                             |
| sourceMap           | 生成sourceMap文件                                            |
| declarationMap      | 声明文件的sourceMap                                          |
| typeRoots           | 第三方声明文件目录，默认Wienode_modules/@types               |
| types               | 加载的声明文件包                                             |
| removeComments      | 删除注释                                                     |
| noEmit              | 不输出文件，即编译后不生成任何js文件                         |
| noEmitOnError       | 在报错时不输出任何文件                                       |
|                     |                                                              |
|                     |                                                              |



## 17 命名空间

TypeScript与ECMAScript 2015一样，任何包含顶级`import`或者`export`的文件都被当成一个模块。相反地，如果一个文件不带有顶级的`import`或者`export`声明，那么它的内容被视为全局可见。也就是说如果在两个文件中都声明了a且没带import或export，那么就会报错（重复声明），可以通过添加export解决，也可以使用命名空间

明明export就可解决导入和全局变量的问题，为什么还需要namespace？

- 使用命名空间可以将代码分成多个逻辑模块，每个模块可以有自己的变量、函数、类等，可以在不同的模块中使用相同的名称，而不会产生命名冲突。同时，命名空间中的代码也可以被导出到命名空间的外部使用，方便其他模块引用。

- 虽然使用 `export` 关键字可以将变量、函数、类等导出到命名空间的外部使用，但是如果没有命名空间的话，所有的变量、函数、类等都必须在全局作用域中声明，容易产生命名冲突和代码混乱的问题。

```typescript
// -----------命名空间的声明和使用-----------
// 在文件A中
namespace A{
    export const value = 1
}
// 在文件B中
namespace B{
    export const value = 2
}
//这俩互不干扰

// 调用
console.log(A.value);
console.log(B.value);
// 其实质是生成了一个函数，返回对象
```

```typescript
// -----------嵌套命名空间-----------
namespace C{
    export namespace C2{
        export const value = 2
    }
}
console.log(C.C2.value);
```

```typescript
// -----------抽离命名空间-----------
// 其实就是通过import去导入其他文件中的namespace即可
// 假设在另外一个文件中有如下代码
// export namespace D{
//     export const value = 2
// }
// 在该文件中引用
import {D} from './index2'
console.log(D.value);
```

```typescript
// -----------简化命名空间-----------
// 利用import取别名
import DValue = D.value
console.log(DValue);

// 命名空间的合并
// 和interface一样，如果重名会合并，就不演示了
```

在 TypeScript 的命名空间中，如果想要将某个变量、函数、类等导出到命名空间的外部使用，需要使用 `export` 关键字进行导出。如果没有使用 `export`，则该变量、函数、类等只能在该命名空间内部使用。即便是在namespace前面添加export（变量前不加）也不能在外部使用。

## 18 三斜线指令

导入普通ts文件语法  `///<reference path="xxx"/>`

导入的文件的namespace前不用加export，在打包后会自动编译成一个文件

```typescript
// 相当于import导入，仅可放在包含它的文件的最顶端

// 假设在index2中有如下代码：
// export namespace D{
//     export const value = 2
// }

///<reference path="index2.ts"/>
namespace A {
    export function fn(){
        console.log(D.value);
    }
}
console.log(A.fn());
```

导入声明文件语法 `///<reference type="xxx"/>`

```typescript
// 仅当在你需要写一个d.ts文件时才使用这个指令
///<reference types="node" />
```

## 19 声明文件

声明文件以.d.ts结尾

在用import引入一些第三方库时，ts文件会去调用其声明文件，如果没有对应的声明文件就会报错

```typescript
// 下面的express库会因为无声明文件则报错
import express from 'express'
import axios from "axios";
// 可以尝试去为一些热门库安装声明文件
// npm i --save--dev @types/express
// 或手写声明文件

// express.d.ts
declare module 'express'{
    interface Express{
        // 返回一个app
        ():App
        Router():Router
    }
    interface App{
        // use 方法
        use(path:string,router:any):void
        // listen 方法
        listen(port:number,callback?:()=>void)
    }
    interface Router{
        get(path:string,callback:(req:any,res:any)=>void):void
    }
    const express:Express
    export default express
}

// index.ts
const app = express();
const router = express.Router()
app.use('/api',router)
router.get('/api',(req:any,res:any) => {
    res.json({
        code:200
    })
})
app.listen(80, () => {
  	console.log('express server running at http://127.0.0.1');
})
```

```typescript
// declare关键字还可以定义一些全局变量
declare let a :number
declare function fn(b:string){
    console.log(b);
}
declare class Vue{
    
}
declare enum C{
    a = 1
}
```

## 20 装饰器-Decorator

```typescript
// -----------类装饰器 classDecorator-----------
// 用ClassDecorator表示装饰器函数，用@xxx指定装饰器所装饰的对象
// 参数target 返回装饰对象的构造函数
// 当所装饰对象中代码逻辑非常复杂的时候，可以不破坏定义的结构，通过装饰器直接在上面挂载新的属性和方法
const Base: ClassDecorator = (target) => {
    console.log(target); // [class Http]
    // 挂载自己需要的属性和方法
    target.prototype.name = 'zhangsan'
    target.prototype.fn = () => {
        console.log('123');
    }

}
@Base
class Http {
    // 大量逻辑代码
}
// 这里不加as any会报错，因为使用装饰器是在运行时才会挂载上去，在编译时原类型定义时身上并没有fn方法
let http = new Http() as any
http.fn()
```

```typescript
// -----------装饰器工厂-----------
// 可以利用闭包传递参数
const Base2 = (name: string) => {
    const fn: ClassDecorator = (target) => {
        // 新增属性方法
        target.prototype.name = name
    }
    // 这里要return，不然会报错
    return fn
}

@Base2('张三')
class Http2 {
    // 大量代码
}
```

```typescript
// -----------方法装饰器 MethodDecorator-----------
// 三个参数 target,key,descriptor
// 返回一个新的描述符，从而修改方法
const Get = (url: string) => {
    const fn: MethodDecorator = (target, key, descriptor: PropertyDescriptor) => {
        console.log(target, key, descriptor);
        // {} 原型对象
        // getList 被装饰的方法的名称
        // {  描述符
        //     value: [Function: getList],
        //     writable: true,
        //     enumerable: false,
        //     configurable: true
        // } 

        // 这里不写PropertyDescriptor会报错
        // PropertyDescriptor是一个描述属性的内置接口，在 TS 中，每个属性都有一个对应的 PropertyDescriptor 对象，用来描述这个属性的特征
        // interface PropertyDescriptor {  
        //     configurable?: boolean;
        //     enumerable?: boolean;
        //     value?: any;
        //     writable?: boolean;
        //     get?(): any;
        //     set?(v: any): void;
        // }

        // 这里就相当于把原来的getList换成了这个函数
        console.log(descriptor.value); // [Function: getList]
        descriptor.value = function (): string {
            console.log(descriptor.value); // [Function (anonymous)]
            return url + '_temp'
        }
    }
    return fn
}

class Http3 {
    @Get('http://127.0.0.1')
    getList(data: any) {
        return data
    }
}
let http3 = new Http3() as any
console.log(http3.getList()); // http://127.0.0.1_temp
```

```typescript
// -----------参数装饰器 ParameterDecorator-----------
// 三个参数 target,key,index
// 可以用来简化参数结构(这里就不展示了，后面用到再说吧，其实就是没看懂)
const Result = ()=>{
    const fn:ParameterDecorator = (target,key,index)=>{
        console.log(target,key,index); // {} get 1
    }
    return fn
}
class Http4{
    get(name:string,@Result() data:any){
        console.log(data);
    }
}
```

```typescript
// -----------属性装饰器(用得少) PropertyDecorator-----------
// 两个个参数 target,key
const Props:PropertyDecorator = (target,key)=>{
    console.log(target,key); // {} name
    
}

class Http5{
    @Props
    name:string
    constructor(){
        this.name = '123'
    }
}
```

## 案例

### 封装localStorage并支持过期时间

```typescript
// index.ts
import { StorageCls,Key,Expire,Data,Result } from "./type";
import { Dictionaries } from "./enum";
export class Storage implements StorageCls{
    // expire就是过期的时间
    set<T>(key:Key, value:T, expire:Expire = Dictionaries.permanent){
        const data = {
            value,
            // 相当于 permanent: expire
            [Dictionaries.expire]:expire
        }
        localStorage.setItem(key,JSON.stringify(data))
    }
    get<T>(key:Key):Result<T | null>{
        const value = localStorage.getItem(key)
        // 判断是否已存储
        if (value){
            const data:Data<T> = JSON.parse(value)
            const now = new Date().getTime()
            // 判断是否过期
            if (typeof data[Dictionaries.expire] == 'number' && data[Dictionaries.expire] < now){
                this.remove(key)
                return {
                    message: `您的${key}已过期`,
                    value:null
                }
            }else{
                return {
                    message:'success',
                    value:data.value
                }
            }

        }else{
            return {
                message:"找不到对象",
                value:null
            }
        }
    }
    
    remove(key:Key){
        localStorage.removeItem(key)
    } 
    clear(){
        localStorage.clear()
    }
}
```

```typescript
// type/index.ts
import { Dictionaries } from "../enum"
export type Key = string
export type Expire = Dictionaries.permanent | number 
export interface Result<T>{
    message:string
    value:T | null
}
export interface Data<T>{
    value:T
    [Dictionaries.expire]:Expire
}
export interface StorageCls{
    get:<T>(key:Key)=>Result<T | null>
    set:<T>(key:Key, value:T, expire:Expire)=>void
    remove:(key:Key)=>void
    clear:()=>void
}
```

```typescript
// enum/index.ts
export enum Dictionaries {
    expire = '__expire__',
    permanent = 'permanent'
}
```

### 案例-发布与订阅模式

```typescript
interface Evenet{
    on(name:string,fn:Function):void
    emit(name:string,...args:Array<any>):void
    off(name:string,fn:Function):void
    once(name:string,fn:Function):void
}
interface List{
    [key:string]:Array<Function>
}
class Dispatch implements Evenet{
    list:List
    constructor(){
        this.list = {}
    }
    on(name:string,fn:Function){
        const callback = this.list[name] || []
        callback.push(fn)
        this.list[name] = callback
    }
    emit(name:string,...args:Array<any>){
        let callbacks = this.list[name]
        if (callbacks){
            callbacks.forEach(fn => {
                fn.apply(this,args)
            })
        }else{
            console.error('名称错误')
        }
    }
    off(name:string,fn:Function){
        let callbacks = this.list[name]
        if (callbacks && fn){
            this.list[name] = callbacks.filter(call => {
                return call != fn
            })
        }else{
            console.error('名称错误')
        }
    }
    once(name:string,fn:Function){
        let temp = (...args:Array<any>) =>{
            fn.apply(this,args)
            this.off(name,temp)
        }
        this.on(name,temp)
    }
}


const dispatch = new Dispatch()
dispatch.on('post',(...args:Array<any>)=>{
    console.log(args,1);
})
function temp(...args:Array<any>){
    console.log(args,2);
}
dispatch.on('post',temp)
dispatch.on('get',(...args:Array<any>)=>{
    console.log(args,11);
})

dispatch.emit('post',1,2,3,4)
dispatch.off('post',temp)
dispatch.emit('post',1,2,3,4)

```

## 21 Proxy和Reflect

```typescript
// -----------proxy-----------
// proxy支持对象 数组 函数 set map这类引用数据类型
let person = { name: '张三', age: 18 }
// 支持传入两个参数，第一个参数是要代理的对象，第二个参数是描述对象
let personProxy = new Proxy(person, {
    // 拦截取值
    get(target, key, receiver) {
        if (target.age < 18) {
            return Reflect.get(target, key, receiver)
        } else {
            return "已成年！"
        }
    },
    // 拦截赋值
    // 支持传入四个参数
    // 
    set(target, key, value, receiver) {
        // person name '李四' person
        // receiver 当箭头函数嵌套时可能会出现上下文错误，可用这个解决
        console.log(target, key, value, receiver);
        return true
    },
    // // 拦截函数调用
    // apply(){

    // },
    // // 拦截 in 操作符
    // has(){

    // },
    // // 拦截for in
    // ownKeys(){

    // },
    // // 拦截new
    // construct(){

    // },
    // // 拦截delete
    // deleteProperty(){

    // }
})
console.log(personProxy.age); // 已成年！
```

```typescript
// -----------Reflect-----------
// 和Proxy陷阱函数及参数全相同
// 一般我们获取数据都是通过 . 获取
console.log(person.age);
// 还可以通过Reflect去取
console.log(Reflect.get(person, 'age', person))
// 修改值也是一样的
person.name = '李四'
console.log(person.name)
Reflect.set(person, 'name', '张三', person) // 会返回一个true
console.log(Reflect.get(person, 'name', person))
```

```typescript
// -----------实现观察者模式-----------
const list: Set<Function> = new Set()
const autoRun = (cb: Function) => {
    if (!list.has(cb)) {
        list.add(cb)
    }
}
const observable = <T extends object>(params: T) => {
    return new Proxy(params, {
        set(target, key, value, receiver) {
            const result = Reflect.set(target, key, value, receiver)
            // 通知
            list.forEach(fn => fn())
            return result
        }
    })
}
const ob = observable({name:'zhangsan',arr:"吃饭"})

// 放入list中
autoRun(()=>{
    console.log("里面的数据有变化了！")
})

ob.arr = '睡觉' // 里面的数据有变化了！
```

## 22 协变、逆变、双向协变

```typescript
// -----------协变-----------
interface A{
    name:string
    age:number
}
interface B{
    name:string
    age:number
    sex:string
}

let a_temp:A = {
    name: 'zhangsan',
    age: 18
}
let b_temp:B = {
    name: 'lisi',
    age: 20,
    sex: '男'
}
// A B类型都有name和age，这两个类型非常相似，可以称为鸭子类型
// 这种赋值不会报错，称为协变
a_temp = b_temp
// 暂且把A这种类型称为主类型，B为子类型，
// 只要子类型能包含主类型，这种赋值就不会报错，即不可以少但可以多


// -----------逆变-----------
// 值为协变，函数为逆变
let fna = (params:A)=>{
    
}
let fnb = (params:B)=>{

}

// fna = fnb // 报错
// 这里相当于把fna函数赋值给fnb，最终执行的仍然是fna，因此逆变是合理的
fnb = fna 

// -----------双向协变-----------
// 在tsconfig.json里设置 strictFunctionTypes : false
// 可以实现fna = fnb的操作，称之为双向协变，不太安全建议不开启
```



## 23 Partial、Pick、ReadOnly、Record

```typescript
// -----------Partial-----------
// 将传入的泛型类型变为可选
type Person = {
    name:string
    age:number
    text:string
}

// 通过partial将Person里的属性全部变成可选
type p = Partial<Person>
// 相当于
type p_replace = {
    name?: string | undefined;
    age?: number | undefined;
    text?: string | undefined;
}

// 源码(之前学过，在泛型keyof那里)
type Partial_origin<T> = {
    [P in keyof T]?: T[P];
};
```

```typescript
// -----------Pick-----------
// 从泛型中选出某类型
type p2 = Pick<Person,'name'>
// 相当于
type p2_replace = {
    name: string;
}

// 可以选多个
type union = Pick<Person,'name'|'age'>
// 相当于
type union_replace = {
    name: string;
    age: number;
}

// 源码
type Pick_origin<T, K extends keyof T> = {
    [P in K]: T[P];
};
```

```typescript
// -----------ReadOnly-----------
// 和parial差不多，只是把可选换成了readOnly
type Temp = {
    temp1:string
    temp2:number
}
type temp = Readonly<Temp>

// 源码
type Readonly_origin<T> = {
    readonly [P in keyof T]: T[P];
};
```

```typescript
// -----------Record-----------
type Person = {
    name:string
    age:number
}
type key = "A" | "B"
type p = Record<key,Person>
// 相当于
type temp2_replace = {
    A: Person;
    B: Person;
}
let persons:p = {
    A:{name:'zhangsan',age:18},
    B:{name:'lisi',age:20}
}

// 源码
// keyof any会返回 type key = string | number | symbol
type Record_origin<K extends keyof any, T> = {
    [P in K]: T;
};
```

## 24 infer

```typescript
// 定义一个类型，如果是数组类型就返回数组元素的类型，否则就传入什么类型返回什么
type Type<T> = T extends Array<any> ? T[number] : T

// 使用infer
// Fn就像个类型加工站，infer只是个关键字，不用去考虑它的含义，变量名U也可任意取名，只是占位用
// U相当于把对应格式的类型提取出来，如果格式对应不上就返回本身
type Fn<T> = T extends Array<infer U> ? U : T
type T = Array<string>
type Ans = Fn<T> // string


type Arr = ['a','b','c']
type GetFirst<T extends any[]> = T extends [infer one,infer two,infer three] ? one : []
type GetFirstChange<T extends any[]> = T extends [infer one,...any[]] ? one : []
type first = GetFirst<Arr> // 'a'

type GetLast<T extends any[]> = T extends [...any[],infer last] ? last : []
type Last = GetLast<Arr> // 'c'

type Pop<T extends any[]> = T extends [...infer res,infer last] ? res : []
type GetPop = Pop<Arr> // ["a", "b"]

// infer的递归
type Reverse<T extends any[]> = T extends [infer first,...infer reset] ? [...Reverse<reset>,first] : T
type Arr2 = [1,2,3,4,5]
type Arr2_reverse = Reverse<Arr2> // [5, 4, 3, 2, 1]

// infer 只能在条件类型的 extends 子句中使用，
// 且 infer 声明的类型变量只在条件类型的 true 分支中可用
```

