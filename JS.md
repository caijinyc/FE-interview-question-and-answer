# JS
# Ajax
Ajax的原理简单来说通过XmlHttpRequest对象来向服务器发异步请求，从服务器获得数据，然后用javascript来操作DOM而更新页面。

### 手写 Ajax
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

#### readyState 五中状态的含义
- 0  未初始化状态：此时，已经创建了一个XMLHttpRequest对象
- 1	 准备发送状态：此时，已经调用了XMLHttpRequest对象的open方法，并且XMLHttpRequest对象已经准备好将一个请求发送到服务器端
- 2	 已经发送状态：此时，已经通过send方法把一个请求发送到服务器端，但是还没有收到一个响应
- 3	 正在接收状态：此时，已经接收到HTTP响应头部信息，但是消息体部分还没有完全接收到
- 4	 完成响应状态：此时，已经完成了HTTP响应的接收


# 跨域
#### JSONP
**原理：**  
利用 script 标签实现，因为不管哪个网站上的 js 文件，只要引入了就可以去运行，所以不受同源策略的影响。

**实现：**  
当需要通讯的时候，本地创建一个 script 元素，地址指向 api 网址，并提供一个回调函数来接受数据（与后端约定函数名）。

#### CORS 跨域资源共享
需要在服务器端设置响应头

#### PostMessage

# 闭包
闭包是函数和声明该函数的词法环境的组合。

**作用：** 创建私有变量
```js
let m = function () {
  let i = 1
  return funcion () {
    return {
      geti () {
        return i
      },
      seti (newVal) {
        i = newVal
      }
    }
  }
}
```

# 事件模型
JS 中有两种事件模型，分别是 DOM0 和 DOM2。

**DOM0**  
`onclick` 

**DOM2**  
DOM2 的时候就有了**事件捕捉**和**事件冒泡**。顺便提一下事件流，事件流就是**事件捕获 --> 目标事件 --> 事件冒泡**。

**事件捕获的具体流程**  
```
window -> doucument -> html -> body -> ... -> 目标事件
```
事件冒泡则相反

**DOM2 中的添加和删除事件**  
使用 `addEventListener` 和 `removeEventListener` 来添加和删除事件。

**然后利用事件冒泡还可以实现事件捕获**  
使用 event.target 来实现

# 作用域链
**解释一下作用域链**  
内层的函数可以访问外层函数的变量，但是外层的函数无法访问内部函数的变量。

# this
- 如果在调用函数的时候使用了 new，那么函数内的 this 就是一个全新的对象
- 使用 call、apply、bind 的时候，this 就是传入的对象
- 当函数作为对象里的方法被调用的时候，this 指向这个对象
- 如果不符合这些规则的话，this 就指向全局，游览器中就指向 window，**但是在严格模式下，this 的值为 undefined**
- 使用箭头函数的时候，this 表示上下文

# prototype  和 __proto__ 的关系
- `prototype` 指向的是方法的原型
- `__proto__` 指向的是该对象的构造函数的原型对象
- 顺便提一下 `constructor` 指向构造函数

# 继承
参考：[JS实现继承的几种方法](https://zhuanlan.zhihu.com/p/25578222)
#### 1.原型链实现继承
实现：将 Child 的 prototype 设置为 new Parent() 也就是 Child 的 prototype 为 Parent 的实例。  
缺点：
1. 子类型无法给父类型传递参数
2. Child.prototype 定义方法的时候，必须写在 new Parent 之前

#### 2.构造函数实现
实现：在创建 Child 构造函数的时候使用 Parent.call(this)（这里的 this 是一个全新的对象，因为调用构造函数的时候会使用 new）  
缺点：没有原型，且每次生成 Child 实例的时候都需要执行一遍 Parent 函数，无法复用。

#### 3.组合式继承
实现：同时使用方法1和方法2  
优点：因为同时使用了原型和构造函数，这个即实现了方法的复用，又保证每个实例都有自己的属性。  
缺点：Parent 构造函数会被调用两次，一次是在子类型构造函数的内部，一次是在创建 Child 原型的时候。

#### 4. 使用 Object.creat() 实现原型继承
使用构造函数的时候发现，在创建 Child 原型的时候调用 Parent 构造函数只是为了获得 Parent 的原型，所以会想到让 Child.prototype = Parent.prototype，但是样子子的话，如果改变 Child.prototype 就会同时改变 Parent.prototype（浅复制）。所以这个时候就需要用到 Object.creat() 来创建 Child.prototype 的构造函数的原型，这样就完美了。

#### 5.ES6 继承
使用 `extends` 关键字实现继承。

# 数组去重
参考：https://github.com/lifesinger/blog/issues/113
#### 直觉方案
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

##### 优化方案
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

#### 使用 ES6 中的 Set
参考：http://es6.ruanyifeng.com/#docs/set-map

```js
[...new Set(array)]
```

# 回调函数
#### Promise
Promise 简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。有了Promise对象，就可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数。此外，Promise对象提供统一的接口，使得控制异步操作更加容易。

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

# 深拷贝
#### JSON.parse(JSON.stringify(obj))
最简单，但是不能复制函数（不能被 JSON.stringify 的会被忽略），且原型链丢失。

#### 递归实现
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
- 箭头函数的是时候，this 是上下文
- 对象里的函数在被对象引用的时候是指向引用它的对象
- 不符合上述规则的时候，则指向全局，游览器里面则指向 window，在严格模式下则为 undefined

# arguments
#### arguments 是数组吗
arguments 不是一个数组，它是一个类数组的对象。Array.isArray 返回 false

#### 如何转为数组
```js
// 方法1
Array.prototype.slice.apply(arguments)

// 方法2
Array.prototype.concat.apply([], arguments)
```
#### 类数组和数组的区别
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
#### slice
slice 用来创建新的数组，**不会改变原来的数组**。接受两个参数，也就是开始截取的位置和结束截取的位置。当只传递一个参数的时候，截取到最后。

#### splice
**强大的数组操作的方法**，*注意：是操作数组的方法，也是是会改变数组本身！*，返回被删除的元素组成的数组。

- 删除数组中的元素： `splice(0, 2) => 从下标 0 开始删除，删除 2 个元素，并返回被删除的元素组成的数组`
- 插入元素：`splice(2, 0, item1, item2) => 从下标 2 的元素开始 删除 0 个元素，在后面插入 item1、item2`

#### split、join
split 用来分割字符串变为数组，join 用来将数组拼接成字符串。

# data-x
用来自定义属性，通过 dataset 可以添加和获取。当然也可以通过 getAttribute 、setAttribute 获取和设置。

# 实现 bind()、call()、apply()
参考：https://www.jianshu.com/p/6a1bc149b598
#### 实现一个 apply 方法
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
Function.prototype.myApply = function(obj) {
  let context = obj || window

  // 给对象添加一个方法
  // 因为 myApply 是对象中的一个方法，所以 this 指向调用它的对象（函数也是对象）
  // 使用 ES6 的 Symbol 来定义一个唯一值
  let fn = Symbol()
  context[fn] = this
  let str = '', arg = arguments[1]
  for (let i = 0; i < arg.length; i++) {
    str += arg[i] + ','
  }
  let temp = 'context[fn](' + str.replace(/,$/g, '') + ')'
  let returnVal = eval(temp)
  delete context[fn]
  return returnVal
}
```

#### 实现一个 bind 方法
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
