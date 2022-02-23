---
title: JavaScript构造函数的使用
date: 2022-01-28 16:49:48
tags: javascript
categories: js小知识点
---

### 构造函数的基本使用

- 和普通函数一样，只不过 **调用的时候要和 new 连用**，不然就是一个普通函数调用

  ```javascript
  function Person() {}
  var o1 = new Person()  // 能得到一个空对象
  var o2 = Person()      // 什么也得不到，这个就是普通函数调用
  ```

  - 注意： **不写 new 的时候就是普通函数调用，没有创造对象的能力**

- 首字母大写

  ```javascript
  function person() {}
  var o1 = new person() // 能得到一个对象
  
  function Person() {}
  var o2 = new Person() // 能得到一个对象
  ```

  - 注意： **首字母不大写，只要和 new 连用，就有创造对象的能力**

- 当调用的时候如果不需要传递参数可以不写 ()，建议都写上

  ```javascript
  function Person() {}
  var o1 = new Person()  // 能得到一个空对象
  var o2 = new Person    // 能得到一个空对象 
  ```

  - 注意： **如果不需要传递参数，那么可以不写 （），如果传递参数就必须写**

- 构造函数内部的 this，由于和 new 连用的关系，是指向当前实例对象的

  ```javascript
  function Person() {
    console.log(this)
  }
  var o1 = new Person()  // 本次调用的时候，this => o1
  var o2 = new Person()  // 本次调用的时候，this => o2
  ```

  - 注意： **每次 new 的时候，函数内部的 this 都是指向当前这次的实例化对象**

- 因为构造函数会自动返回一个对象，所以构造函数内部不要写 return

  - 你如果 return 一个基本数据类型，那么写了没有意义
  - 如果你 return 一个引用数据类型，那么构造函数本身的意义就没有了

  

### 使用构造函数创建一个对象

- 我们在使用构造函数的时候，可以通过一些代码和内容来向当前的对象中添加一些内容

  ```javascript
  function Person() {
    this.name = 'Jack'
    this.age = 18
  }
  
  var o1 = new Person()
  var o2 = new Person()
  ```

  - 我们得到的两个对象里面都有自己的成员 **name** 和 **age**

- 我们在写构造函数的时候，是不是也可以添加一些方法进去呢？

  ```javascript
  function Person() {
    this.name = 'Jack'
    this.age = 18
    this.sayHi = function () {
      console.log('hello constructor')
    }
  }
  
  var o1 = new Person()
  var o2 = new Person()
  ```

  - 显然是可以的，我们的到的两个对象中都有 sayHi 这个函数
  - 也都可以正常调用

- 但是这样好不好呢？缺点在哪里？

  ```javascript
  function Person() {
    this.name = 'Jack'
    this.age = 18
    this.sayHi = function () {
      console.log('hello constructor')
    }
  }
  
  // 第一次 new 的时候， Person 这个函数要执行一遍
  // 执行一遍就会创造一个新的函数，并且把函数地址赋值给 this.sayHi
  var o1 = new Person()
  
  // 第二次 new 的时候， Person 这个函数要执行一遍
  // 执行一遍就会创造一个新的函数，并且把函数地址赋值给 this.sayHi
  var o2 = new Person()
  ```

  - 这样的话，那么我们两个对象内的 sayHi 函数就是一个代码一摸一样，功能一摸一样
  - 但是是两个空间函数，占用两个内存空间
  - 也就是说 o1.sayHi 是一个地址，o2.sayHi 是一个地址
  - 所以我们执行 console.log(o1 === o2.sayHi)`的到的结果是 false
  - 缺点： **一摸一样的函数出现了两次，占用了两个空间地址**
