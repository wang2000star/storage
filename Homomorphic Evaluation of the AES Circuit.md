# "Homomorphic Evaluation of the AES Circuit"学习笔记

## 1、数学基础

模$q$剩余类整数环限定在$(-\lfloor q/2\rfloor,\lfloor q/2\rfloor]$，用$[z]_q$表示模$q$的整数规约到这个区间。

$n$次分圆多项式：

$$
\phi_n(x) = \prod_{\substack{1\leq k\leq n \\ \gcd(k,n)=1}} (x-e^{2i\pi k/n})

$$

其中$e^{2i\pi k/n}(gcd(k,n)=1)$称为$x^n-1$的$n$次本原单位根。

在不引起混淆时，$\phi_n(x)$的次数记作$\phi(n)$。

常见的低次分圆多项式有：

$\phi_1(x)=x-1$

$\phi_2(x)=x+1$

$\phi_3(x)=x^2+x+1$

$\phi_4(x)=x^2+1$

$\phi_5(x)=x^4+x^3+x^2+x+1$

$......$

性质：

（1）$\phi_{2^h}(x)=x^{2^{h-1}}+1$，特别的，如果$N=2^k$，则$2N$次分圆多项式为$\phi_{2N}(x)=x^N+1$。

（2）$\phi_n(x)|x^n-1$；$\forall k<n,\phi_n(x)\nmid x^k-1$。

（3）分圆多项式在有理数域$\mathbb{Q}$上不可约。

由分圆多项式定义多项式环，$\mathbb{A}=\mathbb{Z}[x]/\phi_m(x)$。

$\mathbb{A}$是第$m$个分圆数域$\mathbb{Q}(\zeta_m)$的整数环。

将$\mathbb{A}_q$定义为次数不超过$\phi(m)-1$的模$q$约化的整数多项式的集合。

中间暂时省略跳过，下面来看AES的同态评估：
