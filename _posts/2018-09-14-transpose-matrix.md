---
layout: post
title: 转置矩阵
category: Algorithm
date: 2018-09-14
---

刷题时的偶然产物，其实就是交换一个矩阵的行和列（然而我是在后来才知道转置这个概念）。
$$
\newcommand{tuple}[2]{\langle #1,#2 \rangle}
\newcommand{quaternion}[4]{\langle #1,#2,#3,#4 \rangle}
\newcommand{ABC}{A \text{， } B \text{， } C}
\DeclareMathOperator{R}{R}
\DeclareMathOperator{O}{O}
\DeclareMathOperator{T}{T}
\DeclareMathOperator{P}{P_{\R}}
$$


假设有偏序集 $$(O,\preccurlyeq)$$，又有二元关系 $$\R$$ 以及偏序关系 $$\P$$，其中：

$$ 
\P =
\left \{
    \tuple{x}{y} \subseteq O
    \mid
    \exists a,b \in S \rightarrow \bigg( \tuple{x}{y} \xrightarrow{\R} \tuple{a}{b} \bigg)
\right \}
$$

如果有 $$A \cup B \cup C \subseteq S $$，那么就有：

$$ (A,\P) \cup (B,\P) \cup (C,\P) \subseteq (S,\P) $$

并且：

$$ O \times A \cup O \times B \cup O \times C \subseteq \R $$

如果把 $$O \text{， } \ABC$$ 组成一个 $$n \times m$$ 的矩阵，其中 $$n$$ 为 $$O$$ 的大小：

$$
M =
\begin{bmatrix}
    O_1 & \dots & O_n \\
    A_1 & \dots & A_n \\
    B_1 & \dots & B_n \\
    C_1 & \dots & C_n
\end{bmatrix}
$$

{% highlight python %}

M = [O, A, B, C]

{% endhighlight %}

那么 $$\ABC$$ 的偏序则取决于 $$\preccurlyeq_O$$，此时我要插入一行 $$D \subseteq S$$ 可以这样：

{% highlight python %}

M.append(D);

{% endhighlight %}

而当我要插入一列 $$m$$ 元组比如 $$\quaternion oabc$$ 时，那么插入的次数就有 $$\O(m)$$ 次。
考虑到新矩阵的所有行仍需满足 $$O$$ 上的偏序关系 $$\preccurlyeq_O$$，当使用链表作为数据结构时，最坏要搜索 $$\O(nm)$$ 次。

当这种插入操作非常频繁时，那么则可以考虑对矩阵进行转置的操作，即交换行和列：

$$
M^{\T} = 
\left\{ 
    x \cup \{ x \xrightarrow{\R} S \} \mid  x \in M_O
\right\}
$$

$$\text{即：}$$

$$
\begin{bmatrix}
    O_1 & A_1 & B_1 & C_1 \\
    \vdots & \vdots & \vdots & \vdots \\
    O_n & A_n & B_n & C_n
\end{bmatrix}
$$

{% highlight python %}

column = range(len(M[0]))
Mt = [[row[i] for row in M] for i in column]

{% endhighlight %}

可以看到变换后的矩阵 $$M^{\T}$$ 为一个 $$m \times n$$ 的矩阵。

如果将所有与 $$O$$ 相互持有二元关系 $$\R$$ 的元素都组成一个元组，相当于构造了一个数组：

$$
M^{\T'} = 
\left\{
\big\langle M_{ji} \big\rangle_{j \in M_i} \mid i \in M
\right\}
$$

$$\text{即：}$$

$$
\begin{bmatrix}
    \quaternion {O_1} {A_1} {B_1} {C_1} \\
    \vdots \\
    \quaternion {O_n} {A_n} {B_n} {C_n}
\end{bmatrix}
=
\bigg\{ e \mid e \in \quaternion{O}{A}{B}{C} \bigg\}
$$

{% highlight python %}

Mt = [tuple(row[i] for row in M) for i in column]

{% endhighlight %}

测试一下：

{% highlight python %}

Mt.append((4, 44, 444, 4444))
print(Mt)

{% endhighlight %}

[完整代码](https://gist.github.com/ldcc/23e64a7c01e95b49f912f39e5dd37bff)

完。