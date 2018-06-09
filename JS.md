# Ajax
Ajax的原理简单来说通过XmlHttpRequest对象来向服务器发异步请求，从服务器获得数据，然后用javascript来操作DOM而更新页面。

### 使用 Promise 实现一个 Ajax 请求
```js
const ajax = function (opt) {
  const promise = new Promise(function(resolve, reject) {
    let xhr = window.XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject('Microsoft.XMLHttp')
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4) {
        if (xhr.status === 200 || xhr.status === 304) {
          resolve(xhr.responseText)
        } else {
          reject(new Error(this.statusText))
        }
      }
    }
    let type = opt.type.toUpperCase(),
        data = opt.data,
        url = opt.url
        dataArr = []
    for (let k in data) {
      dataArr.push(k + '=' + data[k])
    }
    if (type === 'GET') {
      url = url + '?' + dataArr.join('&')
      xhr.open(type, url.replace(/\?$/g, ''), true)
      xhr.send()
    }
    if (type === 'POST') {
      xhr.open(type, url, true)
      xhr.setRequsetHeader('Content-Type', 'application/x-www-form-urlencoded')
      xhr.send(dataArr.join('&'))
    }
  })

  return promise
}
```

### readyState 五中状态的含义
- 0  未初始化状态：此时，已经创建了一个XMLHttpRequest对象
- 1	 准备发送状态：此时，已经调用了XMLHttpRequest对象的open方法，并且XMLHttpRequest对象已经准备好将一个请求发送到服务器端
- 2	 已经发送状态：此时，已经通过send方法把一个请求发送到服务器端，但是还没有收到一个响应
- 3	 正在接收状态：此时，已经接收到HTTP响应头部信息，但是消息体部分还没有完全接收到
- 4	 完成响应状态：此时，已经完成了HTTP响应的接收

# 闭包
可以把闭包理解为就是能够读取其他函数内部变量的函数,因为js中,只有函数内部的子函数才能读取局部变量，因此也可以把闭包定义在一个函数内部的函数。所以闭包本质就是将函数内部和函数外部连接起来的一座桥梁"。

**作用：** 创建私有变量
```js
let m = (function () {
  let i = 1
  return {
    geti () {
      return i
    },
    seti (newVal = 1) {
      i = newVal
    }
  }
})()
```

# 事件模型
JS 中有两种事件模型，分别是 DOM0 和 DOM2。

**DOM0**  
`onclick` 

**DOM2**  
DOM2 的时候就有了**事件捕捉**和**事件冒泡**。顺便提一下事件流，事件流就是**事件捕获 --> 目标事件 --> 事件冒泡**。

**事件捕获的具体流程** (事件冒泡则相反)
```
window -> doucument -> html -> body -> ... -> 目标事件
```

**DOM2 中的添加和删除事件**  
使用 `addEventListener` 和 `removeEventListener` 来添加和删除事件。

**然后利用事件冒泡还可以实现事件委托**  
使用 event.target 来实现

优点：  
- 减少事件注册，节省内存。比如
  - 在 ul 上代理所有 li 的 click 事件。
- 简化了 dom 节点更新时，相应事件的更新。比如
  - 不用在新添加的 li 上绑定 click 事件。
  - 当删除某个 li 时，不用移解绑上面的 click 事件。

# 作用域链
**解释一下作用域链**  
内层的函数可以访问外层函数的变量，但是外层的函数无法访问内部函数的变量。

# this
- 如果在调用函数的时候使用了 new，那么函数内的 this 就是一个全新的对象
- 使用 call、apply、bind 的时候，this 就是传入的对象
- 当函数作为对象里的方法被调用的时候，this 指向这个对象
- 如果不符合这些规则的话，this 就指向全局，游览器中就指向 window，**但是在严格模式下，this 的值为 undefined**
- 使用箭头函数的时候，this 表示上下文

# prototype  和 proto 的关系
- `prototype` 指向的是方法的原型
- `__proto__` 指向的是该对象的构造函数的原型对象
- 顺便提一下 `constructor` ：指向构造函数

# 继承
参考：[JS实现继承的几种方法](https://zhuanlan.zhihu.com/p/25578222)
### 1.原型链实现继承
实现：将 Child 的 prototype 设置为 new Parent() 也就是 Child 的 prototype 为 Parent 的实例。  
缺点：
1. 子类型无法给父类型传递参数
2. Child.prototype 定义方法的时候，必须写在 new Parent 之前

### 2.构造函数实现
实现：在创建 Child 构造函数的时候使用 Parent.call(this)（这里的 this 是一个全新的对象，因为调用构造函数的时候会使用 new）  
缺点：没有原型，且每次生成 Child 实例的时候都需要执行一遍 Parent 函数，无法复用。

### 3.组合式继承
实现：同时使用方法1和方法2  
优点：因为同时使用了原型和构造函数，这个即实现了方法的复用，又保证每个实例都有自己的属性。  
缺点：Parent 构造函数会被调用两次，一次是在子类型构造函数的内部，一次是在创建 Child 原型的时候。

### 4. 使用 Object.creat() 实现原型继承
使用构造函数的时候发现，在创建 Child 原型的时候调用 Parent 构造函数只是为了获得 Parent 的原型，所以会想到让 Child.prototype = Parent.prototype，但是样子子的话，如果改变 Child.prototype 就会同时改变 Parent.prototype（浅复制）。所以这个时候就需要用到 Object.creat() 来创建 Child.prototype 的构造函数的原型，这样就完美了。

### 5.ES6 继承
使用 `extends` 关键字实现继承。具体可以百度 `ES6 阮一峰 class`

# 数组去重
参考：https://github.com/lifesinger/blog/issues/113

### 直接想到的方案
```js
function unique (arr) {
  let ret = [], item
  for (let i = 0; i < arr.length; i++) {
    item = arr[i]
    if (ret.indexOf(item) === -1) {
      ret.push(item)
    }
  }
  return ret
}
```

### 优化方案
因为 IE6-IE8 不支持 indexOf 所以需要判断是否有 indexOf

```js
let _indexOf = [].indexOf ? function (ret, item) {
  return ret.indexOf(item)
} : function (ret, item) {
  for (let i = 0; i < ret.length; i++) {
    if (ret[i] === item) {
      return i
    }
  }
  return -1
}

function unique (arr) {
  let ret = [], item
  for (let i = 0; i < arr.length; i++) {
    item = arr[i]
    if (_indexOf(ret, item) === -1) {
      ret.push(item)
    }
  }
  return ret
}
```

### 最简单的方案：使用 ES6 中的 Set
参考：http://es6.ruanyifeng.com/#docs/set-map

```js
[...new Set(array)]
```

# 回调函数
### Promise
Promise 简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。有了Promise对象，就可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数。此外，Promise对象提供统一的接口，使得控制异步操作更加容易。

**Promise.all() 和 Promise.race()**  
使用 Promise.all() 和 Promise.race() 都可以将多个 Promise 实例包装成一个新的实例，区别在与：
- Promise.all() 当所有 Promise 的状态都变成的成功，才会返回一个所有返回值组成的一个数组给回调
- Promise.race() 当有一个 Promise 的状态成功，就返回那个成功的值给回调
- Promise.all() 和 Promise.race() 发生 rejected 的时候就会立刻返回 rejected，如果 Promise 设置了自己的 catch，则不会触发 Promise.all() 的 catch。


**实现 Promise.finally()**  
```js
Promise.prototype.finally = function (callback) {
  // P 指的是 Promise 对象（constructor） 指向构造函数
  let P = this.constructor
  // then 返回的是新的 Pormise 实例（不是原来那个实例）
  return this.then(
    value  => P.resolve(callback()).then(() => value),
    reason => P.resolve(callback()).then(() => {throw reason})
  )
}
```

**Promise,async/await 三者的区别，以及为什么用 Promise，然后又变成了async/await**  
参考：https://cnodejs.org/topic/58e4914e43ee7e7106c13541  

首先 Pormise 是异步编程的一种解决方案，可以避免层层嵌套的回调地狱。

然后 async/await 是异步编程的新方式，且它是基于 Promise 实现的（await 后面是一个 Promise 对象，如果不是，会被转成一个立即 resolve 的 Promise 对象），然后 async 返回的也是一个 Promise 对象。

那么为什么用 async/await 会更好呢，看例子：

使用Promise是这样的:

```js
const makeRequest = () =>
  getJSON()
    .then(data => {
      console.log(data)
      return "done"
    })

makeRequest()
```
使用Async/Await是这样的:

```js
const makeRequest = async () => {
  console.log(await getJSON())
  return "done"
}

makeRequest()
```
很显然，async 不需要写 then，不需要写匿名函数来处理 resolve 的值，还避免了嵌套代码。

### async 的缺点
当需要检测错误的使用，需要使用 try...catch

# 深拷贝
### JSON.parse(JSON.stringify(obj))
最简单，但是不能复制函数（不能被 JSON.stringify 的会被忽略），且原型链丢失。

### 递归实现
递归的解决方式是，只要是数组或者对象，那就递归，否则就返回。
```js
// 递归深拷贝
function clone (val) {
  let newVal
  if (val instanceof Array) {
    newVal = []
    let i = val.length
    while (i--) {
      newVal[i] = clone(val[i])
    }
    return newVal
  } else if (val instanceof Object) {
    newVal = {}
    for (const key in val) {
      if (val.hasOwnProperty(key)) {
        newVal[key] = clone(val[key])
      }
    }
    return newVal
  } else {
    return val
  }
}
```

# this
- 当使用 apply call bind 函数的时候，this 是传入的对象
- 当 this 被 new 关键词创建的时候，this 是一个全新的对象
- 箭头函数的时候，this 是上下文
- 对象里的函数在被对象引用的时候是指向引用它的对象
- 不符合上述规则的时候，则指向全局，游览器里面则指向 window，在严格模式下则为 undefined

# arguments
### arguments 是数组吗
arguments 不是一个数组，它是一个类数组的对象。Array.isArray 返回 false。

### 如何转为数组
```js
// 方法1
Array.prototype.slice.apply(arguments)

// 方法2
Array.prototype.concat([], arguments)
```
### 类数组和数组的区别
类数组可以拥有 length 属性，以及索引元素。但是没有别的任何 Array 的属性。

# push, pop, shift, unshift
shift()方法：移除数组中的第一项并返回该项  
push()方法：从数组末端添加项

unshift()方法：在数组的前端添加项  
pop()方法：从数组末端移除项

# 字符串反转
方法1：
```js
str.split('').reverse().join('')
```

方法2：
```js    
function reverse (str) {
  let ret = '', i = str.length - 1
  while (i > -1) {
    ret += str[i]
    i--
  }
  return ret
}
```

# map 和 forEach
如果需要返回新的数组，那么就使用 map，不需要返回就用 forEach。（都不会改变数组本身）

# slice splice split 
### slice
slice 用来创建新的数组，**不会改变原来的数组**。接受两个参数，也就是开始截取的位置和结束截取的位置。当只传递一个参数的时候，截取到最后。

### splice
**强大的数组操作的方法**，*注意：是操作数组的方法，也是是会改变数组本身！*，返回被删除的元素组成的数组。

- 删除数组中的元素： `splice(0, 2) => 从下标 0 开始删除，删除 2 个元素，并返回被删除的元素组成的数组`
- 插入元素：`splice(2, 0, item1, item2) => 从下标 2 的元素开始 删除 0 个元素，在后面插入 item1、item2`

### split、join
split 用来分割字符串变为数组，join 用来将数组拼接成字符串。

# data-x
用来自定义属性，通过 dataset 可以添加和获取。当然也可以通过 getAttribute 、setAttribute 获取和设置。

# 实现 bind()、call()、apply()
参考：https://www.jianshu.com/p/6a1bc149b598

### 实现一个 apply 方法
> 思路：apply 是用来调用一个方法的，第一个参数改变方法中调用的 this 的值，第二个参数是数组，是用来传给方法的参数。

1. 先在传入的对象中添加一个方法等于调用的方法
2. 然后调用这个方法
3. 删除这个方法

```js
Function.prototype.myApply = function (obj) {
  let context = obj || window
  context.fn = this
  context.fn()
  delet context.fn
}
```

这样就实现了基本的调用，然后再用 eval 函数来拼接传入 fn 的参数，就 ok 了。

```js
Function.prototype._apply = function (obj) {
  let context = obj || window
  
  // 给对象添加一个方法
  // 因为 myApply 是对象中的一个方法，所以 this 指向调用它的对象（函数也是对象）
  // 使用 ES6 的 Symbol 来定义一个唯一值
  let fn = Symbol()
  context[fn] = this
  let str = '', arg = arguments[1]
  console.log('arg', arg)
  if (!arg) {
    return context[fn]()
  }
  for (let i = 0; i < arg.length; i++) {
    str += arg[i] + ','
  }
  let temp = 'context[fn](' + str.replace(/,$/g, '') + ')'
  let returnVal = eval(temp)
  delete context[fn]
  return returnVal
}
```

### 实现一个 bind 方法
如果不让使用 apply 的话就先实现一个 apply 吧。
```js
Function.prototype.bind = Function.prototype.bind || function (context) {
  let me = this
  let args = Array.prototype.slice.apply(arguments).slice(1)
  return function () {
    let finallyArgs = args.concat(Array.prototype.slice.apply(arguments))
    return me.apply(context, finallyArgs)
  }
}
```

# slice、substr、substring 的区别
### slice
slice() 方法可从已有的数组、字符串中返回选定的元素。  
语法：`str.slice(start, end)`

### substr 和 substring
substr 和 substring 是用来提取字符串的。区别是 substr 的第二个参数是取出的字符数，而 substring 第二个参数和 slice 一样，是要取出字符串的最后一个字符后面那个字符的索引。

简单的来说就一句话：substr 是从下标开始取出指定数目的字符,而 substring 是取出从下标（参数1）开始到下标（参数2）之间的字符（不包括下标为参数2的字符）。
```js
let str = '12345'
console.log(str.substr(2, 3)) // "345"
console.log(str.substring(2, 3)) // "3"
```

# 获取元素的页面坐标
使用 dom.getBoundingClientRect() 来获取元素距离上下左右的距离。就可以计算出元素位于页面的坐标。

# clientWidth、offsetWidth、scrollWidth 的区别
- clientWidth = width(可视区) + padding
- offsetWidth = width(可视区) + padding + border
- scrollWidth = width(内容区)　

# setTimeout() 的原理
setTimeout 方法包含两个参数，第一个参数为一个函数，第二个参数为以毫秒为单位的时间。该方法的实际作用即：在一定时间之后，把一个函数加入消息队列末尾。如果这个时间点消息队列中还存在其他消息，那么该函数会在排在他之前的消息都执行完之后再开始执行。

# ES6 class 类的原理
class 就是一个语法糖，让对象原型的写法更加清晰。里面的 `constructor` 方法对应的就是 ES5 中的构造函数，而 class 里面的方法则是定义了构造函数原型上内容。

# innerHTML 和 innerText 的区别
- innerHTML 指的是从对象的起始位置到终止位置的全部内容，包括 Html 标签。
- innerText 指的是从起始位置到终止位置的内容，但它去除 Html 标签。

# var, let, const 区别
- var 声明的变量，其作用域为该语句所在的函数内，且存在变量提升现象，可以重复声明
- let 会产生块级作用域，不会造成变量提升，无法重新声明(但可以重新赋值)
- const
  - 是常量，若是基本数据类型，具有不变性(无法重新赋值改动)
  - **引用值可以调整内部值！！**(可能设计的时候没有考虑周全!)


# (a == 1 && a == 2 && a == 3) 是否有可能为 true
参考：https://www.cnblogs.com/baizhanshi/p/4604257.html

如果一个值是对象，另一个值是数字或字符串，则将对象转换为原始值，然后再进行比较。对象通过 toString() 方法或 valueOf() 方法转换为原始值。

这个时候我们给对象定义一个 toString 方法，就可以了。
```js
const a = {
  i: 1,
  toString: function () {
    return a.i++;
  }
}
if(a == 1 && a == 2 && a == 3) {
  console.log('Hello World!');
}
```

# 为什么 typeof null 为 Object
从逻辑角度来看，null值表示一个空对象指针，而这正是使用typeof操作符检测null值时会返回“object”的原因。实际上应该是一个历史遗留的BUG。

# JS数据类型有哪些
- 基本类型：number, string, boolean, null, undefined, symbol
- 引用类型：Object, Array, Function

基本类型存放在栈区，引用类型保存的是一个在堆内存中的地址（可以拓展到深复制）。

# 统计单词出现个数
```js
function countWord (str, item) {
  let arr = str.split(' '), counts = 0
  arr.forEach(val => {
    if (val === item) {
      counts++
    }
  })
  return counts
}
```

# 获取 class="a" 的所有 div 元素
方法1：
```js
let div = document.querySelectorAll('div[class="a"]')
```

方法2：
```js
let div = document.querySelectorAll('div.a')
```

方法3：当游览器(IE8以下)不支持 `querySelectorAll`
```js
var list = document.getElementsByClassName('bo-dropdowm-menu');
var rel  = [];
var i;

for (i = 0; i < list.length; ++i)
    {
        if (list[i].tagName === 'DIV') {
            rel.push(list[i]);
        }
    }

// 这个就是你想要的结果了
console.log(rel);
```

# 添加删除元素
`element.appendChild()` 和 `element.removeChild()`