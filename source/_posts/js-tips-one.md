---
title: JS简单优化技巧（1）
date: 2019-02-25 10:41:05
tags:
---

### 常见列举

1. 利用自身 bool 结果，减少冗余 if/else

```javascript
// no good
if (age > 20) {
  return true
} else {
  return false
}
// good
return age > 20
```

2. for 循环长度提前初始化

```javascript
// no good
for (var i = 0; i < arr.length; i++) {
  // sth
}
//good
for (var i = 0, len = arr.length; i < len; i++) {
  // sth
}
// 将数组的长度进行存储，循环时不用每次去读取一次数组长度，提升性能
```

3. 多个变量声明

```javascript
//bad
var a = 'aa'
var b = 'bb'
var c = 'cc'
var d

//good
var a = 'aa',
  b = 'bb',
  c = 'cc',
  d
// 原则：被赋值的在前面，只进行声明放后面
```

4. 多个变量初始化相同值,注意连等

```javascript
//bad
function test() {
  var a = (b = 1)
  console.log(a)
}

//good
function test() {
  var a = 1,
    b = 1
  console.log(a)
}
/*
避免使用连等号声明赋值变量。b会被声明为函数内的全局变量，不推荐使用，容易污染环境
var a = b = 1; 进行语法解析后，等价于：
b = 1;
var a = b;
js中声明变量时，如果不加var关键字，则表示此变量声明为全局变量。
*/
var a = (b = c = {}) //这里abc三个变量指向同一块内存，修改其中一个，其他两个值也会变，如果这不是你希望看到的，请单独赋值初始化
```

5. 利用||运算赋初值

```javascript
//bad
if (value === '' || value == null) {
  value = 'similar'
}
//good
value = value || 'similar'
```

6. 使用 Array.includes 来处理多个条件

```javascript
// no good
function test(fruit) {
  if (value == 'apple' || fruit == 'strawberry') {
    console.log('red')
  }
}

// good
function test(fruit) {
  const arr = ['apple', 'strawberry', 'cherry', 'cranberries']
  if (arr.includes(fruit)) {
    console.log('red')
  }
}
```

### 参考文章

https://www.cnblogs.com/similar/p/5016424.html

https://juejin.im/post/5bb9e3085188255c352d7326
