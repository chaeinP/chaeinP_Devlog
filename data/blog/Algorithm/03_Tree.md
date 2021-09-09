---
title: Algorithm_Tree
date: '2021-07-31'
tags: ['Algorithm']
draft: false
summary: 이진 탐색 트리구현! 순회가 핵심!
---

### What is a tree?

> A data structure that consists of nodes in a parent / child relationship

#### Binary Tree

- Every parent node has at most two children

#### Binary Search Tree

- Every node to the left of a parent node is always less than the parent
- Every node to the right of a parent node is always greater than the parent

#### Make a binary search tree

```js
class Node {
  constructor(value) {
    this.value = value
    this.left = null
    this.right = null
  }
}

class BinarySearchTree {
  constructor() {
    this.root = null
  }
}
```

**Inserting**(Iteratively or Recursively)

- Create a new node
- Start at the root
  - Check if there is a root, if not -the root now becomes that new node
  - If there is a root, check if the value of the new node is greater than or less than the value of the root
  - If it is greater
    - Check to see If there is a node to the right
      - If there is, move to that node and repeat these steps
      - If there is not, add that node as the right property
  - If it is less
    - Check to see If there is a node to the left
      - If there is, move to that node and repeat these steps
      - If there is not, add that node as the left property

```js
insert(value) {
    let newNode = new Node(value);
    if (this.root === null) {
      this.root = newNode;
      return this;
    } else {
      let current = this.root;
      while (true) {
        if (value === current.value) return undefined;
        if (value < current.value) {
          if (current.left === null) {
            current.left = newNode;
            return this;
          } else {
            current = current.left;
          }
        } else if (value > current.value) {
          if (current.right === null) {
            current.right = newNode;
            return this;
          } else {
            current = current.right;
          }
        }
      }
    }
  }
```

**Finding a node**

- Starting at the root
  - Check if there is a root, if not - we're done searching
  - It there is a root, check if the value of the new node is the value we are looking for. If we found it, we're done
  - If not, check to see if the value is greater than or less than the value of the root
  - If is greater
    -Check to see if there is a node to the right - If there is, move to that node and repeat these steps - If there is not, we're done searching
    -If is less
    -Check to see if there is a node to the left - If there is, move to that node and repeat these steps - If there is not, we're done searching

```js
find(value) {
    if (this.root === null) return false;
    let current = this.root;
    let found = false;
    while (current && !found) {
      if (value < current.value) {
        current = current.left;
      } else if (value > current.vlue) {
        current = current.right;
      } else {
        found = true;
      }
    }
    if (!found) return undefined;
    return current;
  }

  contains(value) {
    if (this.root === null) return false;
    let current = this.root;
    let found = false;
    while (current && !found) {
      if (value < current.value) {
        current = current.left;
      } else if (value > current.value) {
        current = current.right;
      } else {
        return true;
      }
    }
    return false;
  }
```

**BFS**

- Create a queue(this can e an array) and a variable to store the values of nodes visited
  - Place the root node in the queue
  - Loop as long as there is anything in the queue
    - Dequeue a node from the queue and push the value of the node into the variable that sotres the nodes
    - If there is a left property on the node dequeued - add it to the queue
    - If there is a right property on the node dequeued - add it to the queue
  - return the variable that stores the values

```js
BFS() {
    let node = this.root;
    let data = [];
    let queue = [];
    queue.push(node);

    while (queue.length) {
      node = queue.shift();
      data.push(node.value);
      if (node.left) queue.push(node.left);
      if (node.right) queue.push(node.right);
    }
    return data;
  }
```

**DFS (Depth First Search)**
**(1) Preorder - recursively**

- Create a variable to store the values of nodes visited
  - Store the root of the BST in a variable called current
  - Write a helper function which accepts a node
  - Push the value of the node to the variable that stores the values
  - If the node has a left property, call the helper function with the left property on the node
  - If the node has a right property, call the helper function with the right property on the node

```js
DFSPreOrder() {
    let data = [];
    let current = this.root;
    function traverse(node) {
      data.push(node.value);
      if (node.left) traverse(node.left);
      if (node.right) traverse(node.right);
    }
    traverse(current);
    return data;
}
```

**(2) Postorder - recursively**

- Create a variable to store the values of nodes visited
  - Store the root of the BST in a variable called current
  - Write a helper function which accepts a node
  - If the node has a left property, call the helper function with the left property on the node
  - If the node has a right property, call the helper function with the right property on the node
  - Push the value of the node to the variable that stores the values
  - Invoke the helper function with the current variable

```js
DFSPostOrder() {
    let data = [];
    let current = this.root;
    function traverse(node) {
      if (node.left) traverse(node.left);
      if (node.right) traverse(node.right);
      data.push(node.value);
    }
    traverse(current);
    return data;
  }
```

**(3) InOrder - recursively**

- Create a variable to store the values of nodes visited
  - Store the root of the BST in a variable called current
  - Write a helper function which accepts a node
  - If the node has a left property, call the helper function with the left property on the node
  - Push the value of the node to the variable that stores the values
  - If the node has a right property, call the helper function with the right property on the node
  - Invoke the helper function with the current variable

```js
DFSInOrder() {
    let data = [];
    let current = this.root;
    function traverse(node) {
      if (node.left) traverse(node.left);
      data.push(node.value);
      if (node.right) traverse(node.right);
    }
    traverse(current);
    return data;
  }
```

### Big O of BST

Not guaranteed

- Insertion - O(log n)
- Finding - O(log n)

### BFS & DFS which one is better?

시간 복잡도 측면에서 BFS 와 DFS 는 크게 차이가 없다. 들다 데이터 양이 늘어날수록 실행 횟수도 증가하기 때문이다. tree의 깊이보다 가로 길이가 넓은 경우 DFS가 더 적절하다. 공간 복잡도가 BFS보다 작기 때문, BFS의 경우에는 탐색이 필요한 형제 노드를 모두 큐에 저장해놔야하기 때문에 트리의 가로 길이가 늘어날 수록 용량을 많이 차지한다. 이 반대의 경우, 즉 가로길이가 좁고 깊이가 깊은 경우에는 BFS가 더 적절하다. 깊이가 깊어질수록 DFS의 재귀가 작동하면서 기억해야할 요소들이 많아지기 때문이다. BFS 는 큐에 저장해놓고 다 날려버리면서 이동하기 때문에 용량 차지가 덜하다.

### DFS inorder, preorder which one is better?

-BST 탐색할 때 inorder로 탐색하면 오름차순으로 sorting된 데이터를 얻을 수 있다.
-BST 탐색할 때 preorder로 탐색하면 트리 모양이 그대로 담긴 데이터를 얻을 수 있다. root data에서 탐색이 시작되기 때문
