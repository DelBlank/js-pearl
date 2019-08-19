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