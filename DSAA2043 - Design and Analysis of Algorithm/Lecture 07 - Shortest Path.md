# Shortest Path

***All algorithms in this lecture are code-required***
***意思是考试可能要考代码***

## menu
- [Dijkstra](#Dijkstra)
- [Bellman-Ford](#Bellman-Ford)
  - [Better in time: SPFA](#SPFA)
- [BFS](#BFS)
- [External Linking](#Reference)
- [Appendix: Test data for codes](#TestCase)


### Dijkstra

A single source shortest path algorithm(计算从某一个点出发，到其他所有点的最短路)

There're also all-pairs shortest paths(多元最短路，计算任意两点间的最短路径), we'll talk about it in the lecture for DP(Dynamic Programming): Floyd Algorihtm $O(n^3)$

---

> Optional: Click to expand the content

<details>

  <summary> Codes for Floyd Algorithm </summary>
  
```python
def Floyd_Warshall(self):
        # Initialize infinity for all vertices and assign known distance to the distance matrix
        distance = {vertex: {vertex: float('inf') for vertex in self.graph} for vertex in self.graph}
        for node in self.graph:
            distance[node][node] = 0
            for neighbor, weight in self.graph[node]:
                distance[node][neighbor] = weight

        # Core Part
        for begin in self.graph:
            for end in self.graph:
                for middle in self.graph:
                    distance[begin][end] = min(distance[begin][end], distance[begin][middle] + distance[middle][end])
        return distance
```

</details>

> Optional Over

---

**Restriction:** the weight of edge must be positive when applying the Dijkstra algorithm.

**Process:**
1. Choose a beginning point, initialize the distance from the beginning point with $\infty$
2. prepare a **priority queue** whose sorting criteria is the distance recorded now, push the beginning point into it.
3. pop the front in the queue, refresh the distance from the neighbor points to the popped point.
4. push the neighbors into the queue.
5. repeat 3-4 until priority queue is empty.

Code is here!
  
```C++
import heapq

class Graph:
    def __init__(self):
        self.graph = {}
        self.visited = set()

    def add_edge(self, start, to, weight):
        # Modify the add_edge to store edges correctly
        if start not in self.graph:
            self.graph[start] = [(to, weight)]
        else:
            self.graph[start].append((to, weight))

        if to not in self.graph:
            self.graph[to] = [(start, weight)]
        else:
            self.graph[to].append((start, weight))

    def dijkstra(self, begin):
        # Initialize distances with infinity for all vertices
        distance = {vertex: float('inf') for vertex in self.graph}
        distance[begin] = 0
        
        # Priority queue to store vertices and their distances
        # Format: (distance, vertex)
        pq = [(0, begin)]

        while pq:
            # Get vertex with minimum distance
            current_distance, current_vertex = heapq.heappop(pq)

            # If we've already processed this vertex, skip it
            if current_vertex in self.visited:
                continue

            # Mark the vertex as visited
            self.visited.add(current_vertex)

            # Check all adjacent vertices
            for neighbor, weight in self.graph[current_vertex]:
                # If we haven't visited this neighbor
                if neighbor not in self.visited:
                    # Calculate new distance
                    new_distance = current_distance + weight
                    
                    # If we found a shorter path, update it
                    if new_distance < distance[neighbor]:
                        distance[neighbor] = new_distance
                        heapq.heappush(pq, (new_distance, neighbor))

        return distance
```

The time complexity is $O(n^2)$ is we just use array, but it can be optimised to $O(mlog(n))$ if we use binary heap. (m is the number of edges and n is the number of nodes.)

Can we [do better](#BFS) if the edge is unweighted?

[Return to the menu](#menu)

### Bellman-Ford

Bellman-Ford Algorithm can handle negative weight of edges when computing the shortest path. However, it sacrifices the time complexity.

**Differences:** Instead of updating the shortest d[v], the Bellman-Ford updates all nodes simultaneously.

```python
def Bellman_Ford(self, begin):
        distance = {vertex: float('inf') for vertex in self.graph}
        distance[begin] = 0
        for _ in range(len(self.graph) - 1):
            for node in self.graph:
                for neighbor, weight in self.graph[node]:
                    if distance[node] + weight < distance[neighbor]:
                        distance[neighbor] = distance[node] + weight
        for node in self.graph:
            for neighbor, weight in self.graph[node]:
                if distance[node] + weight < distance[neighbor]:
                    print("Negative cycle detected")
                    return
        return distance
```

[Return to the menu](#menu)

#### SPFA

By observation, it's easy to find that Bellman-Ford Algorithm is too loose. So many meaningless operations are executed during the loop. How can we improve it?

Here's SPFA(Shortes Path Faster Algorithm) introduced.

**Progress:** 

The algorithm applies a queue to push the next node to update and another array to store the situation that whether the node is in the queue or not.

Under the circumstance, we don't need to push the node already in the queue into the queue repeatedly.

**Restriction:**

In some extremely unfortunate situations, the time complexity could degrade into $O(VE)$ compared to average case $O(\kappa E)$ (Usually, $\kappa$ is much smaller than $V$ in practice)

```python
def SPFA(self, begin):
        distance = {vertex:float('inf') for vertex in self.graph}
        distance[begin] = 0
        queue = [begin]
        st = [begin]
        while queue:
            front = queue.pop(0)
            st.remove(front)
            for node, weight in self.graph[front]:
                if distance[node] > distance[front] + weight:
                    distance[node] = distance[front] + weight
                    if node not in st:
                        st.append(node)
                        queue.append(node)
        return distance
```

[Return to the menu](#menu)

### BFS

If the edge is unweighted, BFS is sufficient to solve the shortest path problem.

```C++
def BFS(self, begin):
        queue = [begin]
        distance = {vertex: float('inf') for vertex in self.graph}
        distance[begin] = step = 0
        while queue:
            front = queue.pop(0)
            if front not in self.visited:
                self.visited.add(front)
                for node, weight in self.graph[front]:
                    if node not in queue:
                        queue.append(node)
                        distance[node] = min(distance[node], distance[front] + 1)
        return distance
```

[Return to the menu](#menu)

### Reference

[Two versions of Dijkstra](https://www.geeksforgeeks.org/dijkstras-shortest-path-algorithm-greedy-algo-7/)

[Bellman-Ford](https://www.geeksforgeeks.org/dijkstras-shortest-path-algorithm-greedy-algo-7/)

[SPFA](https://www.geeksforgeeks.org/shortest-path-faster-algorithm/)

[All](https://oi-wiki.org/graph/shortest-path/#bellmanford-%E7%AE%97%E6%B3%95)

### TestCase

```python
# Example usage:
g = Graph()
# Adding edges: (start, to, weight)
g.add_edge(0, 1, 4)
g.add_edge(0, 2, 1)
g.add_edge(1, 2, 2)
g.add_edge(1, 3, 5)
g.add_edge(2, 3, 8)
g.add_edge(2, 4, 3)
g.add_edge(3, 4, 2)

# Find shortest paths from vertex 0
shortest_paths1 = g.dijkstra(0)
shortest_paths2 = g.Bellman_Ford(0)
shortest_paths3 = g.SPFA(0)
BFS_Depth = g.BFS(0)
    
# Print results
print("===Dijkstra's algorithm===")
for vertex, distance in shortest_paths1.items():
    print(f"Shortest distance from 0 to {vertex}: {distance}")
print("===Bellman-Ford algorithm===")
for vertex, distance in shortest_paths2.items():
    print(f"Shortest distance from 0 to {vertex}: {distance}")
print("===SPFA algorithm===")
for vertex, distance in shortest_paths3.items():
    print(f"Shortest distance from 0 to {vertex}: {distance}")
print("===BFS===")
for node, d in BFS_Depth.items():
    print(f"BFS depth from 0 to {node}: {d}")
```

```
//Answer
Shortest distance from 0 to 0: 0
Shortest distance from 0 to 1: 3
Shortest distance from 0 to 2: 1
Shortest distance from 0 to 3: 6
Shortest distance from 0 to 4: 4
===BFS===
BFS depth from 0 to 0: 0
BFS depth from 0 to 1: 1
BFS depth from 0 to 2: 1
BFS depth from 0 to 3: 2
BFS depth from 0 to 4: 2
```
