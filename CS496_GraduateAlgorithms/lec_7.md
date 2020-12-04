## Permutation routing in the hypercube

s1 -> t1

s2-> t2

s3 -> t3

share one network, queue for one unit of time if another packet is using the same path



example:

d-dim hyper cube, 

n=2^d nodes, each with encoding {0,1}^d

n_i and n_j are connected if only one bit of their encoding is different

n pairs (si, ti) each vertex wants to send a packet and each vertex wants to receive one packet

longest path is d edges, because you have d bits to flip

therefore low bound >= d steps  (if no collision will happen)



suppose routing is oblivious (each node chooses a path on its own):



if deterministic strategy: Delay ~ \sqrt{n} n=2^d

if randomized scheduling (vallant):

​    s_i first choose random intermediate destination v_i,  establish path s_i -> v_i -> t_I

​    use bit fixing strategy for s_i -> v_i -> t_i

​    

Bit fixing:

​    send packet from s to t, each time send to next node by correcting one bit (usually from left to right) of current encoding (init=s encoding) to a bit value  in t encoding 

---

Theorem: the maximum delay $\max_i delay(s_i, t_i) \le O(d)$, w.h.p (with high probability)

Proof:

need to prove $P\{ delay(s_i,t_i) \ge cd\} < o(1)\quad (e.g.:\frac{1}{n})$ where c is a constant value 

Denote the route between $s_i \rightarrow v_i$ by $R_i$

$R_i$ and $R_j$ if they intersect or share at least one edge, and cannot collide more than once 

(because one packet is going to be delayed by one time unit, and there is no shortcut, because between and point A and B, all paths have the same length, this is determined by the bit difference number between A and B)

$delay(R_i) \le \# \{R_i, R_j,interfere\}$



1. We choose $e \in R_i$

   would like to know$\mathbb{E} [\#j\ s.t.\ some\ edge\ e \in R_j | R_i]$

   E COULD BE REPRESENTED AS (p,1,s) -> (P, 0, s) (OR 0->1)

    when s_j -> v_j goes over e:

   v_j has prefix $P$, s_j has suffix $S$

   \# s_j with suffix $S$: $2^{d-|S|} = s^{|P|+1}$ 

   P {v_j that has prefix P} = 2^{d-|P|} / 2^d

   Therefore:

   > $\mathbb{E} [\#j\ s.t.\ some\ edge\ e \in R_j | R_i] \le 2$
   >
   >E[# Rj that interfere] <= 2 d
   >
   >= \sum \I{R_i & R_j interfere} -> let indicator be random variable X_i
   >
   >E [\sum X_i | R_i] <= 2d
   >
   >P {\sum X_i > 10d | Ri} \le e^{-u} * (eu\10d)^10d where u<=2d
   >
   ><= e^{-2d} (2e/10)^10d
   >
   ><= (e^-2 * (2e/10)^10)^d (d~logn)
   >
   >​     |-----------------------| small

