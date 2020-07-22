---
title: 队列 #文章页面上的显示名称，一般是中文
date: 2020-7-22 15:00 #文章生成时间，一般不改，当然也可以任意修改
categories: 学习JavaScript数据结构与算法 #分类
tags: [JavaScript, 数据结构与算法, 学习JavaScript数据结构与算法] #文章标签
description: 学习JavaScript数据结构与算法

---

#### 什么是队列

我们已经学习了栈。队列和栈非常类似，但是使用了不同的原则，而非后进先出。你将在这 一章学习这些内容。 

队列是遵循FIFO（First In First Out，先进先出，也称为先来先服务）原则的一组有序的项。 队列在尾部添加新元素，并从顶部移除元素。最新添加的元素必须排在队列的末尾。 

在现实中，最常见的队列的例子就是排队：

![](/images/js4-1.png)

![](E:\1244832273.github.io\themes\next\source\images\js4-1.png)

#### 创建队列

先从最基本的声明类开始：

```
function Queue() {
 //这里是属性和方法
} 
```

接下来需要声明一些队列可用的方法。

  enqueue(element(s))：向队列尾部添加一个（或多个）新的项。 

 dequeue()：移除队列的第一（即排在队列最前面的）项，并返回被移除的元素。 

 front()：返回队列中第一个元素——最先被添加，也将是最先被移除的元素。队列不 做任何变动（不移除元素，只返回元素信息——与Stack类的peek方法非常类似）。 

 isEmpty()：如果队列中不包含任何元素，返回true，否则返回false。  size()：返回队列包含的元素个数，与数组的length属性类似。

首先要实现的是enqueue方法。这个方法负责向队列添加新元素。这里有一个非常重要的细 节，新的项只能添加到队列末尾：

```
this.enqueue = function(element){
 items.push(element);
}; 
```

接下来要实现dequeue方法。这个方法负责从队列移除项。由于队列遵循先进先出原则，最先 添加的项也是最先被移除的。可以用介绍过的JavaScript的array类的shift方法。如果你 不记得了，这里帮你回忆一下，shift方法会从数组中移除存储在索引0（第一个位置）的元素：

```
this.dequeue = function(){
 return items.shift();
}; 
```

只有enqueue方法和dequeue方法可以添加和移除元素，这样就确保了Queue类遵循先进先 出原则。

##### 完整的Queue类

```
function Queue() {
 var items = [];
 // 添加在末尾
 this.enqueue = function(element){
 	items.push(element);
 };
 // 删除在头部
 this.dequeue = function(){
 	return items.shift();
 };
 // 返回第一个
 this.front = function(){
 	return items[0];
 };
 this.isEmpty = function(){
 	return items.length == 0;
 };
 this.clear = function(){
 	items = [];
 };
 this.size = function(){
 	return items.length;
 }; 
 this.print = function(){
 	console.log(items.toString());
 };
} 
```

下图展示了目前为止执行的所有入列操作，以及队列当前的状态

```
queue.print();
console.log(queue.size()); //输出3
console.log(queue.isEmpty()); //输出false
queue.dequeue();
queue.dequeue();
queue.print(); 
```

![](/images/js4-2.png)

![](E:\1244832273.github.io\themes\next\source\images\js4-2.png)

然后，出列两个元素（执行两次dequeue方法）。下图展示了dequeue方法的执行过程：

![](/images/js5-3.png)

![](E:\1244832273.github.io\themes\next\source\images\js5-3.png)

最后，再次打印队列内容时，就只剩Camila一个元素了。前两个入列的元素出列了，最后 入列的元素也将是最后出列的。也就是说，我们遵循了先进先出原则。

#### 优先队列

元素的添加和移除是基于优先级的。一个现实的例子就是机 场登机的顺序。头等舱和商务舱乘客的优先级要高于经济舱乘客。在有些国家，老年人和孕妇（或 带小孩的妇女）登机时也享有高于其他乘客的优先级。

实现一个优先队列，有两种选项：

设置优先级，然后在正确的位置添加元素；

或者用入列操 作添加元素，然后按照优先级移除它们。

在这个示例中，我们将会在正确的位置添加元素，因此 可以对它们使用默认的出列操作：

```
function PriorityQueue() {
	 var items = [];
	 function QueueElement (element, priority){ // {1}优先级类
		 this.element = element;
		 this.priority = priority;
	 }
	 this.enqueue = function(element, priority){ // 根据优先级来插入而不是简单的push
		 var queueElement = new QueueElement(element, priority);
		 if (this.isEmpty()){ // 如果数组是空的不需要比较优先级直接push
			items.push(queueElement); // {2}
		 } else {
			var added = false; // 根据这个值判断当前优先级是否比列表最大优先级还大
			for (var i=0; i<items.length; i++){
				 if (queueElement.priority < items[i].priority){// 当前优先级和列表每一个对比
					 items.splice(i,0,queueElement); // {3}插入小于item优先级的前一项
					 added = true;
					 break; // {4} 
				}
			}
			if (!added){ //{5} 比最大优先级还大直接push
				items.push(queueElement);
			}
		 }
	 };
	//其他方法和默认的Queue实现相同
}
```

​	默认的Queue类和PriorityQueue类实现上的区别是，要向PriorityQueue添加元素，需 要创建一个特殊的元素（行{1}）。这个元素包含了要添加到队列的元素（它可以是任意类型） 及其在队列中的优先级。

​	如果队列为空，可以直接将元素入列（行{2}）。否则，就需要比较该元素与其他元素的优 先级。当找到一个比要添加的元素的priority值更大（优先级更低）的项时，就把新元素插入 到它之前（根据这个逻辑，对于其他优先级相同，但是先添加到队列的元素，我们同样遵循先进 先出的原则）。要做到这一点，我们可以用JavaScript的array类的splice方法。 一旦找到priority值更大的元素，就插入新元素（行{3}）并终止队列循环（行{4}）。这样， 队列也就根据优先级排序了。

如果要添加元素的priority值大于任何已有的元素，把它添加到队列的末尾就行了（行 {5}）：

```
var priorityQueue = new PriorityQueue();
priorityQueue.enqueue("John", 2);
priorityQueue.enqueue("Jack", 1);
priorityQueue.enqueue("Camila", 1);
priorityQueue.print(); 
```

以上代码是一个使用PriorityQueue类的示例。在下图中可以看到每条命令的结果（以上 代码的结果）：

![](/images/js5-1.png)

![](E:\1244832273.github.io\themes\next\source\images\js5-1.png)

我们在这里实现的优先队列称为最小优先队列，因为优先级的值较小的元素被放置在队列最 前面（1代表更高的优先级）。最大优先队列则与之相反，把优先级的值较大的元素放置在队列最 前面。

#### 循环队列——击鼓传花

还有另一个修改版的队列实现，就是循环队列。循环队列的一个例子就是击鼓传花游戏（Hot Potato）。在这个游戏中，孩子们围成一个圆圈，把花尽快地传递给旁边的人。某一时刻传花停止， 这个时候花在谁手里，谁就退出圆圈结束游戏。重复这个过程，直到只剩一个孩子（胜者）。

在下面这个示例中，我们要实现一个模拟的击鼓传花游戏：

```
function hotPotato (nameList, num){
 var queue = new Queue(); // {1}
 for (var i=0; i<nameList.length; i++){
 	queue.enqueue(nameList[i]); // {2} 将list全部添加到队列中
 }
 var eliminated = '';
 while (queue.size() > 1){
     for (var i=0; i<num; i++){
     	queue.enqueue(queue.dequeue()); // {3} 删除队列的第一个然后再添加到最后
 	 }
     eliminated = queue.dequeue();// {4}删除并取出循环结束后的队列的第一个值
     console.log(eliminated + '在击鼓传花游戏中被淘汰。');
 }
 return queue.dequeue();// {5}
}

var names = ['John','Jack','Camila','Ingrid','Carl'];
var winner = hotPotato(names, 7);
console.log('胜利者：' + winner); 
```

实现一个模拟的击鼓传花游戏，要用到这一章开头实现的Queue类（行{1}）。我们会得到一 份名单，把里面的名字全都加入队列（行{2}）。给定一个数字，然后迭代队列。从队列开头移 除一项，再将其添加到队列末尾（行{3}），模拟击鼓传花（如果你把花传给了旁边的人，你被 淘汰的威胁立刻就解除了）。一旦传递次数达到给定的数字，拿着花的那个人就被淘汰了（从队 列中移除——行{4}）。最后只剩下一个人的时候，这个人就是胜者（行{5}）。

以上算法的输出如下：

 Camila在击鼓传花游戏中被淘汰。

 Jack在击鼓传花游戏中被淘汰。

 Carl在击鼓传花游戏中被淘汰。

 Ingrid在击鼓传花游戏中被淘汰。

 胜利者：John 

下图模拟了这个输出过程：

![](/images/js5-2.png)

![](E:\1244832273.github.io\themes\next\source\images\js5-2.png)