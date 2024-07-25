# "Homomorphic Evaluation of the AES Circuit"学习笔记

## 参考链接

[Homomorphic Evaluation of the AES Circuit：解读 - PamShao - 博客园 (cnblogs.com)](https://www.cnblogs.com/pam-sh/p/16176394.html "博客园")

## 1、数学基础

模$q$剩余类整数环限定在$(-\lfloor q/2\rfloor,\lfloor q/2\rfloor]$，用$[z]_q$表示模$q$的整数规约到这个区间。

$n$次分圆多项式：

$$
\Phi_n(x) = \prod_{\substack{1\leq k\leq n \\ \gcd(k,n)=1}} (x-e^{2i\pi k/n})
$$

这里的$i$是虚数单位，其中$e^{2i\pi k/n}(gcd(k,n)=1)$称为$x^n-1$的$n$次本原单位根。

显然，$\Phi_n(x)$的次数$=\varphi(n)$，即欧拉函数

常见的低次分圆多项式有：

$\Phi_1(x)=x-1$

$\Phi_2(x)=x+1$

$\Phi_3(x)=x^2+x+1$

$\Phi_4(x)=x^2+1$

$\Phi_5(x)=x^4+x^3+x^2+x+1$

$......$

性质：

（1）$\Phi_{2^h}(x)=x^{2^{h-1}}+1$，特别的，如果$N=2^k$，则$2N$次分圆多项式为$\Phi_{2N}(x)=x^N+1$。

（2）$\Phi_n(x)|x^n-1$；$\forall k<n,\Phi_n(x)\nmid x^k-1$。

（3）分圆多项式在有理数域$\mathbb{Q}$上不可约。

分圆多项式的详细性质参考[分圆多项式和分圆域](https://wuli.wiki/online/Cycltm.html "小时百科")

由分圆多项式定义多项式环，$\mathbb{A}=\mathbb{Z}[x]/\phi_m(x)$。

$\mathbb{A}$是第$m$个分圆数域$\mathbb{Q}(\zeta_m)$的整数环。

将$\mathbb{A}_q$定义为次数不超过$\phi(m)-1$的模$q$约化的整数多项式的集合。

中间暂时省略跳过，下面来看AES的同态评估：

## AES-128算法回顾

10轮，每轮对$4\times 4$的字节矩阵（按列排）进行操作，如下：

| $M_{0,0}^0$ | $M_{0,1}^4$ | $M_{0,2}^8$    | $M_{0,3}^{12}$ |
| :------------ | ------------- | ---------------- | ---------------- |
| $M_{1,0}^1$ | $M_{1,1}^5$ | $M_{1,2}^9$    | $M_{1,3}^{13}$ |
| $M_{2,0}^2$ | $M_{2,1}^6$ | $M_{2,2}^{10}$ | $M_{2,3}^{14}$ |
| $M_{3,0}^3$ | $M_{3,1}^7$ | $M_{3,2}^{11}$ | $M_{3,3}^{15}$ |

每轮包含四个操作：轮秘钥加(异或)Add、字节替换(s盒)Sbox、行移位Shift、列混合Mix。

## AES的打包

$8|d$，$\phi(m)/d$个密文槽，每个密文可以存储至少$F_2^8$上的元素即一个字节，因此最少可以存储$\lfloor\frac{\phi(m)}{16d}\rfloor$个AES分组状态矩阵。

## the Frobenius automorphisms

富比尼自同构

等比数列求和公式：

$x+qx+q^2x+...+q^{n-1}x=(x-q^nx)/(1-q)$

令$x=1,q=b/a$，则$(1-(b/a)^n)/(1-b/a)=1+b/a+(b/a)^2+...+(b/a)^{n-1}$

所以  

$$
a^n-b^n)/(a-b)=a^{n-1}+a^{n-2}b+a^{n-3}b^2+...+b^{n-1}
$$

$x-\zeta^i|X^i-({\zeta^i})^i$
