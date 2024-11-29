# Lecture 11 - Greedy Algorithm and Minimum Spanning Tree

## Menu
- [Greedy Algorithm](#GreedyAlgorithm)
  - [Definition](#Definition)
  - [Examples](#Examples)
  - [Proof of Algorithm](#Proof)
- [Minimum Spanning Trees](#MST)
  - [Definition](#Definition)
  - [Prim's Algorithm](#Prim)
  - [Data Structure - Union Find Set](#UFS)
  - [Kruskal's Algorithm](#Kruskal)

### GreedyAlgorithm

#### Definition
A greedy algorithm makes the locally optimal choice at each stage with the hope of finding a global optimum. The main characteristics of a greedy algorithm are:
- Make choices one-at-a-time.
- Never look back.
- Hope for the best.

#### Examples
1. **Activity Selection Problem**: Choose a maximum set of activities that don't overlap.
2. **Job Scheduling**: Schedule jobs to minimize the cost based on their processing time and associated costs.

*Counter Examples:*

- **Unbounded Knapsack Problem**: Maximize the value of items that can be put into a knapsack without exceeding its capacity. A greedy approach does not work here.

#### Proof
The proof often involves showing that:
- At each step, the choice made does not rule out an optimal solution at the end.
- After all choices are made, if no optimal solution has been ruled out, the solution found must be optimal.

### MST

#### Definition
A minimum spanning tree (MST) of an undirected weighted graph is a spanning tree whose sum of edge weights is as small as possible. A spanning tree is a subgraph that is a tree which includes all of the vertices of the graph.

#### Prim
- **Initialization**: Start with an arbitrary vertex and grow the MST one edge at a time by adding the minimum weight edge that connects a vertex in the MST to a vertex outside the MST.
- **Efficient Implementation**: Use a priority queue to select the next light edge.
- **Time Complexity**: O(m log(n)) with a binary heap, or O(m + n log(n)) with a Fibonacci heap.

```markdown
slowPrim(G = (V,E), starting vertex s):
    MST = {}
    verticesVisited = { s }
    while |verticesVisited| < |V|:
        find the lightest edge {x,v} in E so that:
            x is in verticesVisited
            v is not in verticesVisited
        add {x,v} to MST
        add v to verticesVisited
    return MST
```

**Here's the Example code to implement the pseudocode:**

```python
import heapq

def prim(graph, start):
    mst = []
    visited = set()
    edge_heap = []
    heapq.heappush(edge_heap, (0, start, start))  # (weight, from_vertex, to_vertex)

    while edge_heap:
        weight, from_vertex, to_vertex = heapq.heappop(edge_heap)

        if to_vertex not in visited:
            visited.add(to_vertex)
            mst.append((from_vertex, to_vertex, weight))

            for vertex, edge_weight in graph[to_vertex].items():
                if vertex not in visited:
                    heapq.heappush(edge_heap, (edge_weight, to_vertex, vertex))

    return mst
```

#### UFS
- **makeSet(u)**: Create a set {u}.
- **find(u)**: Return the set that u is in.
- **union(u,v)**: Merge the set that u is in with the set that v is in.

```python
class UnionFind:
    def __init__(self, size):
        self.parent = list(range(size))
        self.rank = [0] * size

    def find(self, p):
        if self.parent[p] != p:
            self.parent[p] = self.find(self.parent[p])  # Path compression
        return self.parent[p]

    def union(self, p, q):
        rootP = self.find(p)
        rootQ = self.find(q)
        if rootP != rootQ:
            # Union by rank
            if self.rank[rootP] > self.rank[rootQ]:
                self.parent[rootQ] = rootP
            elif self.rank[rootP] < self.rank[rootQ]:
                self.parent[rootP] = rootQ
            else:
                self.parent[rootQ] = rootP
                self.rank[rootP] += 1
```

#### Kruskal
- **Initialization**: Start with a forest where each vertex is a separate tree and grow the MST by adding the minimum weight edge that connects two different trees.
- **Efficient Implementation**: Use a union-find data structure to keep track of the trees and avoid cycles.
- **Time Complexity**: O(m log(n)) with a union-find data structure.

```markdown
kruskal(G = (V,E)):
    Sort E by weight in non-decreasing order
    MST = {} // initialize an empty tree
    for v in V:
        makeSet(v) // put each vertex in its own tree in the forest
    for (u,v) in E: // go through the edges in sorted order
        if find(u) != find(v): // if u and v are not in the same tree
            add (u,v) to MST
            union(u,v) // merge u’s tree with v’s tree
    return MST
```

**Here's the Example code to implement the pseudocode:**

```python
def kruskal(graph):
    edges = sorted(graph, key=lambda item: item[2])  # Sort by edge weight
    mst = []
    ufs = UnionFind(len(graph))

    for u, v, weight in edges:
        if ufs.find(u) != ufs.find(v):  # Check if adding this edge would form a cycle
            mst.append((u, v, weight))
            ufs.union(u, v)

    return mst
```
