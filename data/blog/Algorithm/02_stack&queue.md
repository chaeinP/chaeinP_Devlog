---
title: Algorithm_Stack&Queue
date: '2021-07-26'
tags: ['Algorithm']
draft: false
summary: 자료구조 중에서 그나마 쉽다는 스택과 큐를 구현해보았다.
---

### Stack

> **A LIFO(Last In First Out) data structure**
> The last element added to the stack will be the first element removed from the stack

#### stack 구현하기

**1. 클래스 선언**

```js
class Node {
  constructor(value) {
    this.value = value
    this.next = next
  }
}

class Stack {
  constructor() {
    this.first = null
    this.last = null
    this.size = 0
  }
}
```

**2. push() 구현**

pseudocode

- The function should accept a value
- Create a new node with that value
- If there are no nodes in the stack, set the first and last property to be the newly created node
- If there is at least one node, create a variable that stores the current first property on the stack
- Reset the first property on the node to be the previously created variable
- Increment the size of the stack by 1

```js
class Stack {
  constructor() {
    this.first = null
    this.last = null
    this.size = 0
  }

  push(val) {
    let newNode = new Node(val)
    if (!this.first) {
      this.first = newNode
      this.last = newNode
    } else {
      let temp = this.first
      this.first = new Node()
      this.first.next = temp
    }
    return ++this.size
  }
}
```

**3. pop() 구현**

pseudocode

- If there are no nodes in the stack, return null
- Create a temporary variable to store the first property on the stack
- If there is only 1 node, set the first and last property to be null
- If there is more than one node, set the first property to be the next property on the current first
- Decrement the size by 1
- Return the value of the node removed

```js
class Stack {
  constructor() {
    this.first = null
    this.last = null
    this.size = 0
  }

  push(val) {
    let newNode = new Node(val)
    if (!this.first) {
      this.first = newNode
      this.last = newNode
    } else {
      let temp = this.first
      this.first = new Node()
      this.first.next = temp
    }
    return ++this.size
  }
}
```

**Big O of Stack**

> Insertion - O(1)
> Removal - O(1)
> Searching - O(N)
> Access - O(N)

### Queue

> **A FIFO(First in First Out) data structure**

#### 배열에서의 Queue

```js
// 1. push() & shift()
let Q = []
Q.push(1) // 1 인덱스 리턴
Q.push(2) // 2 인덱스 리턴
Q.push(3) // 3 인덱스 리턴

Q // [1,2,3]

Q.shift() // 1 value 리턴
Q.shift() // 2 value 리턴
Q.shift() //3 value 리턴

// 2 unshift() & pop()
let Q = []
Q.unshift(1) // 1 배열의 길이 리턴
Q.unshift(2) // 2
Q.unshift(3) // 3

Q.pop() // 1 value 리턴
Q.pop() // 2 value 리턴
Q.pop() // 3 value 리턴
```

#### Queue 구현하기

> 배열을 사용하는 건 편리하지만 인덱싱 과정이 추가되어 메모리를 더 많이 사용한다. 인덱싱이 필요하지 않다면 Queue클래스를 따로 생성해 사용할 수 있다.

**1. 클래스 선언**

```js
class Node {
  constructor(value) {
    this.value = value;
    this.next = null;
  }
}

class Queue {
  constructor() {
    this.first = null;
    this.last = null;
    this.size = 0;
  }
```

**2.enqueue() 구현** == push()

- This function accepts some value
- Create a new node using that value passed to the function
- If there are no nodes in the queue, set this node to be the first and last property of the queue
- Increment the size of the queue by 1

```js
class Node {
  constructor(value) {
    this.value = value
    this.next = null
  }
}

class Queue {
  constructor() {
    this.first = null
    this.last = null
    this.size = 0
  }
  enqueue(val) {
    let newNode = new Node(val)
    if (!this.first) {
      this.first = newNode
      this.last = newNode
    } else {
      this.last.next = newNode
      this.last = newNode
    }
    return ++this.size
  }
}
```

**3.dequeue() 구현** == shift()

- If there is no first property, just return null
- Store the first property in a variable
- See if the first is the same as the last(check if there is only 1 node.) If so, set the first and last to be null.
- If there is more than 1 node, set the first property to be the next property of first
- Decrement the size by 1

```js
class Queue {
  constructor() {
    this.first = null
    this.last = null
    this.size = 0
  }
  dequeue() {
    if (!this.first) return null
    let temp = this.first
    if (this.first === this.last) {
      this.last = null
    }
    this.first = this.first.next
    this.size--
    return temp.value
  }
}
```

**Big O of Queue**

> Insertion - O(1)
> Removal - O(1)
> Searching - O(N)
> Access - O(N)
