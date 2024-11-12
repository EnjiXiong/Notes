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



### Edmonds-Karp Algorithm



### Reference
