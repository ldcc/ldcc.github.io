---
layout: post
title: 转置矩阵
category: ALGO
date: 2018-09-14
---

关于矩阵的转置，其实就是交换一个矩阵的行和列。

$$
\newcommand{tuple}[2]{\langle #1,#2 \rangle}
\newcommand{quaternion}[4]{\langle #1,#2,#3,#4 \rangle}
\DeclareMathOperator{R}{R}
\DeclareMathOperator{O}{O}
\DeclareMathOperator{P}{P_{\R}}
$$

假设有偏序集 $$(O, \preccurlyeq)$$，有二元关系 $$\R$$ 以及偏序关系 $$\P$$，其中：

$$ 
\P :=
\left \{
    \tuple{X}{Y}
    \mid
    \tuple{a}{b} \subseteq O \rightarrow \tuple{a}{b} \xrightarrow{\R} \tuple{X}{Y}
\right \}
$$

且还有偏序集 $$(S, \P)$$，并且 $$A \cup B \cup C \subseteq S $$，那么：

$$ (A,\P) \cup (B,\P) \cup (C,\P) \subseteq (S,\P) $$

此时偏序关系 $$\P$$ 的偏序结果可以说完全取决于偏序关系 $$\preccurlyeq_O$$，并且：

$$ O \times A \cup O \times B \cup O \times C \subseteq \R $$

这时把 $$O \text{， } A \text{， } B \text{， } C$$ 组成一个 $$m \times n$$ 的矩阵，其中 $$n$$ 为 $$O$$ 的大小：

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

此时我要插入一行 $$D \subset S$$ 可以这样：

$$
M =
\begin{bmatrix}
    O_1 & \dots & O_n \\
    A_1 & \dots & A_n \\
    B_1 & \dots & B_n \\
    C_1 & \dots & C_n \\
    D_1 & \dots & D_n
\end{bmatrix}
$$

{% highlight python %}

M.append(D);

{% endhighlight %}

但当我要插入一列 $$m$$ 元组比如 $$\quaternion oabc$$ 时，插入的次数就达到了 $$\O(m)$$ 次。
考虑到新矩阵的所有行仍需满足 $$O$$ 上的偏序关系 $$\preccurlyeq_O$$，当使用链表作为数据结构时，最坏要搜索 $$\O(mn)$$ 次。

当这种插入操作非常频繁时，则可以考虑对矩阵进行转置，即交换行和列：

$$
M^\top = 
\left\{ 
    x \cup \{ x \xrightarrow{\R} S \} \mid  x \in M_O
\right\},
\qquad (M^\top)_{i,j} = M_{j,i}
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

转置后的矩阵 $$M^\top$$ 为一个 $$n \times m$$ 的矩阵。

另外，如果在置转时将 $$M^\top$$ 中所有与 $$O$$ 持有二元关系 $$\R$$ 的元素组成一个 $$m$$ 元组，$$M^\top$$ 就能成为了一种非常方便操作的元组向量结构了：

$$
M^\top \equiv
\left\{
\big\langle M_{:,i} \big\rangle_{j \in M_i} \mid i \in M
\right\},
\qquad (M^\top)_{i,j} \equiv M_{j,i}
$$

$$\text{即：}$$

$$
\bigg\{ e \mid e \in \quaternion{O}{A}{B}{C} \bigg\}
\equiv
\begin{bmatrix}
    \quaternion {O_1} {A_1} {B_1} {C_1} \\
    \vdots \\
    \quaternion {O_n} {A_n} {B_n} {C_n}
\end{bmatrix}
$$

{% highlight python %}

Mt = [tuple(row[i] for row in M) for i in column]

{% endhighlight %}

测试一下：

{% highlight python %}

Mt.append((4, 44, 444, 4444))
print(Mt)

{% endhighlight %}

[完整代码](https://gist.github.com/ldcc/23e64a7c01e95b49f912f39e5dd37bff)。

