## The load balancing problem

---

### Introduction

Load balancing is everywhere in our life, For example: Foreigners waiting outside the custom either automatically (each person is a load balancer) chooses the shortest waiting queue, or are guided by a specific number of guides (each guide is a load balancer) to the target queues selected by them. 

In the computer world, transactions of banks made in parallel by millions of customers, web page request to Google from billions of devices, are usually served by a group of servers, either for speed or robustness. Load balancing is only needed for sharing the gigantic load among servers, but also needed for fault tolerance when one or more servers drop off line.

While there are numerous ways to balance the load, we still would like to get a mathematical analysis of the expectation, upper bounds, lower bounds etc. of different criteria, in this lecture, we are going to analyze the load balancing problem from a various dimensions.

---

### Expectation of the max load

#### Definition

Suppose there are $n$ balls and $n$ bins, let $load(i)$ = number of balls in bin $i$, we would like to know the expectation of $\max\ load(i)$.

**Claim**:
$$
\mathbb{E} \max_i \ load(i) \sim O(\frac{logn}{loglogn})
$$

#### Proof
---
##### General Idea

This proof is kind of long, basically we are going to:

1. Prove $P\{load(i) \ge k\} \le Q$.
2. $Q$ is an infinitesimal when $k$ is $\Theta(\frac{logn}{loglogn})$

Now by showing that when $k$ is $\Theta(\frac{logn}{loglogn})$, probability becomes (approximately) 0, we know the expectation couldn't be larger than $k$, and therefore, the upper bound has the same order as $k$ does.

---

$$
\begin{align*}
load(i) &= \sum_{j=1}^n \mathbb{I}\{ball_j \rightarrow bin_i\} \\
        &= B(n, \frac{1}{n}) \\
        &\sim Poisson(1) \\
        &\text{(By possion limit theorem)}
\end{align*}
$$
Therefore we can represent $P\{load(i) = k\}$ using the Poisson distribution $Poisson(1)$ as:
$$
\begin{align*}
P\{load(i) = k\} \approx \frac{e^{-1}}{k!}
\end{align*}
$$

> Note that RHS of this equation is also the upper bound of $B(n, \frac{1}{n})$ as $n$ approaches $\infty$:
> $$
> \begin{align}
> 
> \lim_{n\to\infty} P(X=k)&=\lim_{n\to\infty}{n \choose k} p^k (1-p)^{n-k} \\
>  &=\lim_{n\to\infty}{n! \over (n-k)!k!} \left({\lambda \over n}\right)^k \left(1-{\lambda\over n}\right)^{n-k}\\
> &=\lim_{n\to\infty}
> \underbrace{\left[\frac{n!}{n^k\left(n-k\right)!}\right]}_F
> \left(\frac{\lambda^k}{k!}\right)
> \underbrace{\left(1-\frac{\lambda}{n}\right)^n}_{\to\exp\left(-\lambda\right)}
> \underbrace{\left(1-\frac{\lambda}{n}\right)^{-k}}_{\to 1} \\
> &= \lim_{n\to\infty}
> \underbrace{\left[ \left(1-\frac{1}{n}\right)\left(1-\frac{2}{n}\right) \ldots \left(1-\frac{k-1}{n}\right)  \right]}_{\to 1\quad (*A)}
> \left(\frac{\lambda^k}{k!}\right)
> \underbrace{\left(1-\frac{\lambda}{n}\right)^n}_{\to\exp\left(-\lambda\right)\ (*B)}
> \underbrace{\left(1-\frac{\lambda}{n}\right)^{-k}}_{\to 1 \ (*C)}      \\
> &= \left(\frac{\lambda^k}{k!}\right)\exp\left(-\lambda\right)
> \end{align}
> $$
> Where stared parts $*A, *B, *C$ monotonically asymptote to their respective upper limit.



Suppose that (c could be anything larger than $e^{-1}$ here):
$$
P\{load(i) = k\} \le  \frac{c}{k!}
$$
then (c' is a constant):
$$
P\{load(i) \ge k\} \le \frac{c'}{k!}
$$
This is easy to prove, since you may easily prove that the geometric series: $\frac{c}{k^n}$ is convergent, and for a factorial series: $\frac{c}{k'!/k!}$ there exists a $k' > k$ and the following relation ship stands: $\frac{c}{k'!/k!} < \frac{c}{k^n}$, therefore the factorial series is also convergent by Direct Comparison Test. 

> Proof:
> $$
> \begin{align*}
> &P\{load(i) \ge k\} \\
> &= P\{\bigcup_{|S| = k}^{|S| \in \{k ... n\}} \{\text{events in}\ S\}\} \\
> & \text{by union bound:} \\
> &\le \text{(number of sets of size k drawn from n elements)} * \frac{1}{n^k} \\
> &={n \choose k} * \frac{1}{n^k}\\
> & \text{since}\ (\frac{n}{k})^k \le {n \choose k} \le (\frac{en}{k})^k\\
> &\le (\frac{en}{k})^k * \frac{1}{n^k} \\
> &= (\frac{e}{k})^k
> \end{align*}
> $$

With $P\{load (i) \ge k\} \le (\frac{e}{k})^k$ we can get (Note: by union bound it is easy to prove $P\{\exists i,\ s.t.\ load (i) \ge k\} \le c'(\frac{e}{k})^k * n$):
$$
P\{\exists i, \ s.t.\ load(i) \ge k\} \le \frac{c'}{k!} * n
$$
For which k this prob << 1 (is extremely small)

$k!$ approximation: using Stirling's approximation: $\sqrt{2 \pi k}(\frac{k}{e})^k \le k! \le e\sqrt{k}(\frac{k}{e})^k$ , we have:

$$
k! \ge (k/e)^k
$$

$$
P\{\exists i,\ s.t.\ load (i) \ge k\} \le \frac{c'}{k!} * n \le c'(\frac{e}{k})^k * n
$$

let $k = \frac{c^* logn}{loglogn}$, right side becomes very small very quickly when $c^* >> 1$, in this case, almost ($prob \approx 1$) all values of $load(i)<k$, therefore, the upper bound of expectation of maximum load is  $O(k)$:
$$
\mathbb{E} \max_i \ load(i) \sim O(k) = O(\frac{logn}{loglogn})
$$


> Proof for $c'(\frac{e}{k})^k * n$ approximates 0 when $k = \frac{c^* logn}{loglogn}$ :
> $$
> \begin{align*}
> &c'(\frac{e}{k})^k * n \\
> &= c'exp(k*log(\frac{e}{k})) * n \\
> &= c'exp(k - klog(k))*n \\
> &= c'exp(\frac{c^*log(n)}{loglog(n)} - \frac{c^*log(n)}{loglog(n)}log(\frac{c^*log(n)}{loglog(n)})) * n \\
> &= c'exp(\frac{-c^{**}log(n)}{loglog(n)} - c^*log(n) + \frac{c^*log(n) * logloglog(n)}{loglog(n)}) * n \\
> &\text{where} \ c^{**} = c^*log(c^*) - c^*, \text{and}\ c^{**} > 0, \text{when}\ c^* > e\\
> &\le c'exp(- c^*log(n) * (1 - \frac{logloglog(n)}{loglog(n)})) * n\\
> &\text{since the term after 1 is less than 1, and converges to 0 as n approaches}\ \infty, \\
> &\text{for a given n, it will have an upper bound constant value.}\\
> &\le c'(n^{-1})^{c^{***}} * n
> \end{align*}
> $$
> value of $(\frac{e}{k})^k * n$ can be validated with the following python program:
>
> ```
> def k(n):
>     const = 1According to the rule of "inherit from more specific class", 
>     return const * math.log(n)/math.log(math.log(n))
> 
> def bound(n):
>     return (math.e/k(n)) ** k(n) * n
> 
> for i in range(3, 10):
>     print(bound(10**i))
> ```
>
> Result:
>
> ```
> # c* = 1
> 375.8857631133321
> 1731.9692710497748
> 7489.40050462691
> 30970.491239128318
> 123752.93253396139
> 480995.2720927469
> 1826802.3673114406
> 
> # c* = 10
> 1.0187311899688037e-37
> 8.007347680669828e-46
> 4.239976121434161e-54
> 1.9716844163751623e-62
> 8.822596094106527e-71
> 3.940753770345024e-79
> 1.7850935085535476e-87
> ```
>
> 

---

## Power of 2 choices

This algorithm is mentioned [here by NGINX](https://www.nginx.com/blog/nginx-power-of-two-choices-load-balancing-algorithm/) 

### Definition

Suppose there are $T,\ s.t. T = \frac{n}{e^3} \sim \frac{n}{20}$ requests, and $n$ bins.

Pick bins $i$ and $j$ at random, assign the ball to the least loaded bin by comparing load(i) and load(j).
$$
w.h.p. \quad \max_i load(i) \le c*\log\log n​
$$

### Proof

Define graph $G$ with its nodes equal to bins. 

T edges, (connect i and j when choose)

P("ordered" edge (i, j)) = $\frac{1}{n^2}$ at some step a. (because there are n^2 edges, i->j and j->i)

**Lemma 1**: w.h.p. the size (vertex number) of the largest connected component $\le 5 \log n$ (small connected component)

**Lemma 2**: w.h.p the following holds:

$\forall S \subseteq V$, (V: vertices set) the average degree in $G[S]$ is at most 5.

Lemma 1 + Lemma 2 will prove our theorem:

If $G$ satisfies Lemma 1 and Lemma 2:

Component C, |C| <= 5

partition vertices in C in O(loglogn) layers: L1, L2, L3, ...

L1 = {v \in C: deg v <= 9 in G[C]}

L2 = {v \in C\L1: deg v < 10 in G[C\L1]}

...

Li = {v \in C\{L1 \cup ... \cup L_{i-1}}: deg v < 10 in G[C\{L1 \cup ... \cup L_{i-1}}]}

avg deg in C => |L1| >= |C|/2



#### Lemma 1 proof

#### Lemma 2 proof

