# Lecture 8 - Network Flow

## Menu
- [Introduction](#Introduction)
- [Ford-Fulkerson Algorithm](#Ford-Fulkerson_Algorithm)
- [Edmonds-Karp Algorithm ](#Edmonds-Karp_Algorithm)
- [Reference](#Reference)

### Introduction

A flow network is a connected and directed graph $G = (V, E)$ with a single source and a single sink.
> There's no edges enter the source or leave the sink.

Each Edge is assigned with a capacity $c_i$ that indicates the maximum burden the edge can afford.

Our goal is to figure out the maximum flow of the sink.

Use $f_{in}(v_i)$ to denote the flow into the node $v_i$. Similarly, $f_{out}(v_i)$ is the flow leaves the node $v_i$.

**Three basic principles:**
1. $f_{out}(source)$ = $f_{in}(sink)$
2. $f_{in}(source)$ = $f_{out}(sink) = 0$
3. For nodes except source and sink, $f_{in}(v_i)$ = $f_{out}(v_i)$, which means the net flow of any intermediate node is 0. $f_{net}(v_i) = 0$

**Augmenting Path:** A non-cycle path from the source to the sink node, and the capacity of each node along the path is positive and meaningful.

Our algorithms are centered on the augmenting path.

### Ford-Fulkerson Algorithm

Time complexity: $O(Cm)$ 

**Procedure:**
1. Find a augmenting path (怎么做呢？不知道，FF算法没给具体的找增广路步骤，所以才产生了各种优化。所以说FF算法只是一个思想大纲。)
2. find the minimum capacity along the path
3. add this to the maximum flow
4. the capacity of edges along the path minus the minimum capacity
5. add the minimum capacity to the inverse of the directed edge
6. repeat 1-5 until there's no augmenting path left.

### Edmonds-Karp Algorithm

Time complexity: $O(nm^2)$

Difference: Apply BFS to find the augmenting path from the shortest path to the above. 
> Only effective when the edge is non-weighted.
> Weighted edges with network flow: apply dijkstra or SPFA algorithm.

```python
class Graph:
    def __init__(self):
        self.graph = {}
        self.vis = set()

    def add_edge(self,node,neighbor,capacity):
        if node in self.graph:
            self.graph[node].append(neighbor,capacity)
        else:
            self.graph[node] = [(neighbor,capacity)]

        if neighbor not in self.graph:
            self.graph[neighbor] = [(node, 0)]
        else:
            self.graph[neighbor].append(node,0)

    # 判断是否还有可供选择的增广路
    # 算法: BFS
    def Find_Path(self, source,sink,parent):
        augmenting_path_exist = False
        queue = []
        queue.append(source)
        self.vis.add(source)
        while queue:
            front = queue.pop(0)
            for nei, cap in self.graph[front]:
                if nei not in self.vis and cap > 0:
                    queue.append(nei)
                    self.vis.add(net)
                    parent[nei] = front
                    if nei == sink:
                        augmenting_path_exist = True
                        break
        return augmenting_path_exist

    def Ford_Fulkerson(self, source, sink):
        max_flow = 0
        parent = {} #用来记录某个点的上一个点有哪些

        while self.Find_Path(source, sink, parent):
            current_flow = float('inf')
            #找到某条增广路能承受的最大流
            backtrace = sink
            while backtrace != source:
                current_flow = min(current_flow, self.graph[parent[backtrace]][backtrace])
                backtrace = parent[backtrace]
            # 然后加上那条增广路的最大流
            max_flow += current_flow
            # 那条路上的流量都减去最大流
            backtrace = sink
            while backtrace != source:
                pa = parent[backtrace]
                self.graph[pa][backtrace] -= current_flow
                self.graph[backtrace][pa] += current_flow #反向加上最大流，设立回溯和反悔机制
                backtrace = pa
        
        return max_flow
```

### Reference

推荐geeks for geeks相关网站
