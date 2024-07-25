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

（4）分圆多项式一定是整系数多项式。

分圆多项式的详细性质参考[分圆多项式和分圆域](https://wuli.wiki/online/Cycltm.html "小时百科")

由分圆多项式定义多项式环，$\mathbb{A}=\mathbb{Z}[x]/\Phi_m(x)$。

$\mathbb{A}$是第$m$个分圆数域$\mathbb{Q}(\zeta_m)$的整数环，$\zeta_m$是$m$次本原单位根。

这里$\mathbb{Q}(\zeta_m)=\sum_{i=0}^{\varphi(m)-1}a_i\zeta_m^i$，$a_i\in \mathbb{Q}$，是有理数域的单扩张。即$\mathbb{Q}(\zeta_m)$也是一个域且是代数数域即有理数的扩域。

将$\mathbb{A}_q$定义为次数不超过$\varphi(m)-1$的模$q$约化的整数多项式的集合。

$\mathbb{A}_q$是模$q$整系数多项式环，是否构成有限域？不一定，除非$q$是素数。

$a\in\mathbb{A}_q$，则可以将看做$a$看做多项式，即$a(X)=\sum_{i=0}^{\varphi(m)-1}a_iX^i$。

因此$a=\langle a_0,a_1....,a_{\varphi(m)-1}\rangle\in (\mathbb{Z}/{q\mathbb{Z})^{\varphi(m)}}$

上面这种称为系数表示法，还有计算表示法：令$b_i=a(\zeta_m^i)=\sum_{j=0}^{\varphi(m)-1}a_j(\zeta_m^i)^j\in\mathbb{Q}(\zeta_m)$

则$b=\langle b_0,b_1,...,b_{\varphi(m)-1}\rangle=\langle a_0,a_1,...,a_{\varphi(m)-1}\rangle V_{\varphi(m)\times \varphi(m)}$

$V=\begin{bmatrix}1&1&1&...&1\\1&\zeta_m&\zeta_m^2&...&\zeta_m^{\varphi(m)-1}\\...\\1&\zeta_m^{\varphi(m)-1}&(\zeta_m^2)^{\varphi(m)-1}&...&(\zeta_m^{\varphi(m)-1})^{\varphi(m)-1}\end{bmatrix}$

显然$V$是一个特殊的范德矩阵，为什么说特殊？因为第二行可以由同一个元素的幂次来表示，这也是FFT里面常用的。

如果已知$a,V$，可以利用FFT来计算$b$。

等比数列求和公式：

$x+qx+q^2x+...+q^{n-1}x=(x-q^nx)/(1-q)$

令$x=1,q=b/a$，则$(1-(b/a)^n)/(1-b/a)=1+b/a+(b/a)^2+...+(b/a)^{n-1}$

所以

$$
(a^n-b^n)/(a-b)=a^{n-1}+a^{n-2}b+a^{n-3}b^2+...+b^{n-1}
$$

$X-\zeta_m^i|X^j-({\zeta_m^i})^j$

所以$X-\zeta_m^i|a(X)-a(\zeta_m^i)$，即$X-\zeta_m^i|a(X)-b_i$。

所以有$a(X)\equiv b_i(mod\;X-\zeta_m^i)$，这正好是多项式形式的中国剩余定理CRT。

这里$b_i$是否是整数？暂且搁置。



中间暂时省略跳过，下面来看AES的同态评估：。

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

BGV方案

5个自同构：加法、乘法、自同构、密钥交换、模交换。

这里的乘法指的是张量积。

伽罗瓦理论参考：

[抽象代数课程笔记 III —— 域论、伽罗瓦理论 - qAlex_Weiq - 博客园 (cnblogs.com)](https://www.cnblogs.com/alex-wei/p/18195700/Abstract_Algebra_Field_Theory_and_Galois_Theory "博客园")
