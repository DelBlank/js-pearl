# js-pearl
js 代码拾遗

## 交换变量值

- 使用中间变量

```js
let a = 0
let b = 1
let temp = a

a = b 
b = temp
```

- 不使用中间变量

```js
let a = 0
let b = 1

a += b
b -= b
a -= b
```

- ES6 解构

```js
let a = 0
let b = 0

[a, b] = [b, a]
```

## 唯一标识

```js
const uniqId = () => Math.random().toString(32).slice(2)

uniqId() // '1jq9msef5m8'
uniqId() // 'q0ff8sggjj8'
```

## js call + apply + bind

```js
// call
Function.prototype.call = (context, ...args) => {
  if(typeof context === 'undefined' || context === null) {
    context = window
  }

  const s = Symbol() // 使用 Symbol，避免 s 与 context 属性冲突
  context[s] = this

  const result = context[s](...args) // 等价于 context.f(...)

  delete context[s]

  return result
}

// apply
Function.prototype.apply = (context, args) => {
  if(typeof context === 'undefined' || context === null) {
    context = window
  }

  const s = Symbol() // 使用 Symbol，避免 s 与 context 属性冲突
  context[s] = this

  const result = context[s](...args) // 等价于 context.f(...)

  delete context[s]

  return result
}

// bind
Function.prototype.bind = (context) => {
  if(typeof context === 'undefined' || context === null) {
    context = window
  }

  const self = this

  return function(...args) {
    self.apply(context, args)
  }
}
```

## 柯里化连加

`f(a)(b)(c).... = a + b + c + ...` 

```js
// 数组累加
function add(arr = []){
  return arr.reduce((acc, cur) => acc + cur)
}

// 柯里化累加
function f(a) {
  const args = [a]
  
  function ff (b) {
    args.push(b);

    return f(add(args))
  }

  ff.toString = function(){
    return add(args)
  }

  return ff
}
```

## 判断变量类型

`Object.prototype.toString.call`

```js
Object.prototype.toString.call('') ;   // [object String]
Object.prototype.toString.call(1) ;    // [object Number]
Object.prototype.toString.call(true) ; // [object Boolean]
Object.prototype.toString.call(Symbol()); //[object Symbol]
Object.prototype.toString.call(undefined) ; // [object Undefined]
Object.prototype.toString.call(null) ; // [object Null]
Object.prototype.toString.call(new Function()) ; // [object Function]
Object.prototype.toString.call(new Date()) ; // [object Date]
Object.prototype.toString.call([]) ; // [object Array]
Object.prototype.toString.call(new RegExp()) ; // [object RegExp]
Object.prototype.toString.call(new Error()) ; // [object Error]
Object.prototype.toString.call(document) ; // [object HTMLDocument]
Object.prototype.toString.call(window) ; //[object global] window 是全局对象 global 的引用
```

## `new` 原理

```js
new F()

// 1. 创建空对象
const obj = new Object()
// 2. 空对象 __proto__ 指向 F.prototype
obj.__proto__ = F.prototype
// 3. 调用函数 
const result = F.call(obj)
// 4 返回结果
return typeof result === 'object' ? result : obj
```

## `Object.create`

```js
function create(obj) {
  function F();
  F.prototype = obj

  return new F()
}
```

## 寄生组合继承

```js
function inherit(Child, Parent){
  const p = Object.create(Parent)
  
  p.constructor = Child
  Child.prototype = p
}
```

### `debounce` `throttle`

```js
function debounce(fn, delay){
  let timeId

  return function(...args){
    const self = this

    timeId && clearTimeout(timeId)

    timeId = setTimeout(() => {
      fn.apply(self, args)
      timeId = null
    }, delay)
  }
}

function throttle(fn, threshhold = 250) {
  let last, timeId

  return function(...args){
    const self = this
    const exec = () => {
      fn.apply(self, args)
      last = now
      timeId = null
    }
    const now = +new Date()

    timeId && clearTimeout(timeId)

    // 开始或超时执行
    if(!last || now - last >= threshhold) {
      exec()
      return
    }
    
    // 时间片走完但是没有持续触发
    timeId = setTimeout(() => {exec()}, last + threshhold - now)
  }
}
```