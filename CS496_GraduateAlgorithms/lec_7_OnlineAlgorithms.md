## Online Algorithms

### Ski rental problem

Scenario 1:

from position X to Y, either by skis for B dollars, or rent skis for A dollars / day.

Ski n times:	Rent: A * n dollars, Buy: B, select min(A * n, B)

Scenario 2:

Allow buying in the middle, rent some days before buying it

*** Yale's minimax principle

Theorem: There exists a 2-competitive (2 is competitive ratio) algorithm for ski rental defined by parameters (A, B, set of {ski days})

competitive ratio = max_I cost_alg(I) / cost_opt(I)

I -> instance of your problem

alg(I) <= C * opt(I)

(definition is for minimization algorithm, deterministic)

(For maximization algorithm, (1/alpha is another representation of something smaller than 1, with alpha >= 1):)

![image-20201104013535259](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201104013535259.png)

Proof of theorem:

let A = 1; B is integer

Algorithm: we rent for the first (B-1) days.

On day B, we buy skis

Need to show: alg(I) <= 2 * Opt(I)



let n be the # of days we ski, opt = min(n, B)

For our algorithm there are two cases:

a. n < B, cost is n

b. n>= B cost is 2B-1

if n > 2B we are done

let I = {1,...,n}

Alg(I) = (n-1)*1 + B = (B-1) + n

opt(I) = min(B, n)

alg(I) / opt(I) >= 2 * (B-1)/B = 2 (1 - 1/B)

![image-20201028011335860](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201028011335860.png)

Theorem 2:

Exists randomized algorithm with competitive ratio:

![image-20201028011610045](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201028011610045.png)

![image-20201028013227094](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201028013227094.png)

![image-20201028014200301](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201028014200301.png)

![image-20201030004009732](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201030004009732.png)

![image-20201030004656036](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201030004656036.png)

## LRU Caching

LRU policy: keeps track of when page was requestes / used last time. Removed the least used page.

Theorem: LRU is k-competitive. (k is cache size in page number)

ALG(I) <= k * OPT(I)

![image-20201030010922375](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201030010922375.png)

![image-20201030011850143](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201030011850143.png)

We request pages from a pool of k pages

LRU makes <= k cache misses

![image-20201030012926163](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201030012926163.png)

![image-20201030013404304](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201030013404304.png)



### Beyond worst-case analysis (BWCA)

(There are many ways to perform BWCA, we will use Resource augmentation here, 

We are using this price (augmented resource) to analyze how well our algorithm can perform compared to the optimum situation which we will not be able to reach)

LRU vs OPT

suppose LRU is working with a cache size slightly bigger than the OPT algorithm, eg: 2k pages

LRU - 2k, OPT - k (2 could be some number = 1 + $\epsilon$)

(because in most cases LRU is dealing with more complex scenarios than an offline scenario)

Theorem: LRU is 2-competitive in resource augmentation

Proof:

also split runtime into blocks, where each block have 2k distinct page requests (Note the same page could be requested multiple times)

![image-20201104015130230](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201104015130230.png)

LRU: if some page is evicted, the page must be some page requested before current time block. (since cache size is 2k), (worst case) evict at most 2k pages

OPT: (has only k pages in cache), (worst case) >= k misses

therefore it is 2-competitive