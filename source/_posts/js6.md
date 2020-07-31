---
	title: 双向链表 #文章页面上的显示名称，一般是中文
	date: 2020-7-27 13:00 #文章生成时间，一般不改，当然也可以任意修改
	categories: 学习JavaScript数据结构与算法 #分类
	tags: [JavaScript, 数据结构与算法, 学习JavaScript数据结构与算法] #文章标签
	description: 学习JavaScript数据结构与算法

---

#### 双向链表

​		链表有多种不同的类型，这一节介绍双向链表。双向链表和普通链表的区别在于，在链表中， 一个节点只有链向下一个节点的链接，而在双向链表中，链接是双向的：一个链向下一个元素， 另一个链向前一个元素，如下图所示： 

![](/images/js6-1.png)

![](E:\1244832273.github.io\themes\next\source\images\js6-1.png)

```javascript
function DoublyLinkedList() { 
 
    var Node = function(element){ 
 
        this.element = element;
        this.next = null;
        this.prev = null; //新增的
    }; 
 
    var length = 0;
    var head = null;
    var tail = null; //新增的 
 
    //这里是方法
}
```

​		在代码中可以看到，LinkedList类和DoublyLinkedList类之间的区别标为新增的。在 Node类里有prev属性（一个新指针），在DoublyLinkedList类里也有用来保存对列表最后一 项的引用的tail属性

​		双向链表提供了两种迭代列表的方法：从头到尾，或者反过来。我们也可以访问一个特定节 点的下一个或前一个元素。在单向链表中，如果迭代列表时错过了要找的元素，就需要回到列表 起点，重新开始迭代。这是双向链表的一个优点

##### 在任意位置插入一个新元素 

​		向双向链表中插入一个新项跟（单向）链表非常类似。区别在于，链表只要控制一个next 指针，而双向链表则要同时控制next和prev（previous，前一个）这两个指针。 

```javascript
this.insert = function(position, element){ 
 
    //检查越界值
    if (position >= 0 && position <= length){ 
 
        var node = new Node(element),
            current = head,
            previous,
            index = 0; 
 
        if (position === 0){ //在第一个位置添加 
 
            if (!head){ //新增的 {1}
                head = node;
                tail = node;
            } else {
                node.next = current;
                current.prev = node; //新增的 {2}
                head = node;
            }
        } else  if (position === length) { //最后一项 //新增的 
 
            current = tail; // {3}
            current.next = node;
            node.prev = current; 
            tail = node; 
 
        } else {
            while (index++ < position){ //{4}
                previous = current;
                current = current.next;
            }
            node.next = current; //{5}
            previous.next = node; 
 
            current.prev = node; //新增的
            node.prev = previous; //新增的
        } 
 
        length++; //更新列表的长度 
 
        return true; 
 
    } else {
        return false;
    }
}; 
```

​		我们来分析第一种场景：在列表的第一个位置（列表的起点）插入一个新元素。如果列表为 空（行{1}），只需要把head和tail都指向这个新节点。如果不为空，current变量将是对列表 中第一个元素的引用。就像我们在链表中所做的，把node.next设为current，而head将指向 node（它将成为列表中的第一个元素）。不同之处在于，我们还需要为指向上一个元素的指针设 一个值。current.prev指针将由指向null变为指向新元素（node——行{2}）。node.prev指 针已经是null，因此不需要再更新任何东西

下图演示了这个过程： 

![](/images/js6-2.png)

![](E:\1244832273.github.io\themes\next\source\images\js6-2.png)

​		现在来分析一下，假如我们要在列表最后添加一个新元素。这是一个特殊情况，因为我们还 控制着指向最后一个元素的指针（tail）。current变量将引用最后一个元素（行{3}）。然后开 始建立第一个链接：node.prev将引用current。current.next指针（指向null）将指向node （由于构造函数，node.next已经指向了null）。然后只剩一件事了，就是更新tail，它将由指 向current变为指向node。

下图展示了这些行为： 

![](/images/js6-3.png)

![](E:\1244832273.github.io\themes\next\source\images\js6-3.png)

然后还有第三种场景：在列表中间插入一个新元素。就像我们在之前的方法中所做的，迭代 列表，直到到达要找的位置（行{4}）。我们将在current和previous元素之间插入新元素。首 先，node.next将指向current（行{5}），而previous.next将指向node，这样就不会丢失节 点之间的链接。然后需要处理所有的链接：current.prev将指向node，而node.prev将指向 previous。

下图展示了这一过程： 

![](/images/js6-4.png)

 ![](E:\1244832273.github.io\themes\next\source\images\js6-4.png)

`我们可以对insert和remove这两个方法的实现做一些改进。在结果为否的 情况下，我们可以把元素插入到列表的尾部。性能也可以有所改进，比如，如果 position大于length/2，就最好从尾部开始迭代，而不是从头开始（这样就 能迭代更少列表中的元素）。` 

##### 从任意位置移除元素 	

​		从双向链表中移除元素跟链表非常类似。唯一的区别就是还需要设置前一个位置的指针。我 们来看一下它的实现：

```javascript
this.removeAt = function(position){ 
 
    //检查越界值
    if (position > -1 && position < length){
    	var current = head, 
    	 previous,
         index = 0; 
 
        //移除第一项
        if (position === 0){ 
 
            head = current.next; // {1} 
 
            //如果只有一项，更新tail //新增的
            if (length === 1){ // {2}
            	tail = null;
            } else {
           	 head.prev = null; // {3}
            } 
 
        } else if (position === length-1){ //最后一项 //新增的 
 
            current = tail; // {4}
            tail = current.prev;
            tail.next = null; 
 
        } else { 
 
            while (index++ < position){ // {5} 
 
                previous = current;
                current = current.next;
            } 
 
            //将previous与current的下一项链接起来——跳过current
            previous.next = current.next; // {6}
            current.next.prev = previous; //新增的
        } 
 
        length--; 
 
        return current.element; 
 
    } else {
        return null;
    }
}; 
```

​		我们需要处理三种场景：从头部、从中间和从尾部移除一个元素

​		我们来看看如何移除第一个元素。current变量是对列表中第一个元素的引用，也就是我 们想移除的元素。需要做的就是改变head的引用，将其从current改为下一个元素 （current.next——行{1}）。但我们还需要更新current.next指向上一个元素的指针（因为 第一个元素的prev指针是null）。因此，把head.prev的引用改为null（行{3}——因为head 也指向列表中新的第一个元素，或者也可以用current.next.prev）。由于还需要控制tail 的引用，我们可以检查要移除的元素是否是第一个元素，如果是，只需要把tail也设为null（行{2}）。 

![](/images/js6-6.png)

 		下一种场景是从最后一个位置移除元素。既然已经有了对最后一个元素的引用（tail），我 们就不需要为找到它而迭代列表。这样我们也就可以把tail的引用赋给current变量（行{4}）。 接下来，需要把tail的引用更新为列表中倒数第二个元素（current.prev，或者tail.prev 也可以）。既然tail指向了倒数第二个元素，我们就只需要把next指针更新为null（tail.next = null）。

下图演示了这一行为： 

![](/images/js6-7.png)

​		第三种也是最后一种场景：从列表中间移除一个元素。首先需要迭代列表，直到到达要找的 位置（行{5}）。current变量所引用的就是要移除的元素。那么要移除它，我们可以通过更新 previous.next和current.next.prev的引用，在列表中跳过它。因此，previous.next将 指向current.next，而current.next.prev将指向previous，

如下图所示

![](/images/js6-8.png)

![](E:\1244832273.github.io\themes\next\source\images\js6-8.png)