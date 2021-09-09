---
title: Algorithm_Graph
date: '2021-07-31'
tags: ['Algorithm']
draft: false
summary: 길찾기 문제, 그래프만 제대로 이해해도 풀 수 있다!
---

### What is Graph

> A graph is an abstract notation used to represent the connection between pairs of objects.

### Make a graph

```js
class Graph {
  constructor() {
    this.adjacencyList = {}
  }

  addVertex(vertex) {
    if (!this.adjacencyList[vertex]) this.adjacencyList[vertex] = []
  }
}
```

**Adding and edge**

- This function should accept two vertices, we can call them vertex1 and vertex 2
- The function should find in the adjacency list the key of vertex1 and push vertex 2 to the array
- The function should find in the adjacency list the key of vertex2 and push vertex1 to the array

```js
addEdge(v1, v2) {
    this.adjacencyList[v1].push(v2);
    this.adjacencyList[v2].push(v1);
  }
```

**Removing an edge**

- This function should accept two vertices, we'll call them vertex1 and vertex2
- The function should reassign the key of vertex1 to be an array that does not contain vertex2
- The function should reassign the key of vertex2 to be an array that does not contain vertex1
- Don't worry about handling errors/invalid vertices

```js
removeEdge(v1, v2) {
    this.adjacencyList[v1] = this.adjacencyList[v1].filter((v) => v !== v2);
    this.adjacencyList[v2] = this.adjacencyList[v2].filter((v) => v !== v1);
  }
```

**Removing a vertex**

- This function should accept a vertex to remove
  - The function should loop as long as there are any other vertices in the adjacency list for that vertex
  - Inside of the loop, call our removeEdge function with the vertex we are removing and any values in the adjacency list for that vertex
  - Delete the key in the adjacency list for that vertex

```js
removeVertex(vertex) {
    while (this.adjacencyList[vertex].length) {
      const adjacentVertex = this.adjacencyList[vertex].pop();
      this.removeEdge(vertex, adjacentVertex);
    }
    delete this.adjacencyList[vertex];
  }
```

**DFS - recursive**

- The function should accept a starting node
- Create a list to store the end result, to be returned at the very end
- Create an object to store visited vertices
- Create a helper function which accepts a vertex
  - The helper function should return early if the vertex is empty
  - The helper function should place the vertex it accepts into the visited object and push that vertex into the result array
  - Loop over all of the values in the adjacencyLst for that vertex
  - If any of those values have not been visited, recursively invoke the helper function with that vertex
- Invoke the helper function with the starting vertex
- return the result array

```js
DFSRecursive(start) {
    const result = [];
    const visited = {};
    const adjacencyList = this.adjacencyList;

    (function dfs(vertex) {
      if (!vertex) return null;
      visited[vertex] = true;
      result.push(vertex);
      adjacencyList[vertex].forEach((neighbor) => {
        if (!visited[neighbor]) {
          return dfs(neighbor);
        }
      });
    })(start);
    return result;
  }
```

**DFS - iterative**

- The function should accept a starting node
- Create a stack to help use keep track of vertices
- Create a list to store the end result, to be returned at the very end
- Create an object to store visited vertices
- Add the starting vertex to the stack, and mark it visited
- While the stack has something in itL
  - Pop the next vertex from the stack
  - If that vertex hasn't been visited yet:
  - Mark it as visited
  - add it to the result list
  - Pusth all of its neighbors in the stack
- return the result array

```js
DFSIterative(start) {
    const stack = [start];
    const result = [];
    const visited = {};
    let cur;

    visited[start] = true;
    while (stack.length) {
      cur = stack.pop();
      result.push(cur);
      this.adjacencyList(cur).forEach((neighbor) => {
        if (!visited[neighbor]) {
          visited[neighbor] = true;
          stack.push(neighbor);
        }
      });
    }
    return result;
  }
```

**BFS**

- This function should accept a starting vertex
- Create a queue(you can use an array) and place the starting vertex in it
- Create an array to store the nodes visited
- Mark the starting vertex as visited
- Loop as long as there is anything in the queue
- Remove the first vertex from the queue and push it into the array that stores nodes visited
- Loop over each vertex in the adjancency list for the vertex you are visiting
- If it is not inside the object that stores nodes visited, mark it as visited and enqueue that vertex
- Once you have finished looping, return the array of visited nodes

```js
BFS(start) {
    const queue = [start];
    const result = [];
    const visited = {};
    visited[start] = true;
    let cur;

    while (queue.length) {
      cur = queue.shift();
      result.push(cur);

      this.adjacencyList[cur].forEach((neighbor) => {
        if (!visited[neighbor]) {
          visited[neighbor] = true;
          queue.push(neighbor);
        }
      });
    }
    return result;
  }
}
```
