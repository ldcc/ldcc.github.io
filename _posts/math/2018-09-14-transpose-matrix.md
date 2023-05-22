---
layout: post
title: Matrix Transpose
categories: 数学笔记
tags: mathematics matrix linear-algebra poset python
date: 2018-09-14
mathjax: true
---

矩阵的转置，就是交换一个矩阵的行和列，实际上这种操作在日常生活中使用得非常频繁。

$$
\newcommand\tuple[2]{\langle #1,#2 \rangle}
\newcommand\quaternion[4]{\langle #1,#2,#3,#4 \rangle}
\newcommand\pr{\mathrm{P_R}}
\newcommand\br{\mathrm{R}}
\newcommand\bo{\mathrm{O}}
$$ Preliminary
{: .hide}

设有偏序集 $(O, \preccurlyeq)$，有二元关系 $\br$ 以及偏序关系 $\pr$，其中：

$$
\pr :=
\left \{
    \tuple{X}{Y}
    \mid
    O \supseteq \tuple{a}{b} \xrightarrow{\br} \{X,Y\}
\right \}
$$

且有偏序集 $(S, \pr)$，及 $A \cup B \cup C \subseteq S$，则：

$$
(A,\pr) \cup (B,\pr) \cup (C,\pr) \subseteq (S,\pr)
$$

此时偏序关系 $\pr$ 的偏序取决于偏序关系 $\preccurlyeq_O$，由此可得：

$$
O \times A \cup O \times B \cup O \times C \subseteq \br
$$

此时将 $O$，$A$，$B$，$C$ 组成一个 $m \times n$ 的矩阵 $M$：

$$
M = \begin{bmatrix}
    O_1 & \dots & O_n \\
    A_1 & \dots & A_n \\
    B_1 & \dots & B_n \\
    C_1 & \dots & C_n
\end{bmatrix}
$$

{% highlight python %}
M = [O, A, B, C]
{% endhighlight %}

此时我要插入一行 $D \subset S$ 可以这样：

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
而当我要插入一列 $m$ 元组比如 $\quaternion{O_k}{A_k}{B_k}{C_k}$ 时，插入的次数就达到了 $\bo(m)$ 次。
考虑到新矩阵的所有行仍需满足 $O$ 上的偏序关系 $\preccurlyeq_O$，最坏则要搜索 $\bo(mn)$ 次。

当这种插入操作非常频繁时，则可以考虑对矩阵进行转置，即交换行和列：

$$
M^\top = 
\left\{ 
    x \cup \{ x \xrightarrow{\br} S \} \mid  x \in M_O
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

转置后的矩阵 $M^\top$ 为一个 $n \times m$ 的矩阵。

由于我在处理数据的过程中使用的数据结构是列表而非集合，所以数据的偏序关系在最开始生成的时候就已经被确定下来了。

加上 Python 自身提供的 List-Comprehension 功能，转置的过程可以书写成更加简单的形式。

另外，如果在置转时将 $M^\top$ 中所有与 $O$ 持有二元关系 $\br$ 的元素组成一个 $m$ 元组，$M^\top$ 就能成为了一种非常方便操作的元组向量结构了：

$$
M^\top =
\Big\{
\big\langle \tau_i \big\rangle_{\tau \ \in \ M} \mid i \in M_O
\Big\},
\qquad (M^\top)_{i,j} \equiv M_{j,i}
$$

$$
\text{即：}\qquad
\begin{bmatrix}
    \tau_1 \\
    \vdots \\
    \tau_n
\end{bmatrix},
\qquad \tau \subseteq \quaternion{O}{A}{B}{C}
$$

{% highlight python %}
Mt = [tuple(row[i] for row in M) for i in column]
{% endhighlight %}

完整代码：<https://gist.github.com/ldcc/23e64a7c01e95b49f912f39e5dd37bff>。
