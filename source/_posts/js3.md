---
title: 栈 #文章页面上的显示名称，一般是中文
date: 2020-7-22 12:00 #文章生成时间，一般不改，当然也可以任意修改
categories: 学习JavaScript数据结构与算法 #分类
tags: [JavaScript, 数据结构与算法, 学习JavaScript数据结构与算法] #文章标签
description: 学习JavaScript数据结构与算法
---

#### 什么是栈

栈是一种遵从后进先出（LIFO）原则的有序集合。新添加的或待删除的元素都保存在栈的 末尾，称作栈顶，另一端就叫栈底。在栈里，新元素都靠近栈顶，旧元素都接近栈底。 

例如，下图里的一摞书或者餐厅里堆放的盘子。

![](/images/js3-2.png)

![](E:\1244832273.github.io\themes\next\source\images\js3-2.png)

#### 栈的创建

我们将创建一个类来表示栈。让我们从基础开始，先声明这个类： function 

```
Stack() { //各种属性和方法的声明 } 
```

首先，我们需要一种数据结构来保存栈里的元素。可以选择数组： var items = []; 接下来，要为我们的栈声明一些方法。

  push(element(s))：添加一个（或几个）新元素到栈顶。

  pop()：移除栈顶的元素，同时返回被移除的元素。 

 peek()：返回栈顶的元素，不对栈做任何修改（这个方法不会移除栈顶的元素，仅仅返 回它）。 

 isEmpty()：如果栈里没有任何元素就返回true，否则返回false。  clear()：移除栈里的所有元素。 

 size()：返回栈里的元素个数。这个方法和数组的length属性很类似。 我们要实现的第一个方法是push。这个方法负责往栈里添加新元素，有一点很重要：该方 法只添加元素到栈顶，也就是栈的末尾。

push方法可以这样写：

```
this.push = function(element){
 items.push(element);
};
```

因为我们使用了数组来保存栈里的元素，所以可以用上一章里学到的数组的push方法来实现。 接着，我们来实现pop方法。这个方法主要用来移除栈里的元素。栈遵从LIFO原则，因此移 出的是最后添加进去的元素。因此，我们可以用上一章讲数组时介绍的pop方法。

栈的pop方法 可以这样写：

```
this.pop = function(){
 return items.pop();
}; 
```

只能用push和pop方法添加和删除栈中元素，这样一来，我们的栈自然就遵从了LIFO原则。 

现在，为我们的类实现一些额外的辅助方法。如果想知道栈里最后添加的元素是什么，可以 用peek方法。这个方法将返回栈顶的元素：

```
this.peek = function(){
 return items[items.length-1];
}; 

```

因为类内部是用数组保存元素的，所以访问数组的最后一个元素可以用 length - 1：

![](/images/js3-1.png)

![](E:\1244832273.github.io\themes\next\source\images\js3-1.png)

在上图中，有一个包含三个元素的栈，因此内部数组的长度就是3。数组中最后一项的位置 是2，length - 1（31）正好是2。 

下一个要实现的方法是 isEmpty，如果栈为空的话将返回true，否则就返回false：

```
this.isEmpty = function(){
 return items.length == 0;
}; 
```

##### 栈的全部代码

```
function Stack() {
 var items = [];
 this.push = function(element){
 items.push(element);
 };
 this.pop = function(){
 return items.pop();
 };
 this.peek = function(){ // 返回最后一位
 return items[items.length-1];
 };
 this.isEmpty = function(){
 return items.length == 0;
 };
 this.size = function(){
 return items.length;
 };
 this.clear = function(){
 items = [];
 };
 this.print = function(){
 console.log(items.toString());
 };
} 
```

#### 从十进制到二进制

现实生活中，我们主要使用十进制。但在计算科学中，二进制非常重要，因为计算机里的所 有内容都是用二进制数字表示的（0和1）。没有十进制和二进制相互转化的能力，与计算机交流 就很困难。 

要把十进制转化成二进制，我们可以将该十进制数字和2整除（二进制是满二进一），直到结 果是0为止。举个例子，把十进制的数字10转化成二进制的数字，过程大概是这样：

![](/images/js3-3.png)

![](E:\1244832273.github.io\themes\next\source\images\js3-3.png)

下面是对应的算法描述：

```
function divideBy2(decNumber){
 var remStack = new Stack(), // new一个栈对象
 rem,
 binaryString = '';
 while (decNumber > 0){ //{1}判断取余后的值是否大于0
     rem = Math.floor(decNumber % 2); //{2} 取余的值
     remStack.push(rem); //{3} 
     decNumber = Math.floor(decNumber / 2); //{4}
 }
 while (!remStack.isEmpty()){ //{5}利用栈的后进先出原则来输出取余后的值
	 binaryString += remStack.pop().toString();
 }
 return binaryString; 
```

在这段代码里，当结果满足和2做整除的条件时（行{1}），我们会获得当前结果和2的余数， 放到栈里（行{2}、{3}）。然后让结果和2做整除（行{4}）。

另外请注意：JavaScript有数字类型， 但是它不会区分究竟是整数还是浮点数。

因此，要使用Math.floor函数让除法的操作仅返回整 数部分。最后，用pop方法把栈中的元素都移除，把出栈的元素变成连接成字符串（行{5}）。 

用刚才写的算法做一些测试，使用以下代码把结果输出到控制台里：

```
console.log(divideBy2(233)); //输出11101001
console.log(divideBy2(10)); //输出1010
console.log(divideBy2(1000)); //输出1111101000 
```

我们很容易修改之前的算法，使之能把十进制转换成任何进制。除了让十进制数字和2整除 转成二进制数，还可以传入其他任意进制的基数为参数，

就像下面算法这样：

```
function baseConverter(decNumber, base){
 var remStack = new Stack(),
 rem,
 baseString = '',
 digits = '0123456789ABCDEF'; //{6}
 while (decNumber > 0){
     rem = Math.floor(decNumber % base);
     remStack.push(rem);
     decNumber = Math.floor(decNumber / base);
 }
 while (!remStack.isEmpty()){
 	baseString += digits[remStack.pop()]; //{7}
 }
 return baseString;
} 
```

我们只需要改变一个地方。在将十进制转成二进制时，余数是0或1；在将十进制转成八进制 时，余数是0到8之间的数；但是将十进制转成16进制时，余数是0到8之间的数字加上A、B、C、 D、E和F（对应10、11、12、13、14和15）。因此，我们需要对栈中的数字做个转化才可以（行 {6}和行{7}）。

可以使用之前的算法，输出结果如下：

```
console.log(baseConverter(100345, 2)); //输出11000011111111001
console.log(baseConverter(100345, 8)); //输出303771
console.log(baseConverter(100345, 16)); //输出187F9 
```

