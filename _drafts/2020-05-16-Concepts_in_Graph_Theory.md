---
layout:     post
title:      Concepts in Graph Theory
toc:        true
categories: [Algorithm]
---
## 1. Undirected Graph
### 1.1 General Definitions of Undirected Graph
一个**Undirected Graph** $$\boldsymbol{G}$$是一个二元组$$< V, E >$$，其中：
* $$V$$ is an nonempty set of **Vertices**;
* $$E$$ is a Multiset of (undirected) edges, in other words, there can be same edges in the set;
* **(Undirected) Edge** is a 2-vertices set, the two vertices are called **end vertices** of the edge,
                        $$\{a, b\}$$ and $$\{b, a\}$$ are different representation of the same edge.

[注] 在本节中，除非显式说明，Graph就代表Undirected Graph。

E.g., for some graph $$G$$:
![Undirected Graph](/assets/posts/graph_theory/example_undirected_graph.png)

$$V=\{v1,\ v2,\ v3,\ v4,\ v5\}$$ and $$E=\{\{v1,\ v2\}, \{v2,\ v5\}, \{v5,\ v5\}, \{v4,\ v5\}, \{v5,\ v4\}\}$$


We have the following terminologies:

#### 1.1.1 adjacent vertices
Two vertices in a graph are said to be **adjacent** if they are joined by an edge.

#### 1.1.2 degree
The **degree** of a vertex is the number of edges connected to that vertex.

#### 1.1.3 parallel edges
Edges that have the same end vertices are parallel.

#### 1.1.4 loop
For any vertex $$v$$, the edge of form $$\{v,\ v\}$$ is a **loop**.

#### 1.1.5 subgraph
A graph $$G_1 = (V_1,\ E_1)$$ is said to be a subgraph of a graph $$G_2=(V_2, E_2)$$
if $$V_1\in V_2$$ and $$E_1\in E_2$$.

### 1.2 Some Common Undirected Graph
We have the definitions for general undirected graph in last section. In this section, we add some
limitatioins to general undirected graph to get some common undirected graphs.

#### 1.2.1 null graph and empty graph
* A graph with no vertices is a **null graph**;
* A graph with no edges is an **empty graph**;

#### 1.2.2 simple graph
A simple graph is a graph without parallel edges or loops.

#### 1.2.3 tree and forest
A connected acyclic graph is called a **tree**.

If every connected component of a graph $$G$$ is a tree, then $$G$$ is a **forest**.

### 1.3 Simple Graph
下面的概念定义基于Simple Graph，也就是说Graph中没有parallel edges和loop。

#### 1.3.1 walk, path, closed walk and cycle

##### walk and path
A **walk** in a graph $$G$$, is a sequence of vertices

$$
\begin{aligned}
v_0,\ v_1,\ v_2,\ ...,\ v_k 
\end{aligned}
$$

and edges

$$
\begin{aligned}
\{v_0,\ v_1\},\ \{v_1,\ v_2\},\ ..., \{v_{k-1},\ v_k\}
\end{aligned}
$$

such that $$\{v_i,\ v_{i+1}\}$$ is an edge of $$G$$ for all $$i$$ where $$0\leq i< k$$.
The walk is said to start at $$v_0$$ and to end at $$v_k$$, and the **length** of the walk
is defined to be $$k$$.

A **path** is a walk where all the $$v_i$$ are different.

Some texts use the word **path** for our definition of walk and the term **simple path** for our definition of path.

##### closed walk and cycle
A **closed walk** in a graph $$G$$ is a sequence of vertices

$$
\begin{aligned}
v_0,\ v_1,\ v_2,\ ...,\ v_k 
\end{aligned}
$$

and edges

$$
\begin{aligned}
\{v_0,\ v_1\},\ \{v_1,\ v_2\},\ ..., \{v_{k-1},\ v_k\}
\end{aligned}
$$

where $$v_0$$ is the same node as $$v_k$$ and $$\{v_i, v_{i+1}\}$$ is an edge of $$G$$ for all $$i$$ where
$$0\leq i< k$$. The **length** of the closed walk is $$k$$. A closed walk is said to be a **cycle** if $$,\ge 3$$
and $$v_0,\ v_1,\ v_2,\ ...,\ v_{k-1}$$ are all different.

Some texts use the word **cycle** for our definition of closed walk and **simple cycle** for our definition of cycle.

#### 1.3.2 Connectivity

##### connectivity of vertex
Two vertices in a graph are said to be **connected** if there is a path that begins at one and ends at the other.
By convention, every vertex is considered to be connected to itself by a path of length zero.

##### connectivity of graph
A graph is said to be **connected** when every pair of vertices are connected.

##### connected component
A **connected component** is a subgraph of a graph consisting of some vertex and every node and edge
that is connected to that vertex.

### 1.4 Tree and Forest

A connected acyclic graph is called a **tree**.

If every connected component of a graph $$G$$ is a tree, then $$G$$ is a **forest**.

## 2. Directed Graph
