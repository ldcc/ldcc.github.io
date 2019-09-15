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
$$ Preliminary
{: .hide}

设有偏序集 $$(O, \preccurlyeq)$$，有二元关系 $$\R$$ 以及偏序关系 $$\P$$，其中：

$$ 
\P :=
\left \{
    \tuple{X}{Y}
    \mid
    \tuple{a}{b} \subseteq O \rightarrow \tuple{a}{b} \xrightarrow{\R} \tuple{X}{Y}
\right \}
$$

且有偏序集 $$(S, \P)$$，及 $$A \cup B \cup C \subseteq S $$，则：

$$ (A,\P) \cup (B,\P) \cup (C,\P) \subseteq (S,\P) $$

此时偏序关系 $$\P$$ 的偏序取决于偏序关系 $$\preccurlyeq_O$$，由此可得：

$$ O \times A \cup O \times B \cup O \times C \subseteq \R $$

此时将 $$O \text{， } A \text{， } B \text{， } C$$ 组成一个 $$m \times n$$ 的矩阵，其中 $$n$$ 为 $$O$$ 的大小：

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

而我要插入一列 $$m$$ 元组比如 $$\quaternion{O_k}{A_k}{B_k}{C_k}$$ 时，插入的次数就达到了 $$\O(m)$$ 次。
考虑到新矩阵的所有行仍需满足 $$O$$ 上的偏序关系 $$\preccurlyeq_O$$，当使用链表作为数据结构时，最坏要搜索 $$\O(mn)$$ 次。

当这种插入操作非常频繁时，则可以考虑对矩阵进行转置，即交换行和列：

$$
M^\top = 
\left\{ 
    x \cup \{ x \xrightarrow{\R} S \} \mid  x \in M_O
\right\},
\qquad (M^\top)_{i,j} = M_{j,i}
$$

$$\text{即：}\qquad
\begin{bmatrix}
    O_1 & A_1 & B_1 & C_1 \\
    \vdots & \vdots & \vdots & \vdots \\
    O_n & A_n & B_n & C_n
\end{bmatrix}
$$

{% highlight python %}

column = range(len(M[0]))
mapTo = lambda construct: lambda f, l: construct(map(f, l))
Mt = mapTo(list)(lambda i: mapTo(list)(lambda row: row[i], M), column)

{% endhighlight %}

转置后的矩阵 $$M^\top$$ 为一个 $$n \times m$$ 的矩阵。

由于我在处理数据的过程中使用的数据结构是列表而非集合，所以数据的偏序关系在最开始生成时就已经被确定下来了。

加上 Python 语言本身提供的 List-Comprehension 功能，转置的过程可以书写成更加简单的形式。

另外，如果在置转时将 $$M^\top$$ 中所有与 $$O$$ 持有二元关系 $$\R$$ 的元素组成一个 $$m$$ 元组，$$M^\top$$ 就能成为了一种非常方便操作的元组向量结构了：

$$
M^\top =
\left\{
\big\langle M_{:,i} \big\rangle_{j \in M_i} \mid i \in M
\right\},
\qquad (M^\top)_{i,j} \equiv M_{j,i}
$$

$$
\text{即：}\qquad
\begin{bmatrix}
    \tau_1 \\
    \vdots \\
    \tau_n
\end{bmatrix},
\qquad \tau \in \quaternion{O}{A}{B}{C}
$$

{% highlight python %}

Mt = [tuple(row[i] for row in M) for i in column]

{% endhighlight %}

完整代码：<https://gist.github.com/ldcc/23e64a7c01e95b49f912f39e5dd37bff>。
