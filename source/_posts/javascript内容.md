---
title: JavaScript 中对变量和函数声明的提前
date: 2017-02-09 11:52:32
description: "在聊函数声明的声明提前之前，有必要介绍下函数定义的几种方法，大部分小伙伴们应该都不陌生。了解的或者不想了解的就痛快地一滚轮滚下去吧，不熟悉的或者想再熟悉一下的就放慢脚步起步走。"
fancybox: false
categories:
tags:
     - JavaScript
     - 函数
---
<Excerpt in index | 首页摘要>
<!-- more -->
<The rest of contents | 余下全文>

定义函数的方法主要有三种：
1.函数声明(Function Declaration)
2.函数表达式Function Expression)
3.new Function构造函数
函数声明的典型格式：
```javascript
function functionName(arg1, arg2, ...){
  <!-- function body -->
}
```
函数表达式：
```
var variable=function(arg1, arg2, ...){
      <!-- function body -->
}
```
# 变量声明被提前
其实是 JavaScript 解析器搞的鬼，解析器将当前作用域内声明的所有变量和函数都会放到作用域的开始处，但是，只有变量的声明被提前到作用域的开始处了，而赋值操作被保留在原处。上述代码对于解析器来说其实是如下这个样子滴：

```javascript
(function() {
  var declaredLater; //声明被提前到作用域开始处了！

  // Outputs: undefined
  console.log(declaredLater);

  declaredLater = "Now it's defined!"; //赋值操作还在原地!

  // Outputs: "Now it's defined!"
  console.log(declaredLater);
})();
```

这就是为什么上述代码不报异常的原因！变量和函数经过“被提前”之后，declaredLater 变量其实就被放在了调用函数的前面，根据 JavaScript 语法的定义，已声明而未被赋值的变量会被自动赋值为 undefined ，所以，第一次打印 declaredLater 变量的值就是 undefined，后面我们对 declaredLater 变量进行了赋值操作，所以，第二次再打印变量就会输出Now it's defined!。

再来看一个例子：
```javascript
var name = "Baggins";

(function () {
    // Outputs: "Original name was undefined"
    console.log("Original name was " + name);

    var name = "Underhill";

    // Outputs: "New name is Underhill"
    console.log("New name is " + name);
})();
```
上述代码中，我们先声明了一个变量 name ，我们的本意是希望在第一次打印 name 变量时能够输出全局范围内定义的 name 变量，然后再在函数中定义一个局部 name 变量覆盖全局变量，最后输出局部变量的值。可是第一次输出的结果和我们的预期完全不一致，原因就是我们定义的局部变量在其作用域内被“提前”了，也就是变成了如下形式：

```javascript
var name = "Baggins";

(function () {
    var name;  //注意：name 变量被提前了！

    // Outputs: "Original name was undefined"
    console.log("Original name was " + name);

    name = "Underhill";

    // Outputs: "New name is Underhill"
    console.log("New name is " + name);
})();
```
# 函数声明“被提前”
前边说的是变量，接下来我们说说函数。

函数的“被提前”还要分两种情况，一种是函数声明，第二种是函数作为值赋值给变量。

先说第一种情况，上代码：
```javascript
// Outputs: "Yes!"
isItHoisted();

function isItHoisted() {
    console.log("Yes!");
}
```
再来看第二种情况：函数作为值赋值给变量。（还记得吗？在 JavaScript 中，函数也可以作为值赋予变量！）还是先上代码：
```javascript
// Outputs: "Definition hoisted!"
definitionHoisted();

// TypeError: undefined is not a function
definitionNotHoisted();

function definitionHoisted() {
    console.log("Definition hoisted!");
}

var definitionNotHoisted = function () {
    console.log("Definition not hoisted!");
};
```
我们做了一个对比，definitionHoisted 函数被妥妥的执行了，符合第一种类型；definitionNotHoisted 变量“被提前”了，但是他的赋值（也就是函数）并没有被提前，从这一点上来说，和前面我们所讲的变量“被提前”是完全一致的，并且，由于“被提前”的变量的默认值是 undefined ，所以报的错误属于“类型不匹配”，因为 undefined 不是函数，当然不能被调用。

# 总结
+ 变量的声明被提前到作用域顶部，赋值保留在原地
+ 函数声明整个“被提前”
+ 函数作为值赋给变量时只有变量“被提前”了，函数没有“被提前”















