## Approximation algorithms

### Definition

Already talked about in Graduate Algorithms.

### The Travel Sales Man Problem (TSP)

#### Definition

Given a graph $G=(V, E)$, find a tour route with minimal length which visits every vertex at least once and returns to the start.

#### A simple 2-approximation algorithm

MST <= OPT (MST: Prim or Kruskal algorithm)

However, tree is not a valid tour, but tour is a tree, we must double every edge (by DFS) to get a valid tour.

2-approximation proof

1. cost(MST) \leq OPT (because tour is a cycle, after removing one edge, it is a tree....., and MST produce trees)
2. cost(ALG) = 2cost(MST)

So approximation factor is 2.

#### A better algorithm

1. find the MST $T$
2. $S=\{v: deg(v)\ are \ odd\}$
3. Find min distance matching $M$ between odd degree nodes.
4. Consider $T \cup M$, all vertices have an even degree
5. $T \cup M$ is an Eulerian graph.
6. ALG returns an eulerian tour.

(Nodes in an Eulerian tour have even degrees, an eulerian graph returns a euerian tour)

Graph $H=(V,E)$, every u: deg u - even

=> $H$ is eulerian and exists tour that visits every edge exactly once.

proof (sufficiency and necessity):

...

https://inst.eecs.berkeley.edu/~cs70/fa06/lectures/eulerian/lec15.pdf



We would like to prove that our algorithm is 1.5-approximation using the following method.

![image-20210113022500104](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20210113022500104.png)

### The set cover problem



 



