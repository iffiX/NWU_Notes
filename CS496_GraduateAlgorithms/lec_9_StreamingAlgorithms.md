## Streaming algorithms

### Introduction to the Misra-Gries algorithm

#### Problem Setting

Suppose we are observing a contiguously flowing stream, we want to detect "heavy hitters" in this stream.

#### How?

Lets define the following algorithm, which

```
cur_element = null
counter = 0
for e in stream:
	if e = cur_element or e = null:
		cur_element = e
		counter = counter + 1
	else:
		counter = counter - 1
		if counter = 0:
			cur_element = null
return cur_element
```

Theorem: if some element x occurs more than $\frac{n}{2}$ times in a stream with n elements, then the algorithm will return x

Proof:

​	define (a virtual counter for each distinct element in the stream):

​		v.counter(y) =  counter, if current element = y 

​							    -counter, otherwise

​	goal:

​		v.counter(x) > 0 at the end of the algorithm



​	for v.counter(y)

​		if e = y, v.counter(y) += 1 

​		if e != y, virtual counter may decrease by 1 (only if previous v.counter(y) >= 0), >= previous v.counter(y) - 1

​	when we observe x: virtual counter is incremented,

​	other cases, maybe decremented.

​	value of v.counter(x) >= # occur of x - (n - # occur of x)  (all occurrences of x - no occurrences of x)

​										= 2 * # occur of x -n > 0

### The Misra-Gries algorithm

Goal of MG: Find elements that occur more than $\frac{1}{k+1}$ times, with the following restrictions:

- Keeps at most k active elements
- For each element, it has a counter counter[e] for e

```
def observe(e):
	if e is in the set of active elements:
		counter[e] += 1
	elif num_active_elements < k:
		counter[e] = 1
	else: # num_active_elements = k
		for x in active_elements:
			counter[x] -= 1
		remove x with counter[x] = 0
```

(Could implement wit search tree or hash table)

**Theorem**: every element that occurs > $\frac{1}{k+1}$ times is output by this algorithm

**Proof**: 

$v.counter[e] = k * counter(e) - \sum_{\text{x is active}} counter[x]$

counter(e) = counter[e], if e is active, otherwise 0

**Claim**:

If we observe e, then virtual counter is incremented by k:

1. When e is active: + k * 1
2. When e is not active, and num_active_elements < k, + k * 1
3. When e is not active, and num_active_elements = k, the sum part will decrease by k * 1, and the minus operator makes it increase k

otherwise, its value doesn't decrease by more than 1:

	1. When e is active, first part -=k, sumpart -= k-1, and turned by minus operator to += k-1, therefore total decrease is 1.
 	2. When e is not active, and num_active_elements < k, added elements cause a decrease of 1
 	3. When e is not active, and num_active_elements = k, increase k*1



e occurs > $\frac{n}{k+1}$ times, where n = # of elements in the stream.

v.counter[e] >= $\frac{n}{k+1} * k - (n - \frac{n}{k+1}) = 0$

Therefore every element that occurs > $\frac{1}{k+1}$times will be output by this algorithm.

### Parallel?

The MG algorithm is a serial algorithm. We cannot apply this to a parallel scenario without further modifications.

### Introduction to the count-min sketch

For every x, we can return cm(e). - estimate on the # occurrences of e, in stream

Let m(e) be the true # of occurrences

Guarantee:

	- cm(e) >= m(e)
	- cm(r) <= m(e) + $\epsilon n$ w.h.p.

(epsilon is very small)

Let $h_1, ..., h_k$ be 2-universal hash functions

Define a counter table: $T^{\{k, l\}}$

```
def add(x):
	for i in {1, ..., k}:
		T[i, h_i(x)] += 1

def freq(x):
	return min_i (T[i, h_i(x)])
```

**Claim 1** :

cm(x) >= m(x)

cm(x)

m(x) # of times we observe x

y's that increment the counter



F = T[i, h_i(x)] - m(x)

   = \sum_{y in stream, y!= x} \I{h_i(x) = h_i(y)}



E[F] = \I {y \in S : y != x} / l    (l is an unknown constant, the ratio of h_i(x) = h_i(y) in all y != x)

​        <= n/l    (or (n-m(x))/l)   (n is the size of stream)

(Markov inequality)

P{F >= \epsilon n} <= (n/l)/ \epsilon * n = 1/(\epsilon l)

P{\forall i, F_i >= \epsilon n} <= (1/(\epsilon l)) ^ k

Let l = \ceil(e/\epsilon)



P{mc(x) >= m(x) + \epsilon n} <= $\frac{1}{e^k}$

Memory:

The # counters = k*l  $\simeq \frac{e k}{\epsilon}$

Parallel:

The sketches can be combined



### Counting the number of distinct elements

Algorithm 1 (sparse sampling):

 -  D - dict / hash table

 -  h - hash function (2-universal / ROM)

    h: u -> {0, ..., 2^M-1}

```
def process(x):
	s1(y) - is the last bit of y
	if s1(h(x)) = 0:
		add x to D
		
	return 2 * size(D)
```

memory consumption: store n/2 elements if n is # of distinct elements

h(x) is random binary string of length M

why not choose with a random variable: value will be skewed

however, using last bit of hash (since same element hash to same function), sampling is less skewed

Expectation of the count is correct (because add probability is exactly half)

Using chernoff bound:

![image-20201111015725832](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201111015725832.png)

\ # distinct elements = n

P{ |ALG/2 - # distinct elements/2|  > \delta n/2}  = P{ |ALG - # distinct elements|  > \delta n} <= 2 * e^{-\frac{\delta^2 n}{6}}  (6 because we are using half of the elements, u = n/2)



Algorithm 2 (from Algorithm 1)

define k, a parameter

```
def process(x):
	let sk(y) be the suffix of y of length k
	if sk(h(x)) = "0...0" (all 0 sequence of length k):
		Add h(x) to D
	return 2^k * len(D)
```

X_i = \I {s_k(h(x)) = 0...0}

P{X_i = 1} = 1/2^k

E[\sum X_i] = n/2^k

memory consumption: m = n/2^k in expectation

(however, k couldn't be too large as we need to guarantee the error bound) 

P{|ALG - n| >= \delta n} <= 2 * e^{-\frac{\delta^2 n}{3 * 2^k}}  



Problem:

if n is small, the hitting probability (with specified hash suffix) is low, and since n is not known in advance, we cannot fix parameter k

We want to design an algorithm which dynamically adapts k



let function z(y) return the longest contiguous 0 suffix length, z(1001000) return 3

store (x_1, z(x_1)), ... , (x_n, z(x_n))

sort by z(x)

for k = 1, ..., log_2 n

count # of elements with z(x) >= k



Observation (second is done with union bound): 

![image-20201111023414488](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201111023414488.png)

(actually there is a better bound by using the properties of a geometric series, since it decrease exponentially fast from k* to k=0)

![image-20201113013819126](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201113013819126.png)

let m* = n / 2^k* (delta is a fixed value, by solving the error rate, we can compute m*)

![image-20201111024933366](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201111024933366.png)

Algorithm 3:

Get m* 

k = 1

if z(x) >= k: 

​	Add (x, z(x))

if len(D) >= m*:

​	Remove (x, z(x)) with z(x) <= k

​	k += 1

return 2^k * len(D)





