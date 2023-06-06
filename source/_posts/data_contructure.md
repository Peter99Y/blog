---
title: 数据结构与算法
---

### 栈结构 (stack)

受限的线性表，遵循**后进先出（las in first out）**
仅能在表的一端进行插入和删除，称为**栈顶**，另一段**栈底**

###### 概念

-   入栈/进栈/压栈

向栈中插入新元素称为，它把新元素放在栈顶元素的上面，使之成为新的栈顶元素。

-   出栈/退栈

从栈中删除元素，先把栈顶元素删除，使相邻元素称为新的栈顶元素。

###### 函数调用栈

A 函数调用 B, B 调用 C。
在执行的过程中，会先把 A 函数压栈，A 函数还没有执行完，所以不会出栈。
入栈顺序：A, B, C 等到最后 C 执行完，C，B，A 就会依次出栈。
比如说递归就是无限调用自己进行入栈，没有出栈，造成栈溢出。

###### 题目

有 6 个元素 654321 呈顺序入栈，请选出不合法的出栈循序
A. 5,4,3,6,1,2  
B. 4,5,3,2,1,6  
C. 3,4,6,5,2,1 false
D. 2,3,4,1,5,6

A. 【5,6】 5 先出栈，6 一定在 5 下面，栈里只剩下 6，此时 4 入栈再出栈，3 入栈出栈，6 出栈
B. 【4,5,6】 4 先出栈，56 一定在 4 下面，5 出栈，3 入栈出栈，2 入栈出栈，1 入栈出栈，6 出栈
D. 【2,3,4,5,6】

###### 基于数组实现栈结构

```
function Stack() {
	this.items = [];

	// push 添加一个新元素到栈顶
	Stack.prototype.push = function (ele) {
		this.items.push(ele);
	};

	// pop 移除栈顶元素，并返回被移除的元素
	Stack.prototype.pop = function () {
		return this.items.pop();
	};

	// peek 返回栈顶元素（不做任何修改）
	Stack.prototype.peek = function () {
		return this.items[this.items.length - 1];
	};

	// isEmpty 栈是否有元素
	Stack.prototype.isEmpty = function () {
		return this.items.length === 0;
	};

	// size 返回栈里元素个数
	Stack.prototype.size = function (ele) {
		return this.items.length;
	};

	// toString 将栈结构的内容以字符形式返回
	Stack.prototype.toString = function (ele) {
		var str = "";
		for (var i = 0; i < this.items.length; i++) {
			str += this.items[i] + " ";
		}
		return str;
	};
}

var s = new Stack();
s.push(1);
s.push(2);
s.push(3);

console.log(s.peek());      // 3
console.log(s.toString());  // 1 2 3
```

###### 利用栈结构将 10 进制转 2 进制

```
function decToBin(dec) {
	var stack = new Stack();

	while (dec > 0) {
		// 将模2的余数押入栈
		stack.push(dec % 2);

		// 除2后的整数结果作为下一次计算的被除数
		dec = Math.floor(dec / 2);
	}

        // pop移除数组最后一位: [0,0,1,0,0,1,1]
	var binStr = "";
	while (!stack.isEmpty()) {
		binStr += stack.pop();
	}
	return binStr;
}

let binNum = decToBin(100);
console.log(binNum);        // 1100100
```

---

### 队列 (queue)

受限的线性表，遵循先进先出（first in first out）
在表的**前端**进行删除操作，表的后端进行**插入**操作。

###### 基于数组实现队列结构

```
function Queue() {
	this.items = [];

	Queue.prototype.enqueue = function (ele) {
		this.items.push(ele);
	};

	Queue.prototype.dequeue = function () {
		return this.items.shift();
	};

	// 查看前端元素
	Queue.prototype.front = function () {
		return this.items[0];
	};

	Queue.prototype.isEmpty = function () {
		return this.items.length === 0;
	};

	Queue.prototype.size = function () {
		return this.items.length;
	};

	Queue.prototype.toString = function () {
		var str = "";
		for (var i = 0; i < this.items.length; i++) {
			str += this.items[i] + " ";
		}
		return str;
	};
}
```

###### 利用队列实现击鼓传花

```
function passGame(nameList, num) {
	// 1.生成一个队列结构
	var queue = new Queue();

	// 2.将所有人依次加入到队列中
	for (var i = 0; i < nameList.length; i++) {
		queue.enqueue(nameList[i]);
	}

	while (queue.size() > 1) {
		// num之前的人重新加入队列
		for (var i = 0; i < num - 1; i++) {
			queue.enqueue(queue.dequeue());
		}
		// num则直接淘汰(删除数组第一位);
		queue.dequeue();
	}

	// 4.返回最后人
	return queue.front()
}

var theLast = passGame(['lili','lucy','tom','lilei','why'], 3)
console.log(theLast)	// 'lilei';
```

###### 优先队列

在插入一个新的元素时候需要与其他数据进行比较，放入到正确的位置

```
function PriorityQueue() {
	this.items = [];

	function QueueElement(ele, pri) {
		this.element = ele;
		this.priority = pri;
	}

	// 插入方法：需要比较之前插入元素的优先级
	PriorityQueue.prototype.enqueue = function (ele, pri) {
		var obj = new QueueElement(ele, pri);

		// 1.第一次插入时无需比较
		if (this.items.length === 0) {
			return this.items.push(obj);
		}

		// 2.插入的元素优先级小于以前的元素优先级，就插入到其前面位置
		var added = false;
		for (var i = 0; i < this.items.length; i++) {
			if (obj.priority < this.items[i].priority) {
				this.items.splice(i, 0, obj);
				added = true;
				break;
			}
		}

		// 3.比较后没有比当前更小的，则插入到最后
		if (!added) {
			this.items.push(obj);
		}
	};

	// ...其他方法和队列一致
	PriorityQueue.prototype.toString = function () {
		var str = "";
		for (var i = 0; i < this.items.length; i++) {
			str += this.items[i].element + "-" + this.items[i].priority + ", ";
		}
		return str;
	};
}

var pq = new PriorityQueue();
pq.enqueue("a", 10);
pq.enqueue("b", 9);
pq.enqueue("c", 8);
pq.enqueue("d", 11);

console.log(pq.toString()); // c-8, b-9, a-10, d-11
```

---

### 链表

线性结构；
链表中每个元素包含存储的数据和指向下一个元素的指针；
![](/images/linkedList.jpg)

---

###### 数组缺点

-   (大多数语言中)数组大小是固定的，需要申请一段**连续的内存空间**，当目前数组不能满足容量大小需要**扩容**(申请更大的数组并将原数组中的元素复制过去)；

-   在数组的开头和中间位置进行插入操作，需要进行大量元素位移，js 中 Array 背后原理亦是如此；

---

###### 链表优缺点

-   在创建时不必确定大小，内存空间不必是连续的，大小可以无限延伸；

-   **获取，修改：**数组通过下标直接进行操作，性能相对高；而链表访问任何一个位置的元素时，里面的元素都是相连的，需要**从头开始访问**(无法跳过某个元素)直到找到对应的元素；

-   **插入，删除：**链表**时间复杂度**可以达到 O(1),性能相对高；

```
function LinkedList() {
	this.head = null;
	this.length = 0;

	function Node(data) {
		this.data = data;
		this.next = null;
	}

	// 追加节点
	LinkedList.prototype.append = function (data) {
		var node = new Node(data);
		if (this.length === 0) {
			this.head = node;
		} else {
			var obj = this.head;

			// obj是最后节点
			// 最后节点obj.next必然指向null;
			while (obj.next) {
				obj = obj.next;
			}
			obj.next = node;
		}
		this.length += 1;
	};

	LinkedList.prototype.toString = function () {
		var obj = this.head;
		var str = "";

		// obj === null，也就不执行里面代码
		while (obj) {
			str += obj.data + " ";
			obj = obj.next;
		}
		return str;
	};
}

var list = new LinkedList()
list.append('a')
list.append('b')
list.append('c')

console.log(list.toString());	// a b c
```

###### 代码实现

```
function LinkedList() {
	this.head = null;
	this.length = 0;

	function Node(data) {
		this.data = data;
		this.next = null;
	}

	// 追加节点
	LinkedList.prototype.append = function (data) {
		var node = new Node(data);
		if (this.length === 0) {
			this.head = node;
		} else {
			var obj = this.head;

			// obj是最后节点
			// 最后节点obj.next必然指向null;
			while (obj.next) {
				obj = obj.next;
			}
			obj.next = node;
		}
		this.length += 1;
	};

	LinkedList.prototype.toString = function () {
		var obj = this.head;
		var str = "";

		// obj === null，也就不执行里面代码
		while (obj) {
			str += obj.data + " ";
			obj = obj.next;
		}
		return str;
	};

	// 插入节点
	LinkedList.prototype.insert = function (index, data) {
		// 越界判断
		if (index < 0 || index > this.length) return false;

		var node = new Node(data);

		// 插入到第一个
		if (index === 0) {
			node.next = this.head;
			this.head = node;
		} else {
			var num = 0;
			// prevObj就是找到插入位置的前一个元素；
			var prevObj = null;
			// obj就是找到插入位置的后一个元素；
			var nextObj = this.head;

			while (num < index) {
				prevObj = nextObj;
				nextObj = nextObj.next;
				num++;
			}
			prevObj.next = node;
			node.next = nextObj;
		}
		this.length += 1;
	};

	// 根据下标获取值
	LinkedList.prototype.get = function (index) {
		if (index < 0 || index >= this.length) return null;

		var nextObj = this.head;
		var num = 0;
		while (num < index) {
			nextObj = nextObj.next;
			num++;
		}
		return nextObj.data;
	};

	// 根据值获取下标
	LinkedList.prototype.indexOf = function (data) {
		var index = 0;
		var current = this.head;

		while (current) {
			if (current.data == data) {
				return index;
			}
			current = current.next;
			index += 1;
		}

		// 没找到
		return -1;
	};

	// 根据下标修改
	LinkedList.prototype.update = function (index, data) {
		if (index < 0 || index >= this.length) return false;

		var current = this.head;
		var num = 0;
		while (num < index) {
			current = current.next;
			num++;
		}

		current.data = data;
		return true;
	};

	// 根据下标移除数据
	LinkedList.prototype.removeAt = function (index) {
		if (index < 0 || index >= this.length) return false;

		if (index === 0) {
			this.head = this.head.next;
		} else {
			var num = 0;
			var nextObj = this.head;
			var prevObj = null;
			while (num < index) {
				prevObj = nextObj;
				nextObj = nextObj.next;
				num++;
			}
			prevObj.next = nextObj.next;
		}
		this.length - 1;
		return this.toString();
		// return true;
	};

	// 根据值移除数据
	LinkedList.prototype.remove = function (data) {
		var index = this.indexOf(data);
		return this.removeAt(index);
	};
}

var list = new LinkedList();
list.append("a");
list.append("b");
list.append("c");
list.insert(2, "d");

console.log(list.toString()); // a b d c
console.log(list.get(1)); // b
console.log(list.indexOf("a")); // 0

// console.log(list.removeAt(1));
// console.log(list.remove("a"));
```

### 双向链表

![](/images/doubleLinkedList.jpg)

head 和 tail 分别指向头部和尾部的节点;
每个节点有 3 个属性，prev 指向前一个节点，next 指向下一个，data 存储当前节点数据;

```
function DoubleLinkedList() {
	this.head = null;
	this.tail = null;
	this.length = 0;

	function Node(data) {
		this.prev = null;
		this.data = data;
		this.next = null;
	}

	DoubleLinkedList.prototype.append = function (data) {
		var node = new Node(data);

		if (this.length === 0) {
			this.head = node;
		} else {
			var current = this.head;
			while (current.next) {
				current = current.next;
			}
			current.next = node;
			node.prev = current;
		}
		this.tail = node;
		this.length += 1;
	};
	DoubleLinkedList.prototype.toString = function () {
		var str = "";
		var current = this.head;

		while (current) {
			str += current.data + " ";
			current = current.next;
		}
		console.log(str);
		return str;
	};

	DoubleLinkedList.prototype.backToString = function () {
		var str = "";
		var current = this.tail;
		while (current) {
			str += current.data + " ";
			current = current.prev;
		}

		console.log(str);
		return str;
	};

	DoubleLinkedList.prototype.insert = function (index, data) {
		if (index < 0 || index > this.length) return false;

		var node = new Node(data);

		if (!this.length) {
			this.head = node;
			this.tail = node;
			this.length += 1;
			return;
		}

		if (index === 0) {
			var nextNode = this.head;
			node.next = nextNode;
			nextNode.prev = node;
			this.head = node;
		} else if (index === this.length) {
			var prevNode = this.tail;
			prevNode.next = node;
			node.prev = prevNode;
			this.tail = node;
		} else {
			// 找到插入下标位置的前一个节点
			var prevNode = null;
			// 找到插入下标位置的后一个节点
			var nextNode = this.head;
			var num = 0;
			while (num < index) {
				prevNode = nextNode;
				nextNode = nextNode.next;
				num++;
			}
			// 修改指针
			prevNode.next = node;
			node.prev = prevNode;
			node.next = nextNode;
			nextNode.prev = node;
		}

		this.length += 1;
		console.log(this.toString());
	};

	// 根据下标正/反向查询数据
	DoubleLinkedList.prototype.getDataByIndex = function (index) {
		if (index < 0 || index > this.length) return null;

		var dir = this.length / 2 > index;
		var current = null;

		if (dir) {
			current = this.head;
			var num = 0;
			while (num < index) {
				current = current.next;
				num++;
			}
		} else {
			current = this.tail;
			var num = this.length - 1;
			while (num > index) {
				current = current.prev;
				num--;
			}
		}

		return current.data;
	};

	DoubleLinkedList.prototype.indexOf = function (data) {
		var index = 0;
		var current = this.head;
		while (current) {
			if (current.data == data) {
				return index;
			}
			current = current.next;
			index++;
		}

		return -1;
	};

	DoubleLinkedList.prototype.update = function (index, data) {
		if (index < 0 || index >= this.length) return false;
		var num = 0;
		var current = this.head;

		while (num < index) {
			current = current.next;
			num++;
		}
		current.data = data;

		console.log(this.toString());
		return true;
	};

	// 移除下标位置元素
	DoubleLinkedList.prototype.removeAt = function (index) {
		if (index < 0 || index >= this.length) return null;

		if (this.length === 1) {
			this.head = null;
			this.tail = null;
		} else {
			if (index === 0) {
				var nextNode = this.head["next"];
				nextNode.prev = null;
				this.head = nextNode;
			} else if (index === this.length - 1) {
				var prevNode = this.tail["prev"];
				prevNode.next = null;
				this.tail = prevNode;
			} else {
				var prevNode = null;
				var current = this.head;
				var num = 0;
				while (num < index) {
					prevNode = current;
					current = current.next;
					num++;
				}
				var nextNode = current.next;
				prevNode.next = nextNode;
				nextNode.prev = prevNode;
			}
		}

		this.length -= 1;
		this.toString();
		return true;
	};

	DoubleLinkedList.prototype.remove = function (data){
		var index = this.indexOf(data);
		this.removeAt(index);
	}
}

var dbList = new DoubleLinkedList();
dbList.append("a");
dbList.append("b");
dbList.append("c");
```
