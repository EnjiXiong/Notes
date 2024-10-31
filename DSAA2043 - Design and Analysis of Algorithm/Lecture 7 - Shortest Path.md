# Shortest Path

##### menu
- [Dijkstra](#Dijkstra)
- [Bellman-Ford](#Bellman-Ford)
  - [Better in time: SPFA](#SPFA)
- [BFS](#BFS)
- [External Linking](#Reference)


### Dijkstra

A single source shortest path algorithm(计算从某一个点出发，到其他所有点的最短路)

**Restriction:** the weight of edge must be positive when applying the Dijkstra algorithm.

**Process:**
1. Choose a beginning point, initialize the distance from the beginning point with $\infty$
2. prepare a **priority queue** whose sorting criteria is the distance recorded now, push the beginning point into it.
3. pop the front in the queue, refresh the distance from the neighbor points to the popped point.
4. push the neighbors into the queue.
5. repeat 3-4 until priority queue is empty.

Code is here!
<details>
  
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
shortest_paths = g.dijkstra(0)
    
# Print results
for vertex, distance in shortest_paths.items():
    print(f"Shortest distance from 0 to {vertex}: {distance}")
```

</details>

The time complexity is $O(n^2)$ is we just use array, but it can be optimised to $O(mlog(n))$ if we use binary heap.

Can we [do better](#BFS) if the edge is unweighted?

[Return to the menu](#menu)

### Bellman-Ford



[Return to the menu](#menu)

#### SPFA



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
