# Lecture 6 - BFS + DFS + SCC + Topological Sorting

***All algorithms in this lecture are code-required***
***意思是考试可能要考代码***

## menu
  - [Basis: What is graph data structure?](#Fundamental-Knowledge)
    - [Professional Expressions](#Terminology)
    - [Representation](#How-to-store-a-graph)
  - [BFS](#BFS)
  - [DFS](#DFS)
    - [Code](#Stack-based-Algorithm)
  - [SCC](#SCC)
    - [Twice BFS and Tarjan's Algorithm](#How-to-check)
  - [Topological sorting](#Topological-Sorting)
  - [External Link](#Referrence)

### Fundamental Knowledge

What is a graph data structure? - a group of vertices and edges that are used to connect these vertices

How to denote a graph? -  𝐺(𝑉, 𝐸)

> • 𝑉 represents the set of vertices/nodes
> 
> • 𝐸 represents the set of edges which are used to connect

#### Terminology

***Path***: a sequence of edges connecting initial node $v_0$ to terminal node $v_n$

• Closed Path: A path where the initial node is same as terminal node, i.e., $v_0$ = $v_n$

• Simple Path: all the nodes of the path are distinct, with the exception $v_0$ = $v_n$

• Closed Simple Path: a simple path with $v_0$ = $v_n$

• Cycle: a path which has no repeated edges or vertices except the first and last

---

***vertices***

• **Adjacent** Nodes: two nodes 𝑢 and 𝑣 are connected via an edge 𝑒

– the nodes 𝑢 and 𝑣 are also called as **neighbors**

• **Degree** of a Node: the number of edges that are connected with the node (某个点连了多少个点)

– A node with degree 0 is called as **isolated node**

---

**Connected** Graph: a graph in which a path exists between every two vertices 𝑢 and 𝑣 in 𝑉

– There are **no isolated nodes** in connected graph

• **Complete** Graph: a graph in which there is an edge between each pair of vertices （完全：任意两点都相连）

– A complete graph contain **$\frac{n(n-1)}{2}$ edges** where 𝑛 is the number of nodes in the graph

• **Weighted** Graph: each edge is assigned with some data such as length or weight (带权)

– The weight of an edge 𝑒, 𝑤(𝑒), must be positive indicating the cost of traversing the edge

• **Digraph**: each edge of the graph is associated with some direction (有向)

– The traversing can be done only in the specified direction

#### How to store a graph?

1. Adjacency Matrix: use an **n * n** matrix if there're n nodes in the graph. The value in the matrix is either 1 or 0, to represent two nodes are connected or not.
> High space complexity, $O(n^2)$, $n$ is the number of nodes
2. Adjacency list: A linked list from the initial path to the terminal.
> Lower space complexity, $O(2m + n)$, $m$ is the number of edges.

> 邻接矩阵和邻接表

```C++
//邻接表建边
//为什么写C++？ 因为python的不熟悉……
int cnt = 0, head[maxn];

struct edge{
    int begin, end;
    int weight;
}e[maxn];

void add_edge(int a, int b, int value){
    e[++cnt].weight = value;
    e[cnt].end = b;
    e[cnt].begin = head[a];
    head[a] = cnt;
}
```

```C++
//邻接矩阵建边
//同样的，不熟悉python，先建C++，python的课后补 // 已补
int graph[maxn][maxn] = {0};

//If we just want to store the node a and b are connected, take the value = 1 after initializing the matrix with all 0.
void add_edge(int a, int b, int value){
    graph[a][b] = value;
    graph[b][a] = value;
}
```

*Python version* is here!
```python
//How to add edges with python elegantly

class Graph:
    def __init__(self):
        self.graph = {} #adj list
    
    def add_edge(self, node, neighbor): #add links doubly(双向建边)
        if node in self.graph:
            self.graph[node].append(neighbor)
        else:
            self.graph[node] = [neighbor]
        
        if neighbor in self.graph:
            self.graph[neighbor].append(node)
        else:
            self.graph[neighbor] = [node]
```

### BFS

Definition: starts traversing the graph from root node and explores all the neighbours. Then, it selects the nearest node and explore all the unexplored nodes.

```python
class Graph:
    def __init__(self):
        self.graph = {} #adjacency list
        self.visited = set()
    
    def add_edge(self, node, neighbor):
        if node in self.graph:
            self.graph[node].append(neighbor)
        else:
            self.graph[node] = [neighbor]

        if neighbor in self.graph:
            self.graph[neighbor].append(node)
        else:
            self.graph[neighbor] = [node]

    def BFS_iterative(self, start): 
        """TODO"""
        queue = []
        queue.append(start)
        while queue:
            front = queue.pop(0)
            if front not in self.visited:
                self.visited.add(front)
                print(front, end=' ')
                queue.extend(self.graph[front])

    def BFS_recursive(self, node):
        """TODO"""
        # An unnecessary method which increases the time complexity of the algorithm

graph = Graph()
graph.add_edge('A', 'B')
graph.add_edge('A', 'C')
graph.add_edge('A', 'D')
graph.add_edge('A', 'E')
graph.add_edge('B', 'F')
graph.add_edge('F', 'H')
graph.add_edge('GG', 'D')
graph.add_edge('D', 'G')
graph.add_edge('G', 'I')
print("===BFS===")
graph.BFS_iterative('A') #output: A B C D E F GG G H I
```

### DFS

Definition: starts with the initial node, and then goes to deeper and deeper until we find the goal node or the node which has no children.
> It's a backtrack algorithm(回溯算法), more convenient to use recursion.

#### Stack-based Algorithm

1. Push the initial node
2. Pop the top and push the neighbors of the popped
3. recursively do the step 2 until reaching the goal or nodes whose degree is 1.

```python
class Graph:
    def __init__(self):
        self.graph = {} #adj list
        self.visited = set()  #hashset to record visited node in constant time.
    
    def add_edge(self, node, neighbor): #add links
        if node in self.graph:
            self.graph[node].append(neighbor)
        else:
            self.graph[node] = [neighbor]

        #The code below is for undirected edge. 
        if neighbor in self.graph:
            self.graph[neighbor].append(node)
        else:
            self.graph[neighbor] = [node]
    
    def dfs_iterative(self, start):
        """TODO"""
        '''
        use stack (list-basis) to implement iterative DFS in style two (i.e, push all neighbours into the stack)  
        '''
        stack = []
        stack.append(start)
        while stack:
            t = stack.pop()
            if t not in self.visited:
                print(t, end=' ')
                self.visited.add(t)
                stack.extend(reversed(self.graph[t]))
        print()
        self.visited.clear()

    def dfs_iterative_two(self, start):
        '''
        use stack (list-basis) to implement iterative DFS in style two (i.e, push one neighbour into the stack)  
        '''
        stack = []
        stack.append(start)
        while stack:
            t = stack[len(stack)-1]
            if t not in self.visited:
                print(t, end = ' ')
                self.visited.add(t)
            cnt = 0
            for node in self.graph[t]:
                if node not in self.visited:
                    stack.append(node)
                    break
                else:
                    cnt += 1
                
            if cnt == len(self.graph[t]):
                stack.pop()
        print()
        self.visited.clear()


    def dfs_recursive(self, node):
        """TODO"""
        if node in self.visited:
            return
        self.visited.add(node)
        print(node,end=' ')

        for neighbor in self.graph[node]:
            if neighbor not in self.visited:
                self.dfs_recursive(neighbor)

graph = Graph()
graph.add_edge('A', 'B')
graph.add_edge('A', 'C')
graph.add_edge('A', 'D')
graph.add_edge('A', 'E')
graph.add_edge('B', 'F')
graph.add_edge('F', 'H')
graph.add_edge('D', 'G')
graph.add_edge('G', 'I')
print('====DFS-Iterative====')
graph.dfs_iterative('A') #output: A B F H C D G I E
graph.dfs_iterative_two('A') #output: A B F H C D G I E
print('====DFS-Recursion====')
graph.dfs_recursive('A') #output: A B F H C D G I E
```

### SCC

Connected Component: find all nodes reachable from s

SCC(Strong connected component): A graph is strongly connected if every pair of nodes is mutually reachable

#### How to check

> What we discuss in this subsection is a directed graph. Undirected graph is too easy to discuss.

Of course, twice BFS can address the problem.

> - Pick any node 𝑠
> 
> – Run BFS from 𝑠 in 𝐺
> 
> – Run BFS from 𝑠 in 𝐺 reverse (reverse the orientation of every edge in the graph)
> 
> – Return true iff all nodes reached in both BFS executions
> 
> – Correctness follows immediately from previous lemma

##### Code
Inherit from the BFS code above. Click [BFS](#BFS) to see the code.

The graph here is a directed graph.

```python
class Graph:
    def __init__(self):
        self.graph = {}
        self.reverse = {}
        self.points = set()
        self.visited = set()

    def add_edge(self, node, neighbor):
        self.points.add(node)
        self.points.add(neighbor)

        if node in self.graph:
            self.graph[node].append(neighbor)
        else:
            self.graph[node] = [neighbor]

        if neighbor in self.reverse:
            self.reverse[neighbor].append(node)
        else:
            self.reverse[neighbor] = [node]

    def BFS(self, start): 
        queue = []
        queue.append(start)
        while queue:
            front = queue.pop(0)
            if front not in self.visited:
                self.visited.add(front)
                if front in self.graph:
                    queue.extend(self.graph[front])

    def BFS_reversed(self, start): 
        queue = []
        queue.append(start)
        while queue:
            front = queue.pop(0)
            if front not in self.visited:
                self.visited.add(front)
                if front in self.reverse:
                    queue.extend(self.reverse[front])

    def SCC(self, start):
        self.BFS(start)
        self.BFS_reversed(start)
        if self.points == self.visited:
            return True
        else:
            return False
        
        
graph = Graph()
graph.add_edge('B', 'A')
graph.add_edge('A', 'C')
graph.add_edge('A', 'D')
graph.add_edge('A', 'E')
graph.add_edge('B', 'F')
graph.add_edge('F', 'H')
graph.add_edge('GG', 'D')
graph.add_edge('D', 'G')
graph.add_edge('G', 'I')
print("===SCC===")
print(graph.SCC('A')) #output: False
```

---

Here introduce another algorithm: Tarjan's Algorithm.

Caution: Hard to understand!

1. DFS all unvisited nodes
2. label the node with unique index.
3. For those who orient at the visited nodes, the property numbers of the nodes become the least unique index along the path
4. Find the union with most nodes whose property numbers is the same.

Time conplexity: $O(n+m)$

<details>

<summary> Model Code </summary>

```python
def tarjans_algorithm(graph):
    index = 0
    stack = []
    indices = {}
    lowlinks = {}
    on_stack = {}
    sccs = []

    def strongconnect(node):
        nonlocal index
        indices[node] = index
        lowlinks[node] = index
        index += 1
        stack.append(node)
        on_stack[node] = True

        for neighbor in graph[node]:
            if neighbor not in indices:
                strongconnect(neighbor)
                lowlinks[node] = min(lowlinks[node], lowlinks[neighbor])
            elif on_stack[neighbor]:
                lowlinks[node] = min(lowlinks[node], indices[neighbor])

        if lowlinks[node] == indices[node]:
            scc = []
            while True:
                w = stack.pop()
                on_stack[w] = False
                scc.append(w)
                if w == node:
                    break
            sccs.append(scc)

    for node in graph:
        if node not in indices:
            strongconnect(node)

    return sccs

# Example usage
graph = {
    'A': ['B'],
    'B': ['C', 'E', 'F'],
    'C': ['D', 'G'],
    'D': ['C', 'H'],
    'E': ['A', 'F'],
    'F': ['G'],
    'G': ['F'],
    'H': ['D', 'G']
}

sccs = tarjans_algorithm(graph)
print("Strongly connected components:", sccs)
```

</details>

<details>

<summary> Optional: Union-Find Set(并查集) Click the triangle to open the content if you want </summary>

非课堂内容用C++写合理吗？合理！

```C++
int family[10000];

int find(int x){
    if (family[x] == x) return x;
    else return family[x] = find(family[x]);
}

void link(int x, int y){
    if(find(x) != find(y)) family[find(x)] = find(y);
}

void search(int x, int y){
    if(find(x) != find(y)) printf("No\n");
    else printf("Yes\n");
}
```

</details>

### Topological Sorting

DAG: A DAG is a directed graph that contains no directed cycles

Topological Sorting: A topological order of a directed graph 𝐺 = (𝑉, 𝐸) is an ordering of its nodes as $𝑣_1, 𝑣_2, … , 𝑣_𝑛$ so that for every edge $(𝑣_𝑖, 𝑣_𝑗)$ we have $i < j$
> Simply speaking: there must be 3 kinds of nodes: nodes only point at others, nodes only pointed by others and linking nodes.

Important Lemma:

1.  If 𝐺 has a topological order, then 𝐺 is a DAG
2.  If 𝐺 is a DAG, then 𝐺 has a node with no entering edges
3.  To find the topological order, it consumes $O(n + m)$

```python
class Graph:
    def __init__(self):
        self.graph = {}
        self.visited = set()
    
    def add_edge(self, node, neighbor):
        if node in self.graph:
            self.graph[node].append(neighbor)
        else:
            self.graph[node] = [neighbor]


    def Topological_Sorting(self):
        stack = []

        def dfs(node):
            if node not in self.visited:
                self.visited.add(node)
                if node in self.graph:
                    for neighbor in self.graph[node]:
                        dfs(neighbor)
                stack.append(node)

        for node in self.graph:
            dfs(node)

        return stack[::-1]

graph = Graph()
graph.add_edge('F', 'A')
graph.add_edge('E', 'A')
graph.add_edge('F', 'C')
graph.add_edge('C', 'D')
graph.add_edge('D', 'B')
graph.add_edge('E', 'B')
print("===Topo===")
l = graph.Topological_Sorting()
print(l) #output: ['E', 'F', 'C', 'D', 'B', 'A']
```

### Referrence

[Graph](https://www.geeksforgeeks.org/introduction-to-graphs-data-structure-and-algorithm-tutorials/)

[BFS](https://www.geeksforgeeks.org/breadth-first-search-or-bfs-for-a-graph/)

[DFS](https://www.geeksforgeeks.org/depth-first-search-or-dfs-for-a-graph/)

[SCC](https://www.geeksforgeeks.org/strongly-connected-components/)

[Topological Sorting](https://www.geeksforgeeks.org/topological-sorting/)
