

## Linear Programming

### Introduction

convexity

![image-20201120021744811](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201120021744811.png)

1. Simplex method

Maximum value must locate on some edge, therefore LP solver each time choose a direction to go, and that's called the "pivotal"

2. Ellipsoid method (polynomial)

![image-20201120022910528](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201120022910528.png)

Each time choose the ellipsoid which contains the solution, make ellipsoid smaller and smaller

3. Interior point methods

![image-20201120022858428](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201120022858428.png)

Similar to gradient descent



### Duality

Compute the set of coefficients which linearly composes constraints into our minimization target

![image-20201120023222392](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201120023222392.png)

![image-20201120023337572](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201120023337572.png)

![image-20201120023857200](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201120023857200.png)

(dual target)

![image-20201125013719047](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201125013719047.png)

(Note A^t coefficients for y)

(Strong duality should use = instead of >=)

![image-20201125014719110](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201125014719110.png)

![image-20201125015017838](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201125015017838.png)

![image-20201125015448203](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201125015448203.png)

and c - A^ty and Ax - b must be all 0 or respective x and y must be 0, because (A^ty - c <= 0, vector) and (Ax - b >= 0, vector) 

(complementary slackness is also known as the KKT condition)

![image-20201125015821501](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201125015821501.png)



y_i = 0 since there is no contact between the point and that hyperplane, then no support force, and y_i = 0

![image-20201125021302387](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201125021302387.png)

![image-20201125021505543](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201125021505543.png)

Farkas' Lemma indicates strong duality

![image-20201125024036315](/home/Administrator/iffi/.config/Typora/typora-user-images/image-20201125024036315.png)

