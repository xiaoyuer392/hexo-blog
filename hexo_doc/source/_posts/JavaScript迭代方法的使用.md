---
title: JavaScript迭代方法的使用
date: 2022-01-30 18:48:55
tags: javascript
categories: js小知识点
---

## 迭代方法
迭代是重复反馈过程的活动，其目的通常是为了逼近所需目标或结果。
每一次对过程的重复称为一次“迭代”。

 ## forEach : 
 数组的API因为调用方式 arr.forEach() ; 
 forEach => this 这样的调用方式可以省去一个参数;

```javascript
 		var arr = [4,3,2,1,0];
         arr.forEach(function( item , index , arr){
             // 根据数组有多少项，执行对应次数的匿名函数;
             console.log( item , index , arr);
         }) 
        // 1. 参数 : 函数(函数会被执行数组项数次，并且传入数组的每一项内容，和遍历时的下标，数组本身)
        // 2. 返回值 : undefined ;
        // 3. 作用  : 遍历数组 ,  写的代码量少，以后可能在各种环境配合下使用起来更简便： 
```
##  map 方法
 有返回值; 就是每次函数执行的返回值组成的数组;
 可以改变数组之中所有项，返回改变之后的新数组;


```javascript
var arr = [4,3,2,1,0];
        var res = arr.map( function( item , index , arr){
            // 基本结构使用和forEach没有任何区别;
            // console.log(item , index , arr)
            return parseInt(item * 1.3 * 10) / 10
        })
        console.log(res);
```
## filter 方法 
有返回值; 一个数组,筛选之后的数组。
如何进行筛选 : 函数的返回值为true，则表示选中当项数据, 数组之中会添加这一项数据。
函数的返回值为false，则表示过滤，数组之中不会添加这一项数据;

```javascript
 var arr = [4,3,2,1,0];
        var res = arr.filter( function( item , index , arr ){
            // return true;
            // 过滤功能在这里要写条件;
            return item > 2;
        })
        // 函数什么的都不写的情况执行结果都是 undefined , 那么一项内容都不会放进新数组之中;
        console.log(res);
```
## every 判定方法 : 
返回值是一个布尔值 : 
必须所有的函数执行结果都为true , 返回结果才为true;

```javascript
var arr = [1,2,3,4,5,"hello world"];
        var res = arr.every( function( item , index , arr ){
            return typeof item === "number";
        })  
        console.log(res);
```
## some 判定方法 : 
返回值是一个布尔值 : 
函数之中有一个执行结果为true , 返回结果则为true;

```javascript
var arr = [1,2,"hello world",3,4,5];
        var res = arr.some( function( item ,index ){
            return typeof item === "boolean";
        })
        console.log(res);
```
## 总结
forEach => 专门用来遍历数组的;  没有返回值
map => 想要更改数组每一项的时候使用这个方法; 返回值是数组;
fileter => 过滤   返回值是数组;
every => 判定全部都; 返回值是布尔值;
some => 判定有一个; 返回值是布尔值;
