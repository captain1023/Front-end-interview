## 一、数据类型

### 1. JavaScript有哪些数据类型，它们的区别？

JavaScript共有八种数据类型，分别是 Undefined、Null、Boolean、Number、String、Object、Symbol、BigInt。

其中 Symbol 和 BigInt 是ES6 中新增的数据类型：

- Symbol 代表创建后独一无二且不可变的数据类型，它主要是为了解决可能出现的全局变量冲突的问题。
- BigInt 是一种数字类型的数据，它可以表示任意精度格式的整数，使用 BigInt 可以安全地存储和操作大整数，即使这个数已经超出了 Number 能够表示的安全整数范围。



这些数据可以分为原始数据类型和引用数据类型：

- 栈：原始数据类型（Undefined、Null、Boolean、Number、String、Symbol、BigInt）
- 堆：引用数据类型（对象、数组和函数）



两种类型的区别在于**存储位置的不同：**

- 原始数据类型直接存储在栈（stack）中的简单数据段，占据空间小、大小固定，属于被频繁使用数据，所以放入栈中存储；
- 引用数据类型存储在堆（heap）中的对象，占据空间大、大小不固定。如果存储在栈中，将会影响程序运行的性能；引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获得实体。
- 堆和栈的分离,使得堆中的内容可以被多个栈共享(可以理解为多线程访问同一个对象).提供了有效的数据交互方式,另一方面,堆中的共享常量和缓存可以被所有栈访问
- 栈中只记录引用类型在堆中的地址


堆和栈的概念存在于数据结构和操作系统内存中，在数据结构中：
- 在数据结构中，栈中数据的存取方式为先进后出。
- 堆是一个优先队列，是按优先级来进行排序的，优先级可以按照大小来规定。


### 2. 数据类型检测的方式有哪些

**（1）typeof**

```javascript
console.log(typeof []);              // object    
console.log(typeof {});              // object
console.log(typeof undefined);       // undefined
console.log(typeof null);            // object
```
其中数组、对象、null都会被判断为object，其他判断都正确。



**（2）instanceof**

`instanceof`可以正确判断对象的类型，**其内部运行机制是判断在其原型链中能否找到该类型的原型**。

```javascript
console.log(2 instanceof Number);                    // false
console.log(true instanceof Boolean);                // false 
console.log('str' instanceof String);                // false 
 
console.log([] instanceof Array);                    // true
console.log(function(){} instanceof Function);       // true
console.log({} instanceof Object);                   // true
```

instanceof 原理是判断一个对象在其原型链中是否存在一个构造函数(Array,Map,Set)的prototype属性

```js
/**
 * isntanceof的原理是:
 * 只要右边变量的prototype在左边变量的原型链上即可
 * 左边变量是个实例对象,所以使用__proto__
 */
let a = [];
function myInstanceof (a,B){
    let aProto = a.__proto__
    let BProto = B.prototype
    while(true){
        if(aProto == null){
            return false
        }
        if(aProto === BProto){
            return true
        }
        aProto = aProto.__proto__
    }
}
console.log(myinstance(a,Array))

```
可以看到，`instanceof`**只能正确判断引用数据类型**，而不能判断基本数据类型。`instanceof` 运算符可以用来测试一个对象在其原型链中是否存在一个构造函数的 `prototype` 属性。

**参考blog**:[https://juejin.cn/post/6844903613584654344](https://juejin.cn/post/6844903613584654344) 


**（3） constructor**

```javascript
console.log((2).constructor === Number); // true
console.log((true).constructor === Boolean); // true
console.log(('str').constructor === String); // true
console.log(([]).constructor === Array); // true
console.log((function() {}).constructor === Function); // true
console.log(({}).constructor === Object); // true
```

- `constructor`有两个作用，一是判断数据的类型，二是对象实例通过 `constructor` 对象访问它的构造函数。需要注意，如果创建一个对象来改变它的原型，`constructor`就不能用来判断数据类型了：

**[扩展]**

Object 的每个实例都具有下列属性和方法。
- Constructor(**属性**):保存着用于创建当前对象的函数。
- hasOwnProperty(propertyName ) :用于检查给定的属性在当前对象实例中(而
不是在实例的原型中)是否存在。其中，作为参数的属性名( propertyName )必 须以字符串形式指定(例如:o.hasOwnProperty("name") )。
- isPrototypeOf(object) :用于检查传入的对象是否是另一个对象的原型
- propertyIsEnumerable(propertyName ) :用于检查给定的属性是否能够使 用for-in 语句来枚举。与hasOwnProperty() 方法一样，作 为参数的属性名必须以字符串形式指定。
- toLocaleString() :返回对象的字符串表示，该字符串与执行环境的地区对应。 toString() :返回对象的字符串表示。
- valueOf() :返回对象的字符串、数值或布尔值表示。通常与toString() 方法的返 回值
- object实例对象:__proto__指向创建它的构造函数的原型,constructor指向构造函数


```javascript
function Fn(){};
 
Fn.prototype = new Array();
 
var f = new Fn();
 
console.log(f.constructor===Fn);    // false
console.log(f.constructor===Array); // true
```

**（4）Object.prototype.toString.call()**

`Object.prototype.toString.call()` 使用 Object 对象的原型方法 toString 来判断数据类型：

```javascript
var a = Object.prototype.toString;
 
console.log(a.call(2));
console.log(a.call(true));
console.log(a.call('str'));
console.log(a.call([]));
console.log(a.call(function(){}));
console.log(a.call({}));
console.log(a.call(undefined));
console.log(a.call(null));
```

同样是检测对象obj调用toString方法，obj.toString()的结果和Object.prototype.toString.call(obj)的结果不一样，这是为什么？

Array、function等**类型作为Object的实例，都重写了toString方法**。根据原型链定义,不会查找到object.prototye上的toString方法了.


### 3. 判断数组的方式有哪些

- 通过Object.prototype.toString.call()做判断

```javascript
Object.prototype.toString.call(obj).slice(8,-1) === 'Array';
```

- 通过原型链做判断

```javascript
obj.__proto__ === Array.prototype;
```

- 通过ES6的Array.isArray()做判断

```
Array.isArray(obj);
```

- 通过instanceof做判断

```javascript
obj instanceof Array
```

- 通过Array.prototype.isPrototypeOf

```javascript
Array.prototype.isPrototypeOf(obj)
```

### 4. null和undefined区别

首先 Undefined 和 Null 都是基本数据类型，这两个基本数据类型分别都只有一个值，就是 undefined 和 null。



undefined 代表的含义是**未定义**，null 代表的含义是**空对象**。一般变量声明了但还没有定义的时候会返回 undefined，null主要用于赋值给一些可能会返回对象的变量，作为初始化。



undefined 在 JavaScript 中不是一个保留字，这意味着可以使用 undefined 来作为一个变量名，但是这样的做法是非常危险的，它会影响对 undefined 值的判断。我们可以通过一些方法获得安全的 undefined 值，比如说 void 0。



当对这两种类型使用 typeof 进行判断时，Null 类型化会返回 “object”，这是一个历史遗留的问题。当使用双等号对两种类型的值进行比较时会返回 true，使用三个等号时会返回 false。

### 6.闭包是什么,优点,缺点,应用场景
- 能够访问其他函数内部变量的函数，被称为 闭包。
- 闭包的应用场景:curry、单例模式、模拟私有属性等  

```js
// 模拟私有属性:function 内部定义私有属性,返回可以访问内部属性的方法
function getGeneratorFunc () {
  var _name = 'John';
  var _age = 22;
    
  return function () {
    return {
      getName: function () {return _name;},
      getAge: function() {return _age;}
    };
  };
}

var obj = getGeneratorFunc()();
obj.getName(); // John
obj.getAge(); // 22
obj._age; // undefined

//单例模式
function Singleton(){
  this.data = 'singleton';
}

Singleton.getInstance = (function () {
  var instance;
    
  return function(){
    if (instance) {
      return instance;
    } else {
      instance = new Singleton();
      return instance;
    }
  }
})();//是个立即执行函数,所以调用的时候用一个括号就可以了

var sa = Singleton.getInstance();
var sb = Singleton.getInstance();
console.log(sa === sb); // true
console.log(sa.data); // 'singleton'

```
- 优点:避免全局变量的污染、希望一个变量长期存储在内存中、模拟私有属性
- 缺点:内存泄露、
  - 内存泄露 是指当一块内存不再被应用程序使用的时候，由于某种原因，这块内存没有返还给操作系统或者内存池的现象。内存泄漏可能会导致应用程序卡顿或者崩溃。
- 内存泄漏排查手段:Chrome->开发者工具
- 内存泄漏解决方案: 1. 使用"use strict"避免不经意间内存泄漏 2. 关注 DOM 生命周期，在销毁阶段记得解绑相关事件 3.少使用闭包(这不纯纯废话😈)
- 闭包在什么时候才会被回收,当引用他的作用域销毁后(执行完毕)才会被垃圾回收器回收

9. JS垃圾回收机制?(TODO)

### 7.原型和原型链是什么?

原型是一个prototype对象，用于表示类型之间的关系。原型对象的用途是为每个实例对象存储共享的方法和属性，它仅仅是一个普通对象而已。并且所有的实例是共享同一个原型对象，因此有别于实例方法或属性，原型对象仅有一份。
每个对象的__proto__指向创建它的构造函数`(注意指向的是构造函数的prototype a.__proto__ == A.prototype)`的prototype,而构造函数的prototype也有__proto__指向他的父辈或者是Object，当查找一个对象中不存在的属性时，会去它的__proto__、__proto__中的__proto__中进行寻找，直到找到或者是null为止


### 8. ===和==区别 [太多了记不住😭]
`===` 严格相等，会比较两个值的类型和值
`==`  抽象相等，比较时，会先进行类型转换，然后再比较值
`==` 规则如下
1. 首先看等号前后有没有NaN,如果存在NaN,一律返回false
2. 再看等号前后有没有布尔,有布尔就转换成数字
3. 接下来看双等号前后有没有字符串,有三种情况
  - 对象是对象,使用toString进行转换
  - 对方是数字,字符串转数字
  - 对方是字符串,直接比较
  - 其他返回false
4. 如果是数字,对方是对象,对象取valueOf进行比较,其余一律返回false
5. null,undefined不会进行类型转换,但它们俩相等

### 9. 封装一个XHR请求(TODO)
> 常问到ajax,axios,fetch的区别和实现,以及用promise封装

### 10. setTimeour和setInterval时间为什么不准
- setTimeout和setInterval中的时间参数并不是到时间就立即执行,而是到点将其回调事件加入事件队列.按照队列先进先出的性质，该回调事件到点之后是否能执行取决于是否属于队列首位，如果前头还有其他事件在等待，则不能按点执行。这并是导致事件等待执行时间出现误差的原因。
> **扩展** 此问题可以深入涉及浏览器原理  
浏览器包括浏览器进程,GPU进程、网络进程、插件进程、渲染进程
**浏览器进程**:负责控制浏览器除标签外的页面,包括地址栏、书签、前进后端按钮、以及负责与其他进程的协调工作,同时提供存储功能
**GPU进程**:负责整个浏览器界面渲染.
**网络进程**:负责发起和接收网络请求
**插件进程**:通过插件进程来隔离,以保证插件崩溃也不会对浏览器和页面造成影响
**渲染进程**:负责控制显示tab标签页内的所有内容,核心任务是将HTML、CSS、JS转为用户可以与之交互的网页,排版引擎Blink和JS引擎V8都是运行在改进程中,默认情况下,Chrome会为每个Tab标签页创建一个渲染进程

**渲染进程中的线程**
- **GUI渲染线程** 负责解析HTML和CSS、构建DOM树 CSSOM树 渲染树 和绘制页面,重绘重排也是在该线程执行
- **JS引擎线程** 一个tab页中只有一个JS引擎线程(单线程),负责解析和执行JS.**它和GUI渲染进程不能同时执行,只能一个一个来,如果JS执行过长就会导致阻塞掉帧**
- **计时器线程** 指setInterval和setTimeout,因为JS引擎是单线程的,所以如果处于阻塞状态,那么计时器就不会准了,所以需要单独的线程来负责计时器工作
- **异步http请求线程** XMLHttpRequest 连接后浏览器开的一个线程,比如请求有回调函数,异步线程就会将回调函数加入事件队列,等待JS引擎空闲执行
- **事件触发线程** 主要用来控制事件循环,比如JS执行遇到计时器,AJAX异步请求等,就会将对应的任务添加到事件触发线程中,在对应事件符合触发条件时触发,就把时间添加到待处理的队列的队尾,等待JS引擎处理

原文传送门👉[我是传送门🚪](https://juejin.cn/post/6991849728493256741)


### 11 深拷贝、浅拷贝
>  浅拷贝是创建一个新对象，这个对象有着原始对象属性值的一份精确拷贝。如果属性是基本类型，拷贝的就是基本类型的值，如果属性是引用类型，拷贝的就是内存地址 ，所以如果其中一个对象发生了改变,另一个对象(对象属性等)也会发生改变,基本类型改变不受影响(因为基本类型直接储存在栈中)
  - Object.assign()
  - ...rest运算符
  - Array.prototype.slice()
> 深拷贝是将一个对象从内存中完整的拷贝一份出来,从堆内存中开辟一个新的区域存放新对象,且修改新对象不会影响原对象。
  - JSON.parse(JSON.stringify()) 缺点:可以实现对象和数组的拷贝,但不能实现对function和正则的拷贝
  - lodash._cloneDeep()
  - 掘金原文传送门 👉[我是传送门🚪](https://juejin.cn/post/6844903929705136141)
  ```js
  //递归实现深拷贝,此处为个人简化版
  function isObject(target){
    const type = typeof target
    return target !== null &&(type === 'object' || type ==='function')
  }
  function getInit(target){
    //保留原型链上的数据
    const constructor = target.__proto__.constructor
    return Object.create(constructor.prototye)
  }

  function clone(target,map = new WeakMap()){
      //基础数据类型
      if(target == null) return target
      if(!isObject(target)) return target 
      if(target instanceof Date) return new Date(target)
      if(target instanceof RegExp) return new RegExp(target)

      const type = Object.prototype.toString().call(target).slice(8,-1);
      const cloneTaget = getInit(target)
      
      if(map.get(target)){
        return map.get(target)
      }
      map.set(target,cloneTaget)

      if(type == "Set"){
        target.forEach((ele,index)=>{
          cloneTarget.add(clone(ele),map)
        })
      }
      if(type == "Map"){
        target.forEach((ele,index)=>{
          cloneTarget.set(index,clone(ele,map))
        })
      }
      if(type == "Object"){
        const keys = Object.keys(target)
        keys.forEach((ele,index)=>{
          cloneTarget[ele] = clone(target[ele],map)
        })
      }
      if(type =="Array"){
        target.forEach((ele,index)=>{
          cloneTarget[index] = clone(target[index],map)
        })
      }

  }
  ```

### 13 扩展运算符：

```javascript
let outObj = {
  inObj: {a: 1, b: 2}
}
let newObj = {...outObj}
newObj.inObj.a = 2
console.log(outObj) // {inObj: {a: 2, b: 2}}
```

Object.assign():

```javascript
let outObj = {
  inObj: {a: 1, b: 2}
}
let newObj = Object.assign({}, outObj)
newObj.inObj.a = 2
console.log(outObj) // {inObj: {a: 2, b: 2}}
```

14. ### 打平数组,k控制和k不控制的
```js
//一次打平
function flat(arr) {
  let result = [];
  arr.forEach(item=>{
    if (Array.isArray(item)) {
      result = result.concat(arrayFlattern(item));
    } else {
      result.push(item);
    }
  });
  return result;
}
//k控制打平,采用闭包
```
```js
function flat1(arr,k){
  let counter = 0
  function flat(arr)(){
    let res = []
    arr.forEach(item=>{
      if(Array.isArray(item) && counter < k){
        res = res.concat(flat(item))
      }else{
        res.push(item)
      }
    })
  }
}
```

15. ### 手写reduce,filter,forEach,map
- **reduce**(fn,initialValue):intialValue可选 fn(previousValue,currentValue,currentIndex,array)
- **filter** 创建一个新数组,不会修改原数组,返回符合条件元素,参数callback(element,index,array),参数thisValue:执行callback时候的this
- **forEach(fn(currentValue,index,array),thisArg)** 改变原数组
- **map(fn(currentValue,index,arry),thisArg)**: 方法创建一个新数组，这个新数组由原数组中的每个元素都调用一次提供的函数后的返回值组成。
```js
//reduce实现,简化版,没有考虑数组为空的情况,使用hasOwnProperty解决
Array.prototye.myReduce = function(reducer,initValue){
  let hasInitial = arguments.length >1
  let ret = hasInitial ? initValue:this[0]
  for(let i = hasInitial?0:1;i<this.length;i++){
    if(!this.hashOwnproperty(i)) continue;
    ret = reducer.call(undefined,ret,this[i],i,this);
  }
  return ret 
}
```
传送门👉[知乎](https://zhuanlan.zhihu.com/p/356678016)

```js
//map实现
Array.prototype.map = function(cb,thisArg){
  if(! Array.isArray(this) || typeof cb!= 'function'){
    throw new Error('error')
  }
  let res = []
  for(let i = 0;i<this.length;i++){
    if(!this.hashOwnproperty(i)) continue;

    res[i] = cb.call(thisArg,this[i],i,this)
  }
  return res
}
```
```js
//forEach实现 和map相比只是少了返回的数组
//forEach是无法在内部打断的
Array.prototype.forEach = function(cb,thisArg){
  if(!typeof cb === "function") throw new Error("error")
  for(let i = 0 ;i<this.length;i++){
    if(!this.hashOwnproperty(i)) continue;
    cb.call(thisArg,this[i],i,this)
  }
}
```
```js
Array.prototype.filter = function(cb,thisArg){
  let res = []
  for(let i = 0;i<this.length;i++){
    if(!this.hashOwnproperty(i)) continue;
    res[i] = cb.call(thisArg,this[i],i,this)
  }
  return res
}
```
**总结:filter和map,reduce 不会改变原数组,所以cb需要有返回值用来创建新数组、forEach会改变原数组**


16. ### 手写bind,考虑new绑定
```js
  Function.prototype.myBind = function(asThis,...args1){
    let fn = function(this,...args2){
      return fn.call(this instanceof fn? this:asThis,...args1,...args2)
    }
    let fnNo = function(){}
    fnNo.prototype = object.create(fn.prototype)//不用new 因为不用执行构造函数

    return  fn
  }
```
17. ### curry 柯里化
>思路 curry要求我们返回一个函数,如果这个函数的参数列表等于原来函数的参数列表,则绑定最后一次执行的this且执行,如果参数列表长度小于原函数则返回一个函数,递归调用curried函数
```js

  function curry(fn){
    return function curried(...args1){
      if(args.length >= fn.length){
        return fn.apply(this,args1)
      }else{
        return function (...args2){
          return curried.apply(this,args2.concat(args1))
        }
      }
    }
  }

fucntion sum(a,b){
    return a + b;
}
let curriedSum = curry(sum);
alert(curriedSum(1)(2));
```


16. **防抖**
> 防抖: n秒内,如果有一次调用则取消之前的调用,重新计时
```js
    function debounce(fn,delay){
      let timmer = null
      return function(){
        let self = this; 
        let args = Array.prototype.slice.call(arguments)
        if(timer) clearTimeout(timer)
        timer = setTimeout(()=>{
          fn.apply(self,args)
        },delay)
    }
```

>     :第一次执行后的n秒内,如果还有执行则不调用
```js 
function throttle(fn,delay){
  let timer = null
  if(!timer){
    fn.apply(this,arguments)
    timer = setTimeout(()=>{
      timer = null
    },delay)
  }
}
```


18. 串行执行Promise和并行执行promise
> 其实js没有并行的概念,只是同时创建了promise同时执行,并行可以用佛forEach和promiseall 👉[原文传送门🚪](https://juejin.cn/post/6844903801296519182) 最后两种方式还不是很能理解
**串行执行,将上一个promise执行后的值,作为下一个promise参数传入执行,arr是个包含promise的数组**  

**方式一.reduce**
```js
//思路:在前一个promise的then里面去调用下一个promise,失败就调用Promise.resolve()终止
//因为promise创建即执行,所以需要封装一层

function delay(time){
  return new promise((resolve,reject)=>{
    console.log(`wait ${time}s`)
    setTimeout(()=>{
      console.log('execute');
      resolve()
    },time*1000)
  })
}

arr.reduce((prev,curr)=>{
  return prev.then(()=>delay(v),Promise.resolve)
})
```

**方式二. async 和await**
```js

  async function(){
    for(const v of arr){
      await delay(v)
    }
  }()
```

**方式三.普通循环**
其实仔细想想方式1的本质是使用一个中间变量（上一次执行结果）来保存链式Promise, 那我们举一反三， 换别的循环也可以实现
```js
let p = promise.resolve()
for(const v of arr){
  p = p.then(()=>{delay(i)})
}
```
**方式四.递归**
```js
function dispatch(i,p=Promise.resolve()){
  if(!arr[i]) return Promise.resolve()
  return p.then(()=> dispatch(i+1,delay(arr[i])))
}
```
**方式五.for wait of(迭代器的异步调用,之后会讲)**
```js
function createAsyncInterable(arr){
  return {
    [Symbol.asyncInterator](){
      return {
        i:0,
        next(){
          if(this.i<arr.length){
            return delay(arr[this.i].then(()=>{value:this.i++,done:false}))
          }
        }
      }
    }
  }
}
(async function(){
  for await(i of createAsyncIterable(arr)){
    
  }
})()
```
***方式6.generator(其实相当于async和await)**
```js
function *gen(){
  for(const v of arr){
    yield delay(v)
  }
}
function run(gen){
  const g = gen()
  function next(data){
    const result = g.next(data)
    if(result.done) return result.value
    result.value.then(function(data){
      next(data)
    })
  }
    next()
}

run(gen)
```

### 19. koa洋葱模型(没了解过)


### 20.render函数
```html
{
  tag: 'DIV',
  attrs:{
  id:'app'
  },
  children: [
    {
      tag: 'SPAN',
      children: [
        { tag: 'A', children: [] }
      ]
    },
    {
      tag: 'SPAN',
      children: [
        { tag: 'A', children: [] },
        { tag: 'A', children: [] }
      ]
    }
  ]
}
把上诉虚拟Dom转化成下方真实Dom
<div id="app">
  <span>
    <a></a>
  </span>
  <span>
    <a></a>
    <a></a>
  </span>
</div>
```
```js
// 真正的渲染函数
function _render(vnode) {
  // 如果是数字类型转化为字符串
  if (typeof vnode === "number") {
    vnode = String(vnode);
  }
  // 字符串类型直接就是文本节点
  if (typeof vnode === "string") {
    return document.createTextNode(vnode);
  }
  // 普通DOM
  const dom = document.createElement(vnode.tag);
  if (vnode.attrs) {
    // 遍历属性
    Object.keys(vnode.attrs).forEach((key) => {
      const value = vnode.attrs[key];
      dom.setAttribute(key, value);
    });
  }
  // 子数组进行递归操作
  vnode.children.forEach((child) => dom.appendChild(_render(child)));
  return dom;
}


```
### 21. 列表转成树形结构 https://juejin.cn/post/6968713283884974088

### 22. Ajax,Fetch、XMLHttpRequest,axios

### 23.事件循环

###


## 二、ES6
> **new的实现原理**
new操作符的实现步骤如下：
     *  创建一个空对象 a
     *  设置这个对象的原型 a.__proto__
     *  将fn的this绑定为这个空对象,并执行构造函数(赋值的过程this.a = a)
     *  判断返回类型,如果**构造函数**内有返回值我们是返回构造函数的属性,如果没有我们返回我们创建的空对象
```js
    /**
     * 例子: let a = new A();
     * 1. 创建一个空对象 a
     * 2. 设置这个对象的原型 a.__proto__
     * 3. 将fn的this绑定为这个空对象,并执行(赋值的过程this.a = a)
     * 4. 判断返回类型,如果构造函数内有返回值我们是返回构造函数的属性,如果没有我们返回我们创建的空对象
     */
    function myNew(){
      let a = {};
      let args = object.prototype.slice.call(argumetns)
      let fn = args.shift();
      a.__proto__ = fn.prototye;
      const ret = fn.apply(a,args)
      return ret || a
    }

    /**
     * return的时候为什么需要判断,因为如果fn 返回一个对象,则按照这个对象构造
     */
    function Product(name, price) {
        this.name = name;
        this.price = price;
        return {
            name: this.name,
            color: 'red'
        }
    }
    var newProduct = new Product('name', 'price');
    console.log(newProduct)//{ name: 'name', color: 'red' }
```

**[扩展]**
  - 每个对象都有constructor属性,指向这个对象的来源(类似构造函数)
  - **这个constructor属性** 是来自__proto__
  - js中所有的对象都具有__proto__属性,在这个属性上封装了这个对象的方法,这个属性被称为隐式原型
  - function对应的prototype,被称为显示原型


> **promise的相关实现**
  **promise常见考点,手写promise,promiseAll,promise实现并发数量控制**
  - **promise.any:当其中一个promise成功,就返回成功的值**
  - **promise.race:当其中任意一个promise成功回失败后,父promise马上也会用子promise的成功返回值或失败详情作为参数调用父promise绑定的响应句柄,并返回该promise对象**

### promise实现
>这里给出了最终版答案,且这版的resolvePromise的判断细节不完整,个人觉得面试写到这个程度足够了,如果想了解如果实现,可以移步掘金原文 传送门➡️[掘金](https://juejin.cn/post/6945319439772434469),只有这篇是使用了微任务队列,其余大部分都是使用setTimeout()方法去模拟微任务队列,如果小伙伴们有更好的资源可以替换

```javascript
const PENDING = "PENDING"
const FULFILLED = "FULFILLED"
const REJECTED = "REJECTED"

class myPromise(){

  constructor(executor){
    // executor 是一个执行器，进入会立即执行
    // 并传入resolve和reject方法
    // 这个executor中的括号内部的箭头函数,相当于我们new promise((resolve,reject)=>{})3
    try{
      executor(this.resolve,this.reject)
    }catch(err){
      this.reject(err)
    }
  }
  //当前的status
  status = PENDING
 //失败成功的返回值
  value = null
  reason = null
  //储存回调函数
  onFulfilledCallbacks = []
  onRejectedCallbacks = []

  resolve = (value)=>{
    if(this.status === PENDING){
      this.status = FULFILLED
      this.value = value
      while(this.onFulfilledCallbacks.length){
        this.onFulfilledCallbacks.shift()(value)
      }
    }
  }
  reject = (reason) =>{
    if(this.status === PENDING){
      this.status === REJECTED
      this.reason = reason
      while(this.onRejectedCallbacks.length){
        //执行回调函数
        this.onRejectedCallbacks.shift()(reason)
      }
    }

    then(onFulfilled,onRejected){
      const realOnFulfilled = typeof onFulfilled === 'function'?onFulfilled:value=>value
      const realOnRejected = typeof onRejected === 'function' ? onRejected:reson=>{throw reason}

      const promise2 = new myPromise((resolve,reject)=>{
        const fulfilledMicrotask = ()=>{
            queueMicrotask()=>{
              try{
                const x = realOnFulfilled(this.value)
                resolvePromise(promise2,x,resolve,reject)
              }catch(error){
                reject(error)
              }
            }
        }


        const rejectedMicrotask = ()=>{
            queueMicrotask()=>{
              try{
                const x = rejectedMicrotask(this.value)
                resolvePromise(promise2,x,resolve,reject)
              }catch(error){
                reject(error)
              }
            }
        }


        if(this.status === FULFILLED){
          fulfilledMicrotask()
        }else if(this.status === REJECTED){
          rejectedMicrotask()
        }else if(this.status === PENDING){
          onFulfilledCallbacks(fulfilledMicrotask)
          onRejectedCallbacks(onRejectedCallbacks)
        }


      })
      return promise2
    }
    static resolve(para){
      if(para instanceof myPromise){
        return para
      }

      return new Promise(resolve=>{
        resolve(para)
      })
    }

    static reject(reason){
      return new myPromise((resolve,reject) =>{
        reject(reason)
      })
    }

  }
}

function resolvePromise(promise2,x,resolve,reject){
  if(promise2 === x){
    return reject(new Typeerror('Chaining cycle detected for promise #<Promise>'))
  }
  //实现链式调用
  if(x instance myPromise){
    x.then(resolve,reject)
  }else{
    resolve(x)
  }
}
```

### promise.all实现
> promise.all接收一个数组,返回一个promise,promise中的value是一个包含执行结果的数组
```js
  function promiseAll(promises){
    return new Promise((resolve,reject)=>{
      let resolverCounter = 0
      let promiseNum = promises.length
      let result = new Array(promise.length)

      for(let i = 0;i<promiseNum;i++){
          promises[i].then(val=>{
            resolverCounter++;
            result[i] = val
            if(resolverCounter == promiseNum){
              return result
            }
          },err=>{
            return reject(err)
          })
      }
    })
  }
```
### promise.race实现
> promise.race和all一样接受一个promises数组,promise中的value是第一个完成promise的结果,有一个完成则完成

```js
function race(promises){
  return new Promise((resolve,reject)=>{
    for(let i = 0;i<promises.length;i++){
      promises[i].then(val=>{
        return resolve(val)
      },err=>{
        return reject(err)
      })
    }
  })
}
```

3. promise 某条高频面试原题：实现有并行限制的Promise调度器 传送门[掘金](https://juejin.cn/post/6854573217013563405)

```js
// JS实现一个带并发限制的异步调度器Scheduler，保证同时运行的任务最多有两个。完善下面代码的Scheduler类，使以下程序能够正常输出：
class Scheduler {
  add(promiseCreator) { ... }
  // ...
}
   
const timeout = time => new Promise(resolve => {
  setTimeout(resolve, time);
})
  
const scheduler = new Scheduler();
  
const addTask = (time,order) => {
  scheduler.add(() => timeout(time).then(()=>console.log(order)))
}

addTask(1000, '1');
addTask(500, '2');
addTask(300, '3'); 
addTask(400, '4');

// output: 2 3 1 4

//思路:使用队列去并判断队列大小去执行队列控制

class Scheduler{
  constructor(){
    this.queue = []
    this.maxCount =2;
    this.runCounts = 0
  }
  add(promiseCreator){
    this.queue.push(promiseCreator)
  }

  taskStart(){
    for(let i = 0;i<this.maxCount;i++){
      this.request()
    }
  }
  request(){
    if(!this.queue || !this.queue.length||this.runCounts >= this.manxCount){
      return 
    }
    this.runCounts++;

    this.queue.shift()().then(()=>{
      this.runCounts--;
      this.request();
    })
  }
}

const timeout = time => new Promise(resolve => {
  setTimeout(resolve, time);
})
  
const scheduler = new Scheduler();
  
const addTask = (time,order) => {
  scheduler.add(() => timeout(time).then(()=>console.log(order)))
}
  
  
addTask(1000, '1');
addTask(500, '2');
addTask(300, '3');
addTask(400, '4');
  
scheduler.taskStart()

```
3. proxy and reflect
  - 大多都是在问vue的响应式

5. iterator和for..of
- iterator是个供for..of消费的接口,
- iterator返回一个迭代器,可以通过next()方法来执行
- 为了让普通对象可迭代,可以为普通对象添加一个名为Symbol.iterator,并定义一个next方法,返回一个包含done和value的对象
```js
let range = {
    from: 1,
    to: 5,
  
    [Symbol.iterator]() {
      this.current = this.from;
      return this;
    },
  
    next() {
      if (this.current <= this.to) {
        return { done: false, value: this.current++ };
      } else {
        return { done: true };
      }
    }
  };
  
  for (let num of range) {
   console.log(num)
  }
  ```
6. generator函数
https://juejin.cn/post/6844903593976266760

7. async和await,原文更容易理解和记忆 传送门👉[掘金](https://juejin.cn/post/7007031572238958629)
  - await后接promise会有阻塞效果,不接promise有可能阻塞不到后面的执行
  - **个人理解** await后面如果跟个promise,await下面的代码就会像是promise.then() then中的代码
  - async函数执行完会返回一个promise(promise的状态取决于async函数内部),但是值为undefined,如果async内有返回值,则promise传出的值是这个返回值
  - generator函数会返回一个迭代器,这个迭代器有next()方法,调用返回一个含有value和done属性的对象
  - yield后面接函数的话,到了对应暂停点yield,会马上执行此函数,并且该函数执行的返回值,会被当作此暂停点对象的value
  ```js
  function fn(num) {
    return new Promise(resolve => {
        setTimeout(() => {
          resolve(num)
        }, 1000)
      })
  }
  function* gen() {
    yield fn(1)
    yield fn(2)
    return 3
  }
  const g = gen()
  console.log(g.next()) // { value: Promise { <pending> }, done: false }
  console.log(g.next()) // { value: Promise { <pending> }, done: false }
  console.log(g.next()) // { value: 3, done: true }

  const g = gen()
  const next1 = g.next()
  next1.value.then(res1 => {
    console.log(next1) // 1秒后输出 { value: Promise { 1 }, done: false }
    console.log(res1) // 1秒后输出 1

    const next2 = g.next()
    next2.value.then(res2 => {
      console.log(next2) // 2秒后输出 { value: Promise { 2 }, done: false }
      console.log(res2) // 2秒后输出 2
      console.log(g.next()) // 2秒后输出 { value: 3, done: true }
    })
  })

  ```
  - generator函数可以用next方法传参,第一次next传参没有用的,只有第二次才有用(相当于第一个值必须是从函数内部到外部的,传递的参数将作为yield的返回值)
  **async和await实现**
  ```js
  function fn(nums){
      return new Promise(resolve=>{
        setTimeout(()=>{
          resolve(nums*2)
        },1000)
      })
  }
  // 
  function* gen(){
      const num1 = yield fn(1)
      const nums2 = yield fn(num1)
      const nums3 = yiled fn(num2)
      return nums3
  }
  //generatortoAsync期望返回一个function,且这个asynFunction执行后返回一个promise
  function generatorToAsync(generatorFn){
    return function(){
      return new Promise((resolve,reject)=>{
        const gen = generatorFn.apply(this,arguemtns)//gen有可能传递参数,this指向调用async函数的作用域 这个相当于是generator返回一个迭代器

        function go(key,arg){
          let res
          try{
            //gen在之前apply的时候被赋值,此时gen应该是一个迭代器对象,定义了next()方法
            // key是传入的值,一般为next,就是调用next方法的返回值
            res = gen[key](arg)//有可能执行返回reject状态的promise,
          }catch(err){
            return reject(err)//报错走catch,直接reject
          }
          const{value,done} = res

          if(done){
            return resolve(value)
          }else{
            //使用promise.resolve()对value的值进行了过滤,value可能是常量,promise,promise有可能是成功或失败,go将第一个promise的返回值传入了第二个promise,不传递可以不传val
            return Promise.resolve(value).then(val=>go('next',val),err=>go('throw',err))
          }
        }
        go("next")
      })
    }
  }
  const asyncFn = generatorToAsync(gen)
  
  ```
8. ES Module 和CommonJS 原文👉[掘金](https://juejin.cn/post/6994224541312483336)
- Commonjs 和 Es Module 有什么区别 ？
- Commonjs 如何解决的循环引用问题 ？
- 既然有了 exports，为何又出了 module.exports ? 既生瑜，何生亮 ？(不是很明白缺陷)
- require 模块查找机制 ？
- Es Module 如何解决循环引用问题 ？
- exports = {} 这种写法为何无效 ？
- 关于 import() 的动态引入 ？
- Es Module 如何改变模块下的私有变量 ？

9. 前端路由实现 👉[掘金](https://juejin.cn/post/6844903589123457031)

10. 算法45度打印数组


11. setTimeout模拟setInterval
> 为什么要setTimeout去模拟setInterval? 因为setInterval是间隔一定的时间向任务队列添加时间,如果之前添加的任务还没执行完则不会添加了,setTimeout会稳定添加

```js
let timer = null
function interval(func,delay){
  let inner = function(){
    func();
    timer = setTimeout(inner,delay)
  }
  timer = setTimeout(inner,delay)
}

```
12. Super关键词
- super既可以当作函数使用,也可以当作对象使用,这两种情况下,它的用法完全不同
- super作为函数调用时代表父类的构造函数 **(父类的constructor)**
```js
  class A{}
  class B extends A{
    constructor(){
      super()
    }
  }
```
- super虽然代表了父类A的构造函数,但是返回的是子类B的实例,即super内部的this指的是B,因此super()在这里相当于A.prototye.constructor.call(this),对应到ES5继承就是foo.call(this,args)
- super作为对象时,在普通方法中指向**父类的原型对象(A.prototype)**,在静态方法中指向父类
- ES6 class constructor内的属性是相当于定义在实例属性上的,constructor是定义在prototype上的
```js

class A{
  p(){
    return 2;
  }
}
class B extends A{
  constructor(){
    super();
    console.log(super.p())//2
  }
}
```
