---
	title: 链表 #文章页面上的显示名称，一般是中文
	date: 2020-7-27 11:00 #文章生成时间，一般不改，当然也可以任意修改
	categories: 学习JavaScript数据结构与算法 #分类
	tags: [JavaScript, 数据结构与算法, 学习JavaScript数据结构与算法] #文章标签
	description: 学习JavaScript数据结构与算法

---

#### 1.什么是链表

​		链表存储有序的元素集合，但不同于数组，链表中的元素在内存中并不是连续放置的。每个 元素由一个存储元素本身的节点和一个指向下一个元素的引用（也称指针或链接）组成。下图展 示了一个链表的结构： 

![](/images/js5-4.png)

![](E:\1244832273.github.io\themes\next\source\images\js5-4.png)

​		相对于传统的数组，链表的一个好处在于，添加或移除元素的时候不需要移动其他元素。然 而，链表需要使用指针，因此实现链表时需要额外注意。数组的另一个细节是可以直接访问任何 位置的任何元素，而要想访问链表中间的一个元素，需要从起点（表头）开始迭代列表直到找到 所需的元素。

​		有一个可能是用来说明链表的最流行的例子，那就是火车。一列火车是由一系列车厢（也 称车皮）组成的。每节车厢或车皮都相互连接。你很容易分离一节车皮，改变它的位置，添加或 移除它。下图演示了一列火车。每节车皮都是列表的元素，车皮间的连接就是指针： 

![](/images/js5-5.png)

![](E:\1244832273.github.io\themes\next\source\images\js5-5.png)



#### 2.创建一个链表

​		理解了链表是什么之后，现在就要开始实现我们的数据结构了。以下是我们的LinkedList 类的骨架：

```javascript
function LinkedList() { 
 
    var Node = function(element){ // {1}         
        this.element = element;         
        this.next = null;    // next默认指向null
    }; 
 
    var length = 0; // {2}     
    var head = null; // {3} 
 
    this.append = function(element){};     
    this.insert = function(position, element){};     	   		
    this.removeAt = function(position){};     
    this.remove = function(element){};     
    this.indexOf = function(element){};     
    this.isEmpty = function() {};     
    this.size = function() {};     
    this.toString = function(){};     
    this.print = function(){}; }
```

​		LinkedList数据结构还需要一个Node辅助类（行{1}）。Node类表示要加入列表的项。它 包含一个element属性，即要添加到列表的值，以及一个next属性，即指向列表中下一个节点 项的指针。

​	LinkedList类也有存储列表项的数量的length属性（内部/私有变量）（行{2}）。 

​		另一个重要的点是，我们还需要存储第一个节点的引用。为此，可以把这个引用存储在一个 称为head的变量中（行{3}）。 

然后就是LinkedList类的方法。在实现这些方法之前，先来看看它们的职责。

```javascript
 append(element)：向列表尾部添加一个新的项。
 insert(position, element)：向列表的特定位置插入一个新的项
 remove(element)：从列表中移除一项。
 indexOf(element)：返回元素在列表中的索引。如果列表中没有该元素则返回-1。
 removeAt(position)：从列表的特定位置移除一项。
 isEmpty()：如果链表中不包含任何元素，返回true，如果链表长度大于0则返回false。
 size()：返回链表包含的元素个数。与数组的length属性类似。
 toString()：由于列表项使用了Node类，就需要重写继承自JavaScript对象默认的 toString方法，让其只输出元素的值
```

##### 向链表尾部追加元素

​		向LinkedList对象尾部添加一个元素时，可能有两种场景：列表为空，添加的是第一个元 素，或者列表不为空，向其追加元素

```javascript
this.append = function(element){ 
 
    var node = new Node(element), //{1}
        current; //{2} 
 
    if (head === null){ //列表中第一个节点 //{3}
        head = node; 
    } else { 
        current = head; //{4} 
 
        //循环列表，直到找到最后一项
        while(current.next){
            current = current.next;
        } 
 
        //找到最后一项，将其next赋为node，建立链接
        current.next = node; //{5}
    } 
 
    length++; //更新列表的长度 //{6} 
}; 
```

首先需要做的是把element作为值传入，创建Node项（行{1}）。 
先来实现第一个场景：向为空的列表添加一个元素。当我们创建一个LinkedList对象时， head会指向null：

![](/images/js5-6.png)

​	

![](E:\1244832273.github.io\themes\next\source\images\js5-6.png)

如果head元素为null（列表为空——行{3}），就意味着在向列表添加第一个元素。因此要 做的就是让head元素指向node元素。下一个node元素将会自动成为null

好了，我们已经说完了第一种场景。再来看看第二个，也就是向一个不为空的列表尾部添加 元素。 

​		要向列表的尾部添加一个元素，首先需要找到最后一个元素。记住，我们只有第一个元素的 引用（行{4}），因此需要循环访问列表，直到找到最后一项。为此，我们需要一个指向列表中 current项的变量（行{2}）。循环访问列表时，当current.next元素为null时，我们就知道 已经到达列表尾部了。然后要做的就是让当前（也就是最后一个）元素的next指针指向想要添 加到列表的节点（行{5}）。

下图展示了这个行为： 

![](/images/js5-7.png)

![](E:\1244832273.github.io\themes\next\source\images\js5-7.png)

而当一个Node元素被创建时，它的next指针总是null。这没问题，因为我们知道它会是列 表的最后一项。 
当然，别忘了递增列表的长度，这样就能控制它，轻松地得到列表的长度（行{6}）。

 我们可以通过以下代码来使用和测试目前创建的数据结构： 

```javascript
var list = new LinkedList();
list.append(15);
list.append(10); 
```

##### 从链表中移除元素 

​		现在，让我们看看如何从LinkedList对象中移除元素。移除元素也有两种场景：第一种是移 除第一个元素，第二种是移除第一个以外的任一元素。我们要实现两种remove方法：第一种是从 特定位置移除一个元素，第二种是根据元素的值移除元素（稍后我们会展示第二种remove方法）。

```javascript
this.removeAt = function(position){ 
 
    //检查越界值
    if (position > -1 && position < length){ // {1}
        var current = head, // {2}
            previous, // {3}
            index = 0; // {4} 
 
        //移除第一项
        if (position === 0){ // {5}
            head = current.next;
        } else { 
 
            while (index++ < position){ // {6} 
 
                previous = current;     // {7}
                current = current.next; // {8}
            } 
 
            //将previous与current的下一项链接起来：跳过current，从而移除它
            previous.next = current.next; // {9}
        } 
 
        length--; // {10} 
 
        return current.element; 
 
    } else {
        return null; // {11}
    }
}; 
```

​		一步一步来看这段代码。该方法要得到需要移除的元素的位置，就需要验证这个位置是有效 的（行{1}）。从0（包括0）到列表的长度（size – 1，因为索引是从零开始的）都是有效的位 置。如果不是有效的位置，就返回null（意即没有从列表中移除元素）。 

​		一起来为第一种场景编写代码：我们要从列表中移除第一个元素（position === 0——行 {5}）。下图展示了这个过程： 

![](/images/js5-8.png)

![](E:\1244832273.github.io\themes\next\source\images\js5-8.png)


​		因此，如果想移除第一个元素，要做的就是让head指向列表的第二个元素。我们将用 current变量创建一个对列表中第一个元素的引用（行{2}——我们还会用它来迭代列表，但稍 等一下再说）。这样current变量就是对列表中第一个元素的引用。如果把head赋为 current.next，就会移除第一个元素。 

​		现在，假设我们要移除列表的最后一项或者中间某一项。为此，需要依靠一个细节来迭代列 表，直到到达目标位置（行{6}——我们会使用一个用于内部控制和递增的index变量）：current 变量总是为对所循环列表的当前元素的引用（行{8}）。我们还需要一个对当前元素的前一个元 素的引用（行{7}）；它被命名为previous（行{3}）

​		因此，要从列表中移除当前元素，要做的就是将previous.next和current.next链接起 来（行{9}）。这样，当前元素就会被丢弃在计算机内存中，等着被垃圾回收器清除。 

​		我们试着通过一些图表来更好地理解。首先考虑移除最后一个元素：

![](/images/js5-9.png) 

![](E:\1244832273.github.io\themes\next\source\images\js5-9.png)

​		对于最后一个元素，当我们在行{6}跳出循环时，current变量将是对列表中最后一个元素 的引用（要移除的元素）。current.next的值将是null（因为它是最后一个元素）。由于还保留 了对previous元素的引用（当前元素的前一个元素） ，previous.next就指向了current。那 么要移除current，要做的就是把previous.next的值改变为current.next

​		现在来看看，对于列表中间的元素是否可以应用相同的逻辑： 

![](/images/js5-10.png)

![](E:\1244832273.github.io\themes\next\source\images\js5-10.png)

​		current变量是对要移除元素的引用。previous变量是对要移除元素的前一个元素的引用。 那么要移除current元素，需要做的就是将previous.next与current.next链接起来。因此， 我们的逻辑对这两种情况都管用

##### 在任意位置插入一个元素 

我们要实现insert方法。使用这个方法可以在任意位置插入一个元素。我们来看 一看它的实现：

 

```javascript
this.insert = function(position, element){ 
 
    //检查越界值
    if (position >= 0 && position <= length){ //{1} 
 
        var node = new Node(element),
            current = head,
            previous,
            index = 0; 
 
        if (position === 0){ //在第一个位置添加 
 
            node.next = current; //{2}
            head = node; 
 
        } else {
            while (index++ < position){ //{3}
                previous = current;
                current = current.next;
            }             
            node.next = current; //{4}
            previous.next = node; //{5}         } 
 
        length++; //更新列表的长度 
 
        return true; 
 
    } else {
        return false; //{6}
    }
}; 
```

​		由于我们处理的是位置，就需要检查越界值（行{1}，跟remove方法类似）。如果越界了， 就返回false值，表示没有添加项到列表中（行{6}）。

​		现在我们要处理不同的场景。第一种场景，需要在列表的起点添加一个元素，也就是第一个 位置。下图展示了这种场景 

![](/images/js5-11.png)

​	![](E:\1244832273.github.io\themes\next\source\images\js5-11.png)

​		current变量是对列表中第一个元素的引用。我们需要做的是把node.next的值设为 current（列表中第一个元素）。现在head和node.next都指向了current。接下来要做的就是 把head的引用改为node（行{2}），这样列表中就有了一个新元素.

​		现在来处理第二种场景：在列表中间或尾部添加一个元素。首先，我们需要循环访问列表， 找到目标位置（行{3}）。当跳出循环时，current变量将是对想要插入新元素的位置之后一个 元素的引用，而previous将是对想要插入新元素的位置之前一个元素的引用。在这种情况下， 我们要在previous和current之间添加新项。因此，首先需要把新项（node）和当前项链接起 来（行{4}），然后需要改变previous和current之间的链接。我们还需要让previous.next 指向node（行{5}）。 

![](/images/js5-12.png)

![](E:\1244832273.github.io\themes\next\source\images\js5-12.png)

​		如果我们试图向最后一个位置添加一个新元素，previous将是对列表最后一项的引用，而 current将是null。在这种情况下，node.next将指向current，而previous.next将指向 node，这样列表中就有了一个新的项。 

现在来看看如何向列表中间添加一个新元素： 

![](/images/js5-13.png)

![](E:\1244832273.github.io\themes\next\source\images\js5-13.png)

​		在这种情况下，我们试图将新的项（node）插入到previous和current元素之间。首先， 我们需要把node.next的值指向current。然后把previous.next的值设为node。这样列表中 就有了一个新的项。 