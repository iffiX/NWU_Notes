## Bloom filters

---

### Introduction

How to store a set, defined with following methods:

```
set.add(x)
set.remove(x)
set.contains(x)
```

Ideas: 
1. Bit set (every bit for a specific property), efficient, can only store pre defined fixed numbers

2. Hash table (hash sets)
     Tree (balanced)
     
     + Fast O(1)
     - Require a lot of memory, objects are complex
3. Trie (Prefix trees, for strings)
4. Bloom filters 
     if $x \in S$, return *yes*
     if $x \notin S$, return with $P_{no} \rightarrow 1$, $P_{yes} \rightarrow 0$ 
     
     - Do not support removing elements

----
### Naive bloom filters

(Assumption: set is sparse, compared to element universe $u$.)

We want to store $m$ elements, with $k$ bits per element.
Let size $n = mk$ (total size in bits)

$T$: Bit set of size $n$ (the underlying storage used to represent values)
$h$: hash function (from universal hash family / ROM), where $h: u \rightarrow \{0, ..., n\}$

Define the following methods for set:

```
def add(x):
    id = h(x)
    T[id] = 1
    
def test(x):
    id = h(x)
    return T[id]
```

**lemma**: 
Let $x_1, ..., x_m$ be distinct elements inserted in the naive bloom filter, $y$ is an element not in $\{x_1, ..., x_m\}$)
then: 
$$
P\{test(y)=1\} <= 1/k
$$
**proof**: 
$$
\begin{align*}
&test(y) = 1 \Leftrightarrow \exists i, h(x_i) = h(y) \\
\end{align*}
$$
then:
$$
\begin{align*}
&P\{test(y) = 1\} = P(\bigcup_i \ {h(x_i) = h(y)}) \\
&= \sum_{i=1}^m P\{h(x_i) = h(y)\} \\
&\le \frac{1}{n} *m \\
&= \frac{1}{m*k} *m \\
&= \frac{1}{k}
\end{align*}
$$



---
### Bloom filters

$h_1, ..., h_k$: hash functions (ROM, independent from each other), where $h_1, ..., h_k: u \rightarrow \{0, ..., n\}$
Let size $n = mk$

Define the following methods for set:

```
def add(x):
    for i = 1...k:
        T[h_i(x)] = 1
    
def test(x):
    return all(T[h_i(x)] = 1 for all i)
```

#### Informal proof
$$
P\{test(y) = 1\} = P\{\forall i, \ T[h_i(y)] = 1\}
$$

The RHS of this equation could be decomposed into:
$$
\begin{align*}
&P\{\forall i, \ T[h_i(y)] = 1\} \\

&= \prod_{i=1}^k P\{T[h_i(y)] = 1\} \\
&= (P\{T[h(y)] = 1\})^k \\
\end{align*}
$$
**Note:** The independence assumption of this transformation is **incorrect**, we will cover this part in the next section

Therefore, the probability of one of $k$ hash values of $y$ having no collision in table $T$ is: 
$$
\begin{align*}
&P\{T[h(y)] = 0\} \\
&= P\{\forall i,j (i=1...k, j=1...m),\quad h_i(y) != h_i(x_j) \} \\
&= (1 - \frac{1}{n})^{mk} \\
&= (1 - \frac{1}{mk})^{mk} \\
&= 1/e \qquad (\text{when} \lim_{mk \rightarrow \infty})\\
\end{align*}
$$
With this result we can compute the value of probability $P\{\forall i, \ T[h_i(y)] = 1\}$:
$$
\begin{align*}
&P\{\forall i, \ T[h_i(y)] = 1\} \\

&= \prod_{i=1}^k P\{T[h_i(y)] = 1\} \\
&= (P\{T[h(y)] = 1\})^k \\
\\
&\text{Note: O(1) is a remainder, since}\ mk\ \text{is not}\ \infty\\
&= (1 - \frac{1}{e} + O(1))^k\\
\end{align*}
$$
Therefore the upper bound of the probability of a false positive (where all $T[h_i(y)] = 1$) is:
$$
P\{\text{false positive}\} \le (1 - 1/e)^k
$$


#### Why informal proof is incorrect

More detailed analysis could be found in this paper: [On the false-positive rate of bloom filters](http://people.scs.carleton.ca/~kranakis/Papers/TR-07-07.pdf)

Lets enumerate all positive conditions in a simple example, where $m=1, k=2, n=mk=2$

Let $A$ be the hash value of $h_1$, $B$ be the hash value of $h_2$, we have the following combination table:

| bits in T |      | bits of h(y) |      |
| --------- | ---- | ------------ | ---- |
| B         | A    | B            | A    |
| B         | A    | A            | B    |
| B         | A    | AB           | -    |
| B         | A    | -            | AB   |
| A         | B    | B            | A    |
| A         | B    | A            | B    |
| A         | B    | AB           | -    |
| A         | B    | -            | AB   |
| AB        | -    | B            | A    |
| AB        | -    | A            | B    |
| AB        | -    | AB           | -    |
| AB        | -    | -            | AB   |
| -         | AB   | B            | A    |
| -         | AB   | A            | B    |
| -         | AB   | AB           | -    |
| -         | AB   | -            | AB   |

As we can see:
$$
\begin{align*}
&P\{h_1(y) = 1\} = P\{h_2(y) = 1\} = 12 / 16 = 3/4 \\
&P\{h_1(y) = 1,h_2(y) = 1\} = 10 / 16 = 5/8
\end{align*}
$$
These two probabilities are not independent in this case, because when table $T$ is fully occupied,   $P\{h_1(y) = 1,h_2(y) = 1\}$ becomes larger, in the upper 8 rows, this probability is 1, while in the lower eight rows, this probability is $1/4$, which is exactly $1/2 * 1/2$, and $P\{h_1(y) = 1\} = P\{h_2(y) = 1\} = 1/2$ in the lower eight rows, so independence only stands when the **load of table T** is not close to 1.

#### Formal proof
Let $n = 2mk$ in this case

**Theorem**: 

After inserting $x_1, ..., x_m$ in Bloom filter, the upper bound of false positive probability is constrained by:

$$
P\{test(y) = 1\} <= \frac{1}{2^k} = \frac{1}{2^{n/2m}} < (1 - 1/e)^{n/2m}
$$
**Proof**:
Let $\mathcal{S}$ be the set of bits set after we insert $x_1, ..., x_m$, there is inequality:
$$
|S| <= mk = n/2
$$
For false positive probability there is:
$$
\begin{align*}
&P\{test(y) = 1\} \\
&= P\{\forall i,\ T[h_i(y)] = 1\} \\
&= P\{h_1(y), h_2(y), ... h_k(y) \in \mathcal{S}\} \\
&= \mathbb{E}_S P\{h_1(y), ..., h_k(y) \ in\ \mathcal{S} | \mathcal{S} = S\} \quad \text{(expectation of probability when given S)}\\
\\
& \text{if the second term below, i.e. the conditional probability is proven} \le 1/2^k\\
&= \sum_{S'} P\{S=S'\} * P\{h_1(y), ..., h_k(y) \in \mathcal{S} | \mathcal{S} = S\} \\
& \text{then:}\\
&\le 1/2^k
\end{align*}
$$
Now goal becomes proving: $ P\{h_1(y), ..., h_k(y) \in \mathcal{S} | \mathcal{S} = S\} \le 1/2^k$

(Why does $n = 2mk$ guarantee independence?)

(Assumption: all hashed bits are independent from each other)
$$
\begin{align*}
&P\{h_i(y) \in \mathcal{S} \forall i | \mathcal{S} = S\} \\
&= \prod_{i=1}^k P\{h_i(y) \in S | S\} \\
&= \prod_{i=1}^k \frac{|\mathcal{S}|}{n} \\
& \le1/2^k
\end{align*}
$$

---
### Hashing load

Suppose there are $n$ balls and $n$ bins, let $load(i)$ = number of balls in bin #i
$$
\mathbb{E} \max_i \ load(i) \sim O(\frac{logn}{loglogn})
$$

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

$P\{\exists i, \ s.t.\ load(i) \ge k\} \le \frac{c'}{k!} * n$
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

#### Another way to prove inequality

Another way to prove: $P\{load (i) \ge k\} \le (\frac{e}{k})^k$ (Note: by union bound it is easy to prove $P\{\exists i,\ s.t.\ load (i) \ge k\} \le c'(\frac{e}{k})^k * n$)

(there is probably some note errors around the union bound line)
$$
\begin{align*}
&P\{load(i) \ge k\} \\
&= P\{\bigcup_{|S| = k}^{|S| \in \{k ... n\}} \{\text{events in}\ S\}\} \\
& \text{by union bound:} \\
&\le \text{(number of sets of size k drawn from n elements)} * \frac{1}{n^k} \\
&={n \choose k} * \frac{1}{n^k}\\
& \text{since}\ (\frac{n}{k})^k \le {n \choose k} \le (\frac{en}{k})^k\\
&\le (\frac{en}{k})^k * \frac{1}{n^k} \\
&= (\frac{e}{k})^k
\end{align*}
$$


---
Power of 2 choices
pick bins i&j at random
assign the ball to the least loaded bin

compare load(i) and load(j)
w.h.p max_i load(i) <= c*loglogn








​             
