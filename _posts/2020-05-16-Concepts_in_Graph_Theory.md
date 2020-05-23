---
layout:     post
title:      Concepts in Graph Theory
toc:        true
categories: [Algorithm]
---
## 1. Undirected Graph
### 1.1 General Undirected Graph
一个**Undirected Graph** $$\boldsymbol{G}$$是一个二元组$$< V, E >$$，其中：
* $$V$$ is an nonempty set of **Vertices**;
* $$E$$ is a Multiset of (undirected) edges, in other words, there can be same edges in the set;
* **(Undirected) Edge** is a 2-vertices set, the two vertices are called **end vertices** of the edge,
                        $$\{a, b\}$$ and $$\{b, a\}$$ are different representation of the same edge.

[注] 除非显式说明，本节的所有概念都基于Undirected Graph。

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

#### 1.3.1 walk and path
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

#### 1.3.2 closed walk and cycle
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

where $$v_0$$ is the same vertice as $$v_k$$ and $$\{v_i, v_{i+1}\}$$ is an edge of $$G$$ for all $$i$$ where
$$0\leq i< k$$. The **length** of the closed walk is $$k$$. A closed walk is said to be a **cycle** if $$k\ge 3$$
and $$v_0,\ v_1,\ v_2,\ ...,\ v_{k-1}$$ are all different.

walk和path的区别在于a sequence of vertices中是否存在重复vertex。

Some texts use the word **cycle** for our definition of closed walk and **simple cycle** for our definition of cycle.

#### 1.3.3 Connectivity

##### connectivity of vertex
Two vertices in a graph are said to be **connected** if there is a path that begins at one and ends at the other.
By convention, every vertex is considered to be connected to itself by a path of length zero.

##### connectivity of graph
A graph is said to be **connected** when every pair of vertices are connected.

##### connected component
A **connected component** is a subgraph of a graph consisting of some vertex and every vertice and edge
that is connected to that vertex.

### 1.4 Tree and Forest

A connected acyclic graph is called a **tree**.

If every connected component of a graph $$G$$ is a tree, then $$G$$ is a **forest**.

## 2. Directed Graph
### 2.1 General Directed Graph
一个**Directed Graph** $$\boldsymbol{G}$$是一个二元组$$< V, E >$$，其中：
* $$V$$ is an nonempty set of **Vertices**;
* $$E$$ is a Multiset of (directed) edges, in other words, there can be same edges in the set;
* **(Directed) Edge** $$(head, tail)$$ is an ordered pair, the first elem is called **head** and the second elem is called **tail**;

[注] 除非显式说明，本节的所有概念都基于Directed Graph。

We have the following terminologies:

#### 2.1.1 degree
With directed graphs, the notion of degree splits into **indegree** and **outdegree**.

#### 2.1.2 (directed) parallel edges
Directed edges that have the same head and tail are **(directed) parallel**.

#### 2.1.3 (directed) loop
For any vertex $$v$$, the directed edge of form $$(v,\ v)$$ is a **(directed) loop**.

### 2.2 Simple Directed Graph
A **simple (directed) graph** is a directed graph without parallel edges or loops.

#### 2.2.1 (directed) walk and (directed) path
A **(directed) walk** in a directed graph $$G$$ is a sequence of vertices

$$
\begin{aligned}
v_0,\ v_1,\ v_2,\ ...,\ v_k 
\end{aligned}
$$

and edges

$$
\begin{aligned}
(v_0,\ v_1),\ (v_1,\ v_2),\ ..., (v_{k-1},\ v_k)
\end{aligned}
$$

A **(directed) path** in a directed graph is a walk where the vertices in the walk are all different.

#### 2.2.2 (directed) closed walk and (directed) cycle
A **(directed) closed walk** in a graph $$G$$ is a sequence of vertices

$$
\begin{aligned}
v_0,\ v_1,\ v_2,\ ...,\ v_k 
\end{aligned}
$$

and edges

$$
\begin{aligned}
(v_0,\ v_1),\ (v_1,\ v_2),\ ..., (v_{k-1},\ v_k)
\end{aligned}
$$

where $$v_0$$ is the same vertice as $$v_k$$ and $$(v_i, v_{i+1})$$ is an (directed) edge of $$G$$ for all $$i$$ where $$0\leq i< k$$.
The **length** of the (directed) closed walk is $$k$$. A (directed) closed walk is said to be a **(directed) cycle**
if $$k\ge 3$$ and $$v_0,\ v_1,\ v_2,\ ...,\ v_{k-1}$$ are all different.

#### 2.2.3 Connectivity
##### Strongly Connectivity
A directed graph $$G=(V,\ E)$$ is said to be **strongly connected** if for every pair of vertices $$u,\ v\in V$$,
there is a (directed) path from $$u$$ to $$v$$ (and vice-versa) in $$G$$.

##### Weakly Connectivity
A directed graph is said to be **weakly connected** (or **connected**) if the corresponding undirected graph
is connnected.

#### 2.2.4 Directed Acyclic Graph (DAG)
A directed graph is called a **directed acyclic graph (DAG)** if it does not contain any (directed) cycles.