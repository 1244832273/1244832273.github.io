---
title: 数组 #文章页面上的显示名称，一般是中文
date: 2020-7-21 14:00 #文章生成时间，一般不改，当然也可以任意修改
categories: JavaScript #分类
tags: [JavaScript, 学习JavaScript数据结构与算法] #文章标签，可空，多标签请用格式，注意:后面有个空格
description: 学习JavaScript数据结构与算法

---

#### 添加元素

​	在数组中添加和删除元素也很容易，但有时也会很棘手。假如我们有一个数组`numbers`，初始化成了0到9。

```
let numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
```

##### 在数组末尾插入元素

如果想要给数组添加一个元素（比如`10`），只要把值赋给数组中最后一个空位上的元素即可。

```
numbers[numbers.length] = 10;
```

> 在JavaScript中，数组是一个可以修改的对象。如果添加元素，它就会动态增长。在C和Java等其他语言里，我们要决定数组的大小，想添加元素就要创建一个全新的数组，不能简单地往其中添加所需的元素。

###### 使用push方法

另外，还有一个`push`方法，能把元素添加到数组的末尾。通过`push`方法，我们能添加任意个元素。

```
numbers.push(11);
numbers.push(12, 13);
```

如果输出`numbers`的话，就会看到从0到13的值。

##### 在数组开头插入元素

现在，我们希望在数组中插入一个新元素（数-1），不像之前那样插入到最后，而是放到数组的开头。为了实现这个需求，首先要腾出数组里第一个元素的位置，把所有的元素向右移动一位。我们可以循环数组中的元素，从最后一位（长度值就是数组的末尾位置）开始，将对应的前一个元素（`i-1`）的值赋给它（`i`），依次处理，最后把我们想要的值赋给第一个位置（索引0）上。我们可以将这段逻辑写成一个函数，甚至将该方法直接添加在`Array`的原型上，使所有数组的实例都可以访问到该方法。下面的代码表现了这段逻辑。

```
Array.prototype.insertFirstPosition = function(value) {
  for (let i = this.length; i >= 0; i--) {
    this[i] = this[i - 1];
  }
  this[0] = value;
};
numbers.insertFirstPosition(-1);
```

下图描述了我们刚才的操作过程。

![](/images/js2-1.png)

![](E:\1244832273.github.io\themes\next\source\images\js2-1.png)

###### 使用`unshift`方法

在JavaScript里，数组有一个方法叫`unshift`，可以直接把数值插入数组的开头（此方法背后的逻辑和`insertFirstPosition`方法的行为是一样的）。

```
numbers.unshift(-2);
numbers.unshift(-4, -3);
```

那么，用`unshift`方法，我们就可以在数组的开始处添加值-2，然后添加-3、-4等。这样数组就会输出数-4到13。

#### 删除元素

目前为止，我们已经学习了如何给数组的开始和结尾位置添加元素。下面来看一下怎样从数组中删除元素。

##### 从数组末尾删除元素`pop`

要删除数组里最靠后的元素，可以用`pop`方法。

```
numbers.pop();
```

> 通过`push`和`pop`方法，就能用数组来模拟栈，你将在下一章看到这部分内容。

现在，数组输出的数是-4到12，数组的长度是17。

##### 从数组开头删除元素shift

如果要移除数组里的第一个元素，可以用下面的代码。

```
for (let i = 0; i < numbers.length; i++) {
  numbers[i] = numbers[i + 1];
}
```

下图呈现了这段代码的执行过程。

![](/images/js2-2.png)

![](E:\1244832273.github.io\themes\next\source\images\js2-2.png)

我们把数组里所有的元素都左移了一位，但数组的长度依然是17，这意味着数组中有额外的一个元素（值是`undefined`）。在最后一次循环里，`i+1`引用了数组里还未初始化的一个位置。在Java、C/C+或C#等一些语言里，这样写可能会抛出异常，因此不得不在`numbers.length- 1`处停止循环。

可以看到，我们只是把数组第一位的值用第二位覆盖了，并没有删除元素（因为数组的长度和之前还是一样的，并且多了一个未定义元素）。

要从数组中移除这个值，还可以创建一个包含刚才所讨论逻辑的方法，叫作`removeFirstPosition`。但是，要真正从数组中移除这个元素，我们需要创建一个新的数组，将所有不是`undefined`的值从原来的数组复制到新的数组中，并且将这个新的数组赋值给我们的数组。要完成这项工作，也可以像下面这样创建一个`reIndex`方法。

```
Array.prototype.reIndex = function(myArray) {
  const newArray = [];
  for(let i = 0; i < myArray.length; i++ ) {
    if (myArray[i] !== undefined) {
      // console.log(myArray[i]);
      newArray.push(myArray[i]);
    }
  }
  return newArray;
}

// 手动移除第一个元素并重新排序
Array.prototype.removeFirstPosition = function() {
  for (let i = 0; i < this.length; i++) {
    this[i] = this[i + 1];
  }
  return this.reIndex(this);
};

numbers = numbers.removeFirstPosition();
```

**使用`shift`方法**

要删除数组的第一个元素，可以用`shift`方法实现。

```
numbers.shift();
```

假如本来数组中的值是从-4到12，长度为17。执行了上述代码后，数组就只有-3到12了，长度也会减小到16。

#### JavaScript的数组方法参考

在JavaScript里，数组是经过改进的对象，这意味着创建的每个数组都有一些可用的方法。数组很有趣，因为它十分强大，并且相比其他语言中的数组，JavaScript中的数组有许多很好用的方法。这样就不用再为它开发一些基本功能了，例如在数据结构的中间添加或删除元素。

下表详述了数组的一些核心方法，其中的一些我们已经学习过了。

| 方法          | 描述                                                         |
| :------------ | :----------------------------------------------------------- |
| `concat`      | 连接2个或更多数组，并返回结果                                |
| `every`       | 对数组中的每个元素运行给定函数，如果该函数对每个元素都返回`true`，则返回`true` |
| `filter`      | 对数组中的每个元素运行给定函数，返回该函数会返回`true`的元素组成的数组 |
| `forEach`     | 对数组中的每个元素运行给定函数。这个方法没有返回值           |
| `join`        | 将所有的数组元素连接成一个字符串                             |
| `indexOf`     | 返回第一个与给定参数相等的数组元素的索引，没有找到则返回`-1` |
| `lastIndexOf` | 返回在数组中搜索到的与给定参数相等的元素的索引里最大的值     |
| `map`         | 对数组中的每个元素运行给定函数，返回每次函数调用的结果组成的数组 |
| `reverse`     | 颠倒数组中元素的顺序，原先第一个元素现在变成最后一个，同样原先的最后一个元素变成了现在的第一个 |
| `slice`       | 传入索引值，将数组里对应索引范围内的元素作为新数组返回       |
| `some`        | 对数组中的每个元素运行给定函数，如果任一元素返回`true`，则返回`true` |
| `sort`        | 按照字母顺序对数组排序，支持传入指定排序方法的函数作为参数   |
| `toString`    | 将数组作为字符串返回                                         |
| `valueOf`     | 和`toString`类似，将数组作为字符串返回                       |

我们已经学过了`push`、`pop`、`shift`、`unshift`和`splice`方法。

###### **使用`reduce`方法**

最后是`reduce`方法。`reduce`方法接收一个有如下四个参数的函数：`previousValue`、`currentValue`、`index`和`array`。因为`index`和`array`是可选的参数，所以如果用不到它们的话，可以不传。这个函数会返回一个将被叠加到累加器的值，`reduce`方法停止执行后会返回这个累加器。如果要对一个数组中的所有元素求和，这就很有用。下面是一个例子。

```
numbers.reduce((previous, current) => previous + current);
```

输出将是`120`



#### ECMAScript 6和数组的新功能

第1章提到过，**ECMAScript 2015**（**ES6**或**ES2015**）和更新的规范（2015+）给JavaScript语言带来了新的功能。

下表列出了ES2015和ES2016新增的数组方法。

| 方法         | 描述                                                         |
| :----------- | :----------------------------------------------------------- |
| `@@iterator` | 返回一个包含数组键值对的迭代器对象，可以通过同步调用得到数组元素的键值对 |
| `copyWithin` | 复制数组中一系列元素到同一数组指定的起始位置                 |
| `entries`    | 返回包含数组所有键值对的`@@iterator`                         |
| `includes`   | 如果数组中存在某个元素则返回`true`，否则返回`false`。E2016新增 |
| `find`       | 根据回调函数给定的条件从数组中查找元素，如果找到则返回该元素 |
| `findIndex`  | 根据回调函数给定的条件从数组中查找元素，如果找到则返回该元素在数组中的索引 |
| `fill`       | 用静态值填充数组                                             |
| `from`       | 根据已有数组创建一个新数组                                   |
| `keys`       | 返回包含数组所有索引的`@@iterator`                           |
| `of`         | 根据传入的参数创建一个新数组                                 |
| `values`     | 返回包含数组中所有值的`@@iterator`                           |

除了这些新的方法，还有一种用`for...of`循环来迭代数组的新做法，以及可以从数组实例得到的迭代器对象。

###### 使用`@@iterator`对象

ES2015还为`Array`类增加了一个`@@iterator`属性，需要通过`Symbol.iterator`来访问。代码如下。

```
let iterator = numbers[Symbol.iterator]();
console.log(iterator.next().value); // 1
console.log(iterator.next().value); // 2
console.log(iterator.next().value); // 3
console.log(iterator.next().value); // 4
console.log(iterator.next().value); // 5
```

然后，不断调用迭代器的`next`方法，就能依次得到数组中的值。`numbers`数组中有15个值，因此需要调用15次`iterator.next().value`。

我们可以用下面的代码来输出`numbers`数组中的15个值。

```
iterator = numbers[Symbol.iterator]();
for (const n of iterator) {
  console.log(n);
}
```

数组中的所有值都迭代完之后，`iterator.next().value`会返回`undefined`。

#### 排序元素

通过本书，我们能学到如何编写最常用的搜索和排序算法。其实，JavaScript里也提供了一个排序方法和一组搜索方法。让我们来看看。

首先，我们想反序输出数组`numbers`（它本来的排序是`1, 2, 3, 4, ..., 15`）。要实现这样的功能，可以用`reverse`方法，然后数组内元素就会反序。

```
numbers.reverse();
```

现在，输出`numbers`的话就会看到`[15, 14, 13, 12, 11, 10, 9, 8, 7, 6, 5, 4, 3, 2, 1]`。然后，我们使用`sort`方法。

```
numbers.sort();
```

然而，如果输出数组，结果会是`[1, 10, 11, 12, 13, 14, 15, 2, 3, 4, 5, 6, 7, 8, 9]`。看起来不大对，是吧？这是因为`sort`方法在对数组做排序时，把元素默认成字符串进行相互比较。

我们可以传入自己写的比较函数。因为数组里都是数，所以可以像下面这样写。

```
numbers.sort((a, b) => a - b);
```

在`b`大于`a`时，这段代码会返回负数，反之则返回正数。如果相等的话，就会返回0。也就是说返回的是负数，就说明`a`比`b`小，这样`sort`就能根据返回值的情况对数组进行排序。

之前的代码也可以表示成如下这样，会更清晰一些。

```
function compare(a, b) {
  if (a < b) {
    return -1;
  }
  if (a > b) {
    return 1;
  }
  // a必须等于b
  return 0;
}
numbers.sort(compare);
```

这是因为JavaScript的`sort`方法接收`compareFunction`作为参数，然后`sort`会用它排序数组。在这个例子里，我们声明了一个用来比较数组元素的函数，使数组按升序排序。

##### 自定义排序

我们可以对任何对象类型的数组排序，也可以创建`compareFunction`来比较元素。例如，对象`Person`有名字和年龄属性，我们希望根据年龄排序，就可以这么写。

```
const friends = [
  { name: 'John', age: 30 },
  { name: 'Ana', age: 20 },
  { name: 'Chris', age: 25 }, // ES2017允许存在尾逗号
];
function comparePerson(a, b) {
  if (a.age < b.age) {
    return -1;
  }
  if (a.age > b.age) {
    return 1;
  }
  return 0;
}
console.log(friends.sort(comparePerson));
```

在这个例子里，最后会输出`Ana(20), Chris(25), John(30)`。

##### 字符串排序

假如有这样一个数组。

```
let names = ['Ana', 'ana', 'john', 'John'];
console.log(names.sort());
```

你猜会输出什么？答案如下所示。

```
["Ana", "John", "ana", "john"]
```

既然`a`在字母表里排第一位，为何`ana`却排在了`John`之后呢？这是因为JavaScript在做字符比较的时候，是根据字符对应的ASCII值来比较的。例如，`A`、`J`、`a`、`j`对应的ASCII值分别是`65`、`74`、`97`、`106`。

虽然`a`在字母表里是最靠前的，但`J`的ASCII值比`a`的小，所以排在了`a`前面。



现在，如果给`sort`传入一个忽略大小写的比较函数，将输出`["Ana", "ana", "John", "john"]`。

```
names = ['Ana', 'ana', 'john', 'John']; // 重置数组的初始状态
console.log(names.sort((a, b) => {
  if (a.toLowerCase() < b.toLowerCase()) {
    return -1;
  }
  if (a.toLowerCase() > b.toLowerCase()) {
    return 1;
  }
  return 0;
}));
```

在这种情况下，`sort`函数不会有任何作用。它会按照现在的大小写字母顺序排序。

如果希望小写字母排在前面，那么需要使用`localeCompare`方法。

```
names.sort((a, b) => a.localeCompare(b));
```

输出结果将是`["ana", "Ana", "john", "John"]`。

假如对带有重音符号的字符做排序的话，也可以用`localeCompare`来实现。

```
const names2 = ['Maève', 'Maeve'];
console.log(names2.sort((a, b) => a.localeCompare(b)));
```

最后输出的结果将是`["Maeve", "Maève"]`。