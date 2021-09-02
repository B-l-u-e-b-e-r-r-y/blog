---
title: var、let、const 的區別
date: 2019-11-27
comments: true
tags: 
- ES6
categories: 
- Javascript
---

# var、let、const 的區別

## var 變數
宣告時可以不給資料，資料可更動，範圍是函式作用域 (function scope)

## let 變數
宣告時可以不給資料，資料可更動，範圍是區塊作用域 (block scope)

## const 常數
宣告時一定要給資料，資料不可變動，範圍是區塊作用域 (block scope)

## 差異比較

 名稱 |類型|宣告時不給資料|資料變動|    範圍
-----|----|-------------|-------|----------
 var |變數|       O     |   O    |函式作用域
 let |變數|       O     |   O    |區塊作用域
const|常數|       X     |   X    |區塊作用域