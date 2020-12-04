## Parameterized algorithms

FPT - fixed param tractable

1. Approximation algorithms

2. OPT (abuse notation, both algorithm and result)

   OPT = k 

   run time <= f(k) * poly(n)  (n is the size of the problem, k is the parameter, usually the cost of a solution type k)

3. BWCA (beyond worst case analysis) - model "real world"

---

Problem: longest path visit every vertex at most 1 in a graph 

​    -> reduced to Hamiltonion path problem: if \exists a path that visits every vertex once.

Problem 2: Is there a path of length k? (For k = n, is is equal to Problem 1, it is NP hard, cannot be polynomial time)

​	Suppose k is small. 

![image-20201113020052946](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201113020052946.png)

Color coding

- Color all vertices with k colors (randomly, independently)
- Look for a path: 1->2->3....->k

Repeat for $k^k \log (\frac{1}{\delta})$ times

Loop: for color i, check nodes with color i-1, see whether nodes in i are connected to any previous color marked nodes, mark them. All nodes with color 1 are marked.

complexity: O(n) * connectivity * k^k



For a specified set of nodes: P{P_1 has color 1, ..., P_i has color i} = (1/k)^k

So we choose iteration number to be  $k^k \log (\frac{1}{\delta})$

P{don't find a path in G in iteration t} <= (1-(1/k)*k)

P{we fail T times} <= (1-(1/k)*k)^T = (1-(1/k)*k)^{k^k log 1/\delta} <= (1/e)^log 1/\delta = \delta

Or prove it like:

![image-20201113025909928](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201113025909928.png)

---

P{all vertices on a path of length k have distinct colors} = k!/k^k >= (k/e)^k/k^k = (1/e)^k

(no order, P is larger, so in the Loop we have to check marking as existence (for uniqueness) instead of i-1 -> i)



Vertex cover

![image-20201113022905938](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201113022905938.png)

Algorithm:

Is there a vertex cover of size at most k?

First reduction:

![image-20201113023301257](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201113023301257.png)

(Because in rule 2, u cannot be contained in G since its degree > k, our budget is k)

![image-20201113023920878](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201113023920878.png)

Kernel (G' K') (G' is a sub graph of current graph G, G'=(V', E'))

G' has at most K'^2 edges, at most K'^2 vertices

Brute force: check all subsets V' of size <=K'  (|V'| = K'^2)

Runtime: f(K) + poly(n)

