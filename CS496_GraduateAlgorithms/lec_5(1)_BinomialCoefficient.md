## Binomial Coefficient

### Definition

**Binomial coefficient**:
$$
{n \choose k} = \frac{n!}{k!(n-k)!} \\
{n \choose k} = {n \choose n-k}
$$
**Binomial theorem**:
$$
(a+b)^n = \sum_{k=0}^n{n \choose k} a^k b^{n-k}
$$
**Binomial distribution**:
$$
B(n, p): P\{\sum_iX_i = k\} = {n \choose k}p^k q^{n-k}
$$
where $P\{X_i=1\}=p, P\{X_i=0\}=q, p+q=1$

**Central Limit Theorem**:

CLT applied to binomial distribution:
$$
{\displaystyle P(X=k)\to {\frac {1}{\sqrt {npq}}}\cdot {\frac {1}{\sqrt {2\pi }}}e^{-{\frac {1}{2}}x_{\mu _{n}}^{2}}}
$$

### Useful Inequalities

1. $(\frac{n}{k})^k \le {n \choose k} \lt (\frac{en}{k})^k$ Note: $k = \min(k, n-k)$
2. 





  