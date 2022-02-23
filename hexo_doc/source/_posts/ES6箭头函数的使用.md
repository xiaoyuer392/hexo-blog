---
title: ES6箭头函数的使用
date: 2022-01-27 11:49:28
tags: javascript
categories: js小知识点
---

## 箭头函数
箭头函数是 ES6 里面一个简写函数的语法方式

箭头函数只能简写函数表达式，不能简写声明式函数

```javascript
function fn() {} // 不能简写
const fun = function () {} // 可以简写
const obj = {
  fn: function () {} // 可以简写
}
```
语法： (函数的行参) => { 函数体内要执行的代码 }

```javascript
const fn = function (a, b) {
  console.log(a)
  console.log(b)
}
// 可以使用箭头函数写成
const fun = (a, b) => {
  console.log(a)
  console.log(b)
}
const obj = {
  fn: function (a, b) {
    console.log(a)
    console.log(b)
  }
}
// 可以使用箭头函数写成
const obj2 = {
  fn: (a, b) => {
    console.log(a)
    console.log(b)
  }
}
```
## 箭头函数的特殊性
箭头函数内部没有 this，箭头函数的 this 是上下文的 this

```javascript
// 在箭头函数定义的位置往上数，这一行是可以打印出 this 的
// 因为这里的 this 是 window
// 所以箭头函数内部的 this 就是 window
const obj = {
  fn: function () {
    console.log(this)
  },
  // 这个位置是箭头函数的上一行，但是不能打印出 this
  fun: () => {
    // 箭头函数内部的 this 是书写箭头函数的上一行一个可以打印出 this 的位置
    console.log(this)
  }
}

obj.fn()
obj.fun()
```
按照我们之前的 this 指向来判断，两个都应该指向 obj
但是 fun 因为是箭头函数，所以 this 不指向 obj，而是指向 fun 的外层，就是 window
箭头函数内部没有 arguments`这个参数集合

```javascript
const obj = {
  fn: function () {
    console.log(arguments)
  },
  fun: () => {
    console.log(arguments)
  }
}
obj.fn(1, 2, 3) // 会打印一个伪数组 [1, 2, 3]
obj.fun(1, 2, 3) // 会直接报错
```
函数的行参只有一个的时候可以不写 ()其余情况必须写

```javascript
const obj = {
  fn: () => {
    console.log('没有参数，必须写小括号')
  },
  fn2: a => {
    console.log('一个行参，可以不写小括号')
  },
  fn3: (a, b) => {
    console.log('两个或两个以上参数，必须写小括号')
  }
}
```
函数体只有一行代码的时候，可以不写 {}，并且会自动 return

```javascript
const obj = {
  fn: a => {
    return a + 10
  },
  fun: a => a + 10
}

console.log(fn(10)) // 20
console.log(fun(10)) // 20
```
## 函数传递参数的时候的默认值
我们在定义函数的时候，有的时候需要一个默认值出现
就是当我不传递参数的时候，使用默认值，传递参数了就使用传递的参数

```javascript
function fn(a) {
  a = a || 10
  console.log(a)
}
fn()   // 不传递参数的时候，函数内部的 a 就是 10
fn(20) // 传递了参数 20 的时候，函数内部的 a 就是 20
```
在 ES6 中我们可以直接把默认值写在函数的行参位置

```javascript
function fn(a = 10) {
  console.log(a)
}
fn()   // 不传递参数的时候，函数内部的 a 就是 10
fn(20) // 传递了参数 20 的时候，函数内部的 a 就是 20
```
这个默认值的方式箭头函数也可以使用

```javascript
const fn = (a = 10) => {
  console.log(a)
}
fn()   // 不传递参数的时候，函数内部的 a 就是 10
fn(20) // 传递了参数 20 的时候，函数内部的 a 就是 20
```
注意： 箭头函数如果你需要使用默认值的话，那么一个参数的时候也需要写 （）
