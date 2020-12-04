## Approximation Algorithms

### Problem definition

For a minimization problem 


$$
ALG(I) \le A * OPT(I)
$$
Where:

1. $A$ - approximation factor, $A \ge 1$ 
2. $ALG(I)$ - cost of some problem instance returned by our algorithm
3. $OPT(I)$ - cost returned by the optimal solution.

For a maximization algorithm:
$$
ALG(I) \ge A * OPT(I)
$$
Where $A \le 1$

The exact optimal solution is usually hard to find, and we would like to find a sub-optimal solution.

### The vertex cover problem

![image-20201118013819464](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201118013819464.png)

### An algorithm

Algorithm:

​	While ($\exists$ uncovered edge (u, v)):

  -   pick an uncovered(u, v)

  -   Add both u and v to S

      return S

(Why choose two nodes? eg: in the star topology, if choose one node, the worst case is choosing all leave nodes and not choosing the center node, while choosing two nodes will definetely cover the center node.)

Claim: approximation factor of this algorithm is 2

Proof: ALG(I) = 2k OPT(I) >= k, because OPT has to choose half of the nodes of all edges to cover all edges. (If all edges are disjoint and don't share nodes, we can remove "connecting edges" to reduce other instances to this?)

### The set cover problem

Find the minimum set of indices $I$, s.t.
$$
\bigcup_{i\in I} s_i = u
$$
(The collection of subset family $\{s_i\}$ is predetermined)

### An algorithm

let $V_k$ be the set of uncovered elements, and $V_0 = u$

while ($V_t \neq \emptyset$)

​	Find $s_j$ that covers most elements in $V_t$

​	Add $s_j$ to solution

​	$V_{t+1} = V_t - S_j$

return solution



Claim: $ALG(I) \le O(\log n) * OPT(I)$, where $n$ is $|u|$

Proof:

​	$V_0 = V \Rightarrow |V_0| = n$

​	$|V_1|=?$

​	$k = OPT(I) \Rightarrow \exists \text{some set} s_j \text{of size} \ge \frac{n}{k}$

​	Our algorithm chooses a set of size $\ge \frac{n}{k}$

​	$V_1 = V - s_j \Rightarrow |v-1| \le n - \frac{n}{k} = (1-1/k)*n$

​	

​	We would like to prove $|V_t| \le (1-1/k)^t * n$

​	By induction:

​	![image-20201118021853878](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201118021853878.png)

​	![image-20201118022500727](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201118022500727.png)

​	because OPT(I) = k, (ALG(I) will at least cover some lements, so V_t < u, and remaining needed sets >= k)

​	let $t = k * logn$, gets |V_t| <= 1

### Weighted set cover problem

Assign weight $w_i$ to set $s_i$, find set cover which $min \sum_{i \in I}w_i$

![image-20201118022639973](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201118022639973.png)

Need to solve:
$$
min \sum_{i=1}^m w_i * x_i \qquad s.t. \forall u \in U, \sum_{i: u\in s_i} x_i \ge 1
$$


 (Every element is at least covered by one set)

This is an integer programming problem

We can do a relaxation, and let $x_i \in [0, 1]$ instead of $x_i \in \{0, 1\}$

Solve the new linear programming problem, obtain $x_i, ..., x_m$

![image-20201118023806917](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201118023806917.png)

Correction (xi * 2logn)

LP <= OPT (LP is better), because LP includes the set of IP (integer programming) solutions, it works in a relaxed environment

ALG <= O(logn) * LP

P{u is not covered}

= P{s_i is not in solution \forall i u\in s_i}

= \prod_{i: u \in s_i} P {s_i is not in solution}

= \prod_{i: u \in s_i} (1-x_i 2log n)^+  (+ means replace with zero if prob < 0)

<= \prod_{i: u \in s_i} e^{-x_i 2logn} (because e^-t >= 1-t)

= e^{\sum_{i: u \in s_i} x_i 2logn} 

<= e^{-2logn} (because sum of all x_i >= 1)

= 1/n^2

therefore:

P {at least one element is not covered} <= 1/n



\E [cost of solution] = \E[\sum_i \I {s_i is chosen in solution} * w_i]

= \sum_i P{s_i is chosen in the set}

<= \sum 2log(n) x_i w_i

= 2log(n) \sum x_i w_i

[reference essay](http://people.brunel.ac.uk/~mastjjb/jeb/natcor_ip_rest.pdf)



